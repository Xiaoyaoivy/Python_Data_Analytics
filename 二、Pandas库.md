# 二、pandas库

```python
>> import pandas as pd
>> import numpy as np
```

pandas的核心为两大数据结构：Series（一维数据）和Dataframe（多维数据）

## 2.1 Series对象

```python
# Series s.index/s.values
>> s = pd.Series([12,94,12,1]) #提供了索引index
>> s
0    12
1    94
2    12
3     1
dtype: int64
    
>> s = pd.Series([12,94,12,1],index=['a','b','c','d']) #定义索引index
>> s
a    12
b    94
c    12
d     1
dtype: int64

>> s = np.array([1,2,3,4])
>> s_new = pd.Series(s)
# s_new是s的引用，并不是副本，会同时改变。
```

**Series对象组成的元素**

```python
>> s.unique() #返回结果为一个数组包含Series去重后的元素，顺序随意
array([12, 94,  1], dtype=int64)
    
>> s.value_counts() #返回各个不同的元素，计算出现次数
12    2
94    1
1     1
dtype: int64
    
>> s.isin([12,1]) #isin判断所属关系
0     True
1    False
2     True
3     True
dtype: bool
>> s[s.isin([12,1])]
s[s.isin([12,1])]
0    12
2    12
3     1
dtype: int64

# NaN(Not a number，非数值) np.NaN即可创建
# isnull()和notnull()函数用来识别有没有对应元素的索引

# Series用作字典
>> mydict = {'red':2000,'blue':1000,'yellow':500,'orange':1000}
>> myseries = pd.Series(mydict)
>> myseries
blue      1000
orange    1000
red       2000
yellow     500
dtype: int64
```

## 2.2 DataFrame对象

DataFrame可以理解为一个由Series组成的字典，其中每一列的名称为字典的键，形成DataFrame的列的Series作为字典的值。将存放有标签的数组赋给index选项，将存放有列名称的数组赋给column选项。

```python
>> data = {'color':['blue','red','yellow','green','white'],
          'object':['ball','pen','pencil','paper','mug'],
          'price':[1.2,1.0,1.3,1.4,1.5]}
>> frame = pd.DataFrame(data)
>> frame
    color  object  price
0    blue    ball    1.2
1     red     pen    1.0
2  yellow  pencil    1.3
3   green   paper    1.4
4   white     mug    1.5
>> frame = pd.DataFrame(data,index=['one','second','third','forth','fifth'])
>> frame
         color  object  price
one       blue    ball    1.2
second     red     pen    1.0
third   yellow  pencil    1.3
forth    green   paper    1.4
fifth    white     mug    1.5

#另一种创建的方式
>> frame = pd.DataFrame(np.arange(16).reshape((4,4)),
                        index = ['one','second','third','forth'],
                       columns = ['ball','pen','pencil','paper'])
>> frame
        ball  pen  pencil  paper
one        0    1       2      3
second     4    5       6      7
third      8    9      10     11
forth     12   13      14     15
```

**选取元素**

```python
>> frame.columns #所有列的名称
>> frame.index #所有列表
>> frame.values #存储元素

#用ix属性和行的索引值获得DataFrame的行
>> frame.ix[2]
ball       8
pen        9
pencil    10
paper     11
Name: third, dtype: int32
#下面更适用
>> frame[2:3] #仅返回第2行的（从0开始计数）
       ball  pen  pencil  paper
third     8    9      10     11
# 需要获取存储在DataFrame中的一个元素，需要依次指定元素所在的列名称、行的索引值或者标签。
>> frame['object'][3]
'paper'

# 用name属性指定DataFrame结构中的索引数组
>> frame.index.name = 'id'
>> frame.columns.name = 'item'
>> frame
item   color  object  price
id                         
0       blue    ball    1.2
1        red     pen    1.0
2     yellow  pencil    1.3
3      green   paper    1.4
4      white     mug    1.5

# 添加新的列
>> frame['new'] = [1.0,2.9,3.0,5.6,5.2]  #其中数组内可以为同一个数字，即frame['new']=[1.9],但不可以是不匹配index的长度

>> frame
item   color  object  price  new
id                              
0       blue    ball    1.2  1.0
1        red     pen    1.0  2.9
2     yellow  pencil    1.3  3.0
3      green   paper    1.4  5.6
4      white     mug    1.5  5.2
>> frame['object'][3] = 'test' #修改赋值

#删除一列
>> del frame['new']

#删除一行(排除特定行)
# 首先保留一行或多行
>> frame[frame.color.isin(['blue','red'])]
item color object price  new
id                          
0     blue   ball   1.2    1
1      red    pen     1  2.9
# 想要除了这两行之外的数据，就需要绕点路了
>> ex_list = list(frame.color)
>> ex_list.remove('blue')
>> ex_list.remove('red')
>> frame[frame.color.isin(ex_list)]
item   color  object  price  new
id                              
2     yellow  pencil    1.3  3.0
3      green   paper    1.4  5.6
4      white     mug    1.5  5.2
```

**用嵌套字典生成DataFrame对象**

```python
# 嵌套字典作为参数传递给DataFrame()构造函数时，会将外部的键解释成列名称，将内部的键解释用作索引的标签
>> ndst = {'red':{2011:12,2013:14},'yellow':{2011:12,2012:13,2013:14},'green':{2011:12,2012:13,2013:14}}
>> frame2 = pd.DataFrame(ndst)
>> frame2
      green   red  yellow
2011     12  12.0      12
2012     13   NaN      13
2013     14  14.0      14
```

**转置**

```python
>> frame.T #行变列，列变行
```

## 2.3 Index 对象

pandas的Index对象有is_unique属性

```python
>> frame.index.is_unique
True
```

**更换**索引pandas的reindex()函数可以更换Series对象/DataFrame对象的索引，可以调整索引序列中各标签的顺序，删除或增加新标签（NaN为其元素）

```python
# 若原标签为a,b,c,d
>> s.reindex(['t','a','b','c']) #按照新的标签重新排列，删除了d所在行，增加了t在第一行但元素为NaN

# 对于缺失的行进行插值包括ffill（索引前面的元素）、bfill（索引后面的元素）
>> s.reindex(range(6),method='ffill')
>> frame.reindex(range(5),method='ffill',columns=['colors','price','new','objects'])
```

**删除**索引 drop()，返回的是删除索引的数据，原来的并没有变

```python
>> frame.drop(['one','second']) #删除行
 ball  pen  pencil  paper
third     8    9      10     11
forth    12    1      14     15
>> frame.drop(['pen','pencil'],axis=1) #删除列，没有axis=1会报错
        ball  paper
one        0      3
second     4      7
third      8     11
forth     12     15
```

**算数运算**

```python
#加法 add() / 减法 sub() / 除法 div() / 乘法 mul()
>> frame1.add(frame2)
```

DataFrame和Series之间的运算，需要保证Series的索引和DataFrame对象的列名称保持一致

**定义函数**

```python
#返回series对象
>> f = lambda x: x.max() - x.min()
# 等价于
>> def f(x)
	return x.max() - x.min()

# 调用该函数
>> frame.apply(f) #或者f(frame)
ball      12
pen       12
pencil    12
paper     12
dtype: int32
>> frame.apply(f,axis=1) #对行进行处理
one       3
second    3
third     3
forth     3
dtype: int64

#返回DataFrame对象，但实际上只是返回多个行
>> f = lambda x: pd.Series([x.min(),x.max()],index=['min','max'])
>> frame.apply(f)
 ball  pen  pencil  paper
min     0    1       2      3
max    12   13      14     15

```

**统计函数**

```python
>> frame.describe()
       ball        pen     pencil      paper
count   4.000000   4.000000   4.000000   4.000000
mean    6.000000   7.000000   8.000000   9.000000
std     5.163978   5.163978   5.163978   5.163978
min     0.000000   1.000000   2.000000   3.000000
25%     3.000000   4.000000   5.000000   6.000000
50%     6.000000   7.000000   8.000000   9.000000
75%     9.000000  10.000000  11.000000  12.000000
max    12.000000  13.000000  14.000000  15.000000
>> frame
        ball  pen  pencil  paper
one        0    1       2      3
second     4    5       6      7
third      8    9      10     11
forth     12   13      14     15
#25分位（表示有25%的数据小于此数值,反映市场的较低端水平。），50分位（中端水平），75分位（较高端水平）#
#step1 :得出四分位间。附表中有N个数据，共N-1个间隔，则四分位间为(N-1)/4。这里N=4，即四分位间为0.75
#step2 :计算25分位值，第一个四分位值（即25分位值）=第（1+0.75）个数的数字，即第1和第2个数字之间的0.75位置，0+（4-0）*0.75=3
#step3 :计算50分位值，第二个四分位值（即50分位值）=第（1+0.75*2）个数的数字，即第2和第3个数字之间的0.5位置，4+（8-4）*0.5=6
#step4 :计算75分位值，第三个四分位值（即75分位值）=第（1+0.75*3）个数的数字，即第3和第4个数字之间的0.25位置，8+（12-8）*0.25=9
```

**排序**

```python
# pandas的sort_index()函数返回一个跟原对象元素相同但顺序不同的新对象（对轴/index进行排序）
>> s.sort_index() #默认升序排列
>> s.sort_index(ascending = False) #降序排列 
>> frame.sort_index()
>> frame.sort_index(axis=1) #按列排列

# 对数据结构中的元素进行排序
#对Series进行排序order()函数
>> s.order()
#对DataFrame进行排序
>> frame.sort_index(by='pen')

# 排位次操作（ranking）rank函数返回从小到大排序的下标，对于平级的数，rank是通过“为各组分配一个平均排名”的方式破坏评级关系
obj = pd.Series([7,-5,7,4,2,0,4])
>> obj.rank() #默认是average，即在相等分组中，为各个值分配平均排名+
0    6.5
1    1.0
2    6.5
3    4.5
4    3.0
5    2.0
6    4.5
dtype: float64
>> obj.rank(method='first') #按值在原始数据中的出现顺序分配排名
0    6.0
1    1.0
2    7.0
3    4.0
4    3.0
5    2.0
6    5.0
dtype: float64
```

**相关性和协方差**

```python
#针对两个Series对象
>> s1.corr(s2) #相关性
>> s1.cov(s2) #协方差
# 用corrwith()方法可以计算DataFrame对象的列或行与Series对象或者其他DataFrame对象元素两两之间的相关性
>> frame.corrwith(s)
>> frame.corrwith(frame2)
```

## 2.4 NaN值

```python
# 复制 
>> np.NaN

# 过滤NaN 
>> s.dropna() #Series 或者 s[s.notnull()]
>> frame.dropna(how = 'all') #删除所有元素均为NaN的行或者列，避免只要行/列有NaN就删除

# 填充
>> frame.fillna({'ball':1,'mug':0,'pen':99}) #将不同列的NaN替换为不同的元素，依次指定列名称及要替换成的元素即可。
```

## 2.5 等级索引和分级 

 等级索引(hierarchical indexing) 

```python
# 构建
>> mser = pd.Series(np.random.rand(8),index=[['w','w','w','b','b','r','r','r'],['u','d','r','u','d','u','d','l']])
>> mser
w  u    0.358769
   d    0.815084
   r    0.917792
b  u    0.515556
   d    0.609428
r  u    0.703287
   d    0.980142
   l    0.429397
dtype: float64
    
# 索引    
>> mser['w'] #第一列索引
u    0.358769
d    0.815084
r    0.917792
dtype: float64
>> mser[:,'u'] #第二列索引
w    0.358769
b    0.515556
r    0.703287
dtype: float64
    
# 将等级索引转换为DataFrame数据
>> mser.unstack()
   d         l         r         u
b  0.609428       NaN       NaN  0.515556
r  0.980142  0.429397       NaN  0.703287
w  0.815084       NaN  0.917792  0.358769
>> frame.stack() #将DataFrame变成Series数据

# 调整某轴上各层级顺序
>> frame.swaplevel('label1','label2') #仅互换位置，不改变数据顺序
# 调整某层各数据元素顺序
>> frame.sortlevel('label1')
# 按层级统计数据
>> frame.sum(level = 'label1') #对行统计
>> frame.sum(level = 'label2',axis = 1) #对列统计
```

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  