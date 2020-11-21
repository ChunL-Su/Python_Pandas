# Pandas
- 1、一维数据表Series基础使用
```
import pandas as pd
import numpy as np

s = pd.Series([1,3,6,np.nan,44,1])
print(s)
>>>
0     1.0
1     3.0
2     6.0
3     NaN
4    44.0
5     1.0
dtype: float64
```
- 2、二维数据表dataframe基础使用
```
df = pd.DataFrame(数据，index=每行的名称，colums=每列的名称)
# 如果只给定数据，则DataFrame的行列默认使用0,1,2....

import numpy as np
import pandas as pd


dates = pd.date_range('20200101', periods=6)
>>>
DatetimeIndex(['2020-01-01', '2020-01-02', '2020-01-03', '2020-01-04',
               '2020-01-05', '2020-01-06'],
              dtype='datetime64[ns]', freq='D')


data = np.random.randn(6, 4)
>>>
[[-0.94094335 -0.33168564 -0.18940501  2.48639959]
 [-0.56339587  0.47170448 -0.39929543 -0.06690498]
 [ 0.02084647  1.36367954  0.42146406 -2.1544801 ]
 [ 0.72335465  0.02788663 -0.38898258  1.24370359]
 [-2.30708935 -0.47542664  0.45255563  0.72925453]
 [ 0.50509322  2.0046136   0.13564196  0.04769483]]


df = pd.DataFrame(data, index=dates, columns=['A','B','C','D'])
>>>
                   A         B         C         D
2020-01-01  0.701996  0.830169 -0.139766 -0.086681
2020-01-02  0.643758  0.976886 -0.457770 -1.933502
2020-01-03 -0.531837 -0.738127 -0.431889  0.676344
2020-01-04 -0.392764 -1.864045 -0.311110 -1.052997
2020-01-05  0.296888 -1.876355  0.903563  1.572263
2020-01-06 -0.515130  0.670423  1.431760  0.414356
```
- 3、以字典形式在DataFrame中添加数据
```python
import pandas as pd
import numpy as np

df = pd.DataFrame({'A':1,
                   'B':pd.Timestamp('20200101'),
                   'C':pd.Series(1,index=list(range(4)),dtype=int),
                   'D':np.array([3]*4,dtype=int),
                   'E':pd.Categorical(["test","train","test1","foo"]),
                   'F':'Foo'})
print(df)
```
```
>>>
   A          B  C  D      E    F
0  1 2020-01-01  1  3   test  Foo
1  1 2020-01-01  1  3  train  Foo
2  1 2020-01-01  1  3  test1  Foo
3  1 2020-01-01  1  3    foo  Foo
```
- 4、pandas中常用的一些小的功能
    - df.describe()  # 快速生成DataFrame中数据的方差平均值等描述数据的结果，但只统计数值数据，字符串等数据会忽略
    - df.T           # 把DataFrame看成是一个矩阵，进行转置操作
    - df.sort_index(axis=1,ascending=False)  #按列名(axis=1)倒序(ascending=False)排序
    - df.sort_insex(axis=0,ascending=True)   #按行名(axis=0)正序(ascending=True)排序
    - df.sort_values(by="列名")

## 使用Dataframe中的数据
- 1、选取出某列的数据
```
print(df['列名'])
print(df.列名)
```
- 2、使用切片的方式选取行数据
```
df[0:3]  # 选取前三行，索引0,1,2的行数据
df[行名1:行名2]   # 选取两行之间(包含两行边界)的数据
```
- 3、select by label:loc 通过标签来选择
```
df.loc[标签名]  # 目前和方法一效果基本一样
df.loc[:,[列名1:列名2]]   # 通过行列的方式具体指定某一小部分的数据
                        # 逗号前为行，逗号后为列
                        # 此处选取了所有的行
```
- 4、select by position:iloc #通过具体的位置选取数据
```
df.iloc[3]   #直接第三行的数据就出来了
df.iloc[3,1]   # 第三行第一个数据(索引为1的数据)
df.iloc[3:5,1:3]  # 第3~4行，1~2列的数据(索引数字)
df.iloc[[1,2,5],1:3] # 不连续选取，只选1，2，5行，1~2列的数据
```
- 5、mixed selection:ix # 混合筛选(数字和标签混合使用)
```
df.ix[:3,['列名1','列名2']]
```
- 6、Boolean indexing # 布尔筛选(选取出符合条件的行)
```
df[df.列名>8]  # 先判断某列所有大于8的数据，然后显示出包含这些数据的行
```
## 添加或更新某区域的值
- 1、选择某位置的值直接修改，类似与列表元素赋值
```
df.iloc[2,2]=111   # 将[2,2]这个位置的值修改为111

df[df.A>4]=0  # 将列名为A的这一列先与4比较，选择出大于4的所有行
              # 然后将选出的所有行的所有值全改为0
df.A[df.A>4]=0  # 这次只改变A这一列的数据为0
```
- 2、新添加一列
```
# 假设原数据只有A,B,C三列
df['D']=np.nan   # 在数据后边添加一列D，并赋值为NAN(空)

# 若想更加具体的快速大量赋值，则使用以下方法
df['E']=pd.Series([1,2,3,4],index='原数据各个行的名称列表')
# [1,2,3,4]为数据，index后边加上原数据的各个行名称
```
- 3、处理丢失的数据(一般以NAN表示)
```
# 1、丢掉某些行或列
df.dropna(axis=0,how='any')  # 丢掉存在nan的行
df.dropna(axis=1,how='all')  # 只丢掉所有值均为nan的列

# 2、将数据中的nan值修改为某个具体的默认值
df.fillna(value=0)  # 将所有nan值修改成0

# 3、检索表格中是否存在nan值
df.isnull()   # 返回一个和原数据同样大小的数据表，但是nan转化为True，非nan转化为False
np.any(df.isnull())==True  # np.any(数据表)=='某数据' 检索数据表中是否存在'某数据'
```
## 导入/导出数据
- 1、导入数据常用方法
```
# pd.read_csv('文件路径')

read_csv
read_excel
read_hdf
read_sql
read_json
read_msgpack
read_html
read_gbq
read_stata
read_sas
read_clipboard
read_pickle
```
- 2、导出数据常用方法
```
to_csv
to_excel
to_hdf
to_sql
to_json
to_msgpack
to_html
to_gbq
to_stata
to_sas
to_clipboard
to_pickle
```
- 3、使用导入方法
```python
import pandas as pd

data = pd.read_csv(r'D:\stu.csv', encoding='gbk')  # 数据中有中文的话，一定加上encoding='gbk'
print(data)
```
```
>>>
  name  age gender
0    A   21      男
1    B   22      女
2    C   23      男
3    D   24      女
4    E   25      女
5    F   26      女
```
- 4、导出方法
```python
import pandas as pd

data = pd.read_csv(r'D:\stu.csv', encoding='gbk')  # 数据中有中文的话，一定加上encoding='gbk'
print(data)

# 以上为导入方法
# 下面是导出成pickle格式的文件
data.to_pickle(r'D:\student.pickle')
```
## 多个DataFrame合并(concatenating)
- 1、直接合并
```python
import pandas as pd
import numpy as np

df1 = pd.DataFrame(np.ones((3, 4))*0, columns=['a', 'b', 'c', 'd'], dtype='int')
df2 = pd.DataFrame(np.ones((3, 4))*1, columns=['a', 'b', 'c', 'd'], dtype='int')
print(df1)
print(df2)
```
```
>>>
   a  b  c  d
0  0  0  0  0
1  0  0  0  0
2  0  0  0  0

   a  b  c  d
0  1  1  1  1
1  1  1  1  1
2  1  1  1  1
```
```
res = pd.concat([df1, df2], axis=0)
print(res)

>>>
   a  b  c  d
0  0  0  0  0
1  0  0  0  0
2  0  0  0  0
0  1  1  1  1
1  1  1  1  1
2  1  1  1  1
```
- 2、可使用ignore_index参数统一第一列的索引
```
res = pd.concat([df1, df2], axis=0)
print(res)

>>>
   a  b  c  d
0  0  0  0  0
1  0  0  0  0
2  0  0  0  0
3  1  1  1  1
4  1  1  1  1
5  1  1  1  1
```
- 3、对于列名不完全相同的数据合并,使用join参数
```
res = pd.concat([df1,df2],join={'inner','outer'})
# join参数默认为'outer'

# 例如有如下df1,df2
# df1
   a  b  c  d
0  0  0  0  0
1  0  0  0  0
2  0  0  0  0
# df2
   b  c  d  e
1  1  1  1  1
2  1  1  1  1
3  1  1  1  1
```
```
res = pd.concat([df1,df2],join='inner')
>>>
   b  c  d
0  0  0  0
1  0  0  0
2  0  0  0
1  1  1  1
2  1  1  1
3  1  1  1
```
- 4、append添加数据
```
res = df1.append([dfm,...,dfn])
# 这样就从上往下一个一个的加在df1的后边了
 ____
|df1|
|dfm|
| · |
| · |
| · |
|dfn|
 ----
# 当然也可以使用axis，ignore_index参数设置合并维度，或index的统一
```
- 5、merge合并
```python
# 针对两组数据key顺序相同
import pandas as pd

left = pd.DataFrame({'key': ['k0', 'k1', 'k2'],
                     'a': ['a0', 'a1', 'a2'],
                     'b': ['b0', 'b1', 'b3']})
right = pd.DataFrame({'key': ['k0', 'k1', 'k2'],
                      'c': ['c0', 'c1', 'c2'],
                      'd': ['d0', 'd1', 'd3']})
res = pd.merge(left, right, on='key')
print(res)
```
```
>>>
  key   a   b   c   d
0  k0  a0  b0  c0  d0
1  k1  a1  b1  c1  d1
2  k2  a2  b3  c2  d3
```
```python
# 如果两组数据存在多个key，或者key的顺序不同，则需要加上how参数
# how = {'left', 'right', 'outer', 'inner'}
import pandas as pd

left = pd.DataFrame({'key1': ['k0', 'k1', 'k2'],
                     'key2': ['k0', 'k0', 'k2'],
                     'a': ['a0', 'a1', 'a2'],
                     'b': ['b0', 'b1', 'b3']})
right = pd.DataFrame({'key1': ['k0', 'k0', 'k2'],
                      'key2': ['k0', 'k1', 'k2'],
                      'c': ['c0', 'c1', 'c2'],
                      'd': ['d0', 'd1', 'd3']})
res = pd.merge(left, right, on=['key1', 'key2'], how='inner')
print(res)
```
```
# inner
# 只有在两组数据中[key1,key2]的顺序相同的数据才会被列出
>>>
  key1 key2   a   b   c   d
0   k0   k0  a0  b0  c0  d0
1   k2   k2  a2  b3  c2  d3
```
```python
# 以上都是针对列key的合并，其实也可以针对行index进行合并
# 只需要在merge函数中添加以下参数
right_index = True
left_index = True
```
## 数据的可视化
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# data = pd.Series(np.random.randn(1000), index=np.arange(1000))   # 一条线
data = pd.DataFrame(np.random.randn(1000, 4), columns=list('ABCD'))  # 四条线
data = data.cumsum()   # 累加过程(也可以对数据进行其他操作)

# 绘制过程
data.plot()  # plot里边有很多参数，可以设置颜色，线条宽度等
plt.show()
```
```python
# 除了plot还有很多图标
# bar,hist,box,kde,area,scatter,hexbin,pie等
# 下面列出scatter的简单使用，其他图类似
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# data = pd.Series(np.random.randn(1000), index=np.arange(1000))   # 一条线
data = pd.DataFrame(np.random.randn(1000, 4), columns=list('ABCD'))  # 四条线
data = data.cumsum()   # 累加过程(也可以对数据进行其他操作)

# 绘制过程
ax = data.plot.scatter(x='A', y='B', color='pink', label='Class 1')
# 两幅图结合在一起用ax
data.plot.scatter(x='A', y='D', color='blue', label='Class 2', ax=ax)

plt.show()
```
