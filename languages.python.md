---
id: 016f3563-9d1c-47ab-a5ba-5746f058da28
title: Python
desc: ''
updated: 1620395406491
created: 1614172374179
---

### tips
```python
arr = [1,2,3]
dir(arr) # 列出变量所指对象的所有方法
type(arr) # 打印变量的类型

arr[::-1] # 倒序
arr[0,3,2] # 即为：arr.__getitem__(slice(None,3,2))

a = (123) # 想定义为tuple，结果成了int
a = (123,) # 这才是定义一个元素是元组
a = 1, 2  # 这也是定义一个元组（因为不可更改）

# 利用set来给list去重
list_a = [1,2,3,3]
list_b = list(set(list_a))

[item for item in range(10) if item % 3 == 0] # [0,3,6,9]

# 以下都是定义tuple
a = (3,)
a = 3,
a = 3,4
a = (3) # 这是int

# 判断类型是否合适
if isinstance(x, (int, float)):
    pass
```

### 迭代器
![](/assets/images/2021-02-25-00-10-55.png)
要实现自己的迭代器，其实就是要实现一个`__iter__()`方法，返回一个迭代器，而`__next__()`方法是迭代器的方法，并不是在自身类里面去实现的方法。（所以你`dir(list)`是看不到数组的`__next__()`方法的

```python
# 迭代时加索引需要enumerate一下
>>> arr = ['a','b','c']
>>> c = [f'{index}:{item}' for index, item in enumerate(arr)] # 其实就是解包(unpack)

```

### zip
```python
a = ['walker','jack','lucy']
b = [23,24,25]
c = ['male','male','female']
r = [f'{name}/{sex}/{age}' for name, age, sex in zip(a,b,c)]
# 其实就是每次从各个数组里抽一个元素出来组成一个tuple, 然后再解包
['walker/male/23', 'jack/male/24', 'lucy/female/25']
# 自己去测如果数组元素数量不一会怎样

# 加索引
for index, (name, age, sex) in enumerate(zip(a,b,c)):
    print(f'{index},{name},{age},{sex}')
```

```python
for i in range(1, 2):
    for j in range(1, 2):
        for k in range(0, 3):
            for l in range(0, 3):
                
```