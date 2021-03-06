## python实用技巧

[**1. lambda函数**](#lambda函数)

[**2. map函数**](#map函数)

[**3. filter函数**](#filter函数)

[**4. reduce函数**](#reduce函数)

[**5. apply和applymap函数、transform/agg**](#apply函数)

[**6. dict转object**](#dict转object)

[**7. KFold函数**](#kfold函数)

[**8. sys.defaultencoding**](#sys)

[**9. pip install error _NamespacePath**](#pip_error)

[**10. zip(\*xx)用法**](#zip)

[**11. dataframe中某一列字符串长度为10的进行切片**](#切片)

[**12. re模块(一些常用的正则轮子)**](#re模块)

[**13. eval**](#eval)

[**14. global用法**](#global)

[**15. 多进程之Process, Pool用法**](#pool)

[**16. CV的多进程实现**](#cv的多进程实现)

[**17. 保存模型**](#保存模型)

[**18. enumerate用法**](#enumerate)

[**19. label数值化方法**](#label数值化方法)

[**20. 列表推导式中使用if else**](#列表推导式中使用if_else)

[**21. 将nparray或list中的最多的元素选出**](#将numpy_array中的最多的元素选出)

[**22. 函数中传入函数demo**](#函数中传入函数demo)

[**23. getattr**](#getattr)

[**24. df宽变长及一列变多列**](#df宽变长及一列变多列)

[**25. groupby使用**](#groupby使用)

[**26. python画图及显示中文**](#python画图及显示中文)

[**27. 给字典按value排序**](#给字典按value排序)

[**28. sorted高级用法**](#sorted高级用法)

[**29. time用法**](#time用法)

[**30. 两层列表展开平铺**](#两层列表展开平铺)

[**31. 读取百度百科词向量**](#读取百度百科词向量)

[**32. logging**](#logging)

---
```python
%reload_ext autoreload
%autoreload 2
%matplotlib notebook

import sys
sys.path.append('..')
```

### lambda函数
```python
# lambda: 快速定义单行的最小函数，inline的匿名函数
(lambda x : x ** 2)(3)
# 或者
f = lambda x : x ** 2
f(3)
```

### map函数
```python
arr_str = ["hello", "this"]
arr_num = [3,1,6,10,12]

def f(x):
    return x ** 2
map(lambda x : x ** 2, arr_num)
map(f, arr_num)
map(len, arr_str)
map(lambda x : (x, 1), arr_str)
```
```python
# 可以对每个列表对应的元素进行操作，比如加总
f1 = lambda x,y,z:x+y+z
list(map(f1,[1,2,10],[2,3,6],[4,3,5]))
# [7,8,21]
```

### filter函数
```python
arr_str = ['hello','hi','nice']
arr_num = [1,6,10,12]
filter(lambda x : len(x) >= 5, arr_str)
filter(lambda x : x > 5, arr_num) 
[(i.word, 'E') if i.flag =='n' else (i.word, 'P') for i in filter(lambda x: x.flag in ('n', 'v'), a) ]
```

### reduce函数
```python
# 在python3里，reduce函数已经被从全局命名空间里移除了，它现在被放置在functools模块里
from functools import reduce
arr_num = [1,6,7,10]
reduce(lambda x, y : x + y, arr_num)
```

### apply函数

 - apply函数是对行进行操作

你可以把apply()当作是一个map()函数，只不过这个函数是专为Pandas的dataframe和series对象打造的。对初学者来说，你可以把series对象想象成类似NumPy里的数组对象。它是一个一维带索引的数据表结构。<br>
<br>
apply() 函数作用是，将一个函数应用到某个数据表中你指定的一行或一列中的每一个元素上。是不是很方便？特别是当你需要对某一列的所有元素都进行格式化或修改的时候，你就不用再一遍遍地循环啦！<br>
```python
df = pd.DataFrame([[4,9],]*3,columns=['A','B'])
df.apply(np.sqrt)
df.apply(np.sum,axis=0)
df.apply(np.sum,axis=1)
df.apply(lambda x : [1,2], axis=1)
df.apply(lambda x : x.split()[0])
```
 > applymap和apply差不多，不过是全局函数，elementwise，作用于dataframe中的每个元素

 - transform/agg是对一列进行操作

由前面分析可以知道，Fare项在测试数据中缺少一个值，所以需要对该值进行填充。 
我们按照一二三等舱各自的均价来填充： 
下面transform将函数np.mean应用到各个group中。
```python
combined_train_test['Fare'] = combined_train_test[['Fare']].fillna(combined_train_test.groupby('Pclass').transform(np.mean))
```

### dict转object
```python
import json
# json格式的str
s = '{"name":{"0":"John","1":"Lily"},"phone_no":{"0":"189101","1":"234220"},"age":{"0":"11","1":"23"}}' 
# load成dict
dic = json.loads(s)
dic
# {"name":{"0":"John","1":"Lily"},"phone_no":{"0":"189101","1":"234220"},"age":{"0":"11","1":"23"}}
# 不能使用dic.name, dic.age 只能dic['name'], dic['age']
class p:
    def __init__(self, d=None):
        self.__dict__ = d
p1 = p(dic)
# 这个时候就可以用p1.name, p1.age了

# 更详细一点
import six
import pprint
# 现在有个字典
conf = {'base':{'good','medium','bad'},'age':'24'}
# conf.age是不行的
定义一个class：
class p:
    def __init__(self, d=None):
        self.__dict__ = d
    def keys(self):
        return self.__dict__.keys()
    def items(self):
        return six.iteritems(self.__dict__)
    def __repr__(self):
        return pprint.pformat(self.__dict__) # 将dict转成字符串
p1 = p(conf)
这个时候就可以p1.base和p1.age
p1这个实例拥有的属性有：
p.__doc__
p.__init__
p.__module__
p.__repr__
p.age * age和base这两个是字典加载进来以后多出来的属性
p.base *
p.items
p.keys
```

### kfold函数
新手用cross_val_score比较简单，后期可用KFold更灵活,
```python
from sklearn.model_selection import cross_val_score, StratifiedKFold, KFold
forest = RandomForestClassifier(n_estimators = 120,max_depth=5, random_state=42)
cross_val_score(forest,X=train_data_features,y=df.Score,scoring='neg_mean_squared_error',cv=3)
# 这里的scoring可以自己写，比如我想用RMSE则
from sklearn.metrics import scorer
def ff(y,y_pred):
    rmse = np.sqrt(sum((y-y_pred)**2)/len(y))
    return rmse
rmse_scoring = scorer.make_scorer(ff)
cross_val_score(forest,X=train_data_features,y=df.Score,scoring=rmse_scoring,cv=5)
```
```python
# Some useful parameters which will come in handy later on
ntrain = titanic_train_data_X.shape[0]
ntest = titanic_test_data_X.shape[0]
SEED = 42 # for reproducibility
NFOLDS = 5 # set folds for out-of-fold prediction
kf = KFold(n_splits = NFOLDS, random_state=SEED, shuffle=True)

def get_out_fold(clf, x_train, y_train, x_test): # 这里需要将dataframe转成array，用x_train.values即可
    oof_train = np.zeros((ntrain,))
    oof_test = np.zeros((ntest,))
    oof_test_skf = np.empty((NFOLDS, ntest))

    for i, (train_index, test_index) in enumerate(kf.split(x_train)):
        x_tr = x_train.loc[train_index]
        y_tr = y_train.loc[train_index]
        x_te = x_train.loc[test_index]

        clf.fit(x_tr, y_tr)

        oof_train[test_index] = clf.predict(x_te)
        oof_test_skf[i, :] = clf.predict(x_test)

    oof_test[:] = oof_test_skf.mean(axis=0)
    return oof_train.reshape(-1, 1), oof_test.reshape(-1, 1)
```

### sys
```python
import sys 
reload(sys) 
sys.setdefaultencoding('utf-8') 
#注意：使用此方式，有极大的可能导致print函数无法打印数据！

#改进方式如下：
import sys #这里只是一个对sys的引用，只能reload才能进行重新加载
stdi,stdo,stde=sys.stdin,sys.stdout,sys.stderr 
reload(sys) #通过import引用进来时,setdefaultencoding函数在被系统调用后被删除了，所以必须reload一次
sys.stdin,sys.stdout,sys.stderr=stdi,stdo,stde 
sys.setdefaultencoding('utf-8')
```

### pip_error

使用pip时出现错误：
AttributeError: '_NamespacePath' object has no attribute 'sort'

解决方法：<br>
1. 关于Anaconda3报错 AttributeError: '_NamespacePath' object has no attribute 'sort'  ，先参考下面这篇博客：<br>
http://www.cnblogs.com/newP/p/7149155.html<br>
按照文中的做法是可以解决conda报错的，总结一下就是：一，把文件夹 D:\ProgramData\Anaconda3\Lib\site-packages\conda\_vendor\auxlib 中的 path.py 中，“except ImportError: ”修改为“except Exception:“；二、找到D:\ProgramData\Anaconda3\lib\site-packages\setuptools-27.2.0-py3.6.egg，删除（不放心的话，剪切到别的地方）

2.然而pip报错的问题还没解决。首先要安装setuptools模块，下载地址是：<br>
https://pypi.python.org/pypi/setuptools#files<br>
下载setuptools-36.5.0.zip解压，命令窗口进入到文件夹然后 python setup.py install

3.安装好setuptools模块之后应该能用easy_install了，我们要借助它来重新安装pip。命令窗口输入命令：easy_install pip

### zip
zip基本用法<br>
```python
a = [1,2,3]
b = [4,5,6]
for i,j in zip(a,b):
    print(i,j)
# 1 4
# 2 5
# 3 6
```

```python
s = '彩符和大汶口文化陶尊符号是第三阶段的语段文字'
print(synonyms.seg(s))
# (['彩符', '和', '大汶口', '文化', '陶尊', '符号', '是', '第三阶段', '的', '语段', '文字'], ['n', 'c', 'ns', 'n', 'nr', 'n', 'v', 't', 'uj', 'n', 'n'])
[x for x in zip(*synonyms.seg(s))]
# [('彩符', 'n'),
  ('和', 'c'),
  ('大汶口', 'ns'),
  ('文化', 'n'),
  ('陶尊', 'nr'),
  ('符号', 'n'),
  ('是', 'v'),
  ('第三阶段', 't'),
  ('的', 'uj'),
  ('语段', 'n'),
  ('文字', 'n')]
```
### 切片
```python
data.msg_from = data.msg_from.astype(str)
data[data.msg_from.apply(len)==10]
```

### re模块
```python
# 将一个问题中的网址、邮箱、手机号、身份证、日期、价格提出来

# 日期 注：这里的{1,4}指的是匹配1到4位，问号指的是0个或1个
DATE_REG1 = "(?:[一二三四五六七八九零十0-9]{1,4}年[一二三四五六七八九零十0-9]{1,2}月[一二三四五六七八九零十0-9]{1,2}[日|号|天|分]?)|\
(?:[一二三四五六七八九零十0-9]+年[一二三四五六七八九零十0-9]+月)|\
(?:[一二三四五六七八九零十0-9]{1,2}月[一二三四五六七八九零十0-9]{1,2}[号|日|天]?)|\
(?:[一二三四五六七八九零十0-9]+年)|\
(?:[一二三四五六七八九零十0-9]+月)|\
(?:[一二三四五六七八九零十0-9]{1,3}[号|日|天])|\
(?:[一二三四五六七八九零十0-9]+小时[一二三四五六七八九零十0-9]+分钟)|\
(?:[一二三四五六七八九零十0-9]+小时)|\
(?:[一二三四五六七八九零十0-9]+分钟)\
"

# 网址
URL_REG = "http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*,]|(?:%[0-9a-fA-F][0-9a-fA-F]))+"
# 手机
PHONE_REG = "[+](?:86)[-\s+]*?1[3-8][0-9]{9}"
# 邮箱
MAIL_REG = "[0-9a-zA-Z_]{0,39}@(?:[A-Za-z0-9]+\.)+[A-Za-z]+"
# 身份证
IDCARD_REG = "\d{18}|\d{17}[Xx]"

# 价格
MONEY_REG1 = "(?:\d+[\.\d+]*万*亿*美*港*元/桶)|\
(?:\d+[\.\d+]*万*亿*美*港*元/吨)|\
(?:\d+[\.\d+]*万*亿*美*港*元/升)|\
(?:\d+[\.\d+]*万*亿*美*港*元/吨)|\
(?:\d+[\.\d+]*万*亿*美*港*元/赛季)|\
(?:\d+[\.\d+]*万*亿*美*港*平方米)|\
(?:\d+[\.\d+]*万*亿*美*港*平方千米)|\
(?:(?:[\d]{1,3},)*(?:[\d]{3})[万亿]*[美港]*元)|\
(?:\d+[\.\d+]*万*亿*美*港*[股|笔|户|辆|倍|桶|吨|升|个|手|点|元|亿|万])"

MONEY_REG2 = "([一二三四五六七八九零十百千万亿|\d|.]+[万|元|块|毛][一二三四五六七八九零十百千万亿|\d|.]*)+"

## add date reg
DATE_REG2 = "(?:[\d]*[-:\.]*\d+[-:\.点]\d+分)|(?:[\d+-]*\d+月份)|(?:\d+[-:\.]\d+[-:\.]\d+)"
# HYPER_REG 2017-09-20
HYPER_REG = "[0-9a-zA-Z]+[-:][0-9a-zA-Z]+[%]*"
```

### eval
```python
eval("['一','二','三']")
输出 ['一','二','三']
eval("{'a':1,'b':2}")
输出 {'a':1,'b':2}
```

### global
```python
a = None

def f1():
    a = 10
    
def f2():
    global a
    a = 10
f1()
print(a)
f2()
print(a)
```
运行完f1()后，a还是None；运行完f2()后，a变成了10。一般规范global变量用大写

### pool

```python
# Process用法（我用的比较多）
from multiprocessing import Manager, Process

mg = Manager()
mg_list = mg.list()
p_proc = []

def ff(x,mg_list):
    print('function f')
    mg_list.append(x**3)
    #return mg_list # 可以不加

print('main line')
for i in [1,2,3]:
    p = Process(target=ff, args=(i,mg_list,))
    p.start()
    p_proc.append(p)
[p.join() for p in p_proc]
```

```python
# Pool用法
from multiprocessing import Pool
import time

def task(msg):
    print ('hello, %s' % msg)
    time.sleep(1)

if __name__ == '__main__':
    pool = Pool(processes=4)

    for x in range(10):
        pool.apply_async(task, args=(x,))

    pool.close()
    pool.join() # 加入主进程中，不然processes done会提前打印

    print('processes done.')
```
结果是，四个四个差不多同时打印，因为设置了四个进程，四个进程之间的打印顺序是乱的
```python
hello, 1
hello, 3
hello, 0
hello, 2
hello, 5
hello, 4
hello, 6
hello, 7
hello, 8
hello, 9
processes done.
CPU times: user 32.5 ms, sys: 49.9 ms, total: 82.4 ms
Wall time: 3.13 s
```
不加进程，单进程结果如下
```python
for x in range(10):
    task(x)
hello, 0
hello, 1
hello, 2
hello, 3
hello, 4
hello, 5
hello, 6
hello, 7
hello, 8
hello, 9
CPU times: user 45.5 ms, sys: 16.7 ms, total: 62.1 ms
Wall time: 10 s
```

### cv的多进程实现

```python
from multiprocessing import Manager, Process
n = 5
kf = KFold(n_splits=n, shuffle=False)
mg = Manager()
mg_list = mg.list()
p_proc = []

def lr_pred(i,tr,va,mg_list):
    print('%s stack:%d/%d'%(str(datetime.now()),i+1,n))
    clf = LogisticRegression(C=3)
    clf.fit(X[tr],y[tr])
    y_pred_va = clf.predict_proba(X[va])
    print('va acc:',myAcc(y[va], y_pred_va))
    mg_list.append((va, y_pred_va))
#     return mg_list # 可以不加

print('main line')
for i,(tr,va) in tqdm_notebook(enumerate(kf.split(X))):
    p = Process(target=lr_pred, args=(i,tr,va,mg_list,))
    p.start()
    p_proc.append(p)
[p.join() for p in p_proc]
# 最后把mg_list中的元组数据拿出来即可
```

### 保存模型
1. 使用 pickle 保存
```python
import pickle #pickle模块

#保存Model(注:save文件夹要预先建立，否则会报错)
with open('save/clf.pickle', 'wb') as f:
    pickle.dump(clf, f)

#读取Model
with open('save/clf.pickle', 'rb') as f:
    clf2 = pickle.load(f)
    #测试读取后的Model
    print(clf2.predict(X[0:1]))
```
2. 使用joblib保存
```python
from sklearn.externals import joblib #jbolib模块

#保存Model(注:save文件夹要预先建立，否则会报错)
joblib.dump(clf, 'save/clf.pkl')

#读取Model
clf3 = joblib.load('save/clf.pkl')

#测试读取后的Model
print(clf3.predict(X[0:1]))
```

3. 可以使用dataframe自带的to_pickle函数，可以把大的文件存成多个
```python
import os
from glob import glob

def mkdir_p(path):
    try:
        os.stat(path)
    except:
        os.mkdir(path)
    
def to_pickles(df, path, split_size=3, inplace=True):
    """
    path = '../output/mydf'
    
    wirte '../output/mydf/0.p'
          '../output/mydf/1.p'
          '../output/mydf/2.p'
    
    """
    if inplace==True:
        df.reset_index(drop=True, inplace=True)
    else:
        df = df.reset_index(drop=True)
    gc.collect()
    mkdir_p(path)
    
    kf = KFold(n_splits=split_size)
    for i, (train_index, val_index) in enumerate(tqdm(kf.split(df))):
        df.iloc[val_index].to_pickle(f'{path}/{i:03d}.p')
    return

def read_pickles(path, col=None):
    if col is None:
        df = pd.concat([pd.read_pickle(f) for f in tqdm(sorted(glob(path+'/*')))])
    else:
        df = pd.concat([pd.read_pickle(f)[col] for f in tqdm(sorted(glob(path+'/*')))])
    return df
```

### enumerate
```python
tuples = [(2,3),(7,8),(12,25)]
for step, tp in enumerate(tuples):
    print(step,tp)
# 0 (2, 3)
# 1 (7, 8)
# 2 (12, 25)
```

### label数值化方法
方法一<br>
```python
# 比如10个类别转成1到10
from sklearn.preprocessing import LabelEncoder
data['label'] = LabelEncoder().fit_transform(data.categ_id)
```
方法二<br>
```python
# 比如10个类别转成onehot形式
import pandas as pd
pd.get_dummies(data.categ_id)
```

### 列表推导式中使用if_else
两种方式：<br>
1. [x for x in data if condition] <br>
2. [exp1 if condition else exp2 for x in data]

### 将numpy_array中的最多的元素选出

将numpy array中的最多的元素选出，如果一样则取最小的那个
```python
arr = np.array([2,2,2,4,5])
np.bincount(arr).argmax()
# output: 2
arr = np.array([1,2,1,4,2,8])
np.bincount(arr).argmax()
# output: 1
```

将list中最多的元素选出，如果一样则取最小的那个
```python
# 方法一
arr = [2,2,2,4,5]
max(set(arr),key=arr.count)
# 方法二
from collections import Counter
Counter(arr).most_common(1)[0][0]
```

### 函数中传入函数demo
```python
# time_function把时间包装了一下给其他的函数
def time_function(f, *args):
    """
    Call a function f with args and return the time (in seconds) that it took to execute.
    """
    import time
    tic = time.time()
    f(*args)
    toc = time.time()
    return toc - tic

two_loop_time = time_function(classifier.compute_distances_two_loops, X_test)
print('Two loop version took %f seconds' % two_loop_time)

one_loop_time = time_function(classifier.compute_distances_one_loop, X_test)
print('One loop version took %f seconds' % one_loop_time)

no_loop_time = time_function(classifier.compute_distances_no_loops, X_test)
print('No loop version took %f seconds' % no_loop_time)
```

### getattr
```python
class A(object):
    def __init__(self):
        pass
    def xx(self,x):
        print('get xx func',x)
a = A()
getattr(a,'xx')(23213) ### 等同于a.xx(23213)
#out[]: get xx func 23213
```

### df宽变长及一列变多列

(1) df宽变长<br>
```python
def explode(df, col, pat=None, drop_col=True):
    """
    :param df:
    :param col: col name
    :param pat: String or regular expression to split on. If None, splits on whitespace
    :param drop_col: drop col is Yes or No
    :return: hive explode
    """
    data = df.copy()
    data_temp = data[col].str.split(pat=pat, expand=True).stack().reset_index(level=1, drop=True).rename(col+'_explode')
    if drop_col:
        data.drop(col, 1, inplace=True)
    return data.join(data_temp)
    
df = pd.DataFrame([[1, 'a b c'], 
                   [2, 'a b'],
                   [3, np.nan]], columns=['id', 'col'])

explode(df, 'col', pat=' ')
```
```python
#	id	col_explode
#0	1	a
#0	1	b
#0	1	c
#1	2	a
#1	2	b
#2 	3	NaN
```
(2) 一列变多列
```python
df.col.str.split(' ', expand=True)
```
```python
#	0	1	2
#0	a	b	c
#1	a	b	None
#2	NaN	NaN	NaN
```

### groupby使用

根据df的personid进行groupby，统计一下用户消费consume这一列特征的相关聚合情况；
比如count, max, kurt

```python
gr = df.groupby('personid')['consume']
df_aggr = gr.agg([('_count','count'),('_max',np.max),('_kurt',pd.Series.kurt)]).reset_index()

# 多个特征聚合统计值拼接
df = df.merge(df_aggr, how='left', on='personid').fillna(0)
```

### python画图及显示中文

```python
# 解决方法一
import matplotlib as mpl
mpl.rcParams['font.sans-serif'] = ['SimHei']
mpl.rcParams['font.serif'] = ['SimHei']

# 如果方法一解决不了
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']  # 解决中文显示问题-设置字体为黑体
plt.rcParams['axes.unicode_minus'] = False  # 解决保存图像是负号'-'显示为方块的问题

# 如果方法二解决不了
import matplotlib
zhfont = matplotlib.font_manager.FontProperties(fname='../simsun.ttc')
plt.title("职业分布情况",fontproperties=zhfont)
plt.xlabel("用户职业",fontproperties=zhfont)
plt.ylabel("逾期用户比例",fontproperties=zhfont)
#或者
import seaborn as sns
p = sns.color_palette()
sns.set_style("darkgrid",{"font.sans-serif":['simhei', 'Arial']})
fig = plt.figure(figsize=(20, 20))
ax1 = fig.add_subplot(3, 2, 1) # 总共3行2列6张，这是第一张图
ax1=sns.barplot(职业分布.index, 职业分布.逾期/职业分布.总数, alpha=0.8, color=p[0], label='train')
ax1.legend()
ax1.set_title(u'职业分布情况',fontproperties=zhfont) 
ax1.set_xlabel(u'用户职业',fontproperties=zhfont)
ax1.set_ylabel(u'逾期用户比例',fontproperties=zhfont)
```

```bash
# Graphviz 中文乱码
centos5.x下 
yum install fonts-chinese 
centos6.x或7.x下 
yum install cjkuni-ukai-fonts

fc-cache -f -v 刷新字体缓存
```

### 给字典按value排序

```python
model = xgb.train()
feature_score = model.get_fscore()
#{'avg_user_date_datereceived_gap': 1207,
# 'buy_total': 2391,
# 'buy_use_coupon': 557,
# 'buy_use_coupon_rate': 1240,
# 'count_merchant': 1475,
# 'coupon_rate': 5615,
# ...
# }
```

方法一：
```python
sorted(feature_score.items(), key=lambda x:x[1],reverse=True)
```

方法二：
```python
df = pd.DataFrame([(key, value) for key,value in feature_score.items()],columns=['key','value'])
df.sort_values(by='value',ascending=False,inplace=True)
```

### sorted高级用法

用法一：<br>
这里，列表里面的每一个元素都为二维元组，key参数传入了一个lambda函数表达式，其x就代表列表里的每一个元素，然后分别利用索引返回元素内的第一个和第二个元素，这就代表了sorted()函数利用哪一个元素进行排列。而reverse参数就如同上面讲的一样，起到逆排的作用。默认情况下，reverse参数为False。<br>
```python
l=[('a', 1), ('b', 2), ('c', 6), ('d', 4), ('e', 3)]
sorted(l, key=lambda x:x[0], reverse=True)
# Out[40]: [('e', 3), ('d', 4), ('c', 6), ('b', 2), ('a', 1)]
sorted(l, key=lambda x:x[1], reverse=True)
# Out[42]: [('c', 6), ('d', 4), ('e', 3), ('b', 2), ('a', 1)]
```

用法二：<br>
```python
# 调整数组顺序使奇数位于偶数前面，奇偶相对顺序不变
# 按照某个键值（即索引）排序，这里相当于对0和1进行排序
a = [3,2,1,5,8,4,9]
sorted(a, key=lambda c:c%2, reverse=True)
# key=a%2得到索引[1,0,1,1,0,0,1] 相当于给a打上索引标签[(1, 3), (0, 2), (1, 1), (1, 5), (0, 8), (0, 4), (1, 9)]
# 然后根据0和1的索引排序 得到[0,0,0,1,1,1,1]对应的数[2,8,4,3,1,5,9]，
# 最后reverse的时候两块索引整体交换位置[1,1,1,1,0,0,0] 对应的数为[3, 1, 5, 9, 2, 8, 4] 这一系列过程数相对位置不变
```

用法三：<br>
需要注意的是，在python3以后，sort方法和sorted函数中的cmp参数被取消，此时如果还需要使用自定义的比较函数，那么可以使用cmp_to_key函数(在functools中)<br>
```python
from functools import cmp_to_key
arr = [3,5,6,4,2,8,1]
def comp(x, y):
    if x < y:
        return 1
    elif x > y:
        return -1
    else:
        return 0
        
sorted(arr, key=cmp_to_key(comp))
# Out[10]: [8,6,5,4,3,2,1]
```

用法三（衍生）：<br>
输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。<br>
```python
# 把数组排成最小的数
from functools import cmp_to_key
arr = [3, 32, 321]
arr = map(str, arr) # or [str(x) for x in arr]
ll = sorted(arr, key=cmp_to_key(lambda x,y:int(x+y)-int(y+x)))
print(int(''.join(ll)))
# Out[3]: 321323
```

### time用法

```python
import time
s = 'Jun-96'
time.mktime(time.strptime(s,'%b-%y'))
# strptime函数是将字符串按照后面的格式转换成时间元组类型；mktime函数则是将时间元组转换成时间戳
```

### 两层列表展开平铺

性能最好的两个方法

1. 方法一
```python
input = [[1,2],[3,4,5],[7]]
[item for sublist in input for item in sublist]
```

2. 方法二
```python
from itertools import chain
list(chain(*input))
# list(chain.from_iterable(input))
```

### 读取百度百科词向量

```python
from bz2 import BZ2File as b2f
import tarfile
path = 'data/sgns.target.word-ngram.1-2.dynwin5.thr10.neg5.dim300.iter5.bz2'
fp = b2f(path)
lines = fp.readlines()

def get_baike_wv(lines):
    d_ = {}
    for line in lines:
        tmp = line.decode('utf-8').split(' ')
        d_[tmp[0]] = [float(x) for x in tmp[1:-1]]
    return d_
baike_wv_dict = get_baike_wv(lines)
```

### logging

```python
import logging
logging.basicConfig(format='%(asctime)s : %(levelname)s : %(message)s',\
    level=logging.INFO) # logging.WARNING
```
