# 搭建一个在linux平台上的文本编辑器

参考：https://viewsourcecode.org/snaptoken/kilo/index.html

本文为本人在代码过程中遇到的疑惑点，用于将来复盘方便

## step20

用这个宏来讲 a（0110 0001） 转换成 ctrl a（0000 0001） 的ascii码值

```c
#define CTRL_KEY(k) ((k) & 0x1f)
//CTRL_KEY 宏按位将 k AND 00011111。它将字符的上 3 位设置为0

if (c == CTRL_KEY('q')) break;
```

![image-20240408223740080](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240408223740080.png)

## step27

如何让c函数返回多个值？例如这个获取window行数和列数

-- 向该c函数传递多个指针，在函数内改变指针的内容（解引用后的），相当于向原函数返回多个值

-- 还可以设置该c函数的int返回值，用来标识该函数是否成功



## step46

遇到问题：

利用wsad键可以实现光标的移动，但是用箭头键却不行

发现问题：

通过在代码中插入printf语句进行debug，发现此时的editorReadKey在输入箭头时返回值不对

定位问题：

![image-20240411120842462](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240411120842462.png)

if里的括号加错了，这里的语法是 read一个 0 字节，seq中没有对应的字符，故editorReadKey返回值不对，不会进入后面的switchcase，而是return \x1b

解决问题：

把一个右括号换到 ！前面

## step 50 -- 53

代码已抄（复制粘贴），键盘上没有page up page down灯等键，不知道管不管用









