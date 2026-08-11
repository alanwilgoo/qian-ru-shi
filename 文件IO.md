### **系统IO与标准IO**

对文件的操作，基本上就是输入输出，因此也一般称为IO接口。在操作系统的层面上，这一组专门针对文件的IO接口就被称为系统IO；在标准库的层面上，这一组专门针对文件的IO接口就被称为标准IO，如下图所示：

![](http://edu.yueqian.com.cn/group1/M00/00/E0/wKgA3V_RnN-AFNKGAACxljoLd0Y122.png)  

- 系统IO：是众多系统调用当中专用于文件操作的一部分接口。
- 标准IO：是众多标准函数当中专用于文件操作的一部分接口。

从图中还能看到，标准IO实际上是对系统IO的封装，系统IO是更接近底层的接口。如果把系统IO比喻为菜市场，提供各式肉蛋菜果蔬，那么标准IO就是对这些基本原来的进一步封装，是品类和功能更加丰富的各类酒庄饭店。

### **如何选择系统IO与标准IO**

- 系统IO
    
    - 由操作系统直接提供的函数接口，特点是简洁，功能单一
    - 没有提供缓冲区，因此对海量数据的操作效率较低
    - 套接字Socket、设备文件的访问只能使用系统IO
- 标准IO
    
    - 由标准C库提供的函数接口，特点是功能丰富
    - 有提供缓冲区，因此对海量数据的操作效率高
    - 编程开发中尽量选择标准IO，但许多场合只能用系统IO

总的来讲，这两组函数接口在实际编程开发中都经常会用到，都是基本开发技能。

###### 1.open函数：
- open函数有两个版本，一个有两个参数，一个有三个参数。
- 当打开一个已存在的文件时，指定两个参数即可。
- 当创建一个新文件时，需要用第三个参数指定新文件的权限，否则新文件的权限是随机值。
- 模式flags，可以使用位或的方式，来同时指定多个模式。
- 模式flags中，O_NOCTTY主要用在后台精灵进程，阻止这些精灵进程拥有控制终端。

  - 例代码：
    

```
int main(void)
{
    int fd;

    // 以下三种打开方式，都要求文件已存在，否则失败返回
    fd = open("a.txt", O_RDWR);   // 以可读可写方式打开
    fd = open("a.txt", O_RDONLY); // 以只读方式打开
    fd = open("a.txt", O_WRONLY); // 以只写方式打开

    // 1. 如果文件不存在，则创建该文件，并设置其权限为0644
    // 2. 如果文件已存在，则失败返回
    fd = open("a.txt", O_RDWR|O_CREAT|O_EXCL, 0644);   // 以可读可写方式打开
    fd = open("a.txt", O_RDONLY|O_CREAT|O_EXCL, 0644); // 以只读方式打开
    fd = open("a.txt", O_WRONLY|O_CREAT|O_EXCL, 0644); // 以只写方式打开

    // 1. 如果文件不存在，则创建该文件，并设置其权限为0644
    // 2. 如果文件已存在，则清空该文件的原有内容
    fd = open("a.txt", O_RDWR|O_CREAT|O_TRUNC, 0644);   // 以可读可写方式打开
    fd = open("a.txt", O_RDONLY|O_CREAT|O_TRUNC, 0644); // 以只读方式打开
    fd = open("a.txt", O_WRONLY|O_CREAT|O_TRUNC, 0644); // 以只写方式打开

    // 以下三种打开方式，都要求文件已存在，否则失败返回
    fd = open("a.txt", O_RDWR|O_APPEND, 0644);   // 以可读可写方式追加文件内容
    fd = open("a.txt", O_WRONLY|O_APPEND, 0644); // 以只写方式追加文件内容
}
```

- 关闭文件：

![](http://edu.yueqian.com.cn/group1/M00/00/E0/wKgA3V_RwtuAAaXKAAA2MhutCaI370.png)  

- 关键点
    - 当不再使用一个文件时，应当关闭该文件，防止系统资源浪费。
    - 对同一文件重复执行关闭操作会失败返回，不会有其他副作用。

  

### **2. 标准库函数的错误处理**

在所有的库函数中，如果调用过程出错了，那么该函数除了会返回一个特定的数据来告诉用户调用失效之外，还都会去修改一个大家共同的全局错误码errno，我们可以通过这个错误码，来进一步确认究竟是什么错误。

例如：

```
#include <stdio.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <sys/types.h>

#include <errno.h> // 全局错误码声明所在的头文件

int main()
{
    int fd = open("a.txt", O_RDWR);
    if(fd == -1)
    {
        // 以下两条语句效果完全一致：输出函数出错的原因
        perror("打开a.txt失败");
        printf("打开a.txt失败:%s\n", strerror(errno));
    }
    return 0;
}
```

- 关键点：
    - 如果库函数、系统调用出错了，全局错误码 errno 会随之改变
    - 如果库函数、系统调用没出错，全局错误码 errno 不会改变
    - 一个库函数、系统调用出错后，若未及时处理错误码，则错误码可能会被随后的其他函数修改

提取错误码信息的两种办法：

```
// 1. 使用perror()，直接输出用户信息和错误信息：
if(open("a.txt", O_RDWR) == -1)
{
    perror("打开a.txt失败");
}

// 2. 使用strerror()，返回错误信息交给用户自行处理：
if(open("a.txt", O_RDWR) == -1)
{
    printf("打开a.txt失败:%s\n", strerror(errno));
}
```

  
  ### **3. 文件描述符本质**

函数 open() 的返回值，是一个整型 int 数据。这个整型数据，实际上是内核中的一个称为 fd_array 的数组的下标：

![](http://edu.yueqian.com.cn/group1/M00/03/27/wKgP3GB8Hn2AOXImAACKHiyxD7w833.png?token=null&ts=null)  
文件描述符的本质内涵

  

打开文件时，内核产生一个指向 file{} 的指针，并将该指针放入一个位于 file_struct{} 中的数组 fd_array[] 中，而该指针所在数组的下标，就被 open() 返回给用户，用户把这个数组下标称为文件描述符，如上图所示。

结论：

- 文件描述符从0开始，每打开一个文件，就产生一个新的文件描述符。
- 可以重复打开同一个文件，每次打开文件都会使内核产生系列结构体，并得到不同的文件描述符
- 由于系统在每个进程开始运行时，都默认打开了一次键盘、两次屏幕，因此0、1、2描述符分别代表标准输入、标准输出和标准出错三个文件（两个硬件）。

![](http://edu.yueqian.com.cn/group1/M00/03/27/wKgP3GB8Hn2AXZWxAAB6Dd0I3iU028.png?token=null&ts=null)  
默认打开的输入输出文件
  
  
  
  ### **文件的读写操作**

![](http://edu.yueqian.com.cn/group1/M00/00/E0/wKgA3V_R5BeAFT4ZAACLDZjTbZg424.png)


  - 关键点：
    
    - 参数count是读写字节数的愿望值，实际读写成功的字节数由返回值决定。
    - 读取普通文件时，如果当读到了文件尾，read()会返回0。
    - 读取管道文件时，如果管道中没有数据，read()默认会阻塞。
- 读取文件内容：
    

```
// 1. 将文件 a.txt 中的内容读出来，并显示到屏幕上
int fd = open("a.txt", O_RDWR);

char buf[100];
int n;
while(1)
{
    bzero(buf, 100);
    n = read(fd, buf, 100); // 每次最多读取100个字节

    if(n == 0) // 读完退出
        break;

    printf("%s", buf);
}
close(fd)
```



### **文件的读写位置**

当我们对文件进行读写操作时，系统会为我们记录操作的位置，以便于下次继续进行读写操作的时候，从适当的地方开始。

有几点需要注意：

- 每当open一个文件，系统就会维护一套包括文件操作位置在内的相关信息。
- 对同一个文件描述符进行读写操作时，使用的同一套文件信息，影响的是同一个位置参数。
- 对同一个文件的多个不同的文件描述符进行读写操作时，使用的是不同的文件信息，影响的是不同的位置参数，彼此互相之间独立，这往往会导致文件信息的错乱。

  ## 核心概念总结

1. **偏移量独立**：每个 `open` 返回的 fd 有自己的文件偏移量
    
2. **数据共享**：多个 fd 操作的是同一个文件，写入会互相影响文件内容
    
3. **偏移量不共享**：一个 fd 的 `read/write` 不会改变另一个 fd 的偏移量
  
  所以：**偏移量跟着 fd 走，文件内容是共享的**
  
  
  示例代码：多次打开得到不同的文件描述符，各自读写操作位置独立

```
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>
#include <strings.h>

int main(int argc, char **argv) // ./main a.txt
{
    // 假设文件中的原始内容是：abcdefghijk
    int fd1 = open(argv[1], O_RDWR);
    int fd2 = open(argv[1], O_RDWR);
    char buf[100];

    // 读出1个字节，读完后读写操作位置是第2个字节
    // 此时影响的是fd1，对fd2没有影响
    bzero(buf, 100);
    read(fd1, buf, 1);
    printf("%s\n", buf); // 输出a

    // 写入2个字节，读完后读写操作位置是第3个字节
    // 与上述读操作没有关系
    bzero(buf, 100);
    write(fd2, "xy", 2); // ab被覆盖，原文件变成xycdefghijk

    // 读出3个字节，读完后读写操作位置是第5个字节
    // 此时影响的是fd1，对fd2没有影响
    bzero(buf, 100);
    read(fd1, buf, 3);
    printf("%s\n", buf); // 输出ycd

    return 0;
}
```
  
  
  
  
  ### **读写位置的设置**

对文件进行常规的读写操作的时候，系统会自动调整读写位置，以便于让我们顺利地顺序读写文件，但如果有需要，文件的读写位置是可以任意调整的，调整函数接口如下：

![](http://edu.yueqian.com.cn/group1/M00/03/27/wKgP3GB8ILGAFZCxAAAuHR0cFD8872.png?token=null&ts=null)  

- 关键点：
    1. lseek函数可以将文件位置调整到任意的位置，可以是已有数据的地方，也可以是未有数据的地方，假设调整到文件末尾之后的某个地方，那么文件将会形成所谓“空洞”。
    2. lseek函数只能对普通文件调整文件位置，不能对管道文件调整。
    3. lseek函数的返回值是调整后的文件位置距离文件开头的偏移量，单位是字节。
  
  示例代码：

```
int main(void)
{
    // 假设文件 a.txt 只有一行
    // 内容如下：
    //
    // 1234567890abcde 
    //

    int fd = open("a.txt", O_RDWR);

    // 读取前面10个阿拉伯数字:
    char buf[10];
    read(fd, buf, 10);

    // 将文件位置调整到'c'
    lseek(fd, 2, SEEK_CUR);

    // 将文件位置调整到'1'
    lseek(fd, 0, SEEK_SET);

    // 将文件位置调整到'a'
    lseek(fd, -5, SEEK_END);
}
```
  
  

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

- 写入文件内容：

```
// 2. 将键盘输入的内容，写入文件 a.txt
int fd = open("a.txt", O_RDWR);

char buf[100];
bzero(buf, 100);

// 从键盘输入数据
fgets(buf, 100, stdin);

// 将输入的数据写入文件
write(fd, buf, strlen(buf));
close(fd);
```
  
  
  
  

  
  
  
  
  
  
  
  
  
  
  

  
  
  
  
  
  
  
  
  
  
  

以上代码输出的结果是完全一样的：

```
gec@ubuntu:~$ ./a.out
打开a.txt失败: No such file or directory
打开a.txt失败: No such file or directory
```

  
  
  

  
  
  

  
  
  

一般而言，perror()用起来更加方便，但有时候需要使用strerror()来输出一些更加灵活的信息，比如以上代码，如果打开文件的名字不是固定a.txt，而是取决于外部参数，那么就应该写错：

```
if(open("a.txt", O_RDWR) == -1)
{
    printf("打开%s失败:%s\n", argv[1], strerror(errno));
}
```

  
  
  
  

  
  
  
  

  
  
  
  

以上代码输出的结果是：

```
gec@ubuntu:~$ ./a.out a.txt
打开a.txt失败: No such file or directory
gec@ubuntu:~$ 
gec@ubuntu:~$ ./a.out ELF-V4.tar.gz
打开ELF-V4.tar.gz失败: No such file or directory
gec@ubuntu:~$ 
```