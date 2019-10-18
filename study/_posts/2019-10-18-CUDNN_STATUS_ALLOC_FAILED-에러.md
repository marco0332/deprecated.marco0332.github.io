# CUDNN_STATUS_ALLOC_FAILED 에러

could not create cudnn handle: CUDNN_STATUS_ALLOC_FAILED 라는 에러가 뜨면서 tesnorflow나 keras가 실행되지 않는 이유는 <b>gpu 메모리 할당 문제</b>

## 해결방법

```python
import tensorflow as tf
tf_config = tf.ConfigProto()
tf_config.gpu_options.allow_growth = True

session = tf.Session(config=tf_config) # session 만들 때 config 넣기!!
```

