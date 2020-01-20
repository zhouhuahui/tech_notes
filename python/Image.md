```python
from PIL import Image

size = (416,416)
image = Image.open('D:\\GitRep\\others\\qqwweee.keras-yolo3\\keras-yolo3\\car.jpg')

iw, ih = (500,300)
w, h = size
scale = min(w/iw, h/ih)
nw = int(iw*scale)
nh = int(ih*scale)

image = image.resize((nw,nh), Image.BICUBIC)
new_image = Image.new('RGB', size, (128,128,128))
new_image.paste(image, ((w-nw)//2, (h-nh)//2)) #把输入图片按比例缩小（或放大），使一个边与模型输入尺寸的对应边相同大小，而另一边小于对应的边，然后把图片放到(416,416)的黑色图片的正中央。对于这个例子，效果是，真正的图片内容在new_image的正中央，上面是黑的，下面是黑的
new_image.show()
```

```python
#用ImageDraw在图片上画有用的信息
from PIL import ImageDraw
draw = ImageDraw.Draw(image) #image为Image.Image对象
label_size = draw.textsize(label, font)
draw.rectangle(...)
draw.text(...)
```

