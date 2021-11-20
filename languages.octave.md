---
id: 10a205fc-8c23-471b-85d6-3684efd88c96
title: Octave
desc: ''
updated: 1620231349930
created: 1618244175465
---

```bash
# 注释符号是#, 为了颜色高亮，此处全用#

1 ~= 2      # true
xor(1, 0)   # 1
PS1('>> '); # 把提示符设为'>> '

a = 3                       # 变量，赋值，并打印在标准输出
a = 3;                      # 不打印 （semicolon supressing output)
disp(a)                     # 显示变量
disp(sprintf('#0.2f', a))   # 2.00 c-style printf
format long                 # 显示更多的位数
format short                # 显示更少的位数

# martrix
A = [1 2; 3 4; 5 6]  # 可以用逗号分隔
B = [1 2;
> 3 4;
> 5 6]          # > 代表回车后的新行提示符
ones(2, 3)      # 2x3矩阵，全是1
C= 2*ones(2, 3) # 2x3矩阵， 全是2
size(A)         # [3 2] 表示A为3行2列矩阵，同时结果自己也是一个1x2矩阵
size(size(A))   # 永远是[1 2]
size(A, 1)      # 3 （1返回行数，2返回列数）
C = [A B]       # 行数一样的矩阵横向拼接 == [A, B]
D = [A:B]       # 列数一样的矩阵竖向拼接
E = magic(3)    # 生成一个3x3的矩阵，行，列，对角线和都相等

# vector
v = [1 2 3]     # 1x3 vector 行向量
v2 = [1; 2; 3]  # 3x1 vector 列向量
v3 = 1:0.2:2    # 以1为初始值，0.2为步进值，2为末值的行向量
v4 = 1:6        # 步进值为默认值（1）
w = ones(1, 3)  # [1 1 1]
u = zeros(1, 3) # [0 0 0]
F = [0:1:5]     # 生成0-5的row向量，步长为1

# length
length(v)       # 向量返回个数（不管是行还是列向量）
length(A)       # 矩阵返回mxn中大一点维度（所以其实向量也是这个原理，1永远是小数）

# index
A(1:5)          # 1 3 5 2 4 (返回前5个元素，先列后行) (row vector)
A(:)            # put all element in A into a column vector
A(2,:)          # every element in row 2
                # 把逗号理解为管道, 取出第二行，然后取全部
A([1 3],:)      # full row 1 and row 3
A(2,:)=[7 8]    # set value for row 2
A(:,2)=[7,8]    # set value for column 2 取出全部，然后取第2列
A=[A,[11;12]]   # appen a column ## CAN'T append row【concatenate】

# random
w = rand(1, 3)      # random (0, 1) row vector
w = rand(3, 3)      # random (0, 1) 3x3 matrix
v = randn(1, 3)     # (Gausian distribution, mean=0, variance=1) random number

randperm(10, 3)     # 10选3，所以(10, 10) = 10，即10选10（随机排列）

randi(3)            # 任选一个3以下的数
randi(10,2)         # 任选一个10以下的数，输出成2x2矩阵
randi(10,2,3)       # 同上，输出成2x3矩阵，以此类推2x3*x*y*z...
randi([1,8],3)      # （1，8）之间的数组成3x3矩阵



eye(4)      # 4x4 identity matrix

help eye    # show help

# variables manage
who         # variables in the current scope
whos        # the detail view
clear A     # remove the variable A
clear       # will clear all the variables

# compute data

# period is a element wise operator
A.*B            # 相应元素位置相乘（不是点积）
A.^2            # 每个元素取平方 A.^2 ≠ A^2 (A^2为矩阵相乘，而不是元素平方)
A+2 2+A         # 每个元素加2 (加减乘除同理) （也可以写成A.+2, 2.+A)
A+ones(2,3)*3   # 每个元素加1*3
log(A), exp(A), abs(A), -A  # 元素级运算
sum(A), prod(A), ceil(A), floor(A) # 行、列级运算（默认按列计算，结果为一行）
sum(A,1)        # 按列求和
sum(A,2)        # 按行求和
rand(n)         # 生成nxn的随机数矩阵(0,1)
A'              # transpose of A (单引号')
A < 3           # 变成1和0的矩阵(true=1)
find(A<3)       # [row,col]来接，就是返回行索引和列索引，拼起来就是元素索引
flipud(A)       # 上下颠倒数组up-down
fliplr(A)       # 左右反转数组left-right
inv(A)          # A的逆矩阵，有可能求不出来
pinv(A)         # A的pseudo逆矩阵，即使求不出来也会有值返回（只保证相等后对角线为1）


max(A)          # martrix 最大的【行】
[v,i]=max(A)    # max 解包成 value 和 index
max(A,B)        # 最大的【列】组全成矩阵
max(max(A))     # vector 最大的数
max(A,[],1)     # column wise max, 挑出每列最大的数组成一行
max(A,[],2)     # row wise max, 挑出每行最大的数组成一列

# load data
# then the file name becomes the variable name
load featureX.dat
load('prices.dat')
save hello.mat v        # save variable data to a file (binary format)
save hello.txt v -ascii # save as text (ASCII)
addpath('path/to/swh')  # 添加search path

# plot, histogram
w = -6 + sqrt(10)*(randn(1, 10000)) # (Gaussian dist, mean=-6, var=sqrt(10), sd=10)

## hist
hist(w)     # show histogram
hist(w, 50) # histogram with more bins(buckets)

## plot
t=[0:0.01:0.98];
y1=sin(2*pi*4*t)
y2=cos(2*pi*4*t)
plot(t,y1)
hold on;            # 保留上一次绘制的图
plot(t,y2,'r')      # 自定义颜色
xlabel('time')
ylabel('value')
legend('sin','cos')
title('my plot')
print -dpng 'myplot.png'
close               # 关闭窗口
figure1 plot(t,y1)  # 以独立窗口绘图
figure2 plot(t,y2)
subplot(1,2,1)      # 把窗口分成1行2列，选择第1列绘制
plot(t,y1)
subplot(1,2,2)
plot(t,y2)
axis([0.5 1 -1 1])  # x轴刻度（0.5-1)，y轴刻度（-1，1）
CLF                 # clear figure

A = magic(5)
imagesc(A)          # 绘制一个5x5的颜色块
imagesc(A), colorbar, colormap gray # color 添加了一个颜色刻度指示
                                    # colormap指定配色方案
imagesc(A)                          # 但之后也变成灰阶了，怎么恢复？
```

这句话将产生10句输出(matlab)
```c
fprintf(' x = [%.0f %.0f], y = %.0f \n', [X(1:10,:) y(1:10,:)]');
```

## function

- 一个文件对应一个方法，文件名即是方法名（测试如果不符将如何）
- 第一行就要声明返回值和参数
- 
```javascript
function y = squareThisNumber(x)  // 声明y是返回值， x是入参

y = x^2
```
下面这个文件（函数）能返回两个返回值
```javascript
function [y1, y2] = squareAndCubeThisNumber(x)  // 声明y是返回值， x是入参

y1 = x^2
y2 = x^3

// 调用
// [a, b] = squareAndCubeThisNubmer(2)
```
>难道不就是一个矩阵（向量）吗？

### cost fuction 练习

traning data:(1,1, 2,2, 3,3)
分析
Design Matrix（X），变量矩阵，加上第一列补1，y是结果矩阵，theta是参数矩阵：
$X = \begin{bmatrix} 1 & 1 \\ 1 & 2 \\ 1 & 3 \\ \end{bmatrix},  y = \begin{bmatrix}1\\1\\1\end{bmatrix}, theta = \begin{bmatrix}0\\1\end{bmatrix}$

```javascript
function J = constFunctionJ(X, y, theta)

% X is the "design matrix" containing our trainging examples.
% y is the class labels

m = size(X, 1);          % number of traning examples
predictions = X*Theta;   % predictions of hypothesis on all examples
sqrErrors = (predictions - y).^2;   % squared errors

J = 1/(2*m) * sum(sqrErrors);
```
theta是你用来做predict(hypothesis)的值，也就是你需要调整的值，上面的例子是传入了（0，1），训练数据刚是0，1所以函数返回值是0
```javascript
j = costFunction(X, y, theta)
>> ans = 0
theta = [0;0]
j = costFunction(X, y, theta)
>> ans = 2.333
```

## Vectorization example

$h_\theta(x) = \sum_{j=0}^{n}\theta_i x_j = \theta^Tx$

$\theta = \begin{bmatrix} \theta_0 \\ \theta_1 \\ \vdots \end{bmatrix},\quad \bold x = \begin{bmatrix} x_0 \\ x_1  \\ \vdots \end{bmatrix}$

```python
#% Unvectorized implementation
prediction = 0.0
for j=i:n+1
    prediction = prediction + theta(j) * x(j)
end

#% vectorized implementation
prediction = theta' * x
```

### gradient descent

$\theta_j:=\theta_j-\alpha\frac{1}{m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})\cdot x^{(i)}_j$

$\delta = \frac{1}{m}\sum_{i=1}^{m}(h_\theta(x^{(i)})-y^{(i)})\cdot x^{(i)}_j$

$\therefore \delta = \begin{bmatrix} \delta_0 \\ \delta_1  \\ \vdots \end{bmatrix}\ \Rightarrow\ \theta := \theta - \alpha \cdot \delta$