# 파이썬에서 Pillow로 이미지에 한국어 출력 방법.

OpenCV에서 한국어를 출력하려면 폰트를 빌드하고 OpenCV를 다시 빌드해야 합니다. PIP나 Anaconda를 사용할 수 없어 번거롭기도 합니다. 그래서 pillow를 사용하여 출력하는 것이 대안입니다.

```python
import matplotlib.pyplot as plt
import numpy as np
from PIL import ImageFont, ImageDraw, Image

image1 = np.zeros((480, 640, 3), np.uint8)
image2 = Image.fromarray(image1)
draw = ImageDraw.Draw(image2)
draw.text((10, 20), "안녕하세요", font=ImageFont.truetype("./batang.ttc", 48), fill=(255,255,255))

plt.imshow(image2)
plt.show()
```