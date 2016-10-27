---
layout: post
title: python批量重命名文件
categories:
- 编程
tags:
- python
---

## os.walk

在递归遍历某文件夹及其子文件夹时，使用`os.walk`特别方便。

`os.walk`的输出结果是这样的，是一个生成器对象：

```
(  /dir,               [dir1,dir2......],                  [ file1,file2,................ ]  )，

(  /dir/dir1 ,       [] ,                                    [file10,file12......]       ) ,  

(  /dir/dir2 ,       []                                      [file20,...........]         ) ,
```

生成器每次返回的是一个三元元组(root, dirs, files)，其中root表示路径，dirs表示路径下的文件夹，files表示路径下的文件。具体的执行过程如下：

1，先从根目录进行遍历，读取跟目录的文件夹和文件。

2，以根目录第一个子目录为新的根目录，读取其文件夹和文件。

3，再以2中的第一个子文件夹为根目录，读取文件夹和文件。（这个应该是属于树结构里面的自上而下深度遍历算法）

4，读取1步骤里面其他子目录的文件夹和文件。

## os.rename

上面我们通过os.walk可以获得某个文件夹下及其子文件夹下的所有文件夹和文件，这样我们就可以调用os.rename(old, new)来给文件重命名了。好了，现在说一下我们的具体需求：我想把为知笔记的文件导入到有道云笔记上，但是在为知笔记上写的markdown笔记文件导出为文本文件时，发现后缀名变成了`.md.txt`，也就是多了后缀名，这样直接导入由道云笔记是不行的，一个个修改后缀名又太麻烦，下面我们使用`os.walk`和`os.rename`这两个函数来实现批量文件的后缀名修改。将下面这段代码保存为`rename.py`，然后放入你想要批量修改文件后缀名的文件夹下，假设里面存放的文件都是以`.md.txt`或`.txt`为后缀名的，然后运行`python rename.py`就可以了。

{% highlight python %}
#!/usr/bin/env python
# coding=utf-8
import os

for root, dirs, files in os.walk(os.getcwd()):
    for file in files:
        file_path = os.path.join(root, file)
        print file_path
        if file_path.endswith('.md.txt'):
            new_file_name = file_path[:-4]
            print new_file_name
            os.rename(file_path, new_file_name)
        elif file_path.endswith('.txt'):
            new_file_name = file_path[:-3] + 'md'
            os.rename(file_path, new_file_name)
{% endhighlight %}

