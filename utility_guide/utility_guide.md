# 安装opencv

python扩展包: <https://www.lfd.uci.edu/~gohlke/pythonlibs/>

下载对应的``.whl``文件，然后在此文件所在的文件夹中运行anaconda prompt

```
pip install opencv_python‑3.4.7+contrib‑cp36‑cp36m‑win_amd64.whl
```

# conda

**anconda如何安装新的python环境**

conda create -n tf1 python=2.7.13   
原型是：conda create -n env_name [list of packages]

**安装或删除包**

conda install [list of packages]   
conda remove package_name

**更新包**

conda update --all

**搜索包**

conda search search_term

**保存和加载环境**

conda env export > env.yml  通过重定向将环境保存到文件中和

conda env create -f environment.yml 会创建新环境，和environment.yml的要求一样

**删除环境**

conda env remove -n env_name

# python

**如何安装tensorflow**

pip install --upgrade tensorflow

**如何在windows python2.7下安装tensorflow_gpu**

![1571839750832](D:\GitRep\tech_notes\utility_guide\image\1571839750832.png)

会报错，因为：``Tensorflow for Windows is only supported with Python 3.5 and Python 3.6 (since 1.2).``

python2.7环境下安装tensorflow-gpu的wheel文件，会报关于字符编码的错误（关键字：ascii），这时只要在对应文件中添加下面的代码

```python
importsys
reload(sys)
sys.setdefaultencoding('gb18030')
```

# windows

1. **如何更改C盘下用户文件夹的名字**

2. **windows10如何让文件显示拓展名**

   https://jingyan.baidu.com/article/f00622282564bdfbd3f0c827.html

