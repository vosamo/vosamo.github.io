>在Linux shell中经常用到数组，在Linux中，数组的表示方法为：
array=(val1 val2 val3 .....)元素默认以空格符为间隔，下标从0开始。array[1]=val2

## 下面用一个简单的脚本说明：

```
#!/bin/bash

　　#定义数组

　　A=(a b c def)

　　#把数组按字符串显示输出

　　echo ${A[@]}或者echo ${A[*]}

　　#屏幕显示：a b c def

　　#数组的长度表示${#A[*]}

　　len=${#A[*]}

　　echo ${#A[*]}

　　#屏幕显示：4

　　#改变数组元素的值

　　A[3]='vivian'

　　echo ${A[*]}

　　#屏幕显示：a b c vivian

　　#循环输出数组元素

　　i=0

　　while [ $i -lt $len ]

　　do

　　echo ${A[$i]}

　　let i++

　　done
```

**或者：**


```
for i in ${A[@]}             //不可以是for i in $A

do
   echo $i
done
```


## 将一个字符串赋给一个数组默认是以空格分隔的

比如：

```
str="hello world"
array=($str)
```

则array[0]="hello",array[1]="world"

如果希望以其他字符间隔，可以使用IFS="间隔符"

如IFS=","
```
str="hello world,ni hao,how are you"

array=($str)
```
则：
array[0]="hello world",array[1]="ni hao",array[2]="how are you"