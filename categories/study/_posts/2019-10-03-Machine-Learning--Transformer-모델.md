---
layout: post
title: Machine Learning - Transformer 모델
description: >
  Machine Learning - Transformer 모델에 대하여
excerpt_separator: <!--more-->

---

<!--more-->

# Machine Learning - Transformer 모델

## transformer.py

```python
import tensorflow as tf
from tensorflow.python.keras import layers, Model
from tensorflow.python.keras import backend as K

from util import scaled_dot_product_attention, residual_layer


# PositionWise Feed Forward Layer
class PositionWiseFeedForward(Model):
    def __init__(self, num_units, feature_shape):
        super(PositionWiseFeedForward, self).__init__()

        self.inner_dense = layers.Dense(num_units, activation='relu')
        self.output_dense = layers.Dense(feature_shape)

    def call(self, inputs):
        inner_layer = self.inner_dense(inputs)
        outputs = self.output_dense(inner_layer)

        return outputs


# Multi Head Attention
class MultiHeadAttention(Model):
    def __init__(self, num_units, heads, masked=False):
        super(MultiHeadAttention, self).__init__()

        self.heads = heads
        self.masked = masked

        self.query_dense = layers.Dense(num_units, activation='relu')
        self.key_dense = layers.Dense(num_units, activation='relu')
        self.value_dense = layers.Dense(num_units, activation='relu')

    def call(self, query, key, value):
        query = self.query_dense(query)
        key = self.key_dense(key)
        value = self.value_dense(value)

        query = tf.concat(tf.split(query, self.heads, axis=-1), axis=0)
        key = tf.concat(tf.split(key, self.heads, axis=-1), axis=0)
        value = tf.concat(tf.split(value, self.heads, axis=-1), axis=0)

        attention_map = scaled_dot_product_attention(query, key, value, self.masked)
        attention_outputs = tf.concat(tf.split(attention_map, self.heads, axis=0), axis=-1)
        return attention_outputs


# Encoder
class Encoder(Model):
    def __init__(self, model_dims, ffn_dims, attn_heads, num_layers=1):
        super(Encoder, self).__init__()

        self.self_attention = [MultiHeadAttention(model_dims, attn_heads) for _ in range(num_layers)]
        self.position_feedforward = [PositionWiseFeedForward(ffn_dims, model_dims) for _ in range(num_layers)]

    def call(self, inputs):
        output_layer = None

        for i, (s_a, p_f) in enumerate(zip(self.self_attention, self.position_feedforward)):
            with tf.variable_scope('encoder_layer_' + str(i+1)):
                attention_layer = residual_layer(inputs, s_a(inputs,
                                                             inputs,
                                                             inputs))
                output_layer = residual_layer(attention_layer, p_f(attention_layer))
                inputs = output_layer

        return output_layer


# Decoder
class Decoder(Model):
    def __init__(self, model_dims, ffn_dims, attn_heads, num_layers=1):
        super(Decoder, self).__init__()

        self.self_attention = [MultiHeadAttention(model_dims, attn_heads, masked=True) for _ in range(num_layers)]
        self.encoder_decoder_attention = [MultiHeadAttention(model_dims, attn_heads) for _ in range(num_layers)]
        self.position_feedforward = [PositionWiseFeedForward(ffn_dims, model_dims)]

    def call(self, inputs, encoder_outputs):
        output_layer = None

        for i, (s_a, ed_a, p_f) in enumerate(zip(self.self_attention,
                                                 self.encoder_decoder_attention,
                                                 self.position_feedforward)):
            masked_attention_layer = residual_layer(inputs, s_a(inputs,
                                                                inputs,
                                                                inputs))
            attention_layer = residual_layer(masked_attention_layer, ed_a(masked_attention_layer,
                                                                          encoder_outputs,
                                                                          encoder_outputs))
            output_layer = residual_layer(attention_layer, p_f(attention_layer))
            inputs = output_layer

        return output_layer

```

## util.py

```python
import tensorflow as tf
from tensorflow.python.keras import layers
from tensorflow.python.keras import backend as K

from configs import DEFINES
import numpy as np


# Layer Normalization
def layer_norm(inputs, eps=1e-6):
    feature_shape = inputs.get_shape()[-1:]
    mean = K.mean(inputs, [-1], keepdims=True)
    std = K.std(inputs, [-1], keepdims=True)
    beta = K.zeros(feature_shape, name="beta")
    gamma = K.ones(feature_shape, name="gamma")

    return (gamma * (inputs - mean) / (std + eps)) + beta


# Residual Network: x + F(x)
def residual_layer(inputs, sublayer, dropout=DEFINES.dropout_width):
    return layer_norm(inputs + layers.Dropout(dropout)(sublayer))


# Positional Encoding for Transformer model
def positional_encoding(dim, sentence_length):
    encoded_vec = np.array([pos/np.power(10000, 2*i/dim)
                            for pos in range(sentence_length) for i in range(dim)])
    encoded_vec[::2] = np.sin(encoded_vec[::2])
    encoded_vec[1::2] = np.cos(encoded_vec[1::2])

    return K.constant(encoded_vec.reshape([sentence_length, dim]), dtype=tf.float32)


# Scaled Matrix Multiplication attention
def scaled_dot_product_attention(query, key, value, masked=False):
    key_seq_length = float(key.get_shape().as_list()[-2])
    key = tf.transpose(key, perm=[0, 2, 1])
    outputs = tf.matmul(query, key) / tf.sqrt(key_seq_length)

    if masked:
        diag_vals = K.ones_like(outputs[0, :, :])
        tril = tf.linalg.LinearOperatorLowerTriangular(diag_vals).to_dense()
        masks = K.tile(K.expand_dims(tril, 0), [K.shape(outputs)[0], 1, 1])

        paddings = K.ones_like(masks) * (-2 ** 32 + 1)
        outputs = tf.where(K.equal(masks, 0), paddings, outputs)

    attention_map = K.softmax(outputs)
    return tf.matmul(attention_map, value)
```

