# 一、Numpy库

整个Numpy库的基础是ndarray（N-dimensional array，N维数组）对象。

Numpy数组一旦创立大小固定。

```python
>>a = np.array([1,2,3])  #以python列表为参数
```

调用变量的dtype属性，可以知道新建的ndarray属于哪种数据类型。

```python
>>a.dtype    
dtype('int32')
```

**轴**(axes)数量需要使用ndim属性，即数组的维

```python
>>a.ndim 
1
```

数组**长度**使用size属性即a.size #3

数组的**型**（由N个正整数组成的元组来指定，原著的每个元素对应每一维的大小），用shape属性即a.shape #(3L,)

itemsize属性定义了数组中每个元素的长度为几个字节a.itemsize #4

data属性表示包含数组实际元素的缓冲区。

**创建数组**

```python
>>b = np.array([[1,2,3],[4,5,6]]) #注意是列表

>>c = np.array(((1,2,3),(4,5,6))) #嵌套元组或元组列表

>>d = np.array([(1,2,3),(4,5,6)]) #元组或者列表组成的列表

>>e = np.array([[1,2,3],[4,5,6]], dtype = complex) #定义复数数组

```

**自带的数组创建方法**

```python
>>np.zeros((3,3)) #3*3的float64位数组为0.

>>np.ones ((3,3)) #3*3的float64位数组为1.

>>np.arange(first,end,distance) #first起始位置，end结束位置，distance间距，三个参数可以为整数或浮点数
>>np.arange(0,10)  
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])  # end-1

>>np.arange(0,12).reshape(3,4) #重组数组
array([[ 0,  1,  2,  3],
   [ 4,  5,  6,  7],
   [ 8,  9, 10, 11]]) #输出结果

# reshape(维数，行数，列数) #
>> A
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11],
       [12, 13, 14, 15]])
>>A.reshape(4,2,2)
array([[[ 0,  1],
        [ 2,  3]],

       [[ 4,  5],
        [ 6,  7]],

       [[ 8,  9],
        [10, 11]],

       [[12, 13],
        [14, 15]]])
>> A.reshape(2,2,4)
array([[[ 0,  1,  2,  3],
        [ 4,  5,  6,  7]],

       [[ 8,  9, 10, 11],
        [12, 13, 14, 15]]])

>>np.linspace(fisrt,end,parts) #parts表示分成的部分数
>>np.linspace(0,11,5)
array([  0.  ,   2.75,   5.5 ,   8.25,  11.  ])

>>np.random.random((3,3)) #使用随机数填充数组 [0,1]之间的随机数
```

**算数运算符**（元素集运算，对应元素相乘）

```python
>>a = np.arange(0,9).reshape(3,3);b=ones((3,3));
>>a*b 
a
```

**矩阵积**(∑aibi)

```python
>>np.dot(a,b)
#或者 
>>a.dot(b)
#但不可以写成np.dot(b,a)
```

**自增和自减运算符**

```python
>>a += 1 #数组内元素自增1
>>a -= 1 #数组内元素自减1
```

**通用函数**（对数组中的每一个元素逐一进行操作）

np.sqrt(a)    np.log(a)    np.sin(a)

**聚合函数**(对以阻止进行操作，返回一个单一值作为结果的函数)

a.sum()    a.min()    a.max()   a.mean()   a.std()

**索引、切片、迭代方法**

```python
# 索引：一维数组 #
>>a = np.arange(10,16)
>>a
array([10,11,12,13,14,15])
>>a[[1,3,4]] #数组a的第一个、第三个、第四个
array([11,13,14])
# 索引：二维数组 #
>>b = np.arange(10,19).reshape((3,3))
array([[10,11,12],
       [13,14,15],
       [16,17,18]])
>>b[1,2] #索引
15
```

```python
# 索引 数组接上 #
>> a [1:5:2] #起始位置，截止位置-1，间隔位置 
array([11,13])
>> b[[0,2],0:2] #行列索引不连续，用数组表示
array([10,11],
      [16,17])
```

```python
# 迭代方法 #
>> for i in a: print(i)
>> for row in b: print(row)
[10,11,12]
[13,14,15]
[16,17,18]
>> for row in b.flat:print(row) #遍历每个矩阵
10
11
...
18

# 用函数处理行、列或者单个元素#
# apply_along_axis()函数
# 三个参数，聚合函数（可以自己定义），对应哪条轴（0为按列，1为按照行），操作数组
np.apply_along_axis(np.mean,axis = 0,arr = a) 
```

**条件布尔运算**

```python
>> A = np.random.random((3,3))
>> A
array([[ 0.05817992,  0.70521063,  0.53256765],
       [ 0.52156313,  0.57506253,  0.55110023],
       [ 0.15821219,  0.7034218 ,  0.15971613]])
>> A > 0.5
array([[False,  True,  True],
       [ True,  True,  True],
       [False,  True, False]], dtype=bool)
>> A [A > 0.5]
array([ 0.70521063,  0.53256765,  0.52156313,  0.57506253,  0.55110023,
        0.7034218 ])
```

**形状变换**

```python
# a为1行12列数组
>> b = a.reshape(3,4) #将数组变成3行4列
>> a.shape = (3,4)  #数组b本身改变，没有返回新对象
# b为3行4列数组
>> a = b.ravel()   #将二维数组变成一维数组，按行展开 
>> b.shape = (12)
# 行列转置
>> a.transpose()
```

**数组操作**

```python
# vstack()函数执行垂直入栈操作
>> A = np.vstack(a,b) #a,b为3行3列数组。A为6行3列
# hstack()函数执行垂直入栈操作
>> B = np.vstack(a,b) #a,b为3行3列数组。B为3行6列                                                 # 多个数组之间栈操作
>> a = np.array([0,1,2])
>> b = np.array([3,4,5])
>> c = np.array([6,7,8])
>> np.column_stack((a,b,c)) #将一维数组作为列入栈
array([[0,3,6],
      [1,4,7],
      [2,5,7]])
>> np.row_stack((a,b,c)) #将一维数组作为行入栈
array([[0,1,2],
      [3,4,5],
      [6,7,8]])
>>a = np.array([[0,1,2],[4,5,6],[7,8,9]])
>>a 
array([[0, 1, 2],
       [4, 5, 6],
       [7, 8, 9]])
##注意这里的多维数组的入栈                                                                                    
>>np.column_stack((a,b,c))
array([[0, 1, 2, 3, 6],
       [4, 5, 6, 4, 7],
       [7, 8, 9, 5, 8]])
```

```python
# 数组切分
# 水平切分hsplit() 按照宽度切分为两部分
>> [B,C] = np.hsplit(A,2) #若A为4*4矩阵，则分成两个4*2矩阵，参数不能为3，因为不能平分
# 垂直切分vsplit() 按照高度切分为两部分
>> [B,C] = np.vsplit(A,2) #若A为4*4矩阵，则分成两个2*4矩阵。
# 不对称切分split(),参数 axis=1时索引为列索引，axis=0时索引为行索引,无参数默认为0
>> [B,C,D] = np.split(A,[1,3],axis=1) #A为一列，C为一列
>> [B,C,D,E] = np.split(A,4) #为4个1*4的数组
>> [B,C] = np.split(A,2,1) # 和[B,C] = np.vsplit(A,2) 功能相同 
```

**对象的副本或者视图**

Numpy中，所有赋值运算不会为数组和数组中的任何元素创建副本，即使是切片操作。

需要保留副本可以采用copy()函数

```python
>> c = a.copy()
```

**向量化、广播机制**

广播机制：若两个数组个维度兼容，也就是两个数组的每一维登场，或者其中一个数组为一维数组，则广播机制就适用。

**结构化数组**

```python
>> structed = np.array([(1,'First',0.5,1+2j),(2,'Second',1.3,2-2j),(3,'Third',0.8,1+3j)],dtype=[('id','i2'),('position','a6'),('value','f4'),('complex','c8')])
>> structed
array([(1, b'First', 0.5, (1+2j)),
       (2, b'Second', 1.2999999523162842, (2-2j)),
       (3, b'Third', 0.800000011920929, (1+3j))], 
      dtype=[('id', '<i2'), ('position', 'S6'), ('value', '<f4'), ('complex', '<c8')])

#或者创建后重新定义结构化数组的dtype属性，在元组中指定各字段的名称
>> structed = np.array([(1,'First',0.5,1+2j),(2,'Second',1.3,2-2j),(3,'Third',0.8,1+3j)],dtype=('i2,a6,f4,c8'))
>> structed
array([(1, b'First', 0.5, (1+2j)),
       (2, b'Second', 1.2999999523162842, (2-2j)),
       (3, b'Third', 0.800000011920929, (1+3j))], 
      dtype=[('f0', '<i2'), ('f1', 'S6'), ('f2', '<f4'), ('f3', '<c8')])
>>structed.dtype.names = ('id','order','value','complex') #complex复数

# 获取数组的某一列
>> structed['order']
array([b'First', b'Second', b'Third'], 
      dtype='|S6')
```

**数组数据文件的读写**

```python
# 二进制文件的读写
>> np.save('save_data',data) #写出
>> loaded_data = np.load('save_data.npy') #读入

# 文件中列表形式数据,读入以结构化数组的形式出现
>> data =np.getfromtxt('data.csv',delimiter = ',',names=True)#参数包括存放数据的文件名、用于分割值的字符和是否含有列标题，若有数据缺省则为nan
```

