---
tags:
  - Python
  - 编程
date: 2025-03-04
---

![[附件/post-python-intro-cover.png|720]]

# 写在开头

“人生苦短，我用python”，作为最流行最好用的编程语言，非常好学，一学就会。

# 最基础

## 输出函数print

输出字符串

```python
print('helloworld')
```

输出数字

```python
print('520')
```

输出含有运算符的表达式（此时不要加单引号）

```python
print('1+2')#输出1+2
print(1+2)#输出3
```

将数据输出到文件中,注意点：使用file=fp，且不带单引号

```python
fp=open('D:/text.txt','a+')#a+：如果文件不存在就创建，如果文件存在就追加(意味着该程序每运行一次，文件里的helloworld就会多一个）
print('helloworld',file=fp)
fp.close()
```

不换行输出（输出内容在一行中）

```python
print('hello''world''python')
print('hello','world','python')#加了逗号输出中间才有空格
```

转义字符

```python
print('hello\nworld')#\n：即为newline，换行
print('hello\tworld')#\t为四个空格位，但是此处输出只有三个空格位，这是因为四个为一组，o已经占掉了\t里的一位，只剩三位了
print('helloooo\tworld')#这里的\t就是输出四位空格
print('hello\rworld')#\r即为return，指回车，会把前面的hello覆盖掉，输出只有world
print('hello\bworld')#\b即为backward，会把前一位o删掉

print('http:\\\\baidu.com')#此时会输出http:\\baidu.com
print('老师说:\'你们好\'')#\后跟单引号会直接输出单引号，但如果不用\则会报错

#原字符：不希望转义字符起作用就用原字符，就是在字符串之前加上r或者R
print(r'hello\nworld')#\此时\n会一并输出
#注意事项：要用原字符时，最后一个不能是反斜线，即不可以是print(r'hello\nworld\')，因为此时最后一个单引号会被当成输出，但两个反斜线可以
print(r'hello\nworld\\')
```

字符编码

无论是汉字还是字母在电脑中都算是字符，一个字符对应一个整数，这个整数可使用二进制，八进制，十进制，十六进制，当然最后到计算机中都会变成二进制，因为计算机只识别二进制

```python
print(chr(0b100111001011000))#0b即二进制，chr表示该二进制对应的ASCII码对应的字符，即汉字乘
print(ord('乘'))#会输出乘在ASCII码中对应的十进制数字，即20056，转换成二进制也就是上面的0b100111001011000
```

保留字（给任何对象命名时都不能用）

```python
import keyword
print(keyword.kwlist)#运行的输出结果就是所有的保留字
```

标识符（可以是字母、数字、下划线，不能以数字开头，不能是保留字，严格区分大小写）

变量

```python
name='玛利亚'
print(name)
print('标识',id(name))
print('类型',type(name))
print('值',name)
```

变量可以多次赋值，但在多次赋值后会指向最后一个

```python
name='玛利亚'
name='吴坷航'
print(name)
```

## 数据类型

整数类型 int 如98

默认的是十进制，二进制0b,八进制0o，十六进制0x

```python
n1=90
n2=-180
n3=0
print(n1,type(n1))
print(n2,type(n2))
print(n3,type(n3))#会发现他们的类型都是int
```

```python
print('十进制',118)
print('二进制',0b10101111)#不加0b计算机会当成十进制就是10101111，加上0b就是175
print('八进制',0o176)
print('十六进制',0x1EAF)
```

浮点数类型 float 如3.1415

```python
a=3.1415
print(a,type(a))
```

但是由于计算机是通过二进制进行存储的，所以存储浮点数会有误差，如下

```python
a=1.1
b=2.2
print(a+b)
```

输出结果并非3.3，因此需要导入模块Decimal来矫正

```python
from decimal import Decimal
print(Decimal('1.1')+Decimal('2.2'))#此时输出结果就是3.3了
```

布尔类型 bool 只有True False

```python
f1=True
f2=False
print(f1,type(f1))
print(f2,type(f2))
```

同时布尔值可以转成整数计算

```python
print(f1+1)#输出2
print(f2+1)#输出1
```

字符串类型 str 如'人生苦短，我用python'

可以使用单引号’，双引号“和三引号，其中三引号可以分行

```python
str1='人生苦短，我用python'
print(str1,type(str1))
```

数据类型转换

```python
name='吴坷航'
age=20
print(type(name),type(age))#说明两者数据类型不同
#print('我叫'+name+'今年'+age),程序报错，因为+为连接符，不能将str类型和int类型直接连接，要进行数据转换
print('我叫'+name+'今年'+str(age))
```

str()可以将其他类型转换成str类型

```python
a=90
b=3.14
c=False
print(str(a),str(b),str(c),type(str(a)),type(str(b)),type(str(c)))
```

int()可以将其他类型转换成int类型，注意：文字、字母类和小数类字符串无法转成int，浮点数类转化时抹零取整

```python
f1=98.7
f2='98.7'
print(int(f1),type(int(f1)))#会输出98
#print(int(f2),type(int(f2)))会报错，小数类字符串无法转成int
```

float()可以将其他类型转换成float类型，注意：文字、字母类和小数类字符串无法转成int，整数类型转换后会加.0

```python
'''嘿嘿，
我是
多行注释哦'''
```

## 输入函数input

```python
present=input('你想要什么礼物')
print(present,type(present))
```

从键盘录入两个整数，计算两个整数的和

```python
a=input('请输入一个加数')
b=input('请输入另一个加数')
print(a+b)
#此时输入10和20，会输出1020，因为a和b都是str类型，此时+起连接作用
```

```python
a=input('请输入一个加数')
a=int(a)
b=input('请输入另一个加数')
b=int(b)
print(a+b)#此时会输出30
```

# 运算符

## 算数运算符

标准运算符（加+，减-，乘*，除/，整除//）

```python
print(1+1)
print(1/2)
print(11/2)#输出5.5
print(11//2)#输出5，只取整数部分
```

取余运算%

```python
print(11%2)
```

幂运算

```python
print(2**3)#表示2的3次方
```

一正一负进行整除，会向下取整

```python
print(9//-4)#-3
print(-9//4)#-3
```

一正一负进行取余，公式：余数=被除数-除数*商

```python
print(9%-4)#-3 9-(-4)*(-3)=-3
print(-9%4)#3 -9-4*(-3)=3
```

赋值运算符，运算顺序从右到左

```python
i=3+4
print(i)#7
```

支持链式赋值

```python
a=b=c=20
print(a,id(a))
print(b,id(b))
print(c,id(c))#输出a,b,c的值相同，且标识（即地址）也相同
```

支持参数赋值

```python
a=20
a+=30#相当于a=a+10
print(a)
a-=10#相当于a=a-20
print(a)
a*=2#相当于a=a*2
print(a)
a/=3#相当于a=a/3
print(a)
a//=2#相当于a=a//2
print(a)
a%=3#相当于a=a%3
print(a)
```

支持系列解包赋值

```python
a,b,c=20,30,40#要求等号左边和右边数量相等
print(a,b,c)
```

交换两个变量的值

```python
a,b=10,20
print('交换之前：',a,b)
a,b=b,a
print('交换之后：',a,b)
```

比较运算符（结果为布尔类型）> < >= <= !=

```python
a,b=10,20
print('a>b吗？',a>b)#输出False
print(a<b)#True
print(a>=b)#False
print(a<=b)#True
print(a==b)#False
print(a!=b)#True
```

比较的是值还是标识呢？比较的是值

比较对象的标识用is

```python
a=10
b=10
print(a==b)#True 说明a和b的值，即value相等
print(a is b)#True 说明a和b的id标识也相等
```

```python
lst1=[11,22,33,44]
lst2=[11,22,33,44]
print(lst1==lst2)#value -->True
print(lst1 is lst2)#id -->False
```

为什么，验证：

```python
print(id(lst1))
print(id(lst2))#发现两者的id不相同
print(lst1 is not lst2)#True
```

## 布尔运算符

**and,or,not,in,not in**

and并且

```python
a,b=1,2
print(a==1 and b==2)#True  True and True-->True
print(a==1 and b<2)#False True and False-->False
print(a!=1 and b!=2)#False False and False-->False
```

or 或者

```python
print(a==1 or b==2)#True
print(a!=1 or b==2)#True
print(a!=1 or b!=2)#False
```

not取反

```python
f=True
f2=False
print(not f)#False
print(not f2)#True
```

in和not in

```python
s='helloworld'
print('w' in s)#True
print('w' not in s)#False
```

## 位运算符

&按位与，只有同为1时结果为1，其它时候为0

```python
print(4&8)#4是00000100,8是00001000，因此输出0
```

|按位或，只要有一个为1结果就是1

```python
print(4|8)#输出12
```

左移位运算符<<，高位溢出低位补0，结果相当于乘2

```python
print(4<<1)#8
print(4<<2)#16
```

右移位运算符>>，高位补0低位溢出，结果相当于除2

```python
print(4>>1)#2
```

****

运算符的优先级：

算数运算符（先算乘除再算加减，有幂运算优先幂运算）>位运算>比较运算（运算结果是True和False）>布尔运算

有括号优先计算括号里的

****

Python一切皆对象，所有的对象都有一个布尔值

以下对象的布尔值是False：False，数值0，None，空字符串，空列表，空元组，空字典，空集合

```python
print(bool(False))
print(bool(0))
print(bool(None))
print(bool(''))
print(bool([]))#空列表
print(bool(list()))#空列表
print(bool(()))#空元组
print(bool(tuple()))#空元组
print(bool({}))#空字典
print(bool(dict()))#空字典
print(bool(set()))#空集合
```

除以上对象的bool值为False，其他对象的bool值都为True

# 语法结构

## 顺序结构

```python
print('程序开始')
print('打开冰箱门')
print('把大象放冰箱')
print('关冰箱门')
print('程序结束')
```

## 分支结构-单分支结构 if

```python
money=1000
s=int(input('请输入取款金额'))
if money>=s:
    money=money-s
    print('取款成功，余额为：',money)
```

## 分支结构-双分支结构 if else

从键盘录入一个整数，判断是奇数还是偶数

```python
num=int(input('请输入一个整数'))
if num%2==0:
    print(num,'是偶数')
else:
    print(num,'是奇数')
```

## 分支结构-多分支结构 多选一执行if elif elif ...else（可省略）

从键盘录入整数作为成绩

90-100 A

80-89 B

70-79 C

60-69 D

0-59 E

小于0或大于100为非法数据

```python
score=int(input('请输入一个成绩：'))
if 90<=score<=100:#冒号不能忘
    print('A级')
elif 80<=score<=89:
    print('B级')
elif 70<=score<=79:
    print('C级')
elif 60<=score<=69:
    print('D级')
elif 0<=score<=59:
    print('E级')
else :
    print('成绩有误')
```

## 分支结构-嵌套if

会员购物金额 >=200 8折

>=100 9折

<100 不打折

非会员 >=200 9折

<200 不打折

```python
answer=input('您是会员吗？y/n')
money=float(input('请输入您的金额：'))
if answer=='y': #会员
    if money>=200:
        print('打8折，付款金额为：',money*0.8)
    elif money>=100:
        print('打9折，付款金额为：',money*0.9)
    else:
        print('不打折，付款金额为：',money)
else:
    if money>=200:
        print('打9.5折，付款金额为：',money*0,95)
    else:
        print('不打折，付款金额为：',money)
```

## 条件表达式

语法结构：x if 判断条件 else y ，如果判断条件为True，表达式的返回值为x，否则为y

从键盘录入两个整数，比较两个整数的大小

已学过的写法：

```python
num_a=int(input('请输入一个整数'))
num_b=int(input('请输入另一个整数'))
if num_a>=num_b :
    print(num_a,'大于等于',num_b)
else :
    print(num_a,'小于',num_b)
```

条件表达式写法：

```python
num_a=int(input('请输入一个整数'))
num_b=int(input('请输入另一个整数'))
print(str(num_a+'大于等于'+_num_b) if num_a>=num_b else str(num_a)+'小于'+str(num_b) )
```

## pass语句

什么都不做，只是一个占位符，用于还没想好代码怎么写的情况

```python
answer=input('您是会员吗？y/n')
if answer=='y':
    pass
else:
    pass
```

## range的三种创建方式

第一种创建方式 range(stop) 创建一个【0，stop）的整数序列，步长为1

```python
r=range(10)
print(r)#range(0, 10)
print(list(r))#[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

第二种创建方式 range(start,stop) 创建一个【start，stop）的整数序列，步长为1

```python
r=range(1,10)
print(list(r))#[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

第三种创建方式  range(start,stop,step) 【start，stop）的整数序列，步长为step

```python
r=range(1,10,2)
print(list(r))#[1, 3, 5, 7, 9]
```

判断指定整数在序列中是否存在，用in和not in

```python
print(10 in r)#False 表示10不在当前序列中
print(10 not in r)#True
```

## 循环结构：while循环 for in循环

while循环

```python
a=1
while a<10:
    print(a)
    a+=1
```

四步循环法

1.初始化变量

2.条件判断

3.条件执行体

4.改变变量

计算0到4的和

```python
sum=0
a=0
while a<5:
    sum+=a
    a+=1
print('和为',sum)
```

计算1到100之间的偶数的和

```python
sum=0
a=1
while a<=100:
    if a%2==0:#改进：if not bool(a%2):
        sum+=a
    a+=1#这里要注意缩进，如果不和if对齐，就会认为是if里的内容
print('和为',sum)
```

## 语法结构：for 自定义对象 in 可迭代对象（字符串，序列）

```python
for item in 'Python':
    print(item)
```

range()产生的整数序列也是可迭代对象

```python
for i in range(10):
    print(i)
```

如果循环体中不需要用到自定义变量，可将变量写成_

```python
for _ in range(5):
    print('人生苦短，我用python')
```

使用for循环计算1到100的偶数和

```python
sum=0
for item in range(1,101):
    if item%2==0:
        sum+=item
print('1到100偶数和',sum)
```

输出100到999之间的水仙花数（153=1*1*1+5*5*5+3*3*3）

```python
for item in range(100,1000):
    ge=item%10
    shi=item//10%10
    bai=item//100%10
    if ge**3+shi**3+bai**3==item:
        print(item)
```

## break语句:用于结束循环结构

从键盘录入密码，最多三次，如果正确就结束循环

```python
for item in range(3):
    pwd=input('请输入你的密码')
    if pwd=='8888':
        print('密码正确')
        break
    else :
        print('密码不正确')
```

也可以用while

```python
while a<3:
    pwd=input('请输入你的密码')
    if pwd=='8888':
        print('密码正确')
        break
    else :
        print('密码不正确')
    a+=1
```

## continue语句：用于结束当前循环，进入下一个循环

输出1-50之间所有5的倍数

```python
for item in range(1,50):
    if item%5==0:
        print(item)
```

使用continue来写

```python
for item in range(1,50):
    if item%5!=0:
        continue
    print(item)
```

## else语句

三种情况：

1. if...else 已讲过
2. while...else 没有碰到break时执行
3. for...else 没有碰到break时执行

```python
for item in range(3):
    pwd=input('请输入你的密码')
    if pwd=='8888':
        print('密码正确')
        break
    else :
        print('密码不正确')
else:#这个else是和for并列的
    print('对不起，三次密码均输入错误')
```

## 嵌套循环

输出一个三行四列的矩形

```python
for i in range(1,4):#行数
    for j in range(1,5):
        print('*',end='\t')#不换行输出:end= 就是不让print输出后换行，print默认输出后会换行
    print()#换行：如果print里面不是空的，就会跳行输出，如果是空的也会，所有就相当于跳行的意思
```

打印一个九九乘法表

```python
for i in range(1,10):
    for j in range(1,i+1):
        print(i,'*',j,'=',i*j, end='\t')
    print()
```

二重循环中的break和continue用于控制本层循环

```python
for i in range(5):
    for j in range(1,11):
        if j%2==0:
            #break
            continue
        print(j,end='\t')
    print()
```

# 列表

创建列表的第一种方式：[]

```python
lst=['hello','world',98]
```

创建列表的第二种方式：运用内置函数list()

```python
lst2=list(['hello','world',98])
```

可以通过索引获取制定对象的值

正向索引：从0到N-1  负向索引：从-N到-1

```python
print(lst)
print(lst[0],lst[-3])#hello hello
```

可以通过index()函数获取指定对象的索引

```python
lst=['hello','world',98,'hello']
print(lst.index('hello'))#0 列表中存在多个相同元素时，只返回第一个元素的索引，所以只返回0
```

也可以在指定的[start,stop)范围内查找

```python
lst=['hello','world',98,'hello']
print(lst.index('hello',1,4))#3
```

列表的切片

```python
lst=[10,20,30,40,50,60,70,80]
print(lst[1:6:1])#start=1,stop=6,step=1 [20, 30, 40, 50, 60]
```

省略step

```python
print(lst[1:6:])#默认步长为1
```

```python
print('原列表',id(lst))
lst2=lst[1:6:1]
print('切的片段',id(lst2))
print(lst[1:6:2])#[20, 40, 60]
```

省略start

```python
print(lst[:6:2])#[10, 30, 50] 默认start为0
```

省略stop

```python
print(lst[1::2])#[20, 40, 60, 80] 默认stop为N
```

step为负数的情况

```python
print(lst[::-1])#[80, 70, 60, 50, 40, 30, 20, 10]，即逆序，第一个元素是原列表第一个元素，最后一个元素为原列表第一个元素
print(lst[7::-1])#[80, 70, 60, 50, 40, 30, 20, 10]
print(lst[6::-2])#[70, 50, 30, 10]
```

## 列表元素的判断和遍历

in和not in

运算符中讲过

```python
print('p' in 'python')#True
```

列表中同样

```python
lst=[10,20,'python','hello']
print(10 not in lst)#False
print(100 not in lst)#True
```

列表元素的遍历

```python
for item in lst:
    print(item)
```

## 列表元素的增加操作

append() 向列表的末尾添加一个元素

```python
lst=[10,20,30]
print('添加元素之前',lst,id(lst))
lst.append(100)
print('添加元素之后',lst,id(lst))#id没有变，说明还是那个lst
```

```python
lst2=['hello','world']
#lst.append(lst2)
#print(lst) 这种还是把lst2作为一个元素
lst.extend(lst2)
print(lst)
```

insert()在列表任意位置添加一个元素

```python
lst.insert(1,90)#在索引为1的位置上添加一个90
print(lst)
```

切片 在列表的任意位置上添加至少一个元素

```python
lst3=[True,False,'hello']
lst[1:]=lst3
print(lst)#[10, True, False, 'hello']
```

## 列表元素的删除操作

```python
remove() 从列表中移除一个元素
lst=[10,20,30,40,50,60,30]
lst.remove(30)#有重复的只移除第一个
print(lst)
```

## pop() 删除一个指定索引上的元素

```python
lst.pop(1)#将索引为1上的元素移除
print(lst)
lst.pop()#不写索引，默认删除列表的最后一个元素
print(lst)
```

## 切片

至少删除一个元素，但在切片后会产生新的列表对象

```python
new_lst=lst[1:3]
print(new_lst)
```

如何用切片不产生新的列表对象而删除原列表中的内容

```python
lst[1:3]=[]
print(lst)
```

## clear() 清除列表中元素

```python
lst.clear()
print(lst)
```

## del 删除列表

```python
del lst
```

print(lst) 程序会报错，因为此时已经没有lst这个列表了

## 列表元素的修改

为指定索引元素赋予一个新值

```python
lst=[10,20,30,40]
lst[2]=100
print(lst)
```

为指定的切片赋予一个新值

```python
lst[1:3]=[300,400,500,600]
print(lst)
```

## 列表元素的排序操作

sort() 默认升序

```python
lst=[20,40,10,98,54]
print('排序前的列表',lst,id(lst))
lst.sort()
print('排序后的列表',lst,id(lst))#标识没有更改，没有产生新的列表对象
```

可通过关键字参数，实现降序

```python
lst.sort(reverse=True) #reverse=True表示降序排序 reverse=False表示升序排序
print(lst)
```

使用内置函数sorted()

```python
new_lst=sorted(lst)
print(lst)
print(new_lst)#实现升序
#使用关键字参数实现降序
desc_lst=sorted(lst,reverse=True)
print(desc_lst)
```

两个方法的区别：sort()方法是对原列表进行操作，而内置函数sorted()是产生了一个新列表

## 列表生成式

```python
lst=[i for i in range(1,10)]
print(lst)#[1, 2, 3, 4, 5, 6, 7, 8, 9]
lst2=[i*i for i in range(1,10)]
print(lst2)#[1, 4, 9, 16, 25, 36, 49, 64, 81]
#若要列表中的元素为2,4,6,8,10
lst3=[i*2 for i in range(1,6)]
print(lst3)#[2, 4, 6, 8, 10]
```

# 字典

Python内置的数据结构之一，与列表一样是可变序列，以键值对方式存储数据，是无序序列

字典的实现原理和查字典类似，根据key查找value所在位置

## 字典的创建

第一种方式，使用{}

```python
scores={'张三':100,'李四':98,'王五':45}
print(scores)
print(type(scores))
```

第二种方式：使用内置函数dict()

```python
student=dict(name='jack',age='20')
print(student)
```

## 字典元素的获取

第一种方式[]

```python
scores={'张三':100,'李四':98,'王五':45}
print(scores['张三'])#100
#print(scores['陈六']) 会报错
```

第二种方式get()

```python
print(scores.get('张三'))#100
print(scores.get('陈六'))#不报错，显示None
print(scores.get('陈六',99))#99是在查找'陈六'所对应的value不存在时所提供的默认值
```

## key的判断

```python
scores={'张三':100,'李四':98,'王五':45}
print('张三' in scores)#True
print('张三' not in scores)#False

del scores['张三']#删除key-value对
print(scores)
scores.clear()#清空字典所有元素
print(scores)

scores={'张三':100,'李四':98,'王五':45}
scores['陈六']=98#新增元素
print(scores)
scores['陈六']=100#修改元素
print(scores)
```

## 获取字典视图的三种方法

keys() 获取所有的key

```python
keys=scores.keys()
print(keys)
print(type(keys))
print(list(keys))#将所有的key组成的视图转成列表
```

value() 获取所有的value

```python
values=scores.values()
print(values)
print(type(values))
print(list(values))
```

items()获取所有的key-value对

```python
items=scores.items()
print(items)
print(list(items))#[('张三', 100), ('李四', 98), ('王五', 45), ('陈六', 100)]小括号叫元组
```

## 字典元素的遍历

```python
for item in scores:
    print(item)#只会输出键不会输出值
    print(item,scores[item],scores.get(item))#可以通过方括号或者内置函数输出值
```

字典的特点：字典中所有元素都是key-value对，key不允许重复，value允许重复

```python
d={'name':'张三','name':'李四'}
print(d)#{'name': '李四'},即key不允许重复
d={'name':'张三','nickname':'张三'}
print(d)#{'name': '张三', 'nickname': '李张三'},即value可以重复
```

字典的元素是无序的，因此不能插入对象

字典占用内存较大，但查找速度较快

## 字典生成式 内置函数zip()

```python
items=['Books','Fruits','Others']
prices=[96,78,85,100,120]
d={item:price for item,price in zip(items,prices)}
print(d)#{'Books': 96, 'Fruits': 78, 'Others': 85} 只生产三个，说明数量不匹配时，以少的为准
d={item.upper():price for item,price in zip(items,prices)}#upper()内置函数，可使字母全部大写
print(d)
```

# 元组

Python内置的数据结构之一，是一个不可变序列，从外观上来讲，元组和列表的区别是元组是()，列表是[]

可变序列：列表，字典

不可变序列：元组，字符串，没有增、删、改的操作

```python
s='hello'
print(id(s))
s=s+'world'
print(s,id(s))#标识变了，说明字符串是不可变序列
```

## 元组的创建方式

第一种方式,使用()

```python
t=('Python','world',98)
print(t)
print(type(t))
```

第二种方式，使用内置函数tuple()

```python
t1=tuple(('Prthon','world',98))
print(t1)
print(type(t1))
```

第一种方式里小括号可以省略

```python
t2='Prthon','world',98
print(t2)
print(type(t2))
```

如果元组只有一个元素，必须加上小括号和逗号，不然就会被判定为其他类型

```python
t=(10,)#逗号不能省
```

## 空元祖

空列表

```python
lst=[]
lst1=list()
```

空字典

```python
d={}
d2=dict()
```

空元组

```python
t4=()
t5=tuple()
```

元组是不可变序列，所以如果元组里的对象是不可变对象则没有任何办法，但如果元组里的对象是可变对象如列表，则可变对象里的数据可以修改

```python
t=(10,[20,30],9)
print(t)
#t[1]=100 会报错，因为元组不允许修改元素，但列表是可以修改的，且修改后内存地址不变：
t[1].append(100)
print(t)
```

元组是可迭代对象，可以使用for in进行遍历

```python
t=('Prthon','world',98)
for item in t:
    print(item)
```

# 集合

Python语言提供的内置数据结构，和列表、字典一样属于可变类型，集合是没有value的字典

## 集合的创建方式

第一种 {}

```python
s={2,3,4,5,5,6,7,7}
print(s)#{2, 3, 4, 5, 6, 7} 表明集合中的元素不能重复
```

第二种 set()

```python
s1=set(range(6))
print(s1,type(s1))
s2=set([2,3,4,5,6])#set()也可将列表类型转成集合类型
print(s2,type(s2))
s3=set((1,2,4,4,5,6))#set()也可将集合类型转成集合类型
print(s3,type(s3))
s4=set('python')#set()也可将字符串类型转成集合类型
print(s4,type(s4))
```

## 定义一个空集合

```python
s6={}#不可以这样，这样是字典类型
print(type(s6))#dict
s7=set()
print(type(s7))#set
```

## 集合元素的判断操作

```python
s={10,20,30,40,50}
print(10 in s)
print(10 not in s)
```

## 集合元素的新增操作

调用add()一次添加一个元素

```python
s.add(80)
print(s)
```

调用update()一次至少添加一个元素

```python
s.update({200,300,400})#可以添加集合
print(s)
s.update([43,24,52])#可以添加列表
s.update((3,2,1))#可以添加元组
print(s)
```

## 集合元素的删除操作

通过remove()一次删除指定元素,如果元素不存在会报错

```python
s.remove(300)
print(s)
```

通过discard()一次删除指定元素,但元素不存在不会报错

```python
s.discard(100)
print(s)
```

通过pop()一次删除任意元素，这种情况指定参数会报错，因为删除的元素是任意的

```python
s.pop()
print(s)
```

通过clear()清空集合

```python
s.clear()
print(s)
```

## 集合的关系

两个集合是否相等，通过==和!=

```python
s={10,20,30,40}
s2={30,40,20,10}
print(s==s2)#True
```

一个集合是否另一个集合的子集，通过issubset()

```python
s1={10,20,30,40,50,60}
s2={10,20,30,40}
s3={10,20,90}
print(s2.issubset(s1))#True
print(s3.issubset(s1))#False
```

一个集合是否是另一个集合的超集/母集，通过issuperset()

```python
print(s1.issuperset(s2))#True
```

两个集合是否含有交集，通过isdisjoint()

```python
print(s2.isdisjoint(s3))#这里注意，有交集为False
s4={12,111}
print(s2.isdisjoint(s4))#没有交集为True
```

## 集合的数学操作

求两个集合的交集

```python
s1={10,20,30,40}
s2={20,30,40,50,60}
print(s1.intersection(s2))#{40, 20, 30}
print(s1 & s2)#{40, 20, 30}
```

求两个集合的并集

```python
print(s1.union(s2))#{40, 10, 50, 20, 60, 30}
print(s1 | s2)#{40, 10, 50, 20, 60, 30}
```

求两个集合的差集

```python
print(s1.difference(s2))#10
print(s1-s2)
print(s2.difference(s1))#{50, 60}
print(s2-s1)#{50, 60}
```

求对称差集（即两个差集的并集）

```python
print(s1.symmetric_difference(s2))#{50, 10, 60}
print(s1^s2)#{50, 10, 60}
```

## 集合的生成式

将列表生成式里的[]改成{}就是集合生成式（没有元组生成式）

复习一下列表生成式

```python
lst=[i*i for i in range(6)]
print(lst)#[0, 1, 4, 9, 16, 25]
```

集合生成式

```python
s={i*i for i in range(6)}
print(s)#{0, 1, 4, 9, 16, 25}
```

## 总结：

列表（list） 可变 可重复 有序  []

元组（tuple）不可变 可重复 有序 ()

字典（dict） 可变 key不可重复value可重复 无序 {key:value}

集合（set） 可变 不可重复 无序 {}

# 字符串

字符串的驻留机制:仅保留一份相同且不可变的字符串的方法，对相同的字符串仅保留一份拷贝，后续创建相同字符串时，不会开辟新空间，而是把该字符串地址赋给新空间

```python
a='Python'
b="Python"
c='''Python'''
print(a,id(a))
print(b,id(b))
print(c,id(c))#标识全部相同
```

在需要字符串拼接时建议使用str类型的join方法而非+

## 字符串的查询操作

index()查找子串substr第一次出现的位置，若子串不存在会报错

rindex()查找子串substr最后一次出现的位置，若子串不存在会报错

find()查找子串substr第一次出现的位置，若子串不存在不会报错，会输出-1

rfind()查找子串substr最后一次出现的位置，若子串不存在不会报错，会输出-1

```python
s='hello,hello'
print(s.index('lo'))#3
print(s.find('lo'))#3
print(s.rindex('lo'))#9
print(s.rfind('lo'))#9
```

## 字符串的大小写转换

upper():把字符串中所有字符都转成大写

lower()：把字符串中所有字符都转成小写

swapcase():把所有大写转成小写，所有小写转成大写

capitalize():把第一个字符转成大写，其余字符转成小写

title():把每个单词的第一个字符转成大写，每个单词的其他字符转成小写

```python
s='hello python'
a=s.upper()#会生成一个新的对象
print(a)#HELLO PYTHON
b=s.lower()#虽然本身就已经是小写了，但还是会生成新的对象
print(b==s)#True
print(b is s)#False
print(s.title())#Hello Python
```

## 字符串内容的对齐

center():居中对齐，第一个参数指定宽度，第二个参数指定填充元符，默认是空格，设置宽度小于实际宽度则返回原字符串

ljust():左对齐，第一个参数指定宽度，第二个参数指定填充元符，默认是空格，设置宽度小于实际宽度则返回原字符串

rjust():右对齐，第一个参数指定宽度，第二个参数指定填充元符，默认是空格，设置宽度小于实际宽度则返回原字符串

zfill():右对齐，左边用0填充，该方法只接受一个参数，用于指定字符串的宽度，设置宽度小于实际宽度则返回原字符串

```python
s='hello,Python'
print(s.center(20,'*'))#****hello,Python****
print(s.ljust(20,'*'))#hello,Python********
print(s.rjust(20,'$'))#$$$$$$$$hello,Python
print(s.zfill(20))#00000000hello,Python
print(s.zfill(10))#hello,Python
print('-8900'.zfill(8))#-0008900 会自动把0添到-后面
```

## 字符串的劈分方法

split():

从字符串左边开始劈分，默认的劈分字符是空格字符串，返回的值都是一个列表

也可以通过参数sep指定劈分符；

也可以通过参数maxsplit指定劈分字符串时的最大劈分次数，经过最大劈分次数后，剩余的子串会单独作为一部分

rsplit()：

从字符串右侧开始劈分，其他和split()一样

```python
s='hello world Python'
lst=s.split()
print(lst)#['hello', 'world', 'Python']
print(s.split('w'))#['hello ', 'orld Python'] 没有w了
s1='hello|world|Python'
print(s1.split(sep='|',maxsplit=1))#['hello', 'world|Python']
print(s1.rsplit(sep='|',maxsplit=1))#['hello|world', 'Python']
```

## 字符串的判断方法

isidentifier() 判断指定的字符串是不是合法的标识符

isspace()判断指定字符串是否全部由空白符组成（回车，换行，水平制表符）

isalpha()判断指定字符串是否全部由字母组成

isdecimal()判断指定字符串是否由十进制数字组成

isnumeric()#判定字符串是否全部由数字组成

isalnum()#判定字符串是否全部由字母和数字组成

```python
s='hello,python'
print(s.isidentifier())#False 逗号不是合法的标识符（字母，数字，下划线）
print('hello'.isidentifier())#True
print('\t'.isspace())#True
print('a b'.isalpha())#False 空格不是字母
print('张三'.isalpha())#True 汉字也是字母
print('12三'.isnumeric())#True 汉字里的一二三既算字母又算数字
```

## 字符串的替换

replace():第一个参数指定被替换的子串，第二个参数指定替换子串的字符串，该方法返回替换后得到的字符串，替换前的字符串不受影响，可以通过第三个参数指定最大替换次数

```python
s='hello,Python'
print(s.replace('Python','Java'))#hello,Java
s1='hello,Python,Python,Python'
print(s1.replace('Python','Java',2))#hello,Java,Java,Python
```

## 字符串的合并

join()：将列表或者元组中的字符串合并成一个新的字符串

```python
lst=['hello','world','Python']
print('|'.join(lst))#hello|world|Python
print(''.join(lst))#helloworldPython
```

## 字符串的比较操作

运算符：> >= < <= == !=

比较规则：首先比较两个字符串中的第一个字符，如果相等则比较下一个字符，依次比较下去直到不相等为止

比较原理：两个字符比较时，比较的是原始值，通过调用内置函数ord()可以获得指定字符的原始值

同时，调用内置函数chr()也可以获得指定原始值对应的字符

```python
print('apple'>'app')#True
print('apple'>'banana')#False
print(ord('a'),ord('b'))#97 98
print(chr(97),chr(98))#a b
```

## 字符串的切片操作

字符串是不可变类型，不可增，删，改，切片操作会产生新的对象

```python
s='hello,Python'
print(s[1:5:1])#ello [start:stop:step]
print(s[::-1])#nohtyP,olleh
s1=s[:5]
s2=s[6:]
print(s1)
print(s2)
s3='!'
newstr=s1+s2+s3
print(newstr)#helloPython!
```

## 格式化字符串

格式化字符串既按一定格式输出的字符串，两种方式：

1. %作占位符 %s字符串 %i或%d整数 %f浮点数
2. {}作占位符
3. f-string

```python
name='张三'
age=20
print('我叫%s,今年%d岁' % (name,age))#我叫张三,今年20岁 中间的%是固定符号
print('我叫{0}，今年{1}岁，我真的叫{0}'.format(name,age))#我叫张三，今年20岁，我真的叫张三 所有的{0}都指向name，所有的{1}都指向age
print(f'我叫{name},今年{age}岁')#我叫张三,今年20岁
```

格式化里的宽度和精度

```python
print('%10d' % 99)#        99 10表示宽度
print('%.3f' % 3.1415926)#3.142 .3表示精度
print('%10.3f' % 3.1415926)#     3.142 同时表示了宽度和精度
```

另类的表示宽度和精度的方式

```python
print('{0:.3}'.format(3.1415926))#3.14  .3表示宽度
print('{0:.3f}'.format(3.1415926))#3.142  .3f才表示精度
print('{0:.3f}'.format(3.1415926))#同时设置宽度和精度
```

## 字符串的编码转换

编码

```python
s='天涯共此时'
print(s.encode(encoding='GBK'))#在GBK这种编码中，一个中文占两个字节
print(s.encode(encoding='UTF-8'))#在UTF-8这种编码中，一个中文占三个字节
```

解码

```python
byte=s.encode(encoding='GBK') #要先编码才能解码，byte代表的就是一个二进制数据（字节类型的数据）
print(byte.decode(encoding='GBK'))#天涯共此时
```

# 函数

函数的定义

```python
def calc(a,b):#a和b称为形式参数，简称形参
    c=a+b
    return c
result=calc(10,20)#10和20称为实际参数，简称实参
print(result)#30
```

函数调用的参数传递：

1. 位置实参，即位置一一对应，如上
2. 关键字实参，即根据形参名称调用实参

```python
res=calc(b=20,a=10)#=左侧的a和b称为关键字参数
print(res)#30
```

```python
def fun(arg1,arg2):
    print('arg1=',arg1)
    print('arg2=',arg2)
    arg1=100
    arg2.append(10)
    print('arg1=',arg1)
    print('arg2=',arg2)
n1=11
n2=[22,33,44]
print(n1,n2)
fun(n1,n2)#n1,n2是实参，arg1,arg2是形参，实参和形参名称可以不一样
print(n1,n2)
```

在函数调用过程中，进行参数的传递

如果是不可变对象，如n1，在函数体修改过程中不会影响实参的值（arg1的修改为100，不会影响到n1的值）

如果是可变对象，如n2，在函数体修改过程中会影响实参的值（arg2的修改为.append()，会影响到n2的值）

```python
def fun(num):
    odd=[] #存奇数
    even=[] #存偶数
    for i in num:
        if i%2:
            odd.append(i)
        else :
            even.append(i)
    return odd,even

lst=[10,29,34,23,44,53,55]
print(fun(lst))#([29, 23, 53, 55], [10, 34, 44])是元组类型
```

函数的返回值：

1. 如果函数没有返回值（函数结束后，不需要向调用处提供数据），return可以省略不写
2. 函数的返回值，如果是一个，直接返回该类型的数据
3. 函数的返回值，如果是多个，返回结果为元组

## 可变参数

可变参数分为个数可变的位置参数和个数可变的关键字参数

个数可变的位置参数：

1. 定义参数时，可能无法事先确定传递的位置参数的个数时，使用可变的位置参数
2. 使用*来定义
3. 结果为一个元组

```python
def fun(*args):
    print(args)
fun(10)  # (10,) 即使一个元素也是元组
fun(10, 20)
```

个数可变的关键字形参：

1. 无法事先确定传递的关键字实参的个数时
2. 使用**定义
3. 结果为一个字典

```python
def fun1(**args):
    print(args)
fun1(a=10)  # {'a': 10}
fun1(a=10, b=20, c=30)  # {'a': 10, 'b': 20, 'c': 30}
'''
个数可变的位置参数和关键字参数都只能有一个，即：
def fun (*args,*a):
    pass
会报错
'''
```

## 变量的作用域

分为局部变量和全局变量

```python
a=100 #全局变量

def calc(x,y):
    return a+x+y

print(a)
print(calc(x=10,y=20))

def calc2(x,y):
    a=200 #局部变量
    return a+x+y #这里的a是全局变量还是局部变量？ 局部变量

print(calc2(x=10,y=20)) #230
print(a) #100
```

在局部，当局部变量和全局变量名称相同时，局部变量优先级更高

```python
def calc3(x,y):
    global s #s是在函数中定义的变量，本该为局部变量，但使用了global关键字申明，就变成了全局变量
    s=300 #申明和赋值，必须分开执行，不能放在一行
    return x+y+s

print(calc3(x=10,y=20)) #330
print(s) #300
```

## 匿名函数lambda

没有名字的函数，只能使用一次，一般在函数的函数体只有一句代码且只有一个返回值时，可以使用匿名函数来简化

```python
def calc(a,b):
    return a+b
print(calc(a=10,b=20))

#匿名函数
s=lambda a,b:a+b #s就跟表示一个匿名函数
print(type(s))
#调用匿名函数
print(s(a=10,b=20))
```

## 递归函数

在一个函数的函数体内部调用该函数本身

一个完整的递归由两部分组成：一部分是递归调用，一部分是递归终止条件

使用递归计算N的阶乘

```python
def fac(n):
    if n==1:
        return 1
    else:
        return n*fac(n-1)
print(fac(5))
```

斐波那契数列

```python
def fac(n):
    if n==1 or n==2:
        return 1
    else:
        return fac(n-1)+fac(n-2)
print(fac(9))#第九位上的数字
for i in range(1,10):
    print(fac(i),end='\t')
print()
```

## 类型转换函数

bool(obj) 获取指定对象obj的布尔值

str(obj) 将指定对象obj转成字符串类型

int(x) 将x转成int类型

float(x) 将x转成float类型

list(sequence) 将序列转成列表类型

tuple(sequence) 将序列转成元组类型

set(sequence) 将序列转成集合类型

## 数学函数

abs(x) 获取x的绝对值

divmod(x,y) 获取x和y的商和余数

max(sequence) 获取sequence的最大值

min(sequence) 获取sequence的最小值

sum(iter) 对可迭代对象进行求和运算

pow(x,y) 获取x的y次幂

round(x,d) 对x进行保留d位小数，结果四舍五入

```python
print('绝对值:',abs(100),abs(-100))
print('商和余数:',divmod(13,4))
print('最大值:',max([10,4,56]))
print('最小值:',max('hello'))
print('求和:',sum([10,34,45]))
print('x的y次幂:',pow(2,3))
print(round(3.1415)) #round函数只有一个参数时，保留整数
print(round(3.9415)) #4
print(round(3.1415,2)) #3.14
print(round(310.1415,-1)) #-1位时对个位四舍五入
```

## 迭代器操作函数

sorted(iter) 对可迭代对象进行重新排序

reversed(sequence) 反转序列生成新的迭代器对象

zip(iter1,iter2) 将iter1和iter2打包成元组并返回一个可迭代的zip对象

enumerate(iter) 根据iter对象创建一个enumerate对象

all(iter) 判断可迭代对象iter中所有元素布尔值是否都为True

any(iter) 判断可迭代对象iter中所有元素布尔值是否都为False

next(iter) 获取迭代器下一个元素

filter(function,iter) 通过指定条件过滤序列并返回一个迭代器对象

map(function,iter) 通过函数function对可迭代对象iter的操作返回一个迭代器对象

```python
lst=[54,56,77,4]
asc_lst=sorted(lst)
desc_lst=sorted(lst,reverse=True)
print('原列表',lst)
print('升序',asc_lst)
print('原降序',desc_lst)
```

```python
new_lst=reversed(lst)
print(type(new_lst)) #<class 'list_reverseiterator'>结果是一个迭代器对象
print(list(new_lst)) #需要转换才能看到列表
```

```python
x=['a','b','c','d']
y=['10','20','30','40','50']
zipobj=zip(x,y)
print(type(zipobj)) #<class 'zip'>
print(list(zipobj)) #[('a', '10'), ('b', '20'), ('c', '30'), ('d', '40')]
#按少的那个来
```

```python
enum=enumerate(y,start=1)
print(type(enum))
print(tuple(enum))
```

剩余函数都大同小异

## 其他一些实用函数

fortmat(value,format_spec) 将value以format_spec格式进行显示

len(s) 获取s的长度或s元素个数

id(obj) 获取对象的内存地址

type(x) 获取x的数据类型

eval(s) 指s这个字符串所表示的Python代码,即去掉字符串的引号

```python
print(format(3.14,'20')) #数值型默认右对齐
print(format('hello','20')) #字符串默认左对齐
print(format('hello','*<20')) #<表示左对齐，*表示填充符，20表示宽度
print(format('hello','*^20')) #居中对齐
print(format(3.1415926,'.2f')) #3.14
print(format(20,'b')) #二进制 10100
```

```python
print(eval('10+30')) #40
print(eval('10>30')) #False
```

# 类和对象

类（class）就是从多个对象中抽取出“像”的属性从而归纳总结出来的一个类别

例如int,float,str这些都是类

我们可以自己定义类，**类名称首字母必须大写**

编写一个Person类型

```python
class Person():
    pass

class Cat():
    pass

class Dog():
    pass
```

现在已经创建类了，但类是模版，相当于图纸，还需要创建自定义类的对象才可以使用，语法：对象名=类名()

```python
per=Person() #per就是Person类型的对象
c=Cat()
d=Dog()
print(type(per)) #<class '__main__.Person'>
```

## 类的具体语法：

1. 类属性 直接定义在类中，方法外的变量
2. 实例属性 定义在__init__方法中，使用self打点的变量
3. 实例方法 定义在类中的函数，而且自带参数self
4. 静态方法 使用装饰器@staticmethod修饰的方法
5. 类方法 使用装饰器@classmethod修饰的方法

```python
class Student:
    school='北京大学' #类属性
    #初始化方法
    def __init__(self,name,age): #name,age是方法的参数，是局部变量，name,age的作用域是整个__init__方法
        self.name=name #=左侧是实例属性，name是局部变量，将局部变量name赋值给实例属性self.name
        self.age=age #实例名称和局部变量名称可以相同，带self.的是实例属性
     #定义在类中的函数我们称为方法，特点是自带参数self
    def show(self):
        print(f'我叫{self.name},今年:{self.age}岁了') #局部变量name用不了，而实例属性self.name在整个类中都能用

    #静态方法中不能调用实例属性，也不能调用实例方法
    @staticmethod
    def sm():
        print('这是一个静态方法')

    #类方法，自带参数cls,也不能调用实例属性，也不能调用实例方法
    @classmethod
    def cm(cls):
        print('这是一个类方法')
#至此，类创建完毕，接下来创建对象

#创建类的对象
stu=Student(name='wkh',age=21)#为什么传两个参数，因为__init__方法中有两个形参，至于self是自带参数不用管，只需要传自己定义的参数就行
#实例属性，使用对象名打点进行调用
print(stu.name,stu.age) #在类当中使用的self.name，但是在创建对象之后就要使用对象名进行调用了
#类属性，直接使用类名，打点调用
print(Student.school)
#实例方法，使用对象名进行打点调用
stu.show()
#类方法，直接使用类名打点调用
Student.cm()
#静态方法，直接使用类名打点调用
Student.sm()
```

示例：编写学生类，并创建三个学生对象

```python
class Student:
    school='北京大学'

    def __init__(self,name,age):
        self.name=name
        self.age=age

    def show(self):
        print(f'我叫{self.name},今年:{self.age}岁了')

stu=Student(name='wkh',age=21)
stu2=Student(name='zcw',age=22)
stu3=Student(name='xyq',age=23)

Student.school='西北工业大学' #给类属性赋值

# 将学生对象存储到列表中
lst=[stu,stu2,stu3] #列表中的元素是Student类型对象
for item in lst: #item是列表中的元素，也是Student类型对象
    item.show() #对象名打点调用实例方法
```

## 动态绑定属性和方法

```python
stu2.gender='男'
print(stu2.name,stu2.age,stu2.gender)

def introduce():
    print('动态绑定成了stu2的方法')
stu2.fun=introduce()#fun就是stu2对象的方法
stu2.fun()
```

## Python中权限控制

单下划线开头：表示受保护，允许类本身和子类进行访问，但实际可以被外部代码访问

双下划线开头：表示私有，只允许定义该属性或方法的类本身访问

首尾双下划线：一般表示特殊的方法

```python
class Student():
    #首尾双下划线
    def __init__(self,name,age,gender):
        self._name=name #self._name受保护的
        self.__age=age #self.__age私有的，只能类本身访问
        self.gender=gender #普通的实例属性，类的内部，外部及子类都可以访问

    def _fun1(self):
        print('受保护，子类及本身可以访问')

    def __fun2(self):
        print('私有的，只有定义的类可以访问')

    def show(self): #普通的实例方法
        self._fun1() #类本身访问受保护的方法
        self.__fun2() #类本身访问私有的方法
        print(self.name) #受保护的实例属性
        print(self.__age) #私有的实例属性

#创建一个学生类对象
stu=Student(name='wkh',age=21,gender='男')
print(stu._name)
#print(stu.__age) 会报错
stu._fun1()
#stu.__fun2()会报错
#私有实例属性和方法真的不能访问吗？也有办法
print(stu._Student__age)
stu._Student__fun2() #对象名+_类名+方法名
#当然，通过对象名+_类名+方法名的方法调用私有属性或方法我们并不推荐，更推荐使用装饰器将方法转成属性
#使用@property将方法转成属性使用，这部分需要的时候再学
```

## 继承的概念

(以下这段转自我看到的解释的很好的帖子)

### Python单继承

```python
class Goat(object):
    def __init__(self):
        self.name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class My_favor(Goat):
    pass
M = My_favor()
M.god()
```

分析： My_favor继承了goat这个类，所以就拥有goat类的所有属性和方法。

### Python多继承

```python
class Goat(object):
    def __init__(self):
        self.name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class Mamba(object):
    def __init__(self):
        self.name = "科比"
        print(f"{self.name}最具职业精神的伟大篮球运动员")
    def god(self):
        print(f"{self.name}是我最喜欢的篮球运动员，他的曼巴精神深深鼓励了我")

# class My_favor(Goat,Mamba):
class My_favor(Mamba,Goat):
    pass
M = My_favor()
M.god()
```

分析：如果一个类继承多个父类的时候，优先继承的是第一个类的属性和方法，也即若想优先继承哪个类，就把哪个类放在最前面即可。

```python
class Goat(object):
    def __init__(self):
        self.name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class Mamba(object):
    def __init__(self):
        self.name = "科比"
        print(f"{self.name}最具职业精神的伟大篮球运动员")
    def god(self):
        print(f"{self.name}是我最喜欢的篮球运动员，他的曼巴精神深深鼓励了我")

# class My_favor(Goat,Mamba):
class king_james(Mamba,Goat):
    def __init__(self):
        self.name = "詹姆斯"
        print(f"{self.name}最全能的伟大的篮球运动员")
    def god(self):
        print(f"{self.name}是历史上唯一一个为三个城市带来总冠军且获得FMVP的球员")
M = king_james()
M.god()
```

分析：如果子类和父类拥有同名属性和方法，子类创建对象调用属性和方法的时候，调用到的是子类里面的同名属性和方法。

### 使用super的继承

上述方法在继承父类的属性和方法时，若父类的类名修改的话，子类在继承的时候也要相应的进行修改，同时代码量大，复杂。接下里介绍super方法继承。

继承一个类

```python
class Goat(object):
    def __init__(self):
        self.name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class Mamba(object):
    def __init__(self):
        self.name = "科比"
        print(f"{self.name}最具职业精神的伟大篮球运动员")
    def god_mamba(self):
        print(f"{self.name}是我最喜欢的篮球运动员，他的曼巴精神深深鼓励了我")

# class My_favor(Goat,Mamba):
class king_james(Mamba):
    def __init__(self):
        self.name = "詹姆斯"
        print(f"{self.name}最全能的伟大的篮球运动员")
    def god(self):
        print(f"{self.name}是历史上唯一一个为三个城市带来总冠军且获得FMVP的球员")
        super().__init__()
        super().god_mamba()

# print(king_james.__mro__)
M = king_james()
M.god()
```

分析：使用super来继承Mamba这个类。

继承多个类

```python
class Goat(object):
    def __init__(self):
        self.name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god_goat(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class Mamba(Goat):
    def __init__(self):
        self.name = "科比"
        print(f"{self.name}最具职业精神的伟大篮球运动员")
        super().__init__()
        super().god_goat()
    def god_mamba(self):
        print(f"{self.name}是我最喜欢的篮球运动员，他的曼巴精神深深鼓励了我")

# class My_favor(Goat,Mamba):
class king_james(Mamba):
    def __init__(self):
        self.name = "詹姆斯"
        print(f"{self.name}最全能的伟大的篮球运动员")
    def god(self):
        print(f"{self.name}是历史上唯一一个为三个城市带来总冠军且获得FMVP的球员")
        super().__init__()
        super().god_mamba()

# print(king_james.__mro__)
M = king_james()
M.god()
```

分析：同时调用多级类。

定义私有属性和方法

```python
class Goat(object):
    def __init__(self):
        self.__name = "乔丹"
        print(f"{self.name}将篮球推向了全世界")
    def god_goat(self):
        print(f"{self.name}是历史上最伟大的篮球运动员,篮球之神")

class Mamba(Goat):
    def __init__(self):
        self.name = "科比"
        print(f"{self.name}最具职业精神的伟大篮球运动员")
        super().__init__()
        super().god_goat()
    def god_mamba(self):
        print(f"{self.name}是我最喜欢的篮球运动员，他的曼巴精神深深鼓励了我")

# class My_favor(Goat,Mamba):
class king_james(Mamba):
    def __init__(self):
        self.name = "詹姆斯"
        print(f"{self.name}最全能的伟大的篮球运动员")
    def god(self):
        print(f"{self.name}是历史上唯一一个为三个城市带来总冠军且获得FMVP的球员")
        super().__init__()
        super().god_mamba()

# print(king_james.__mro__)
M = king_james()
M.god()
```

分析：设置私有属性或者函数，在属性或者函数钱加”__“即可。由于上面中self.__name = "乔丹"，设置了私有属性，所以只能用Mamba的初始化来进行执行。

# 后记

学到这里Python基础就已经入门。
