# # MACHINE_LEARNING - Quick Draw Doodle Recognition Challenge 시작

https://www.kaggle.com/mrisdal/exploring-survival-on-the-titanic/code

# 1등 참고 커널
https://www.kaggle.com/c/quickdraw-doodle-recognition/discussion/73738#latest-550028
https://www.kaggle.com/paulorzp/ensemble-weighted-voting

# shuffle CSVs
https://www.kaggle.com/gaborfodor/shuffle-csvs

https://www.kaggle.com/echomil/mobilenet-126x126x3-100k-per-class



## Mobile Net
1. 하이퍼 파라미터.
   - Augmentation True로 하면 수평으로 플립, 0.5확률로. 그 때 Batch_size는 512에서 448로. 메모리 부족으로.
   - 케라스 모바일넷 doc에는 모델 학습을 less downsized' images가 정확도 더 잘나옴. 이 케이스에서는 Image_size를 64에서 128로 했을 때 정확도 증가했음. 근데 126이 더 괜찮다는? 확인해봐야.
2. Image encoding

   - raw strokes에서 다른 인코딩 방식으로부터 3개 이미지 생성

   - 채널1은 stroke 일경우 presence of line. Single point 255 값 가짐. 아니면 0.

   - 채널 2는 stroke 시간으로. 보통 테두리 먼저 그리고 디테일 나중에. 이 가정은 각 stroke에 가중치 부여하는데 유용함. 첫번째 stroke는 255로, 그 다음부터 계속 13씩 125까지 줄어듬.

   - 채널 3은 스트로크 포인트 시간으로 기록(한 획 기준). 스트로크 방향에는 어떤 패턴이 있음. 예를들어 거미를 그리면 다리에서 몸통으로 그림. 첫번째 스트로크는 255값을, 그리고 다음부터 20씩 줄어듦.

     ```python
     def draw_cv2(raw_strokes, size=256, lw=6, augmentation = False):
         img = np.zeros((BASE_SIZE, BASE_SIZE, 3), np.uint8)
         for t, stroke in enumerate(raw_strokes):
             points_count = len(stroke[0]) - 1
             grad = 255//points_count
             for i in range(len(stroke[0]) - 1):
                 _ = cv2.line(img, (stroke[0][i], stroke[1][i]), (stroke[0][i + 1], stroke[1][i + 1]), (255, 255 - min(t,10)*13, max(255 - grad*i, 20)), lw)
         if size != BASE_SIZE:
             img = cv2.resize(img, (size, size))
         if augmentation:
             if random.random() > 0.5:
                 img = np.fliplr(img)
         return img
     ```

3. Data generators
    Beluga kernel 베이스. 오리지날 데이터셋 클래스는 파일별로 분리되어 있음. 성능을 향상시키기 위해 파일들을 merged in separate kernels to single files 모든 클래스를 랜덤하게 가지고 있도록. 더욱 빠른 제너레이터를 주며, 싱글 배치를 생성하는데 시간 두배 감소.

  ```python
  3. def image_generator(size, batchsize, lw=6, augmentation = False):
         while True:
             for filename in ALL_FILES:
                 for df in pd.read_csv(filename, chunksize=batchsize):
                     df['drawing'] = df['drawing'].apply(eval)
                     x = np.zeros((len(df), size, size,3))
                     for i, raw_strokes in enumerate(df.drawing.values):
                         x[i] = draw_cv2(raw_strokes, size=size, lw=lw, augmentation = augmentation)
                     x = x / 255.
                     x = x.reshape((len(df), size, size, 3)).astype(np.float32)
                     y = keras.utils.to_categorical(df.y, num_classes=NCATS)
                     yield x, y
  
  def valid_generator(valid_df, size, batchsize, lw=6):
      while(True):
          for i in range(0,len(valid_df),batchsize):
              chunk = valid_df[i:i+batchsize]
              x = np.zeros((len(chunk), size, size,3))
              for i, raw_strokes in enumerate(chunk.drawing.values):
                  x[i] = draw_cv2(raw_strokes, size=size, lw=lw)
              x = x / 255.
              x = x.reshape((len(chunk), size, size,3)).astype(np.float32)
              y = keras.utils.to_categorical(chunk.y, num_classes=NCATS)
              yield x,y
          
  def test_generator(test_df, size, batchsize, lw=6):
      for i in range(0,len(test_df),batchsize):
          chunk = test_df[i:i+batchsize]
          x = np.zeros((len(chunk), size, size,3))
          for i, raw_strokes in enumerate(chunk.drawing.values):
              x[i] = draw_cv2(raw_strokes, size=size, lw=lw)
          x = x / 255.
          x = x.reshape((len(chunk), size, size, 3)).astype(np.float32)
          yield x
          
          
  train_datagen = image_generator(size=IMG_SIZE, batchsize=BATCH_SIZE, augmentation = AUGMENTATION)
  
  valid_df = pd.read_csv(VALIDATION_FILE)
  valid_df['drawing'] = valid_df['drawing'].apply(eval)
  validation_steps = len(valid_df)//BATCH_SIZE
  valid_datagen = valid_generator(valid_df, size=IMG_SIZE, batchsize=BATCH_SIZE)
  ```


4. 시각화

  ```python
  single_class_df = valid_df[valid_df['y'] == 2]
  single_class_gen = valid_generator(single_class_df, size=IMG_SIZE, batchsize=BATCH_SIZE)
  x, y = next(single_class_gen)
  plot_batch(x)
  
  # 트레이닝 배치 시각화
  x, y = next(train_datagen)
  plot_batch(x)
  
  # validation 배치 시각화
  x, y = next(valid_datagen)
  plot_batch(x)
  ```


5. 모델 정의

   
