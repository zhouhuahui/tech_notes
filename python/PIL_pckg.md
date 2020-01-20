# 保存图片

```python
from PIL import Image
im = Image.fromarray(resized_pred.astype(np.uint8))
im.save(output_dir + '/' + i_name + '_segmentation.png')
#astype(no.uint8)
```

.jpg图片是[0,255]的，.png图片是[0.0,1.0]的