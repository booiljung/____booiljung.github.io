# 라이브러리별 이미지 형태

2019년 4월 14일

라이브러리별로 메모리에서 이미지 형태가 다르며 혼란을 주기 때문에 확인용으로 작성하였습니다.

## OpenCV

OpenCV는 Mat 클래스에 이미지를 저장합니다. 특별히 지정하지 않으면 [채널수, 세로크기, 가로크기] 형태로 저장되며, 3채널은 BGR 순서입니다.

파이썬에서 skimage 형식을 OpenCV로 가져오기 위해서는 전치를 해야 합니다.

```python
# skimage -> opencv image
cvimage = npimage.transpose((2, 0, 1))
```

Pytorch의 경우에도 콘볼루션 기반 모델에 입력하는 경우 대부분 채널이 피쳐맵에 대응되므로 `[채널수, 세로크기, 가로크기]`로 OpenCV와 동일 합니다.

## skimage

sci-kit은 인기 있는 머신러닝 패키지이며 skimage라는 이미지 처리 패키지를 별도로 제공합니다. 특별히 지정하지 않으면 [세로크기, 가로크기, 채널수] 입니다.

파이썬에서 OpenCV 이미지를 skimage로 가져오기 위해서는 전치를 해야 합니다.

```python
# opencv image -> skimage
npimage = cvimage.transpose((1, 2, 0))
```

## 참조

- skimage
- opencv

