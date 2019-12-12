---
layout: post
title: Machine Learning - ConvNet 학습 시각화
description: >
  Machine Learning - ConvNet 학습 시각화에 대하여
excerpt_separator: <!--more-->
---

<!--more-->

# Machine Learning - ConvNet 학습 시각화

ConvNet의 표현은 시각적인 개념을 학습한 거이기 때문에 시각화하기 좋다.

- ConvNet 중간 layer의 출력을 시각화 : 연속된 층이 입력을 어떻게 변형시키는지 이해하고 개별적인 ConvNet 필터의 의미를 파악하는데 도움이 됨.

- ConvNet 필터를 시각화 : ConvNet의필터가 찾으려는 시각적인 패턴과 개념이 무엇인지 파악 가능.

- 클래스 활성화에 대한 히트맵(heatmap)을 이미지에 시각화 : 이미지의 어느 부분이 주어진 클래스에 속하는데 기여했는지 이해하고 이미지에서 객체 위치를 추정(localization)하는데 도움이 됨.

  > 히트맵에서 보통 파란색, 녹색, 빨간색 순으로 값이 높아짐을 표현한다.

1. 중간 층의 출력 시각화

   ```python
   from keras import models
   import matplotlib.pyplot as plt
   
   # 상위 8개 층의 출력 추출
   layer_outputs = [layer.output for layer in model.layers[:8]]
   # 입력에 대해 8개 층의 출력을 반환하는 모델
   activation_model = models.Model(inputs=model.input, outputs=layer_outputs)
   
   activations = activation_model.predict(img_tensor)
   plt.matshow(activations[0][0, :, :, 19], cmap='viridis') # 첫 번째 활성화 층의 20번째 채널
   ```

   모든 활성화에 있는 채널 시각화

   ```python
   layer_names = []
   for layer in model.layers[:8]:
       layer_names.append(layer.name)
   
   images_per_row = 16
   
   for layer_name, layer_cativation in zip(layer_names, activations):
       n_features = layer_activation.shape[-1] # 특성 맵에 있는 특성의 수
       size = layer_activation.shape[1]        # 특성 맵의 크기는 (1, size, size, n_features)
       n_cols = n_features // images_per_row
       display_grid = np.zeros((size * n_cols, images_per_row * size))
       
       for col in range(n_cols):
           for row in range(images_per_row):
               channel_image = layer_activation[0, :, :, col * images_per_row + row]
               channel_image -= channel_image.mean()  # 그래프로 나타내기 좋게 특성 처리
               channel_image /= channel_image.std()
               channel_image *= 64
               channel_imaeg += 128
               # np.clip(input, min, max)
               # input에 있는 값이 min보다 작으면 min으로, max보다 크면 max로
               channel_image = np.clip(channel_image, 0, 255).astype('uint8')
               display_grid[col * size : (col + 1) * size,
                            row * size : (row + 1) * size] = channel_image
       
       scale = 1. / size
       plt.figure(figsize=(scale * display_grid.shape[1],
                           scale * display_grid.shape[0]))
       oplt.title(layer_name)
       plt.grid(False)
       plt.imshow(display_grid, aspect='auto', cmap='viridis')
   
   plt.show()
   ```

   중간에 특성을 처리하는 부분은 임의의 실수인 층의 출력을 픽셀로 표현이 가능한 0~255 사이의 정수로 변환.
   평균을 빼고 표준 편차로 나누어 표준 점수(standard score)로 바꾸었음. 그다음 표준 점수 2.0 이내의 값(약 95%)들이 0~255 사이에 놓이도록 증폭시킨 후 클리핑함.

   시각화 결과를 보면 층이 깊어질 수록 비어있는 활성화가 늘어남을 알 수있다. 필터에 인코딩된 패턴이 입력 이미지에 나타나지 않았다는 것을 의미한다.

2. ConvNet 필터 시각화

   각 필터가 반응하는 시각적 패턴을 그리는 방법이다. 빈 입력 이미지에서 시작해서 특정 필터의 응답을 최대화하기 위해 컨브넷 입력 이미지에 경사 상승법을 적용, 결과적으로 입력 이미지는 선택된 필터가 최대로 응답하는 이미지가 될 것이다.

   특정 ConvNet 층의 한 필터 값을 최대화하는 손실 함수 정의, 활성화 값을 최대화하기 위해 입력 이미지를 변경하도록 확률적 경사 상승법 사용.

   ```python
   from keras.applications import VGG16
   from keras import backend as K
   
   model = VGG16(weights='imagenet',
                 include_top=False)
   
   layer_name = 'block3_conv1'
   filter_index = 0
   
   # 필터 시각화를 위한 손실 텐서 정의
   layer_output = model.get_layer(layer_name).output
   loss = K.mean(layer_output[:, :, :, filter_index])
   
   # 입력에 대한 손실의 gradient 구하기
   grads = K.gradients(loss, model.input)[0]
   
   # gradient clipping
   # 경사 상승법 과정을 부드럽게 하기 위한 기법.
   # gradient tensor를 L2 norm으로 나누어 정규화.
   # 입력 이미지에 적용할 수정량의 크기를 항상 일정 범위 안에 놓을 수 있다.
   # keras.optimizers 모듈 아래에 있는 옵티마이저를 사용할 때는 clipnorm과 clipvalue 매개변수 설정.
   # clipnorm 매개변수 값이 gradient L2 norm보다 클 경우 각 gradient L2 norm을 clipnorm 값으로 정규화.
   # clipvalue 매개변수를 지정하면 gradient 최대 절댓값은 clipvalue가 됨.
   grads /= (K.sqrt(K.mean(K.square(grads))) + 1e-5) # 0 나눗셈 방지하기 위해 1e-5 더함
   
   # 입력 값에 대한 NumPy 출력 값 추출
   # 경사 상승법이라서 keras.optimizers의 모듈 아래에 있는 옵티마이저를 사용할 수 없음.
   iterate = K.function([model.input], [loss, grads])
   
   import numpy as np
   loss_value, grads_value = iterate([np.zeros((1, 150, 150, 3))])
   
   # 확률적 경사 상승법을 사용한 손실 최대화
   input_img_data = np.random.random((1, 150, 150, 3)) * 20 + 128 # 노이즈 회색 이미지로 시작
   
   step = 1.
   for i in range(40):
       loss_value, grads_value = iterate([input_img_data])
       input_img_data += grads_value * step
    
   # tensor를 이미지 형태로 변환하기 위한 유틸리티 함수
   def deprocess_image(x):
       # tensor의 평균이 0, 표준 편차가 0.1이 되도록 standardization
       x -= x.mean()
       x /= (x.std() + 1e-5)
       x *= 0.1
       
       # [0, 1]로 clipping
       x += 0.5
       x = np.clip(x, 0, 1)
       
       # RGB 배열로 변환
       x *= 255
       x = np.clip(x, 0, 255).astype('uint8')
       return x
   
   # 필터 시각화 이미지를 만드는 함수
   def generate_pattern(layer_name, filter_index, size=150):
       layer_output = model.get_layer(layer_name).output
       loss = K.mean(layer_output[:, :, :, filter_index])
       
       grads = K.gradients(loss, model.input)[0]
       grads /= (K.sqrt(K.mean(K.square(grads))) + 1e-5)
       
       iterate = K.function([model.input], [loss, grads])
       input_img_data = np.random.random((1, size, size, 3)) * 20 + 128.
       
       step = 1.
       for i in range(40):
           loss_value, grads_value = iterate([input_img_data])
           input_img_data += grads_value * stepp
           
       img = input_img_data[0]
       return deprocess_image(img)
   ```

   모든 층에 있는 필터를 시각화

   ```python
   layer_name = 'block1_conv1'
   size = 64
   margin = 5
   
   results = np.zeros((8 * size + 7 * margin, 8 * size + 7 * margin, 3), dtype='uint8')
   for i in range(8):
       for j in range(8):
           filter_img = generate_pattern(layer_name, i + (j * 8), size=size)
           horizontal_start = i * size + i * margin
           horizontal_end = horizontal_start + size
           vertical_start = j * size + j * margin
           vertical_end = vertical_start + size
           results[horizontal_start: horizontal_end,
                   vertical_start: vertical_end, :] = filter_img
           
   plt.figure(figsize=(20, 20))
   plt.imshow(results)
   ```

3. 클래스 활성화의 히트맵 시각화하기

   이미지의 어느 부분이 최종 분류 결정에 기여하는지 시각화. 또는 이미지에 특정 물체가 있는 위치를 파악하는데 사용 됨. 이 기법의 종류를 일반적으로 클래스 활성화 맵(Class Activation Map, CAM) 시각화라고 함.

   여기서 사용할 구현 방법은 <b>"Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization," arXiv (2017)</b>에 기술되어 있음.

   - 입력 이미지가 주어지면 Conv layer에 있는 특성 맵의 출력을 추출.
   - 특성 맵의 모든 채널 출력에 채널에 대한 클래스의 gradient 평균을 곱함.

   즉, '입력 이미지가 각 채널을 활성화하는 정도'에 대한 공간적인 맵을 '클래스에 대한 각 채널의 중요도'로 가중치를 부여하여 '입력 이미지가 클래스를 활성화하는 정도'에 대한 공간적인 맵을 만드는 것

   ```python
   from keras.applications.vgg16 import VGG16
   
   # 이번 예제에서는 최상단의 분류기까지 포함
   model = VGG16(weights='imagenet')
   
   # 이미지 전처리
   from keras.preprocessing import image
   from keras.applications.vgg16 import preprocess_input, decode_predictions
   import numpy as np
   
   img_path = './example.jpg'
   img = image.load_img(img_path, target_size=(224, 224))
   
   x = image.img_to_array(img)
   x = np.expand_dims(x, axis=0)
   x = preprocess_input(x)
   
   # 상위 예측 클래스가 386번 인덱스로 나왔을 경우
   example_output = model.output[:, 386]
   
   last_conv_layer = model.get_layer('block5_conv3')
   grads = K.gradients(example_output, last_conv_layer.output)[0]
   pooled_grads = K.mean(grads, axis=(0, 1, 2)) # 특성 맵 채널별 gradient 평균값이 담긴 (512,) 벡터
   
   # 특성 맵 출력울 구함.
   iterate = K.function([model.input],
                        [pooled_grads, last_conv_layer.output[0]])
   pooled_grads_value, conv_layer_output_value = iterate([x])
   
   # 해당 클래스에 대한 '채널의 중요도'를 특성 맵 배열의 채널에 곱함.
   for i in range(512):
       conv_layer_output_value[:, :, i] *= pooled_grads_value[i]
       
   # 만들어진 특성 맵에서 채널 축을 따라 평균한 값이 클래스 활성화의 히트맵이다.
   heatmap = np.mean(conv_layer_output_vaue, axis=-1)
   
   # 히트맵 후처리 및 출력
   heatmap = np.maximum(heatmap, 0)
   heatmap /= np.max(heatmap)
   plt.matshow(heatmap)
   
   # OpenCV를 사용해서 히트맵에 원본 이미지를 겹친 이미지 생성
   import cv2
   
   img = cv2.imread(img_path)
   heatmap = cv2.resize(heatmap, (img.shape[1], img.shape[0])) # 히트맵을 원본 이미지 사이즈로 변경
   heatmap = np.uint8(255 * heatmap)						  # 히트맵을 RGB 포맷으로 변환
   heatmap = cv2.applyColorMap(heatmap, cv2.COLORMAP_JET)		# 히트맵으로 변환.
   
   superimposed_img = heatmap * 0.4 + img					   # 0.4는 히트맵의 강도
   cv2.imwrite('./example.jpg', superimposed_img)
   ```

   