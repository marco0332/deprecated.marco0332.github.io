---
layout: post
title: Machine Learning - Seq2Seq + Attention
description: >
  Machine Learning - Seq2Seq + Attention에 대하여
excerpt_separator: <!--more-->
---

<!--more-->

# Machine Learning - Seq2Seq + Attention

![seq2seq](https://user-images.githubusercontent.com/27988544/64659430-b5a17e80-d476-11e9-8a38-6e7279ae4519.jpg)

## 구현

```python
import os

import tensorflow as tf
from tensorflow.python.keras import layers, models, Input
from tensorflow.python.keras.callbacks import ModelCheckpoint, EarlyStopping
from tensorflow.python.keras.optimizers import Adam
from tensorflow.python.keras.utils import to_categorical

import matplotlib.pyplot as plt
from tensorflow.python.keras import backend as K
import numpy as np

from model_metrics import rouge_compute, bleu_compute
import data
from configs import DEFINES


class Model:

    def __init__(self):
        # voca data를 저장한 디렉토리를 생성 및 경로설정
        DATA_OUT_PATH = './data/data_out/'
        data_out_path = os.path.join(os.getcwd(), DATA_OUT_PATH)
        os.makedirs(data_out_path, exist_ok=True)

        # 체크 포인트를 저장한 디렉토리를 생성 및 경로설정
        check_point_path = os.path.join(os.getcwd(), DEFINES.check_point_path)
        os.makedirs(check_point_path, exist_ok=True)

        # 데이터를 통한 사전 구성
        self.char2idx, self.idx2char, self.vocabulary_length = data.load_voc()

        # 모델에 사용할 파라메터 정의
        self.model_params = {
            'model_hidden_size': DEFINES.model_hidden_size,      # 가중치 크기
            'learning_rate': DEFINES.learning_rate,              # 학습율
            'embedding_size': DEFINES.embedding_size,            # 임베딩 크기
            'max_sequence_length': DEFINES.max_sequence_length,  # Sequence 최대 길이
            'multilayer': DEFINES.multilayer,                    # 다중 레이어 사용 여부
            'layer_size': DEFINES.layer_size,                    # 다중 레이어 사용시 층 수
            'dropout_rate': DEFINES.dropout_width                # 드랍아웃
        }
        self.seq2seq = None
        self.encoder_model = None
        self.predict_model = None
        self.history = None
        self.build()

    # Expand Layer Method
    def RepeatVectorLayer(self, rep, axis):

        return layers.Lambda(lambda x: K.repeat_elements(K.expand_dims(x, axis), rep, axis),
                             lambda x: tuple((x[0],) + x[1:axis] + (rep,) + x[axis:]))

    # model build
    def build(self):
        question_input = Input(shape=(self.model_params['max_sequence_length']), dtype='float32', name='question')
        text_input = Input(shape=(self.model_params['max_sequence_length']), dtype='float32', name='text')

        with tf.variable_scope('embedding') and tf.device('/gpu:0'):
            question_embedding_layer = layers.Embedding(self.vocabulary_length, self.model_params['embedding_size'])
            question_embedding = question_embedding_layer(question_input)
            text_embedding_layer = layers.Embedding(self.vocabulary_length, self.model_params['embedding_size'])
            text_embedding = text_embedding_layer(text_input)

        with tf.variable_scope('encoder_scope') and tf.device('/gpu:0'):
            encoder = layers.LSTM(self.model_params['model_hidden_size'],
                                  dropout=self.model_params['dropout_rate'],
                                  recurrent_dropout=self.model_params['dropout_rate'],
                                  return_sequences=True,
                                  return_state=True)
            encoder_output, encoder_h, encoder_c = encoder(question_embedding)

        with tf.variable_scope('decoder_scope') and tf.device('/gpu:0'):
            decoder = layers.LSTM(self.model_params['model_hidden_size'],
                                  dropout=self.model_params['dropout_rate'],
                                  recurrent_dropout=self.model_params['dropout_rate'],
                                  return_sequences=True,
                                  return_state=True)
            decoder_output, _, _ = decoder(text_embedding, initial_state=[encoder_h, encoder_c])

        with tf.variable_scope('attention_scope') and tf.device('/gpu:0'):
            # expanded -> (BS, Encoder_Sequence_Length, Decoder__Sequence_Length, HS)
            expanded_decoder = self.RepeatVectorLayer(self.model_params['max_sequence_length'], 2)(decoder_output)
            expanded_encoder = self.RepeatVectorLayer(self.model_params['max_sequence_length'], 1)(encoder_output)
            concat_layer = layers.Concatenate()([expanded_decoder, expanded_encoder])

            dense1_ts_layer = layers.Dense(self.model_params['model_hidden_size'] // 2, activation='tanh')
            dense1_layer = layers.TimeDistributed(dense1_ts_layer)
            dense1 = dense1_layer(concat_layer)
            dense2_ts_layer = layers.Dense(1)
            dense2_layer = layers.TimeDistributed(dense2_ts_layer)
            dense2 = dense2_layer(dense1)
            dense2 = layers.Reshape(
                (self.model_params['max_sequence_length'], self.model_params['max_sequence_length']))(dense2)

            softmax_score = layers.Softmax()(dense2)
            softmax_score = self.RepeatVectorLayer(self.model_params['model_hidden_size'], 2)(softmax_score)

            permute_encoder = layers.Permute((2, 1))(encoder_output)
            expanded_encoder = self.RepeatVectorLayer(self.model_params['max_sequence_length'], 1)(permute_encoder)

            multi_layer = layers.Multiply()([softmax_score, expanded_encoder])
            multi_layer = layers.Lambda(lambda x: K.sum(x, axis=-1),
                                        lambda x: tuple(x[:-1]))(multi_layer)
            context_layer = layers.Concatenate()([multi_layer, decoder_output])
            attention_dense_layer = layers.Dense(self.model_params['max_sequence_length'], activation='tanh')
            attention_layer = layers.TimeDistributed(attention_dense_layer)(context_layer)

            model_output_layer = layers.Dense(self.vocabulary_length, activation='softmax')
            model_output = model_output_layer(attention_layer)

        with tf.variable_scope('loss_scope') and tf.device('/gpu:0'):
            model = models.Model(inputs=[question_input, text_input],
                                 outputs=model_output)

            # 가중치 로드
            if os.path.exists(DEFINES.check_point_path + "/checkpoint-80-1.3753.h5"):
                print(DEFINES.check_point_path)
                print("******************************************************")
                print("**                  Model Loading                   **")
                print("******************************************************")
                model.load_weights(DEFINES.check_point_path + "/checkpoint-80-1.3753.h5")

            model.compile(optimizer=Adam(lr=DEFINES.learning_rate),
                          loss='categorical_crossentropy',
                          metrics=['accuracy'])

        model.summary()
        self.seq2seq = model

        ###############################################################
        # Predict Model Part
        with tf.variable_scope('predict_scope') and tf.device('/gpu:0'):
            self.encoder_model = models.Model(inputs=question_input,
                                              outputs=[encoder_output, encoder_h, encoder_c])
            self.encoder_model.summary()
            encoder_output_to_input = Input(shape=(self.model_params['max_sequence_length'], self.model_params['model_hidden_size'],))
            decoder_input = Input(shape=(1,), dtype='float32')
            decoder_embedded = text_embedding_layer(decoder_input)
            decoder_state_h = Input(shape=(self.model_params['model_hidden_size'],))
            decoder_state_c = Input(shape=(self.model_params['model_hidden_size'],))
            decoder_output, decoder_h, decoder_c = decoder(decoder_embedded, initial_state=[decoder_state_h, decoder_state_c])

            expanded_decoder = self.RepeatVectorLayer(self.model_params['max_sequence_length'], 2)(decoder_output)
            expanded_encoder = self.RepeatVectorLayer(1, 1)(encoder_output_to_input)
            concat_layer = layers.Concatenate()([expanded_decoder, expanded_encoder])

            dense1_layer = layers.TimeDistributed(dense1_ts_layer)
            dense1 = dense1_layer(concat_layer)
            dense2_layer = layers.TimeDistributed(dense2_ts_layer)
            dense2 = dense2_layer(dense1)
            dense2 = layers.Reshape((1, self.model_params['max_sequence_length']))(dense2)

            softmax_score = layers.Softmax()(dense2)
            softmax_score = self.RepeatVectorLayer(self.model_params['model_hidden_size'], 2)(softmax_score)

            permute_encoder = layers.Permute((2, 1))(encoder_output_to_input)
            expanded_encoder = self.RepeatVectorLayer(1, 1)(permute_encoder)

            multi_layer = layers.Multiply()([softmax_score, expanded_encoder])
            multi_layer = layers.Lambda(lambda x: K.sum(x, axis=-1),
                                        lambda x: tuple(x[:-1]))(multi_layer)
            context_layer = layers.Concatenate()([multi_layer, decoder_output])
            attention_layer = layers.TimeDistributed(attention_dense_layer)(context_layer)

            decoder_attention_output = model_output_layer(attention_layer)
            self.predict_model = models.Model(inputs=[encoder_output_to_input, decoder_input, decoder_state_h, decoder_state_c],
                                              outputs=[decoder_output, decoder_h, decoder_c, decoder_attention_output])
        self.predict_model.summary()

    # training
    def train(self):
        # 훈련 데이터와 평가 데이터 로드
        train_question, train_answer, test_question, test_answer = data.load_data()

        # 훈련셋 생성
        train_input_enc, train_input_dec, train_target_dec = data.preprocessing(train_question + test_question,
                                                                                train_answer + test_answer,
                                                                                self.char2idx)

        # 최고 성능 보이는 모델만 저장
        model_path = DEFINES.check_point_path + '/checkpoint-{epoch:02d}-{val_loss:.4f}.h5'
        cb_checkpoint = ModelCheckpoint(filepath=model_path, monitor='val_acc',
                                        period=10)
        # 학습 자동 중단
        cb_early_stopping = EarlyStopping(monitor='val_loss',
                                          patience=20)

        self.history = self.seq2seq.fit(x=[train_input_enc, train_input_dec],
                                        y=train_target_dec[1],
                                        verbose=2,
                                        validation_split=0.2,
                                        batch_size=DEFINES.batch_size,
                                        epochs=DEFINES.train_steps,
                                        callbacks=[cb_checkpoint, cb_early_stopping])

    def predict(self, input):
        predict_input_enc, predict_input_dec, predict_target_dec = data.preprocessing([input], [""], self.char2idx)
        encoder_output, encoder_state_h, encoder_state_c = self.encoder_model.predict(predict_input_enc)

        # 문장 생성
        target_seq = predict_input_dec[0]
        stop_condition = False
        predictions = []

        while not stop_condition:
            decoder_output, decoder_state_h, decoder_state_c, result = self.predict_model.predict(
                [encoder_output, target_seq, encoder_state_h, encoder_state_c]
            )
            predictions += [np.argmax(result)]

            encoder_state_h = decoder_state_h
            encoder_state_c = decoder_state_c

            if len(predictions) > self.model_params['max_sequence_length']:
                stop_condition = True

        # 예측 결과 확인
        print(predict_target_dec[0])
        print(predictions)
        answer = data.pred_next_string(predictions, self.idx2char)
        return answer

    # Visualization Training Result
    def draw_history(self):
        # summarize history for accuracy
        plt.plot(self.history.history['acc'])
        plt.plot(self.history.history['val_acc'])
        plt.title('model accuracy')
        plt.ylabel('accuracy')
        plt.xlabel('epoch')
        plt.legend(['train', 'test'], loc='upper left')
        plt.show()

        # summarize history for loss
        plt.plot(self.history.history(['loss']))
        plt.plot(self.history.history['val_loss'])
        plt.title('model loss')
        plt.ylabel('loss')
        plt.xlabel('epoch')
        plt.legend(['train', 'test'], loc='upper left')
        plt.show()

```

