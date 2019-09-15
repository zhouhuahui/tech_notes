```python
import numpy as np

a = [[[0,1],
      [2,3]],
     [[4,5],
      [6,7]]]
a = np.array(a)
print(a)
print(a[...,None])
#目的是增加第4维，而且None表示第4维的长度不确定，要系统自己默认，这里默认为1
```

