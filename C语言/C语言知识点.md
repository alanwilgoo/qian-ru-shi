1.负数的补码是原码符号位（二进制最左边第一位）不变，其他位取反，末尾加1  
正数以原码方式放在内存中

2.八进制：一般0开头，比如0144–代码里控制符号可以用#o来显示不同进制前缀  
十六进制：一般0x，代码里可以用#x显示出进制前缀

3.浮点数想保留小数点后几位就在%后添加.n+符号控制符

4.char本质上是单字节整型

5.字符串有结束字符，所以至少也占1个字节

6.scanf为键盘的标准输入，用来从键盘输入一个单词，放入指定内存地址或者数组中（数组的话不能用取址符）如果想输入字符串（能空格的）只能用afghat

注意scanf不同printf，输入时末尾不用加换行符

7.注意逻辑与和逻辑或，它们中的左右两个表达式不一定都会被执行，执行完左边可能会被一锤定音。

8.异或相同为0，不同为1

9.int a[2][3];

// 代码释义：  
// 1, a[2]是数组的定义，表示该数组拥有两个元素  
// 2, int [3]是元素的类型，表示该数组元素是一个具有三个元素的整型数组  
注意：数组元素类型放在数组定义两边（美观）

10.onst 的核心作用是“承诺”该变量是只读的（不可被修改），一旦初始化后，其值就不能再被改变。在 C/C++ 中（底层视角）const 修饰的是内存的读写权限。

基本类型：const int a = 5; 变量 a 存储在内存中，编译器会检查并阻止你写 a = 10;。一旦试图修改，编译报错。

11.数组名会被转化为指向该数组首元素的指针。

# **结构体内容**

1.首先数组在定义初始化之后就不能放在等号左边赋值了。  
所以如果结构体有成员是数组，只能通过memcpy（）来内存拷贝等方式给成员赋值。（注意字符串只能用来初始化字符数组，不能用来后续赋值）如果把成员定义为字符数组指针就可以赋值了。

结构体尺寸：  
2.结构体元素的m值：注意结构体的m值控制始末地址位置，m值控制起始地址但不控制成员实际大小（实际大小看成员种类）。long8的m值是4哦（越省成本越好）  
![image.png](http://edu.yueqian.com.cn/group1/M00/01/BB/rBJlJmpgZBuAEMSDAADqq_iB8xc738.png)

加入packed取消对齐

（易错）3.定义完结构体后，主函数使用时：为结构体的成员进行初始化一般按照图上方法进行：  
![image.png](http://edu.yueqian.com.cn/group1/M00/01/BC/rBJlJmpgnqKAeP7KAABYUkNX0zg053.png)  
切不可和定义一样写。

##### 头文件

1.#ifndef _SOME_HEADER_H_  
#define _SOME_HEADER_H_  
… …  
#endif 有什么作用?

答：准确来说是"头文件卫士"利用条件编译，防止同一个头文件被多次 #include 时内容被重复编译，避免重复定义错误。第一次包含时定义宏，第二次包含时宏已存在，头文件内容被跳过。

#### 顺序表

![image.png](http://edu.yueqian.com.cn/group1/M00/01/BD/rBJlJmpivHmASYolAAHypARcOmk726.png)

##### 数组与指针

![image.png](http://edu.yueqian.com.cn/group1/M00/01/BD/rBJlJmpjRbmASiydAAZjZOhl3WA648.png)

计算元素的个数：  
![image.png](http://edu.yueqian.com.cn/group1/M00/01/BD/rBJlJmpjRqqAYBysAASx78VYSl8206.png)

指针是存储变量地址的变量，解引用通过指针修改所指向的变量。

偏移量和下标的关系：  
![image.png](http://edu.yueqian.com.cn/group1/M00/01/BD/rBJlJmpjTbiAXq2KAARyV0g_WVM903.png)

![image.png](http://edu.yueqian.com.cn/group1/M00/01/BD/rBJlJmpjToqAL4o3AAOYiVBM0es657.png)

易错点：  
![image.png](http://edu.yueqian.com.cn/group1/M00/01/BE/rBJlJmpjVLWARzFaAANr870dN1M016.png)

![image.png](http://edu.yueqian.com.cn/group1/M00/01/BE/rBJlJmpjVU2ASeuVAAF2-he8aUM911.png)

![image.png](http://edu.yueqian.com.cn/group1/M00/01/BE/rBJlJmpjVi6AB_WrAAIQJlf7U_E478.png)

否则会下标越界，非法访问内存。  
数组名师是常量地址。

所以数组名代表指向首元素，数组名偏移得看元素类型，元素是int，就偏移一个int。元素是一个数组，就偏移一个数组。但对于指针来说自己只是在往后偏移一个元素。