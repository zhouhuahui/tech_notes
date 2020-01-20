# untitled

```python
#keras运行物体分割模型来计算
self.sess = keras.backend.get_session()
self.yolo_model = keras.models.load_model(model_path, compile=False)

out_boxes, out_scores, out_classes = self.sess.run(
            [self.boxes, self.scores, self.classes],
            feed_dict={
                self.yolo_model.input: image_data,
                self.input_image_shape: [image.size[1], image.size[0]],
                K.learning_phase(): 0
            })
```



# 使用keras的基本框架

<https://keras.io/> keras官方文档

```python
import numpy as np 
import os
import skimage.io as io
import skimage.transform as trans
import numpy as np
from keras.models import *
from keras.layers import *
from keras.optimizers import *
from keras.callbacks import ModelCheckpoint, LearningRateScheduler

def unet(input_size):
    inputs = Input(input_size)
    #网络框架
    #......
    conv10 = Conv2D(1,1,activation='sigmoid')(conv9) #最后一层
    model = Model(input=inputs,output=conv10)
    model.compile(optimizer = Adam(lr = 1e-4), loss = 'binary_crossentropy', 			metrics['accuracy'])
    return model
model = unet()
model_checkpoint = ModelCheckpoint('unet_membrane.hdf5', 							monitor='loss',verbose=1, save_best_only=True)
model.fit_generator(train_generator,steps_per_epoch=,epochs=,callbacks=					[model_checkpoint]) #generator是生成训练数据的生成器
model.predict_generator(test_generator)
```

# keras模块

## keras.preprocessing.ImageDataGenerator生成器的flow_from_directory的用法

可从**keras文档**中了解一些，方便产生分批batch_size数量的图片。ImageDataGenerator还有其他的生成图片的方法。突然想起了以前使用过离线keras官方文档的技巧。

值得注意的是，flow_from_directory中class_mode参数的用法：若class_mode为none，则此方法返回的只是图片；若class_mode不为none，则会返回图片和标签，但具体不了解。

用法：

*图片分类*

```python
from keras.preprocessing.image import ImageDataGenerator

train_datagen = ImageDataGenerator(rescale=1./255) #还可以指定其他参数进行数据增强
test_datagen = ImageDataGenerator(rescale=1./255)

train_generator = train_datagen.flow_from_directory(
                train_dir, # 目標目錄
                target_size=(150, 150), # 所有影象調整為150x150
                batch_size=20,
                class_mode='binary') # 二進位制標籤，我們用了binary_crossentropy損失函式
validation_generator = test_datagen.flow_from_directory(
                validation_dir, # 目標目錄
                target_size=(150, 150), # 所有影象調整為150x150
                batch_size=20,
                class_mode='binary')

history = model.fit_generator(
    train_generator,
    steps_per_epoch=100,
    epochs=30,
    validation_data=validation_generator,
    validation_steps=50)
```

*图片分割*

```python
'''
很多方面和上面相同，只是在产生train_generator时，有所不同，用了两个生成器，分别产生图片和分割图片(标签)。
'''
```

```python
def trainGenerator(batch_size,train_path,image_folder,mask_folder,aug_dict,image_color_mode = "grayscale",
                    mask_color_mode = "grayscale",image_save_prefix  = "image",mask_save_prefix  = "mask",
                    flag_multi_class = False,num_class = 2,save_to_dir = None,target_size = (256,256),seed = 1):
    '''
    can generate image and mask at the same time
    use the same seed for image_datagen and mask_datagen to ensure the transformation for image and mask is the same
    if you want to visualize the results of generator, set save_to_dir = "your path"
    '''
    image_datagen = ImageDataGenerator(**aug_dict)
    mask_datagen = ImageDataGenerator(**aug_dict)
    image_generator = image_datagen.flow_from_directory(
        train_path,
        classes = [image_folder],
        class_mode = None,
        color_mode = image_color_mode,
        target_size = target_size,
        batch_size = batch_size,
        save_to_dir = save_to_dir,
        save_prefix  = image_save_prefix,
        seed = seed)
    mask_generator = mask_datagen.flow_from_directory(
        train_path,
        classes = [mask_folder],
        class_mode = None,
        color_mode = mask_color_mode,
        target_size = target_size,
        batch_size = batch_size,
        save_to_dir = save_to_dir,
        save_prefix  = mask_save_prefix,
        seed = seed)
    train_generator = zip(image_generator, mask_generator)
    for (img,mask) in train_generator:
        img,mask = adjustData(img,mask,flag_multi_class,num_class)
        yield (img,mask)
```

keras可以在训练过程中自动计算accuracy

## keras.applications

参考：<https://blog.csdn.net/sinat_26917383/article/details/72859145>

### include_top=False

是否包含最后的3个全连接层（whether to include the 3 fully-connected layers at the top of the network）。用来做fine-tuning专用，专门开源了这类模型。 

### vgg16

```python
inputs = keras.layers.Input(shape=input_shape)
inputs = Lambda(lambda x: keras_vgg16.preprocess_input(x))(inputs)
base_model = keras_vgg16.VGG16(input_tensor=inputs,
                               include_top=False,
                               weights='imagenet') #keras.models.Model对象
```



