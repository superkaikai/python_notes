
[![Analytics](https://ga-beacon.appspot.com/GA-80121379-2/notes-python)](https://github.com/binzhouchn/feature_engineering)

# python笔记
> 版本：0.0.3<br>
> 作者：binzhou<br>
> 邮件：binzhouchn@gmail.com<br>

`Github`加载`ipynb`的速度较慢，建议在 [Nbviewer](http://nbviewer.ipython.org/github/lijin-THU/notes-python/blob/master/index.ipynb) 中查看该项目。

---

## 简介

默认安装了 `Python 3.6`，以及相关的第三方包  `numpy`， `pandas`，`sklearn`

> life is short.use python.

推荐使用[Anaconda](http://www.continuum.io/downloads)，这个IDE集成了大部分常用的包。

推荐下载[Anaconda-tsinghua](https://mirrors.tuna.tsinghua.edu.cn/)，清华镜像下载速度快。
或者[USTC](https://mirrors.ustc.edu.cn/)科大

```
# pip install镜像选择：
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

pip install镜像配置（Linux）
```
# 先在home或者和anaconda文件夹平级的的.pip文件夹下新建pip.conf配置文件然后把以后代码复制进去
[global]
trusted-host =  pypi.tuna.tsinghua.edu.cn
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```
pip install镜像配置（Windows）
```
# 进入目录（C:\Users\Administrator）下新建一个pip文件夹，文件夹里建一个pip.ini 文本文件，内容如下：
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host =  pypi.tuna.tsinghua.edu.cn
或者
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com
或者
pip install numpy --upgrade -i https://pypi.tuna.tsinghua.edu.cn/simple
``` 

