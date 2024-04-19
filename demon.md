# Linux系统编程学习笔记<br>
自用学习，笔记复习。<br>
学习时间：2024年4月20日<br>
学习来源：[Linux系统编程（李慧琴）](https://www.bilibili.com/video/BV1yJ411S7r6/?vd_source=a8863c3524d97c332bd90fc50541d8cb)<br>
参考资料：Unix环境高级编程，Linux系统编程<br>
## 1 前言
### 1.1 环境
平台：Centos7.6

工具：vim，gcc，make
### 1.2 GCC
Linux系统下的GCC（GNU Compiler Collection）是GNU推出的功能强大、性能优越的多平台编译器，是GNU的代表作品之一。gcc是可以在多种硬体平台上编译出可执行程序的超级编译器，其执行效率与一般的编译器相比平均效率要高20%~30%。

GCC编译器能将C、C++语言源程序、汇程式化序和目标程序编译、链接成可执行文件，如果没有给出可执行文件的名字，gcc将生成一个名为a.out的文件。

GCC编译器编译C源文件为可执行文件的步骤：

- C源文件—->预处理.i—->编译.s（生成汇编文件）—->汇编.o/.obj（生成目标文件）—->链接.out（生成可执行文件）—->可执行文件

> gcc命令参数（选项）

|参数|含义|示例|
|----|---|---|
|-c|对文件进行预处理、编译和汇编，生成obj文件|gcc -c hello.c|
|-S|只进行预处理和编译，生成汇编代码|gcc -S hello.c|
|-E|只进行预处理，这个选项不生成文件，可以使用重定向或者-o选项使其生成一个文件|gcc -E hello.c > hello.i或者gcc -E hello.c -o hello.i|
|-o|指定目标的名称，默认为a.out|gcc -o hello hello.c|

>过程演示<br>

例如源代码<font face="黑体" color=red size=4>main.c：</font>
``` c
#include<stdio.h>

int main(void) {
    printf("Hello World!\n");
    return 0; 
}
``` 
- 预编译：这个过程处理宏定义和include，去除注释，不会对语法进行检查。可以看到预编译后，源文件的代码从6行扩展了很多行，生成main.i


工具：vim，gcc，make
### 1.2 GCC
Linux系统下的GCC（GNU Compiler Collection）是GNU推出的功能强大、性能优越的多平台编译器，是GNU的代表作品之一。gcc是可以在多种硬体平台上编译出可执行程序的超级编译器，其执行效率与一般的编译器相比平均效率要高20%~30%。

GCC编译器能将C、C++语言源程序、汇程式化序和目标程序编译、链接成可执行文件，如果没有给出可执行文件的名字，gcc将生成一个名为a.out的文件。

GCC编译器编译C源文件为可执行文件的步骤：

- C源文件—->预处理.i—->编译.s（生成汇编文件）—->汇编.o/.obj（生成目标文件）—->链接.out（生成可执行文件）—->可执行文件

> gcc命令参数（选项）

|参数|含义|示例|
|----|---|---|
|-c|对文件进行预处理、编译和汇编，生成obj文件|gcc -c hello.c|
|-S|只进行预处理和编译，生成汇编代码|gcc -S hello.c|
|-E|只进行预处理，这个选项不生成文件，可以使用重定向或者-o选项使其生成一个文件|gcc -E hello.c > hello.i或者gcc -E hello.c -o hello.i|
|-o|指定目标的名称，默认为a.out|gcc -o hello hello.c|

>过程演示<br>

例如源代码<font face="黑体" color=red size=4>main.c：</font>
``` c
#include<stdio.h>

int main(void) {
    printf("Hello World!\n");
    return 0; 
}
``` 
- 预编译：这个过程处理宏定义和include，去除注释，不会对语法进行检查。可以看到预编译后，源文件的代码从6行扩展了很多行，生成main.i

```BASH
gcc -E main.c > main.i # 或者 gcc -E main.c -o main.i
```

``` c
// ...
// 只展示了一部分
extern int ftrylockfile (FILE *__stream) __attribute__ ((__nothrow__ , __leaf__)) ;


extern void funlockfile (FILE *__stream) __attribute__ ((__nothrow__ , __leaf__));
# 943 "/usr/include/stdio.h" 3 4

# 2 "main.c" 2
int main(void) {
    printf("Hello World!\n");
    return 0;
}
```
- 编译：这个阶段，检查语法，生成汇编代码main.s

```BASH
gcc -S main.c -o main.s
```
<font size="3" color=red>main.s</font>的内容：

```PLAINTEXT
        .file   "main.c"
        .section        .rodata
.LC0:
        .string "Hello World!"
        .text
        .globl  main
        .type   main, @function
main:
.LFB0:
        .cfi_startproc
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset 6, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register 6
        movl    $.LC0, %edi
        call    puts
        movl    $0, %eax
        popq    %rbp
        .cfi_def_cfa 7, 8
        ret
        .cfi_endproc
.LFE0:
        .size   main, .-main
        .ident  "GCC: (GNU) 4.8.5 20150623 (Red Hat 4.8.5-44)"
        .section        .note.GNU-stack,"",@progbits
```
- 汇编：这个阶段，生成目标代码<font size="3" color=red>main.o</font>
```BASH
gcc -c main.s -o main.o
```
+ 链接：生成可执行代码<font size="3" color=red>main</font>。
    * 链接分为两种，一种是静态链接，另外一种是动态链接。
    + 使用静态链接的好处是，依赖的动态链接库较少，对动态链接库的版本不会很敏感，具有较好的兼容性；缺点是生成的程序比较大。
    * 使用动态链接的好处是，生成的程序比较小，占用较少的内存。
```BASH
    gcc main.o -o main
```
- 运行
```BASH
    [root@HongyiZeng c]# ./main
    Hello World!
```
一步到位：
```BASH
    gcc main.c -o
```
此时会默认生成一个名为<font size="3" color=red>a.out</font>的可执行文件。

```BASH
    gcc main.c -o myHello
```
此时会生成一个名为<font size="3" color=red>myHello</font>的可执行文件

### 1.3 make
make命令是GNU工程化中的一个编译工具。make是依赖于Makefile来编译多个源文件的工具。在Makefile里同样是用gcc（或者别的编译器）来编译程序。<br>

可以使用以下命令直接生成可执行文件：
```BASH
    make main
```
### 1.4 makefile
1.4.1 使用gcc的例子
创建文件
BASH
touch main.c tool1.c tool1.h tool2.c tool2.h
可以使用vim * -p打开当前目录下的所有文件。-p是打开多个文件的选项。

在命令模式下，gt切换到下一个标签页，gT切换到上一个标签页。

退出多个标签时，可加上a，例如:qa或者:wqa等

tool1.h和tool1.c
C
1
2
3
4
#ifndef __TOOL1_H__
#define __TOOL1_H__
    void mytool1();
#endif
C
1
2
3
4
5
#include <stdio.h>
#include "tool1.h" 
void mytool1() {
	printf("tool1 print...\n");
}
tool2.h和tool2.c
C
1
2
3
4
#ifndef __TOOL2_H__
#define __TOOL2_H__
    void mytool2();
#endif
C
1
2
3
4
5
6
#include <stdio.h>
#include "tool2.h"

void mytool2() {
    printf("tool2 print...\n");
}
main.c
C
1
2
3
4
5
6
7
8
#include "tool1.h"
#include "tool2.h"

int main(void) {
    mytool1();
    mytool2();
    return 0;
}
对所有文件进行编译：

BASH
1
gcc *.c
执行a.out

BASH
1
2
3
[root@HongyiZeng makefile]# ./a.out 
tool1 print...
tool2 print...
1.4.2 语法规则
PLAINTEXT
1
2
3
4
目标 ... : 依赖 ...
	命令1
	命令2
	. . .
目标即要生成的文件。如果目标文件的更新时间晚于依赖文件更新时间，则说明依赖文件没有改动，目标文件不需要重新编译。否则会进行重新编译并更新目标文件。默认情况下Makefile的第一个目标为终极目标。
依赖：即目标文件由哪些文件生成。
命令：即通过执行命令由依赖文件生成目标文件。注意每条命令之前必须有一个tab保持缩进。
代码示例

新建makefile，touch makefile
MAKEFILE
1
2
3
4
5
6
7
8
mytool:main.o tool1.o tool2.o
    gcc main.o tool1.o tool2.o -o mytool
main.o:main.c
    gcc -c main.c -o main.o
tool1.o:tool1.c
    gcc -c tool1.c -o tool1.o
tool2.o:tool2.c
    gcc -c tool2.c -o tool2.o
执行make即可。
makefile会监视哪些源文件发生了变化，一旦发生变化，则会在有这个变化文件的编译链上重新编译。

假设没有变化
BASH
1
2
[root@HongyiZeng makefile]# make
make: `mytool' is up to date.
将tool1.c修改一点，再次make
BASH
1
2
3
4
5
6
[root@HongyiZeng makefile]# make
gcc -c tool1.c -o tool1.o
gcc main.o tool1.o tool2.o -o mytool
[root@HongyiZeng makefile]# ./mytool 
tool1.c is updated...
tool2 print...
发现只有tool1.c影响到的地方被重新编译了，没有发生变化的地方则不会被重新编译。

代码清理clean：我们可以编译一条属于自己的clean语句，来清理make命令所产生的所有文件，例如：
MAKEFILE
1
2
3
4
5
6
7
8
9
10
11
mytool:main.o tool1.o tool2.o
    gcc main.o tool1.o tool2.o -o mytool
main.o:main.c
    gcc -c main.c -o main.o
tool1.o:tool1.c
    gcc -c tool1.c -o tool1.o
tool2.o:tool2.c
    gcc -c tool2.c -o tool2.o
    
clean:
	rm *.o mytool -rf
使用：

BASH
1
make clean
变量：$符号表示取变量的值，当变量名多于一个字符时，使用()

变量赋值：

=：最普通的等号，在Makefile中容易搞错赋值等号，使用 =进行赋值，变量的值是整个Makefile中最后被指定的值。

MAKEFILE
1
2
3
VIR_A = A
VIR_B = $(VIR_A) B
VIR_A = AA
经过上面的赋值后，最后VIR_B的值是AA B，而不是A B，在make时，会把整个Makefile展开，来决定变量的值

:=：表示直接赋值，赋予当前位置的值。

MAKEFILE
1
2
3
VIR_A := A
VIR_B := $(VIR_A) B
VIR_A := AA
最后BIR_B的值是A B，即根据当前位置进行赋值。

?=：表示如果该变量没有被赋值，赋值予等号后面的值。

MAKEFILE
1
VIR ?= new_value
如果VIR在之前没有被赋值，那么VIR的值就为new_value。

MAKEFILE
1
2
VIR := old_value
VIR ?= new_value
这种情况下，VIR的值就是old_value

+=：和平时写代码的理解是一样的，表示将符号后面的值添加到前面的变量上

预定义变量CC：c编译器的名称，默认值为gcc

GCC编译选项CFLAGS参数

选项	说明
-c	用于把源码文件编译成 .o 对象文件,不进行链接过程
-o	用于连接生成可执行文件，在其后可以指定输出文件的名称
-g	用于在生成的目标可执行文件中，添加调试信息，可以使用GDB进行调试
-Wall	生成常见的所有告警信息，且停止编译，具体是哪些告警信息，请参见GCC手册，一般用这个足矣！
-w	关闭所有告警信息
-O	表示编译优化选项，其后可跟优化等级0\1\2\3，默认是0，不优化
GCC链接选项LDFLAGS参数

选项	说明
-llibrary	链接时在标准搜索目录中寻找库文件，搜索名为liblibrary.a 或 liblibrary.so
-Ldir	用于把新目录添加到库搜索路径上，可以使用相对和绝对路径，“-L.”、“-L./include”、“-L/opt/include”
-static	使用静态库链接生成目标文件，避免使用共享库，生成目标文件会比使用动态链接库大
使用示例：例如posix线程中，查看创建线程的介绍pthread_create中：



需要在编译和链接的选项中加上-pthread，因此，在编写makefile时需要加上：

MAKEFILE
1
2
CFLAGS+=-pthread
LDFLAGS+=-pthread
执行make时：

BASH
1
cc -pthread -phread test.c -o test
改造后的makefile

MAKEFILE
1
2
3
4
5
6
7
8
9
10
11
12
13
14
OBJS=main.o tool1.o tool2.o
CFLAGS+=-c -Wall -g # 编译选项

mytool:$(OBJS)
    $(CC) $(OBJS) -o mytool
main.o:main.c
    $(CC) $(CFLAGS) main.c -o main.o
tool1.o:tool1.c
    $(CC) $(CFLAGS) tool1.c -o tool1.o
tool2.o:tool2.c
    $(CC) $(CFLAGS) tool2.c -o tool2.o

clean:
    rm *.o mytool -rf
$的其他用法：

$^ 表示所有的依赖文件
$@ 表示生成的目标文件
$< 代表第一个依赖文件
再次改造后的makefile

MAKEFILE
1
2
3
4
5
6
7
8
9
10
11
12
13
14
OBJS=main.o tool1.o tool2.o
CFLAGS+=-c -Wall -g

mytool:$(OBJS)
    $(CC) $^ -o $@
main.o:main.c
    $(CC) $(CFLAGS) $^ -o $@
tool1.o:tool1.c
    $(CC) $(CFLAGS) $^ -o $@
tool2.o:tool2.c
    $(CC) $(CFLAGS) $^ -o $@

clean:
    rm *.o mytool -rf
实际上上面的三段代码都有固定的模式，如下：

MAKEFILE
1
2
xxx.o:xxx.c
	$(CC) $(CFLAGS) $^ -o $@
相同的部分xxx可以用通配符%代替。可以简化为：

MAKEFILE
1
2
3
4
5
6
7
8
9
10
OBJS=main.o tool1.o tool2.o
CFLAGS+=-c -Wall -g

mytool:$(OBJS)
    $(CC) $^ -o $@
%.o:%.c
    $(CC) $(CFLAGS) $^ -o $@
    
clean:
    rm *.o mytool -rf
1.5 POSIX
来源：https://zhuanlan.zhihu.com/p/392588996 侵权必删

1.5.1 简介
POSIX：可移植操作系统接口（Portable Operating System Interface of UNIX，缩写为 POSIX ）；

发布者为电气与电子工程师协会（Institute of Electrical and Electronics Engineers），简称IEEE。

POSIX是IEEE为要在各种UNIX操作系统上运行的软件而定义的一系列API标准的总称，其正式称呼为IEEE 1003，而国际标准名称为ISO/IEC 9945。

1.5.2 历史
POSIX是Unix的标准。

1974年，贝尔实验室正式对外发布Unix。因为涉及到反垄断等各种原因，加上早期的Unix不够完善，于是贝尔实验室以慷慨的条件向学校提供源代码，所以Unix在大专院校里获得了很多支持并得以持续发展。

于是出现了好些独立开发的与Unix基本兼容但又不完全兼容的OS，通称Unix-like OS。包括：

美国加州大学伯克利分校的Unix4.xBSD(Berkeley Software Distribution)。
贝尔实验室发布的自己的版本，称为System V Unix。
其他厂商的版本，比如Sun Microsystems的Solaris系统,则是从这些原始的BSD和System V版本中衍生而来。
20世纪80年代中期，Unix厂商试图通过加入新的、往往不兼容的特性来使它们的程序与众不同。

为了提高兼容性和应用程序的可移植性，阻止这种趋势， IEEE开始努力标准化Unix的开发，后来由 Richard Stallman命名为Posix。

这套标准涵盖了很多方面，比如Unix系统调用的C语言接口、shell程序和工具、线程及网络编程。

1.5.3 可移植性
① 系统调用和库函数
Linux下对文件操作有两种方式：系统调用（system call）和库函数调用（Library functions）。

系统调用：系统调用是通向操作系统本身的接口，是面向底层硬件的。通过系统调用，可以使得用户态运行的进程与硬件设备(如CPU、磁盘、打印机等)进行交互，是操作系统留给应用程序的一个接口。
库函数调用：库函数（Library function）是把函数放到库里，供别人使用的一种方式。
方法是把一些常用到的函数编完放到一个文件里，供不同的人进行调用。一般放在.lib文件中。

库函数调用则是面向应用开发的，库函数可分为两类：

一类是C语言标准规定的库函数，
一类是编译器特定的库函数。
(由于版权原因，库函数的源代码一般是不可见的，但在头文件中你可以看到它对外的接口)。

glibc 是 Linux 下使用的开源的标准 C 库，它是 GNU 发布的 libc 库，即运行时库。这些基本函数都是被标准化了的，而且这些函数通常都是用汇编直接实现的。

glibc 为程序员提供丰富的 API（Application Programming Interface），这些API都是遵循POSIX标准的，API的函数名，返回值，参数类型等都必须按照POSIX标准来定义。

POSIX兼容也就指定这些接口函数兼容，但是并不管API具体如何实现。

② 区别


如上图所示：

库函数是语言或应用程序的一部分，而系统调用是内核提供给应用程序的接口，属于系统的一部分
库函数在用户地址空间执行，系统调用是在内核地址空间执行，库函数运行时间属于用户时间，系统调用属于系统时间，库函数开销较小，系统调用开销较大
系统调用依赖于平台，库函数并不依赖
函数库调用	系统调用
定义差别	在所有的ANSI C编译器版本中，C函数库是相同的	各个操作系统的系统调用是不同的
调用差别	它调用函数库中的一段程序或函数	调用系统内核的服务
运行空间	用户地址空间	内核地址空间
开销	属于过程调用，开销小	需要在用户空间和内核空间上下文切换，开销较大
个数	在C函数库libc中大约有300个函数	在Linux中大约有100多个系统调用
典型调用	pringf，fread，malloc	chdir，fork，write，brk
系统调用是为了方便使用操作系统的接口，而库函数则是为了人们编程的方便。

库函数调用与系统无关，不同的系统，调用库函数，库函数会调用不同的底层函数实现，因此可移植性好。

1.5.4 实例
当应用程序调用printf()函数时，printf函数会调用C库中的printf，继而调用C库中的write，C库最后调用内核的write()。

而另一些则不会使用系统调用，比如strlen, strcat, memcpy等。



printf函数执行过程中，程序运行状态切换如下：

PLAINTEXT
1
用户态–>系统调用–>内核态–>返回用户态
printf函数、glibc库和系统调用在系统中关系图如下：



2 标准IO
该节对应APUE的第五章——标准IO库

2.1 简介
IO分为标准IO（stdio）和系统调用IO（sysio）；

系统调用IO根据操作系统的实现方式而定，对于程序员来说会造成很大困扰（例如打开文件，Linux的系统调用为open，而Windows的系统调用为opendir），于是又有了标准IO，提供了一套标准的IO实现的库函数（例如pringtf，fopen等），它实际上也是调用了系统IO进行操作，但是屏蔽了系统调用IO，方便程序员调用。

常用的标准IO库函数如下：

打开关闭文件	输入输出流	文件指针操作	缓存相关
fopen	fgetc，fputc	fseek	fflush
fclose	fgets，fputs	ftell	
fread，fwrite	rewind	
printf族，scanf族	
注意FILE类型贯穿始终；

2.2 fopen
C 库函数 fopen 使用给定的模式 mode 打开 filename 所指向的文件。

C
1
FILE *fopen(const char *filename, const char *mode)
filename — 字符串，表示要打开的文件名称。
mode — 字符串，表示文件的访问模式，该指针指向以下面字符开头的字符串：
模式	描述
“r”	打开一个用于读取的文件。该文件必须存在。
“w”	创建一个用于写入的空文件。如果文件名称与已存在的文件相同，则会删除已有文件的内容，文件被视为一个新的空文件。有则清空，无则创建。
“a”	追加到一个文件。写操作向文件末尾追加数据。如果文件不存在，则创建文件。
“r+”	打开一个用于更新的文件，可读取也可写入。该文件必须存在。
“w+”	创建一个用于读写的空文件。
“a+”	打开一个用于读取和追加的文件。
只有模式r和r+要求文件必须存在，其他模式都是有则清空，无则创建；

mode也可以包含字母b，放在最后或者中间，表示二进制流。例如“rb”，“r+b”；

该函数返回一个 FILE 指针。否则返回 NULL，且设置全局变量 errno 来标识错误。该全局变量在头文件errno.h中声明：（只展示部分）
C
1
2
3
4
5
6
7
#define EPERM 1 /* Operation not permitted */
#define ENOENT 2 /* No such file or directory */
#define ESRCH 3 /* No such process */
#define EINTR 4 /* Interrupted system call */
#define EIO 5 /* I/O error */
#define ENXIO 6 /* No such device or address */
#define E2BIG 7 /* Argument list too long */
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>

int main(void) {
    FILE *fp;
    fp = fopen("tmp", "r");
    if(fp == NULL) {
        fprintf(stderr, "fopen() failed! errno = %d.\n", errno);
        exit(1);
    }
    puts("OK!");
    exit(0);
}
编译执行后打印结果：

PLAINTEXT
1
fopen() failed! errno = 2.
可知errno为2，为No such file or directory；

在C标准中定义了两个函数帮助打印输出errno的对应错误原因，一个是strerror，另一个是perror；

perror包含在stdio.h中：

C
1
2
3
4
5
6
//函数原型
/*
*功能：根据error打印对应的错误信息
*参数：s: 用户自定义信息字符串，一般是出错的函数名
*/
void perror(const char *s);
修改后的程序为：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>

int main(void) {
    FILE *fp;
    fp = fopen("tmp", "r");
    if(fp == NULL) {
        // fprintf(stderr, "fopen() failed! errno = %d.\n", errno);
        perror("fopen()");
        exit(1);
    }
    puts("OK!");
    exit(0);
}
打印结果：

PLAINTEXT
1
fopen(): No such file or directory
strerror包含在<string.h>头文件中

C
1
2
3
4
5
//函数原型
/*
*功能：将某个错误代码转换成对应的错误信息
*/
char *strerror(int errnuum);
修改后的程序为：

C
1
fprintf(stderr, "fopen:%s\n", strerror(errno));
fopen函数解析：

由函数原型可知，fopen函数返回的是一个FILE类型的指针，FILE是一个结构体，由typedef进行了重命名，而指针实际上是指向结构体的指针。

问题：指针指向的内存空间是哪一块（或者说FILE结构体放在内存的哪一块）？是堆，是栈，还是静态区？

栈
C
1
2
3
4
5
6
7
8
9
10
11
// 简单的fopen源码分析
FILE *fopen(const char *filename, const char *mode) {
    FILE tmp;
    
    // 给结构体成员赋值初始化
    tmp.xxx = xxx;
    tmp.yyy = yyy;
    ...
        
    return &tmp;
}
分析：tmp变量的存储类别是自动类型（块作用域，自动存储期），当程序退出这个块时，释放刚才为变量tmp匹配的内存，因此，指针指向的地址实际上没有tmp，是一个没有被分配的内存；

静态区
C
1
2
3
4
5
6
7
8
9
10
11
// 简单的fopen源码分析
FILE *fopen(const char *filename, const char *mode) {
    static FILE tmp;
    
    // 给结构体成员赋值初始化
    tmp.xxx = xxx;
    tmp.yyy = yyy;
    ...
        
    return &tmp;
}
加上static，将tmp保存在静态区（静态无链接），但是只能存在一个FILE实例（因为只有这一个内存区供指针指向）；例如：

C
1
2
3
fp1 = fopen("a", "r");
fp2 = fopen("b", "r");
// 此时fp1实际指向了b，第二次的结果会把第一次的结果覆盖掉
堆（正解）
C
1
2
3
4
5
6
7
8
9
10
11
12
// 简单的fopen源码分析
FILE *fopen(const char *filename, const char *mode) {
    FILE *tmp = NULL;
    tmp = malloc(sizeof(FILE));
    
    // 给结构体成员赋值初始化
    tmp->xxx = xxx;
    tmp->yyy = yyy;
    ...
        
    return tmp;
}
此时变量tmp具有动态存储期，从调用malloc分配内存到调用free释放内存为止，而free就在fclose函数中被调用。

2.3 fclose
C 库函数 fclose 关闭流 stream。刷新所有的缓冲区。

C
1
int fclose(FILE *stream)
stream — 这是指向 FILE 对象的指针，该 FILE 对象指定了要被关闭的流。
如果流成功关闭，则该方法返回零。如果失败，则返回 EOF。
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>

int main(void) {
    FILE *fp;
    fp = fopen("tmp", "r");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }
    puts("OK!");
    fclose(fp); // 释放内存
    exit(0);
}
2.4 fgetc和fputc
getchar和putchar
C
1
int getchar(void); // 从标准输入 stdin 获取一个字符（一个无符号字符）。这等同于 getc 带有 stdin 作为参数
C
1
int putchar(int char); // 把参数 char 指定的字符（一个无符号字符）写入到标准输出 stdout 中。这等同于 putc 带有 stdout 作为参数
getc和putc
C
1
int getc(FILE *stream); // 从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。 
C
1
int putc(int char, FILE *stream); // 把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。
fgetc和fputc
C
1
2
int fgetc(FILE *stream); // 从指定的流 stream 获取下一个字符（一个无符号字符），并把位置标识符往前移动。
// 该函数以无符号 char 强制转换为 int 的形式返回读取的字符，如果到达文件末尾或发生读错误，则返回 EOF。
C
1
2
int fputc(int char, FILE *stream); // 把参数 char 指定的字符（一个无符号字符）写入到指定的流 stream 中，并把位置标识符往前移动。
// 如果没有发生错误，则返回被写入的字符。如果发生错误，则返回 EOF，并设置错误标识符。
getc，putc和fgetc，fputc的区别

两者的使用完全相同，只是实现不同。这里的f指的是function，而不是file。

getc，putc是通过宏定义实现，而fgetc，fputc是通过函数来实现。

宏只占用编译时间，不占用调用时间，而函数相反，因此内核的实现通常使用宏来定义函数，减少调用时间。

代码示例

需求：拷贝文件

PLAINTEXT
1
./mycpy src dest
实现代码：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
#include <stdio.h>
#include <stdlib.h>

// 命令行传参
int main(int argc, char **argv) {

    FILE *fps, *fpd;
    int ch; // 存储读入的字符

    if(argc < 3) {
        fprintf(stderr, "Usage:%s <src_file> <dest_file>\n", argv[0]);
        exit(1);
    }

    fps = fopen(argv[1], "r");
    if(fps == NULL) {
        perror("fopen()");
        exit(1);
    }

    fpd = fopen(argv[2], "w");
    if(fpd == NULL) {
        fclose(fps);
        perror("fopen()");
        exit(1);
    }

    while(1) {
        ch = fgetc(fps);
        if(ch == EOF) { // 读到文件末尾结束循环
            break;
        }
        fputc(ch, fpd);
    }
    
	// 释放内存，后开的先关
    fclose(fpd);
    fclose(fps);

    exit(0);
}
使用：

BASH
1
./mycpy /usr/local/test /temp/out
2.5 fgets和fputs
gets和puts
C
1
2
char *gets(char *str); // 从标准输入 stdin 读取一行，并把它存储在 str 所指向的字符串中。当读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。
// 如果成功，该函数返回 str。如果发生错误或者到达文件末尾时还未读取任何字符，则返回 NULL。
C
1
2
int puts(const char *str); // 把一个字符串写入到标准输出 stdout，直到空字符，但不包括空字符。换行符会被追加到输出中。
// 如果成功，该函数返回一个非负值为字符串长度（包括末尾的 \0），如果发生错误则返回 EOF。
fgets和fputs
C
1
2
3
4
// 从指定的流 stream 读取一行，并把它存储在 str 所指向的字符串内。当读取 (n-1) 个字符时，或者读取到换行符时，或者到达文件末尾时，它会停止，具体视情况而定。
char *fgets(char *str, int n, FILE *stream); 
// 如果成功，该函数返回相同的 str 参数。如果到达文件末尾或者没有读取到任何字符，str 的内容保持不变，并返回一个空指针。
// 如果发生错误，返回一个空指针。
C
1
2
3
//  把字符串写入到指定的流 stream 中，但不包括空字符。
int fputs(const char *str, FILE *stream);
// 该函数返回一个非负值，如果发生错误则返回 EOF。
区别

fgets比gets安全，使用gets编译时会警告。所以不要使用gets！

原因：函数 gets 可以无限读取，不会判断上限，所以程序员应该确保 buffer 的空间足够大，以便在执行读操作时不发生溢出。也就是说，gets 函数并不检查缓冲区 buffer 的空间大小，事实上它也无法检查缓冲区的空间。

如果函数的调用者提供了一个指向堆栈的指针，并且 gets 函数读入的字符数量超过了缓冲区的空间（即发生溢出），gets 函数会将多出来的字符继续写入堆栈中，这样就覆盖了堆栈中原来的内容，破坏一个或多个不相关变量的值。

fgets读取结束的条件，满足其一即可：

读到size-1个字符时停止，size位置存放\0
读到换行符'\n'时停止
读到文件末尾EOF
简单的实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
#define SIZE 5
char buf[SIZE]; // 栈上的动态内存
fgets(buf, SIZE, stream);

如果stream = "abcde"
则buf = "abcd\0"(读到size-1)，文件指针指向e

如果stream = "ab"
则buf = "ab\n\0"(读到换行符)，文件指针指向EOF

极端的情况：
如果stream = "abcd"
则需要fgets读取两次才能读完
第一次读取的为"abcd\0"(读到SIZE-1)，指针指向'\n'
第二次读取的为"\n\0"(读到换行符)，指针指向EOF
代码示例

用fgets和fputs代替fgtec和fputc：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
#include <stdio.h>
#include <stdlib.h>
#define SIZE 1024

int main(int argc, char **argv) {

    FILE *fps, *fpd;
    char buf[SIZE];

    if(argc < 3) {
        fprintf(stderr, "Usage:%s <src_file> <dest_file>\n", argv[0]);
        exit(1);
    }

    fps = fopen(argv[1], "r");
    if(fps == NULL) {
        perror("fopen()");
        exit(1);
    }

    fpd = fopen(argv[2], "w");
    if(fpd == NULL) {
        fclose(fps);
        perror("fopen()");
        exit(1);
    }

    while(fgets(buf, SIZE, fps) != NULL)
            fputs(buf, fpd);

    fclose(fpd);
    fclose(fps);

    exit(0);
}
2.6 fread和fwrite
fread从给定流 stream 读取数据到 ptr 所指向的数组中。

C
1
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream)
ptr — 这是指向带有最小尺寸 size*nmemb 字节的内存块的指针。
size — 这是要读取的每个元素的大小，以字节为单位。
nmemb — 这是元素的个数，每个元素的大小为 size 字节。
stream — 这是指向 FILE 对象的指针，该 FILE 对象指定了一个输入流。
成功读取的元素总数会以 size_t 对象返回，size_t 对象是一个整型数据类型。如果总数与 nmemb 参数不同，则可能发生了一个错误或者到达了文件末尾。
fwrite把 ptr 所指向的数组中的数据写入到给定流 stream 中。

C
1
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream)
ptr — 这是指向要被写入的元素数组的指针。
size — 这是要被写入的每个元素的大小，以字节为单位。
nmemb — 这是元素的个数，每个元素的大小为 size 字节。
stream — 这是指向 FILE 对象的指针，该 FILE 对象指定了一个输出流。
如果成功，该函数返回一个 size_t 对象，表示元素的总数，该对象是一个整型数据类型。如果该数字与 nmemb 参数不同，则会显示一个错误。
简单的实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
fread(buf, size, nmemb, fp);

// 情况1：数据量足够
// 情况2：文件只有5个字节

// 读10个对象，每个对象1个字节
fread(buf, 1, 10, fp);

// 情况1：
// 第一次读：返回10（读到10个对象），读到10个字节
// 情况2：
// 第一次读：返回5（读到5个对象），读到5个字节

//--------------------------------

// 读1个对象，每个对象10个字节
fread(buf, 10, 1, fp);

// 情况1：
// 第一次读：返回1（读到1个对象），也读到10个字节
// 情况2：
// 第一次读：返回0（读不到1个对象，因为1个对象要10字节，而文件只有5个字节）
代码示例

用fread和fwrite代替fgtec和fputc：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
#include <stdio.h>
#include <stdlib.h>
#define SIZE 1024

int main(int argc, char **argv) {

    FILE *fps, *fpd;
    char buf[SIZE];
    int n;

    if(argc < 3) {
        fprintf(stderr, "Usage:%s <src_file> <dest_file>\n", argv[0]);
        exit(1);
    }

    fps = fopen(argv[1], "r");
    if(fps == NULL) {
        perror("fopen()");
        exit(1);
    }

    fpd = fopen(argv[2], "w");
    if(fpd == NULL) {
        fclose(fps);
        perror("fopen()");
        exit(1);
    }
	
    // 如果成功读到n(n>0)个对象，则返回n
    // 将这n个对象写入流中
    while((n = fread(buf, 1, SIZE, fps)) > 0) 
            fwrite(buf, 1, n, fpd);

    fclose(fpd);
    fclose(fps);

    exit(0);
}
2.7 printf和scanf
printf一族函数

printf：发送格式化输出到标准输出 stdout。
C
1
int printf(const char *format, ...);
fprintf：发送格式化输出到流 stream 中。可以实现格式化输出的重定向，例如重定向至文件中。
C
1
int fprintf(FILE *stream, const char *format, ...);
sprintf：发送格式化输出到 str 所指向的字符串。它能够将多种数据类型（整型、字符型）的数据综合为字符串类型。有溢出风险，可以使用snprintf来防止。
C
1
int sprintf(char *str, const char *format, ...)
atoi：把参数 str 所指向的字符串转换为一个整数（类型为 int 型）。包含在stdlib.h中。
C
1
int atoi(const char *str)
代码示例：

C
1
2
3
4
5
6
7
8
9
10
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    char str[] = "123456";

    printf("%d\n", atoi(str)); // 123456

    exit(0);
}
C
1
2
3
4
5
6
7
8
9
10
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    char str[] = "123a456";
	// 遇到字符就停止
    printf("%d\n", atoi(str)); // 123

    exit(0);
}
C
1
2
3
4
5
6
7
8
9
10
11
12
13
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    char buf[1024];
    int year = 2022, month = 11, day = 28;
	
    // 将格式化输出重定向为字符串
    sprintf(buf, "%d-%d-%d", year, month, day);
    puts(buf);

    exit(0);
}
scanf一族函数

scanf
fscanf
sscanf
2.8 fseek和ftell
fseek：设置流 stream 的文件位置为给定的偏移 offset，参数 offset 意味着从给定的 whence 位置查找的字节数。

C
1
int fseek(FILE *stream, long int offset, int whence)
stream — 这是指向 FILE 对象的指针，该 FILE 对象标识了流。
offset — 这是相对 whence 的偏移量，以字节为单位。
whence — 这是表示开始添加偏移 offset 的位置。它一般指定为下列常量之一：
常量	描述
SEEK_SET	文件的开头
SEEK_CUR	文件指针的当前位置
SEEK_END	文件的末尾
如果成功，则该函数返回零，否则返回非零值。
ftell：返回给定流 stream 的当前文件位置。

C
1
long int ftell(FILE *stream)
stream — 这是指向 FILE 对象的指针，该 FILE 对象标识了流。
该函数返回位置标识符的当前值。如果发生错误，则返回 -1L，全局变量 errno 被设置为一个正值。
程序实例——求程序的有效字节

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv){

    FILE *fp;
    if(argc < 2) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }

    fp = fopen(argv[1], "r");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }
	// 将指针定位在文件末尾
    fseek(fp, 0, SEEK_END);

    printf("%d\n", ftell(fp));

    exit(0);
}


rewind：设置文件位置为给定流 stream 的文件的开头。

C
1
void rewind(FILE *stream)
相当于(void) fseek(stream, 0, SEEK_SET);

注意

fseek和ftell中偏移offset的修饰类型是long，因此只能对2G左右大小的文件进行操作，否则会超出long的范围；

fseeko和ftello则将偏移的修饰类型使用typedef定义为offset_t，具体类型交由系统决定，因此不存在文件大小的限制。但是这两个函数不是C标准库函数，而是隶属于POSIX标准（POSIX是标准C库的超集，或者说，C库是普通话，而POSIX是方言）。

2.9 fflush
fflush：刷新流 stream 的输出缓冲区。刷新，指的是将缓冲区（内存上的一片区域）的内容写入到磁盘（外存）中，或者输出到终端上显示。

C
1
int fflush(FILE *stream)
如果参数为NULL，则刷新所有的已打开的流
如果成功，该函数返回零值。如果发生错误，则返回 EOF，且设置错误标识符（即 feof）。
代码示例

C
1
2
3
4
5
6
7
8
#include <stdio.h>

int main() {
    printf("Before while(1)");
    while(1);
    printf("After while(1)");
    exit(0);
}
打印结果：

C
1
// 什么都不打印
原因：

对于标准输出，输出缓冲区刷新的时机：
输出缓冲区满
或者遇到换行符\n
强制刷新，或者进程结束
因此，可以修改为：

C
1
2
3
4
5
6
7
8
9
#include <stdio.h>
#include <stdlib.h>
int main() {
    // 遇到\n刷新
    printf("Before while(1)\n");
    while(1);
    printf("After while(1)\n");
    exit(0);
}
或者修改为：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
#include <stdio.h>
#include <stdlib.h>

int main() {

    printf("Before while(1)");
    // 强制刷新
    fflush(stdout);
	// 或者 fflush(NULL);
    
    while(1);

    printf("After while(1)");

    exit(0);
}
缓冲区的作用：大多数情况下是好事，合并系统调用，增加程序的吞吐量。

缓冲的分类：

行缓冲line buffered：针对标准输出（终端设备），有换行刷新，缓冲满刷新，强制刷新三种，后两个和全缓冲一致；
全缓冲fully buffered：默认缓冲机制（除标准输出【终端设备】，例如重定向到文件），有缓冲满刷新，强制刷新两种，强制刷新例如调用fflush函数，或者进程结束时也会强制刷新；此时换行符仅仅只是个换行符，没有刷新功能；
无缓冲unbuffered：例如stderr，需要立即输出，数据会立即读入内存或者输出到外存文件和设备上；
setvbuf：定义流 stream 应如何缓冲。理解即可。

C
1
int setvbuf(FILE *stream, char *buffer, int mode, size_t size)
stream — 这是指向 FILE 对象的指针，该 FILE 对象标识了一个打开的流。
buffer — 这是分配给用户的缓冲。如果设置为 NULL，该函数会自动分配一个指定大小的缓冲。
mode — 这指定了文件缓冲的模式：
模式	描述
_IOFBF	全缓冲：对于输出，数据在缓冲填满时被一次性写入。对于输入，缓冲会在请求输入且缓冲为空时被填充。
_IOLBF	行缓冲：对于输出，数据在遇到换行符或者在缓冲填满时被写入，具体视情况而定。对于输入，缓冲会在请求输入且缓冲为空时被填充，直到遇到下一个换行符。
_IONBF	无缓冲：不使用缓冲。每个 I/O 操作都被即时写入。buffer 和 size 参数被忽略。
2.10 getline
之前介绍的函数，都不能获得完整的一整行（有缓冲区大小的限制），而下面介绍的getline函数则可以动态分配内存，当装不下完整一行时，又会申请额外的内存来存储。

getline是C++标准库函数，但不是C标准库函数，而是POSIX所定义的标准库函数（在POSIX IEEE Std 1003.1-2008标准出来之前，则只是GNU扩展库里的函数）。在gcc编译器中，对标准库stdio进行了扩展，加入了一个getline函数。

getline会生成一个包含一串从输入流读入的字符的字符串，直到以下情况发生会导致生成的此字符串结束：

到文件结束
遇到函数的定界符
输入达到最大限度
函数原型：

C
1
2
3
#define _GNU_SOURCE // 通常将这种宏写在makefile中，现在的编译器没有了该宏，直接使用即可
#include <stdio.h>
ssize_t getline(char **lineptr, size_t *n, FILE *stream);
lineptr：指向存放该行字符的指针，如果是NULL，则有系统帮助malloc，请在使用完成后free释放。该参数是一个二级指针，因此传参需要一级指针的地址。即函数会把读取到的字符串的首地址存放在一级指针中。

C
1
2
3
4
5
// 传参：
char *ptr;
// 函数内的实际操作：
// 假设读取到的字符串Hello的首地址为0x000
&ptr = 0x000; // 此时ptr就指向了Hello
n：如果是由系统malloc的指针填0；

stream：函数需要读取的FILE流

返回值：成功返回读取的字节数，失败或读完返回-1。

代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
int main(int argc, char **argv) {
    FILE *fp;
    // 一定要初始化，否则指针会指向内存中的随机位置
    char *linebuf = NULL;
    size_t linesize = 0;
    if(argc < 2) {
        fprintf(stderr, "Usage...\n");
    }
    fp = fopen(argv[1], "r");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }
    while(1) {
        // 当返回-1时则读完
    	if(getline(&linebuf, &linesize, fp) < 0)
            break;
       	printf("%d\n", strlen(linebuf));
    }
    fclose(fp);
    exit(0);
}
2.11 临时文件
临时文件产生的问题：

如何命名不冲突
如何保证及时销毁
tmpnam：生成并返回一个有效的临时文件名，该文件名之前是不存在的。如果 str 为空，则只会返回临时文件名。

存在并发问题，可能会产生两个或多个名字相同的临时文件。

C
1
char *tmpnam(char *str)
str — 这是一个指向字符数组的指针，其中，临时文件名将被存储为 C 字符串。
返回一个指向 C 字符串的指针，该字符串存储了临时文件名。如果 str 是一个空指针，则该指针指向一个内部缓冲区，缓冲区在下一次调用函数时被覆盖。
如果 str 不是一个空指针，则返回 str。如果函数未能成功创建可用的文件名，则返回一个空指针。
tmpfile：以二进制更新模式(wb+)创建临时文件。被创建的临时文件会在流关闭的时候或者在程序终止的时候自动删除。

该文件没有名字（匿名文件）只返回指向FILE的指针，因此不存在命名冲突的问题，同时会自动删除，因此可以及时销毁。

C
1
FILE *tmpfile(void)
如果成功，该函数返回一个指向被创建的临时文件的流指针。如果文件未被创建，则返回 NULL。
3 系统调用IO
该节对应APUE的第三章——文件IO

3.1 简介
UNIX系统的大多数文件IO只需用到5个函数：

open
close
read
write
lseek
3.2 文件描述符
3.2.1 FILE结构体
查看stdio.h头文件中，有FILE结构体的定义：

C
1
2
3
4
5
6
7
8
9
10
11
//stdio.h
typedef struct _iobuf {
    char*  _ptr;        //文件输入的下一个位置
    int    _cnt;        //当前缓冲区的相对位置
    char*  _base;       //文件初始位置
    int    _flag;       //文件标志
    int    _file;       //文件有效性
    int    _charbuf;    //缓冲区是否可读取
    int    _bufsiz;     //缓冲区字节数
    char*  _tmpfname;   //临时文件名
} FILE;
其中_file就是文件描述符。

3.2.2 文件描述符
文件描述符（fd，file descriptor）是文件IO（也系统IO）中贯穿始终的类型。如下图所示：



当某一个进程执行系统调用open函数，会创建一个结构体，该结构体类似于FILE结构体，其中最基本的成员有一个指针pos，用于指向inode文件的某一个位置；
同时，该进程会维护一个数组（文件描述符表），该数组存储上述结构体的地址，而数组下标就是文件描述符fd，即文件描述符的本质就是一个整型数；

该数组默认大小为1024，即可以打开的最大文件数量为1024，但可以设置ulimit来更改数组大小；注意该数组和对应产生的结构体只存在于这个进程空间内，而不是进程间共享；
当调用open函数时，系统会自动打开三个流stdin，stdout和stderr，这三个流分别占据该数组的0,1,2号位置；
结构体FILE中的成员_file就是整型数组下标fd，即文件描述符
每打开一个新文件，则占用一个数组空间，而且是空闲的最小的数组下标。即文件描述符优先使用当前可用范围内最小的。同一个文件可以被多次打开，但是每打开一次都需要一个新的文件描述符和新的结构体，例如图中的结构体1和结构体2，指向了同一个inode；
执行系统调用close时，就将对应fd下标的数组空间清除掉，并清除该地址指向的结构体；

结构体中有一个成员用于记录引用计数，例如图中，将5号位置的0x006地址复制一份存储在6号位置，此时有两个指针指向了同一个结构体3，此时结构体3的引用计数为2，当5号指针free时，结构体3的引用计数减1为1，不为0，则不会释放掉，否则6号位置的指针将成为野指针；
3.3 open和close
3.3.1 文件权限
① rwx
Linux下一切皆文件，不同的用户对文件拥有不同的权限。

文件具有三种权限：

PLAINTEXT
1
rwx    可读可写可执行,转换为数字就是421
针对文件	针对目录
r	是否可以查看文件内容	是否能够列出ls目录内容
w	是否可以编辑文件内容	是否能够创建、删除、复制、移动文档
x	是否能够执行该文件	是否可以进入目录和获得目录下文件的列表，要对目录下存在的文件进行读取和修改，必须要能进入目录，所以必须要目录有执行权限
② 文件属性
查看当前目录下的所有文件的属性：

BASH
1
ll


或者可以查看单个文件权限：

BASH
1
ll atoi
基本的文件属性格式如下：

PLAINTEXT
1
类型 权限 链接数 属主 属组 大小 修改日期 文件名
类型和权限：
第1列表示文件的类型：dcb-lsp
d：目录
-：普通文件
l：软链接（类似Windows的快捷方式）
b：块设备文件（例如硬盘、光驱等）
p：管道文件
c：字符设备文件（例如屏幕等串口设备）
s：套接口文件
第2至10列为文件或目录的权限，分为3组：
拥有者权限owner：文件和文件目录的所有者
所属组group：文件和文件目录的所有者所在的组的其他用户
其它用户other：不属于上面的两个的用户
链接数：有多少文件名链接到此节点（i-node）；每个文件都会将它的权限与属性记录到文件系统的i-node中，不过我们使用的目录树却是使用文件名来记录，因此每个文件名就会连接到一个i-node，这个属性记录的就是有多少不同的文件名链接到相同的一个i-node号码。
例如第一行的文件属性为：

PLAINTEXT
1
-rwxr-xr-x 1 root root 8496 Nov 28 14:54 atoi
则为普通文件，拥有者有读写和执行权限，而所属组用户和其他用户只有读取和执行权限，没有写权限；属主和属组均为root；

③ umask
Linux具有默认权限：

一个目录被创建，默认权限是drwxrwxrwx，即777

一个普通文件被创建，默认权限是-rw-rw-rw-，即666

但实际上所创建的文件和目录，看到的权限往往不是上面这个值。原因就是创建文件或目录的时候还要受到 umask 的影响。umask值表明了需要从默认权限中去掉哪些权限来成为最终的默认权限值。

查看umask的值：

BASH
1
2
[root@HongyiZeng io]# umask
0002
可以看到umask值为0002

第一个0与特殊权限（特殊权限的内容参见6.6.2节）有关，后三位002则与普通权限rwx有关
002中第一个0与拥有者权限有关，表示从拥有者权限减0，也就是权限不变，所以文件的创建者的权限就是是默认权限rw（6 - 0 = 0）
002中第二个0与组权限有关，表示从组的权限减0，所以组的权限也保持默认权限（rw）
002最后一位2则与系统中其他用户的权限有关，由于w=2，所以需要从其他用户默认权限rw减去2，也就是去掉写（w）权限，则其他人的权限为rw - w = r（6 - 2 = 4，对应r权限）
最终，创建文件的最终默认权限为 -rw-rw-r--
同理目录权限的计算也是如此。

也可以临时更改umask的值：

BASH
1
2
3
[root@HongyiZeng io]# umask 027
[root@HongyiZeng io]# umask
027
即去掉所属组用户的写权限，去掉其他用户的所有权限。

如果需要永久更改umask的话，需要修改文件/etc/bashrc中的umask值：



3.3.2 open
安装man手册：

BASH
1
yum install -y man-pages
open用于打开或创建一个文件或者设备。

所在头文件：

C
1
2
3
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
函数原型1：

C
1
int open(const char *pathname, int flags);
将准备打开的文件或是设备的名字作为参数path传给函数，flags用来指定文件访问模式。
open系统调用成功返回一个新的文件描述符，失败返回-1。
其中，flags是由必需文件访问模式和可选模式一起构成的(通过按位或|)：

必需部分	可选部分（只列出常用的）
O_RDONLY：以只读方式打开	O_CREAT：按照参数mode给出的访问模式创建文件
O_WRONLY：以只写方式打开	O_EXCL：与O_CREAT一起使用，确保创建出文件，避免两个程序同时创建同一个文件，如文件存在则open调用失败
O_RDWR：以读写方式打开	O_APPEND：把写入数据追加在文件的末尾
O_TRUNC：把文件长度设置为0，丢弃原有内容
O_NONBLOCK：以非阻塞模式打开文件
其中，对于可选部分，又分为文件创建选项和文件状态选项：

文件创建选项：O_CREAT，O_EXCL，O_NOCTTY，O_TRUNC
文件状态选项：除文件创建选项之外的选项
fopen和open的文件访问模式的联系

C
1
2
3
4
5
r -> O_RDONLY // 只读存在的文件
r+ -> O_RDWR // 读写存在的文件
w -> O_WRONLY|O_CREAT|O_TRUNC // 只写，并且有则清空，无则创建
w+ -> O_RDWR|O_CREAT|O_TRUNC // 读写，并且有则清空，无则创建
// ...
函数原型2：

C
1
int open(const *path, int flags, mode_t mode);
在第一种调用方式上，加上了第三个参数mode，主要是搭配O_CREAT使用，这个参数规定了属主、同组和其他人对文件的文件操作权限。只列出部分：

字段	含义
S_IRUSR	读权限
S_IWUSR	写权限 ——文件属主
S_IXUSR	执行权限
可以用数字设定法：

数字	含义
0	无权限
1	x
2	w
4	r
注意mode还要和umask计算才能得出最终的权限；

例如：

C
1
int fd = open("./file.txt",O_WRONLY | O_CREAT, 0600);
创建一个普通文件，权限为0600，拥有者有读写权限，组用户和其他用户无权限。

补充：变参函数

变参数函数的原型声明为：

C
1
type VAFunction(type arg1, type arg2, ...);
变参函数可以接受不同类型的参数，也可以接受不同个数的参数。

参数可以分为两部分：个数确定的固定参数和个数可变的可选参数。函数至少需要一个固定参数，固定参数的声明和普通函数一样；可选参数由于个数不确定，声明时用 ... 表示。固定参数和可选参数共同构成一个函数的参数列表。

以printf为例，它就是一个变参函数：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
int printf(const char *fmt, ...){    
    int i;    
    int len;    
    va_list args; /* va_list 即 char * */
    
    va_start(args, fmt);    
    /* 内部使用了 va_arg() */
    len = vsprintf(g_PCOutBuf,fmt,args);
    
    va_end(args);    
    for (i = 0; i < strlen(g_PCOutBuf); i++)
    {
        putc(g_PCOutBuf[i]);
    }    
    return len;
}
3.3.3 close
close：关闭一个文件描述符

C
1
2
3
#include <unistd.h>

int close(int fd);
返回 0 表示成功，或者 -1 表示有错误发生，并设值errno；

3.4 read，write和lseek
read所在头文件和函数原型：

C
1
2
3
#include <unistd.h>

ssize_t read(int fd, void *buf, size_t count);
从与文件描述符fd相关联的文件中读取前count字节的内容，并且写入到数据区buf中
read系统调用返回的是实际读入的字节数，发生错误返回-1
write所在头文件和函数原型：

C
1
2
3
#include <unistd.h>

ssize_t write(int fd, const void *buf, size_t count);
把缓存区buf中的前count字节写入到与文件描述符fd有关的文件中
write系统调用返回的是实际写入到文件中的字节数，发生错误返回-1，注意返回0不是发生错误，而是写入的字节数为0
lseek所在头文件和函数原型：

C
1
2
3
4
#include <sys/types.h>
#include <unistd.h>

off_t lseek(int fd, off_t offset, int whence);
lseek设置文件位置为给定的偏移 offset，参数 offset 意味着从给定的 whence 位置查找的字节数。

whence取值：

字段	含义
SEEK_SET	文件开头
SEEK_END	文件末尾
SEEK_CUR	文件当前位置
代码示例

用系统调用io实现mycpy的功能。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

#define BUFSIZE 1024 // 缓冲区大小

int main(int argc, char **argv) {
    // 源文件和目标文件的文件描述符
    int sfd, dfd;
    // 读写缓冲
    char buf[BUFSIZE];
    // len：读文件的返回字节数
    // ret：写文件的返回字节数
    // pos：写文件的当前位置
    int len, ret, pos;

    if(argc < 3) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }
	
    // 以只读方式打开文件，打开文件失败
    if((sfd = open(argv[1], O_RDONLY)) < 0) {
        perror("open()");
        exit(1);
    }
	
    // 以只读方式打开文件，有则清空，无则创建
    // 打开文件失败
    if((dfd = open(argv[2], O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {
        close(sfd);
        perror("open()");
        exit(1);
    }

    while(1) {
        if((len = read(sfd, buf, BUFSIZE)) < 0) {
            perror("read()");
            break;
        }
		
        // 读完文件
        if(len == 0)
            break;

        pos = 0;
        // 防止读到的字节没有完全写入文件
        // 保证读多少，就写多少
        while(len > 0) {
            if((ret = write(dfd, buf + pos, len)) < 0) {
                perror("write()");
                exit(1);
            }
            pos += ret;
            len -= ret;
        }
    }
	// 关闭文件描述符
    close(dfd);
    close(sfd);

    exit(0);
}
3.5 IO效率
文件I/O：文件I/O又称为无缓冲IO，低级磁盘I/O，遵循POSIX相关标准。任何兼容POSIX标准的操作系统上都支持文件I/O。
标准I/O：标准I/O是ANSI C建立的一个标准I/O模型，又称为高级磁盘I/O，是一个标准函数包和stdio.h头文件中的定义，具有一定的可移植性。标准I/O库处理很多细节。例如缓存分配，以优化长度执行I/O等。标准的I/O提供了三种类型的缓存（行缓存、全缓存和无缓存）。
Linux 中使用的是GLIBC，它是标准C库的超集。不仅包含ANSI C中定义的函数，还包括POSIX标准中定义的函数。因此，Linux 下既可以使用标准I/O，也可以使用文件I/O。

缓存是内存上的某一块区域。缓存的一个作用是合并系统调用，即将多次的标准IO操作合并为一个系统调用操作。

文件IO不使用缓存，每次调用读写函数时，从用户态切换到内核态，对磁盘上的实际文件进行读写操作，因此响应速度快，坏处是频繁的系统调用会增加系统开销（用户态和内核态来回切换），例如调用write写入一个字符时，磁盘上的文件中就多了一个字符。

标准IO使用缓存，未刷新缓冲前的多次读写时，实际上操作的是内存上的缓冲区，与磁盘上的实际文件无关，直到刷新缓冲时，才调用一次文件IO，从用户态切换到内核态，对磁盘上的实际文件进行操作。因此标准IO吞吐量大，相应的响应时间比文件IO长。但是差别不大，建议使用标准IO来操作文件。

两种IO可以相互转化：

fileno：返回结构体FILE的成员_file，即文件描述符。标准IO->文件IO

C
1
int fileno(FILE *stream);
fdopen：通过文件描述符fd，返回FILE结构体。文件IO->标准IO

C
1
FILE *fdopen(int fd, const char *mode);
注意：即使对同一个文件，也不要混用两种IO，否则容易发生错误。

原因：FILE结构体的pos和进程中的结构体的pos基本上不一样。

C
1
2
3
4
FILE *fp;
// 连续写入两个字符
fputc(fp) -> pos++
fputc(fp) -> pos++    
但是，进程维护的结构体中的pos并未加2；只有刷新缓冲区时，该pos才会加2；

代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    putchar('a');
    write(1, "b", 1);

    putchar('a');
    write(1, "b", 1);

    putchar('a');
    write(1, "b", 1);

    exit(0);
}
打印结果：

PLAINTEXT
1
bbbaaa
解析：遇到文件IO则立即输出，遇到标准IO，则需要等待缓冲区刷新的时机，这里是进程结束后，进行了强制刷新，将3个a字符输出到终端上。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    putchar('a');
    fflush(stdout);
    write(1, "b", 1);

    putchar('a');
    fflush(stdout);
    write(1, "b", 1);

    putchar('a');
    fflush(stdout);
    write(1, "b", 1);

    exit(0);
}
打印结果：

PLAINTEXT
1
ababab
strace命令能够显示所有由用户空间程序发出的系统调用。

以上面第一个程序为例：

PLAINTEXT
1
strace ./ab
打印结果：



BUFSIZE对IO效率的影响



图中用户CPU时间是程序在用户态下的执行时间；系统CPU时间是程序在内核态下的执行时间；时钟时间是两个时间的总和；

BUFSIZE受栈大小的影响；此测试所用的文件系统是Linux ext4文件系统，其磁盘块长度为4096字节。这也证明了图中系统 CPU 时间的几个最小值差不多出现在BUFFSIZE 为4096 及以后的位置，继续增加缓冲区长度对此时间几乎没有影响。

3.6 C程序的内存空间布局
补充：内存地址和内存空间

内存地址是一个编号，通常由16进制表示，它代表一个内存空间。在计算机中存储器的容量是以字节为基本单位的，也就是说一个内存地址代表一个字节（8bit）的存储空间，即按字节寻址。

假设一个int类型的变量x占用4个字节，则会占用4个连续的内存空间，x的内存地址则为第一个内存空间的地址。

对于32位操作系统，内存地址长度为32位，则可以表示2的32次方个内存空间（可寻址空间），即4GB；

计算：2^32 * 1B = 2^32B = 2^22 KB = 2^12 MB = 2^2 GB = 4GB

对于64位操作系统，内存地址长度为64位，则可以表示2的64次方个内存空间（16777216TB）；但实际上，主板和CPU的限制导致一般的电脑所支持的内存最大只有16GB而已。

C程序（例如a.out）运行时会被加载入内存中，而这个内存一般分为五个分区：栈区、堆区、数据区、常量区、代码区。





3.6.1 动态区
动态区的内容会根据执行情况而动态变化。

① 栈区
栈（stack）是用户存放程序临时创建的局部变量，在函数被调用时，其参数也会被压入发起调用的进程栈中，并且等调用结束后，函数的返回值也会被存放在回栈中。

栈的大小：最大大小由编译时确定，不会太大。
释放和分配：由编译器自动分配释放，由操作系统自动管理，无须手动管理。
栈区地址：由高地址向低地址生长。
若越界访问则会出现段错误（Segmentation Fault）
若多次递归调用增加栈帧导致越界则会出现栈溢出（Stack Overflow）
栈的大小可以通过ulimit命令查看：

BASH
1
2
ulimit -s # 只查看stack的大小
ulimit -a # 查看当前所有的资源限制，stack 字段，单位Kbytes


可以看到栈的大小默认为8192KB，即8M；

代码示例

C
1
2
3
4
5
6
int main() {
    int x =10;  // 栈存储
    int y = 20; // 栈存储
    
    return 0;
}
② 堆区
堆区存放：程序运行中动态存储分配的空间
堆区大小：视内存大小而定，由程序员进行分配。
堆区地址：由低地址向高地址生长
代码示例

C
1
2
3
4
5
6
7
int main() {
    int x =10;  // 栈分配
    int y = 20; // 栈分配
    char *p = (char*)malloc(256); //堆分配
    
    return 0;
}
3.6.2 静态区
静态区的内容在整个程序的生命周期内都存在，由编译器在编译的时候分配。

① 数据区
根据数据是否被初始化又分为：bss段与data段。

未初始化数据段（bss）
通常将此段称为bss段，这一名称来源于早期汇编程序的一个操作符，意思是block started by symbol（由符号开始的块）。

存放未初始化的全局变量，属于静态内存分配。在程序开始执行之前，内核将此段初始化为0。

代码示例

C
1
2
3
4
5
6
long sum[1000]; // 此变量存放在非初始化数据段中

int main(void) {
    // ...
    return 0;
}
已初始化数据段（data）
存放已初始化的全局变量和静态变量，属于静态内存分配，其内容由程序初始化。

代码示例

C
1
2
3
4
5
6
float PI= 3.14f; // 此变量以初值存放在初始化数据段中

int main(void) {
    // ...
    return 0;
}
② 常量区
常量区存放字符串常量、const修饰的全局变量。程序运行期间，常量区的内容不可以被修改。

③ 代码区
代码区（text），又叫：正文段、代码段。

通常是用来存放程序执行代码的一块内存区域。这部分区域的大小在程序运行前就已经确定，并且内存区域通常属于只读，某些架构也允许代码段为可写，即允许修改程序。

3.6.3 栈的地址测试
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
#include <stdio.h>
#include <stdlib.h>

int main() {
    
    int x = 10; // 栈分配
    int y = 10; // 栈分配

    int *p = &x; // 栈分配

    printf("&x = %p\n", &x);
    printf("&y = %p\n", &y);
    printf("&p = %p\n", &p);

    exit(0);
}
打印结果：

PLAINTEXT
1
2
3
&x = 0x7ffd1ce3e33c
&y = 0x7ffd1ce3e338
&p = 0x7ffd1ce3e330
可以看到x，y，p的地址从高向低依次排列。

3.6.4 堆的地址测试
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
#include <stdio.h>
#include <stdlib.h>

int main() {
    int x = 10;
    int y = 10;

    int *p = &x;
    char *q = (char *)malloc(256 * sizeof(char)); // 堆上分配

    printf("&x = %p\n", &x);
    printf("&y = %p\n", &y);
    printf("&p = %p\n", &p);
    printf("&q = %p\n", &q);
	
    free(q);
    exit(0);
}
打印结果：

PLAINTEXT
1
2
3
4
&x = 0x7ffd4ab330fc
&y = 0x7ffd4ab330f8
&p = 0x7ffd4ab330f0
&q = 0x7ffd4ab330e8
可以看到分配在堆区的q的地址在其他三个的低处，且距离较远。

3.6.5 静态区演示
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
#include <stdio.h>
// 数据区：data段
char m = 'a';
char n = 'a';

// 数据区：bss段
char arr1[10];

// 数据区：data段
char static MAX = 'a';
// 数据区：bss段
char static MIN;

void test() {
    // 栈区
    int x, y;
    
    // 常量区
    const int z = 10;
    
    // 数据区：data段
    static char a = 'a';
    // 数据区：bss段
    static char b;

    // "Hello World"在常量区
    // p在栈区
    const char *p = "Hello World";

    // "123456"和arr均在栈区，且地址相同
    char arr2[] = "123456";
}

int main(void) {
    test();
    return 0;
}
3.7 文件共享
如果两个独立进程各自打开了同一文件：



在完成每个write后，在文件表项（即类似于FILE的结构体）中的当前文件偏移量即增加所写入的字节数。如果这导致当前文件偏移量超出了当前文件长度，则将i节点表项中的当前文件长度设置为当前文件偏移量（也就是该文件加长了）。
如果用O_APPEND标志打开一个文件，则相应标志也被设置到文件表项的文件状态标志中。每次对这种具有追加写标志的文件执行写操作时，文件表项中的当前文件偏移量首先会被设置为i节点表项中的文件长度。这就使得每次写入的数据都追加到文件的当前尾端处。
若一个文件用lseek定位到文件当前的尾端，则文件表项中的当前文件偏移量被设置为i节点表项中的当前文件长度。lseek函数只修改文件表项中的当前文件偏移量，不进行任何I/O操作。
可能有多个文件描述符指向同一个文件表项（例如使用dup），对于多个进程读取同一文件都能正确工作。每个进程都有它自己的文件表项，其中也有它自己的当前文件偏移量。但是，当多个进程写同一文件时，则可能产生预想不到的结果。为了说明如何避免这种情况，需要理解原子操作的概念。

原子操作：不可分割的操作；

原子操作的作用：解决竞争和冲突；

3.8 dup和dup2
dup函数用于复制文件描述符，重定向输入输出。

C
1
2
3
#include <unistd.h>
int dup(int oldfd);
int dup2(int oldfd, int newfd);
返回值：

成功：
dup函数返回当前系统可用的最小整数值，并且该描述符对应的文件与参数描述符oldfd所对应的文件一致，即指向同一个结构体；
dup2函数返回第一个不小于newfd的整数值，分两种情况：
如果newfd已经打开，则先将其关闭，再指向文件描述符oldfd的结构体；
如果newfd等于oldfd，则什么也不做；
失败：dup和dup2函数均返回-1，并设置errno。
代码示例

需求：将puts重定向到一个文件中

方法1：
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <fcntl.h>

#define FNAME "/tmp/out"

int main(void) {
    int fd;
    close(1); // 关闭stdout，使描述符1空闲
    if((fd = open(FNAME, O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {
        perror("open()");
        exit(1);
    }

    puts("Hello World");

    exit(0);
}
结果：

BASH
1
2
3
[root@HongyiZeng sysio]# ./dup
[root@HongyiZeng sysio]# cat /tmp/out
Hello World
方法2：使用dup
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
#define FNAME "/tmp/out"

int main(void) {
    int fd;

    if((fd = open(FNAME, O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {
        perror("open()");
        exit(1);
    }
    // 关闭stdout
    close(1);
    // 复制fd，让其占据1的描述符
    dup(fd);
    // 关闭fd
    close(fd);

    puts("Hello World");

    exit(0);
}
图示：



注意结构体中有引用计数，当fd=3被关闭时，还有fd=1指向这个结构体，因此结构体不会被销毁掉。存在并发问题。

方法3：使用dup2
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
#define FNAME "/tmp/out"

int main(void) {
    int fd;

    if((fd = open(FNAME, O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {
        perror("open()");
        exit(1);
    }
    // 如果fd = 1，则什么也不做，返回fd
    // 如果fd != 1，则关闭1指向的结构体，再打开1，指向fd的结构体，返回1
    dup2(fd, 1);
    if(fd != 1) {
        close(fd);
    }
    puts("Hello World");

    exit(0);
}
dup2是一个原子操作，相当于：

C
1
2
3
4
dup2(fd, 1);
// 相当于:
close(1);
dup(fd);
3.9 fcntl和ioctl
fcntl针对文件描述符提供控制。

C
1
2
3
4
#include <unistd.h>
#include <fcntl.h>

int fcntl(int fd, int cmd, ... /* arg */ );
返回值：若成功，则依赖于cmd，若失败，则返回-1

函数功能：

复制一个已有的描述符（cmd=F_DUPFD或F_DUPFD_CLOEXEC）
获取/设置文件描述符标志（cmd=F_GETFD或F_SETFD）
获取/设置文件状态标志（cmd=F_GETFL或F_SETFL）
获取/设置异步I/O所有权（cmd=F_GETOWN或F_SETOWN）
获取/设置记录锁（cmd=F_GETLK、F_SETLK或F_SETLKW）
ioctl：用于控制设备

C
1
2
3
#include <sys/ioctl.h>

int ioctl(int d, int request, ...);
ioctl函数一直是IO操作的杂物箱。不能用本章中其他函数表示的I/O操作通常都能用ioctl表示。终端I/O是使用ioctl最多的地方。

3.10 /dev/fd目录
对于每个进程，内核都提供有一个特殊的虚拟目录/dev/fd。

该目录中包含/dev/fd/n形式的文件名，其中n是与进程中打开文件描述符相对应的编号。也就是说，/dev/fd/0就对应于进程的标志输入。

打开/dev/fd目录中的一个文件等同于复制对应的文件描述符，所以下面两行代码是等价的：

C
1
2
3
fd = open("/dev/fd/1", O_WRONLY);
// 等价于：
fd = dup(1);
3.11 补充：几个文件的区别
3.11.1 用户变量
~/.bashrc和~/.bash_file这两个看到~，应该明白，这是用户目录下的，即里面的环境变量也叫shell变量，是局部的，只对特定的shell有效，用vim在用户目录下的.bash_profile文件中增加变量，变量仅会对当前用户有效，并且是“永久的”。

要让刚才的修改马上生效，需要执行以下代码：

BASH
1
source ~/.bash_profile
两个的区别：.bash_profile只在会话开始时被读取一次，而.bashrc则每次打开新的终端时，都会被读取。

当shell是交互式登录shell时，读取.bash_profile文件，如在系统启动、远程登录或使用su -切换用户时；
当shell是交互式登录和非登录shell时都会读取.bashrc文件，如：在图形界面中打开新终端或使用su切换用户时，均属于非登录shell的情况。
3.11.2 全局变量
/etc/profile 和/etc/profile.d，前面的是文件，后面一看也就明白.d表示目录， /etc/profile里面的变量是全局的，对所有用户的shell有效。

用vim在文件/etc/profile文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。

要让刚才的修改马上生效，需要执行以下代码

BASH
1
source /etc/profile
4 文件系统
该节对应APUE的第四章——文件和目录，第六章——系统数据文件和信息

4.1 Linux文件目录构成
4.1.1 概述


树状目录结构：



目录解释：

/bin：bin 是 Binaries (二进制文件) 的缩写，这个目录存放着最经常使用的命令。
/boot：这里存放的是启动 Linux 时使用的一些核心文件，包括一些连接文件以及镜像文件。
/dev ：dev 是 Device(设备) 的缩写，该目录下存放的是 Linux 的外部设备，在 Linux 中访问设备的方式和访问文件的方式是相同的（一切皆文件）。
/etc：etc 是 Etcetera(等等) 的缩写，这个目录用来存放所有的系统管理所需要的配置文件和子目录。
/home：用户的主目录（家目录），在 Linux 中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的，如上图中的 alice、bob 和 eve。
/lib：lib 是 Library(库) 的缩写这个目录里存放着系统最基本的动态连接共享库，其作用类似于 Windows 里的 DLL 文件。几乎所有的应用程序都需要用到这些共享库。
/lost+found：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。
/media：linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。
/mnt：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了。
/opt：opt 是 optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。
/proc：proc 是 Processes(进程) 的缩写，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
/root：该目录为系统管理员，也称作超级权限者的用户主目录。
/sbin：s 就是 Super User 的意思，是 Superuser Binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序。
/tmp：tmp 是 temporary(临时) 的缩写这个目录是用来存放一些临时文件的。
/usr：usr 是 unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 C:/Windows/ 目录。
/usr/lib理解为C:/Windows/System32
/usr/local：用户级的程序目录，可以理解为C:/Progrem Files/。用户自己编译的软件默认会安装到这个目录下。
/opt：用户级的程序目录，可以理解为D:/Software，opt有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接rm -rf掉即可。
/var：var 是 variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。
/run：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。
切换用户

BASH
1
su 用户名
~代表当前登录用户的用户目录（家目录）。

如果当前的用户是root，则~代表/root：

BASH
1
2
3
4
5
[lighthouse@HongyiZeng ~]$ su root
Password: 
[root@HongyiZeng lighthouse]# cd ~
[root@HongyiZeng ~]# pwd
/root
如果当前的用户是其他用户，则~代表/home/用户名：

BASH
1
2
[lighthouse@HongyiZeng ~]$ pwd
/home/lighthouse
4.1.2 /etc/passwd
/etc/passwd为用户信息文件存放路径。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:998:997:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:997:995::/var/lib/chrony:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
syslog:x:996:994::/home/syslog:/bin/false
lighthouse:x:1000:1000::/home/lighthouse:/bin/bash
每一行分为7个字段，以冒号:进行分割，含义如下：

字段	含义
用户名	用户登录系统时使用的用户名
密码	密码位，通常将passwd文件中的口令字段使用一个x来代替，将/etc /shadow作为真正的口令文件
UID	用户标识号
GID	缺省组标识号
注释性描述	例如存放用户全名等信息
宿主目录	用户登录系统后的缺省目录
命令解释器	用户使用的shell，默认为bash
4.1.3 /etc/group
/ect/group 文件是用户组配置文件，即用户组的所有信息都存放在此文件中。只列出部分：

C
1
2
root:x:0:
lighthouse:x:1000:lighthouse
每一行分为4个字段，以冒号:进行分割，含义如下：

字段	含义
组名	用户组名称
组密码	和 /etc/passwd 文件一样，这里的 “x” 仅仅是密码标识
GID	组标识号
组中的用户	此字段列出每个群组包含的所有用户
4.1.4 /usr/include
linux系统编程往往需要引用c头文件，linux下，头文件一般存储到/usr/include：



补充 inode
① 简介
文件数据是储存在硬盘上的，硬盘的最小存储单位叫做扇区。每个扇区存储512字节，而连续的8个扇区组成了一个块（block），大小为4kB。文件数据都存储在块中，为了能够方便找到存储数据的位置我们还必须找到一个地方存储文件的属性，这种存储文具属性信息的区域叫做inode（索引节点）。

② inode的信息
inode（本质上是一个结构体）包含文件的属性信息有以下内容：注意，没有文件名和inode编号

文件的字节数
文件拥有者的id
文件所属组id
文件的读写执行权限
文件的时间戳，共有三个：
ctime指inode上一次变动的时间
mtime指文件内容上一次变动时间
atime指文件上一次打开时间。
硬链接数，即有多少个文件指向这个inode
文件数据块（block）的位置，即指向数据块的指针，包括一级指针，二级指针和三级指针，一般为15个指针

inode也会消耗硬盘空间，所以硬盘格式化的时候，操作系统自动将硬盘划分为两个区域。一个是数据区，存放文件数据，另一个是inode区（inode table），存放inode所包含的信息，inode区本质上是一个结构体数组，数组下标就是inode编号inode_num。



每个inode节点（结构体）的大小，一般是128字节或者256字节。inode节点的总数。在格式化时就给定，一般是每1kb或者每2kb就设置一个inode。假定在一块1GB的硬盘中，每个inode节点的大小就会达到128MB。

inode编号是很重要的，当系统在找一个文件时，步骤如下：

通过文件名先找到文件的inode编号（数组下标）
通过inode编号找到文件inode信息（结构体）
通过inode信息中的block指针找到文件内容。
③ 相关命令
stat显示文件的状态信息。stat命令的输出信息比ls命令的输出信息要更详细。

BASH
1
2
3
4
5
6
7
8
9
10
11
[root@HongyiZeng sysio]# ls
ab  ab.c  dup  dup.c  mycpy  mycpy.c  test
[root@HongyiZeng sysio]# stat ab.c
  文件: ‘ab.c’
  大小: 193             块: 8          IO 块: 4096   普通文件
设备: fd01h/64769d    Inode: 797269      硬链接: 1
权限: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
最近访问: 2022-12-02 16:05:47.044135336 +0800
最近修改: 2022-12-02 16:05:45.239158807 +0800
最近改动: 2022-12-02 16:05:45.245158729 +0800
 创建时间: -
ls -i用于查看某个文件的inode编号。

BASH
1
2
[root@HongyiZeng sysio]# ls -i ab.c
797269 ab.c
④ 硬链接
一般情况下，文件名和inode号码是一一对应关系，每个inode号码对应一个文件名，但是Linux系统允许多个文件名指向同一个inode号码，这就意味着，可以用不同的文件名访问同样的内容；对文件内容进行修改，会影响到所有文件名；但是删除一个文件名，不影响另一个文件名访问，相当于源文件的副本，这种情况叫做硬链接。

inode信息中有一项叫做链接数，记录指向该inode的文件总数，这时会加1，反过来，删除一个文件名，会减一，当链接数变为0时，表明没有文件指向这个inode号码，系统就会回收这个inode号码与文件数据块区。

⑤ 符号链接
文件A与文件B的号码虽然不一样，但是文件A的内容是文件B的路径，A就是B的软链接文件。读取文件A时，系统会自动访问导向文件B的文件名，然后再根据B的inode去访问存储在块中的数据。

而这意味着，文件A依赖于文件B而存在，若删除了文件B，打开文件A就会报错。这就是软连接与硬链接最大的不同：文件A指向文件B的文件名，而不是inode号码，文件B的inode链接数不会发生变化。

4.2 文件和目录
4.2.1 stat
系统调用stat用于获取文件的属性。

C
1
2
3
4
5
6
7
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>

int stat(const char *path, struct stat *buf);
int fstat(int fd, struct stat *buf);
int lstat(const char *path, struct stat *buf);
stat：第一个形参：文件路径； 第二个形参：一个指向结构体stat的指针，因此需要传入结构体的地址；
fstat：第一个形参是文件描述符；
lstat：lstat函数的形参跟stat函数的形参一样。其功能也跟stat函数功能一样，仅有一点不同：stat函数是穿透（追踪）函数，即对软链接文件进行操作时，操作的是链接到的那一个文件，不是软链接文件本身；而lstat函数是不穿透（不追踪）函数，对软链接文件进行操作时，操作的是软链接文件本身。注：软链接严格来说应该叫符号链接。
返回值：成功返回0，失败返回-1，并且将详细错误信息赋值给errno全局变量。
struct stat类型的说明：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
struct stat {
    dev_t st_dev;     /* 文件的设备编号 */
    ino_t st_ino;     /* 索引结点编号 */
    mode_t st_mode;    /* 文件类型和权限*/
    nlink_t st_nlink;   /*硬链接数 */
    uid_t st_uid;     /*用户ID*/
    gid_t st_gid;     /* 组ID*/
    dev_t st_rdev;    /* 设备类型（若此文件为设备文件，则为设备编号*/
    off_t st_size;    /* 文件大小，以字节为单位*/
    blksize_t st_blksize; /*文件系统的I/O块大小*/
    blkcnt_t st_blocks;  /* 块数 */
    time_t st_atime;   /* 访问时间 */
    time_t st_mtime;   /* 修改时间 */
    time_t st_ctime;   /* 更改时间 */
}; 
代码示例

打印出文件的大小。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>

// 注意返回值是off_t类型
static off_t flen(const char *fname) {
    struct stat statres; // 声明一个stat类型的结构体statres
    if(stat(fname, &statres) < 0) {
        perror("stat()");
        exit(1);
    }
	// 返回st_size成员
    return statres.st_size;
}

int main(int argc, char **argv) {
    if(argc < 2) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }

    printf("total size: %lld\n", flen(argv[1]));

    exit(0);
}
off_t类型用于指示文件的偏移量，通常就是long类型，其默认为一个32位的整数，在gcc编译中会被编译为long int类型，在64位的Linux系统中则会被编译为long long int，这是一个64位的整数，其定义在unistd.h头文件中可以查看。

C
1
2
3
4
5
6
7
8
9
10
11
12
# ifndef __off_t_defined
#  ifndef __USE_FILE_OFFSET64
typedef __off_t off_t;
#  else
typedef __off64_t off_t;
#  endif
#  define __off_t_defined
# endif
# if defined __USE_LARGEFILE64 && !defined __off64_t_defined
typedef __off64_t off64_t;
#  define __off64_t_defined
# endif
4.2.2 空洞文件
在描述文件属性的结构体stat中，有以下三个描述文件大小的成员：

C
1
2
3
4
5
struct stat {
    off_t st_size;    /* 文件大小，以字节为单位*/
    blksize_t st_blksize; /*文件系统的I/O块大小*/
    blkcnt_t st_blocks;  /* 块数 */
}; 
其中，块大小一般为4096字节，即4KB（一个块为连续8个扇区，每个扇区为512B）；块数为该文件的占用的块数；

注意：st_size ≠ st_blksize * st_blocks；或者说，st_size是文件的逻辑大小，而st_blksize * st_blocks是文件的物理大小；

代码示例

创建一个5GB大小的文件：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(int argc, char **argv) {
    int fd;

    if(argc < 2) {
        fprintf(stderr, "Usage...");
        exit(1);
    }

    if((fd = open(argv[1], O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {
        perror("open");
        exit(1);
    }
	// 先让指针从文件开头向后移动5G个字节
    lseek(fd, 5LL * 1024LL * 1024LL * 1024LL - 1LL, SEEK_SET);
	// 在最后写入一个空字符
    write(fd, "", 1);

    close(fd);

    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
[root@HongyiZeng fs]# make big
cc     big.c   -o big
[root@HongyiZeng fs]# ./big /tmp/bigfile
[root@HongyiZeng fs]# stat /tmp/bigfile 
  File: ‘/tmp/bigfile’
  Size: 5368709120      Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26199       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:19:40.734216438 +0800
Modify: 2022-12-05 16:19:40.734216438 +0800
Change: 2022-12-05 16:19:40.734216438 +0800
 Birth: -
可以看出，大小为5368709120B，但是占用的块数却为8，即实际占用的物理大小为4KB * 8 = 32KB。

如果将该文件进行拷贝，再查看拷贝文件的属性：

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
[root@HongyiZeng fs]# cp /tmp/bigfile /tmp/bigfile.bak
[root@HongyiZeng fs]# stat /tmp/bigfile.bak 
  File: ‘/tmp/bigfile.bak’
  Size: 5368709120      Blocks: 0           IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26200       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:26:39.166784814 +0800
Modify: 2022-12-05 16:26:39.166784814 +0800
Change: 2022-12-05 16:26:39.166784814 +0800
 Birth: -
使用 lseek 可以修改文件的当前读写位置偏移量，此函数不但可以改变位置偏移量，并且还允许文件偏移量超出文件长度，譬如有一个 test_file，该文件的大小是 4K（也就是 4096 个字节），通过 lseek 系统调用可以将该文件的读写偏移量移动到偏移文件头部 6000 个字节处。

接下来使用 write 函数对文件进行写入操作，也就是说此时将是从偏移文件头部 6000 个字节处开始写入数据，也就意味着 4096~6000 字节之间出现了一个空洞，因为这部分空间并没有写入任何数据，所以形成了空洞，这部分区域就被称为文件空洞，那么相应的该文件也被称为空洞文件。文件空洞部分实际上并不会占用任何物理空间，直到在某个时刻对空洞部分进行写入数据时才会为它分配对应的空间，但是空洞文件形成时，逻辑上该文件的大小是包含了空洞部分的大小的，这点需要注意。

空洞文件的作用

空洞文件的好处是：空洞文件对多线程共同操作文件是很有用的。

因为我们在创建一个很大文件的时候，我们就把一个文件分成很多的段，然后采用多线程的方式，让每个线程负责写入其中的某一段的数据。这样的话比我们用单个线程写入是快很多的。

例如：

在使用迅雷下载文件时，还未下载完成，就发现该文件已经占据了全部文件大小的空间，这也是空洞文件；下载时如果没有空洞文件，多线程下载时文件就只能从一个地方写入，这就不能发挥多线程的作用了；如果有了空洞文件，可以从不同的地址同时写入，就达到了多线程的优势；
在创建虚拟机时，你给虚拟机分配了 100G 的磁盘空间，但其实系统安装完成之后，开始也不过只用了 3、4G 的磁盘空间，如果一开始就把 100G 分配出去，资源是很大的浪费。
4.2.3 st_mode
① 简介
st_mode是一个16位的位图，用于表示文件类型，文件访问权限以及特殊权限位。

它的类型为mode_t，其实就是普通的unsigned int，但是只是用了低16位。



② 实例分析
假设st_mode表示为八进制的100664：



则有：

1000: 这是一个常规文件
000: 执行时设置信息为空，黏着位为 0
110-110-100: 用户权限为 RW-，组员权限为RW-，其他人权限为R--
③ 宏
通过手工分析 st_mode 字段，实际上是很不方便的。实际写程序，可使用 st_mode & 掩码来得到 st_mode 中特定的部分。比如：

PLAINTEXT
1
2
3
4
5
st_mode & 0170000 : 得到文件类型
st_mode & 0007000 : 得到执行文件时设置信息
st_mode & 0000777 : 得到权限位
st_mode & 00100: 判断所有者是否可执行
(以上数字均为八进制)
为了方便使用，用 linux 预定义的一些宏来代替这些生硬的数字。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
//bit15 ~ bit12 , 文件类型属性区域
#define  S_IFMT      0170000     文件类型的位遮罩
#define  S_IFSOCK    0140000     socket
#define  S_IFLNK     0120000     符号链接(symbolic link)
#define  S_IFREG     0100000     一般文件
#define  S_IFBLK     0060000     区块装置(block device)
#define  S_IFDIR     0040000     目录
#define  S_IFCHR     0020000     字符装置(character device)
#define  S_IFIFO     0010000     先进先出(fifo)

#define S_ISSOCK(m) (((m) & S_IFMT) == S_IFSOCK)  //提供了一些宏函数来帮助用户执行&操作，是则返回1
#define S_ISLNK(m)  (((m) & S_IFMT) == S_IFLNK)  
#define S_ISREG(m)  (((m) & S_IFMT) == S_IFREG)
#define S_ISBLK(m)  (((m) & S_IFMT) == S_IFBLK)
#define S_ISDIR(m)  (((m) & S_IFMT) == S_IFDIR)
#define S_ISCHR(m)  (((m) & S_IFMT) == S_IFCHR)
#define S_ISFIFO(m) (((m) & S_IFMT) == S_IFIFO)

//bit11 ~ bit9，权限的特殊属性区域
#define  S_ISUID      0004000     文件的(set user-id on execution)位
#define  S_ISGID      0002000     文件的(set group-id on execution)位
#define  S_ISVTX      0001000     文件的sticky位

//bit8 ~ bit0，权限属性区域
//文件所有者（owner）
#define S_IRWXU 00700	/* mask for file owner permissions */
#define S_IRUSR 00400	/* owner has read permission */
#define S_IWUSR 00200	/* owner has write permission */
#define S_IXUSR 00100	/* owner has execute permission */
 //组用户（group）
#define S_IRWXG 00070	/* mask for group permissions */
#define S_IRGRP 00040	/* group has read permission */
#define S_IWGRP 00020	/* group has write permission */
#define S_IXGRP 00010	/* group has execute permission */
 //其他用户（other）
#define S_IRWXO 00007	/* mask for permissions for others (not in group) */
#define S_IROTH 00004	/* others have read permission */
#define S_IWOTH 00002	/* others have write permission */
#define S_IXOTH 00001	/* others have execute permission */
④ 代码示例
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>

static int ftype(const char *fname) {
    struct stat statres;

    if(stat(fname, &statres) < 0) {
        perror("stat()");
        exit(1);
    }

    if(S_ISREG(statres.st_mode))
        return '-';
    else if(S_ISDIR(statres.st_mode))
        return 'd';
    else if(S_ISSOCK(statres.st_mode))
        return 's';
    else
        return '?';
}

static int fper(const char *fname) {
    struct stat statres;

    if(stat(fname, &statres)) {
        perror("fper()");
        exit(1);
    }

    return statres.st_mode & S_IRWXU;
}


int main(int argc, char **argv) {
    if(argc < 2) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }

    printf("File type: %c\n", ftype(argv[1]));
	printf("Permission of owner: %o\n", fper(argv[1]));
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
[root@HongyiZeng fs]# ./ftype ftype.c 
File type: -
Permission of owner: 600
[root@HongyiZeng fs]# ./ftype ftype
File type: -
Permission of owner: 700
4.2.4 文件权限
① umask
umask函数原型：

C
1
2
#include <sys/stat.h>
mode_t umask(mode_t mask);
在进程创建一个新的文件或目录时，如调用open函数创建一个新文件，新文件的实际存取权限是mode与umask按照 mode & ~umask运算以后的结果。umask函数用来修改进程的umask，作用是防止出现权限过松的文件。

② chmod
补充：chmod命令

语法：

PLAINTEXT
1
chmod [对谁操作(ugoa)] [操作符 (+-=)] [赋予的权限(rwxs或数字)] 文件名1 文件名2...


例如：

BASH
1
2
3
4
5
[root@HongyiZeng fs]# ll big.c
-rw-r--r-- 1 root root 420 Dec  5 16:19 big.c
[root@HongyiZeng fs]# chmod 664 big.c
[root@HongyiZeng fs]# ll big.c
-rw-rw-r-- 1 root root 420 Dec  5 16:19 big.c
BASH
1
2
3
4
5
6
7
8
[root@HongyiZeng fs]# ll big.c
-rw-rw-r-- 1 root root 420 Dec  5 16:19 big.c
[root@HongyiZeng fs]# chmod a+x big.c
[root@HongyiZeng fs]# ll big.c
-rwxrwxr-x 1 root root 420 Dec  5 16:19 big.c
[root@HongyiZeng fs]# chmod o-x big.c
[root@HongyiZeng fs]# ll big.c
-rwxrwxr-- 1 root root 420 Dec  5 16:19 big.c
chmod函数原型：

C
1
2
3
#include <sys/stat.h>
int chmod(const char *pathname, mode_t mode);
int fchmod(int fd, mode_t mode);
chmod是对指定的文件进行操作，而fchmod则是对已经打开的文件进行操作。所以它们的第一个参数不一样。

返回值：如果改变成功返回0，否则返回-1

代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
 
int main()
{
    int fd = open("a", O_RDWR);
    if (-1 == fd)
    {
       perror("open fail");
       exit(1);
    }
 	// 将fd的权限更改为0777
    if(-1 == fchmod(fd, 0777))
    {
        perror("fchmod fail");
        exit(1);
    }
 	
    // 将b文件的权限更改为0777
    if (-1 == chmod("b", 0777))
    {
       perror("fchmod fail");
       exit(1);
    }
    
    close(fd);
    return 0;
}
③ 粘住位
在UNIX尚未使用分页技术的早期版本中，S_ISVTX位被称为粘住位（sticky bit）。

如果一个可执行程序文件的这一位被设置了，那么在该程序第一次被执行并结束时，其程序正文部分的一个副本仍被保存在交换区，（程序的正文部分是机器指令部分）。这使得下次执行该程序时能较快地将其装入内存中。其原因是：交换区占用连续磁盘空间，可将它视为连续文件，而且一个程序的正文部分在交换区中也是连续存放的，而在一般的UNIX文件系统中，文件的各数据块很可能是随机存放的。

对于常用的应用程序，例如文本编辑器和C编译器，我们常常设置它们所在文件的粘住位。自然，对于在交换区中可以同时存放的设置了粘住位的文件数是有一定限制的，以免过多占用交换区空间，但无论如何这是一个有用的技术。后来的UNIX版本称它为保存正文位（saved-text bit），因此也就有了常量S_ISVTX。现今较新的UNIX系统大多数都配置有虚拟存储系统以及快速文件系统，所以不再需要使用这种技术。

现今的系统扩展了粘住位的使用范围，允许针对目录设置粘住位。如果对一个目录设置了粘住位，则只有对该目录具有写权限的用户在满足下列之一的情况下，才能删除或更名该目录下的文件或目录：

拥有此文件
拥有此目录
是超级用户
目录/tmp是设置粘住位的典型候选者：任何用户都可在这个目录中创建文件。任一用户（用户、组和其他）对这个目录的权限通常都是读、写和执行（rwx）。但是用户不应能删除或更名属于其他人的文件，为此在这个目录的文件模式中都设置了粘住位。

即：假如/tmp下的文件A被用户U1所有，文件A的权限为777，那么所有用户都可以对该文件进行修改、移动、重命名等操作，但无法删除该文件。通常的用途在于用户团队协作的目录，用户可以相互修改文件，却只有用户所有者才能删除。



图中最后一位t就是粘住位。

4.2.5 文件系统
① 磁盘的结构


磁盘

一个磁盘（如一个 1T 的机械硬盘）由多个盘片（如下图中的 0 号盘片）叠加而成。

盘片的表面涂有磁性物质，这些磁性物质用来记录二进制数据。因为正反两面都可涂上磁性物质，故一个盘片可能会有两个盘面。



磁道和扇区

每个盘片被划分为一个个磁道（一个一个半径不同的同心圆环），每个磁道又划分为一个个扇区（磁道上的一个弧段）。扇区是磁盘的最小组成单元，通常是512字节。如下图：



其中，最内侧磁道上的扇区面积最小，因此数据密度最大。

柱面

每个盘面对应一个磁头。所有的磁头都是连在同一个磁臂上的，因此所有磁头只能“共进退”。

所有盘面中半径相同的磁道组成柱面。如下图：



磁盘容量计算

存储容量 ＝ 磁头数 × 磁道(柱面)数 × 每道扇区数 × 每扇区字节数

磁盘的物理地址

由上，可用（柱面号，盘面号，扇区号）来定位任意一个“磁盘块”，这里的“磁盘块”，实质上就是一个扇区。

可根据该地址读取一个“块”，操作如下：

根据“柱面号”前后移动磁臂，让磁头指向指定柱面；
旋转磁盘，让磁头抵达待读的起始扇区。
激活指定盘面对应的磁头；
旋转磁盘，指定的扇区会从磁头下面划过，这样就完成了对指定扇区的读/写。
块和簇

磁盘块/簇（虚拟出来的）。 块是操作系统中最小的逻辑存储单位。操作系统与磁盘打交道的最小单位是磁盘块。

在Windows下如NTFS等文件系统中叫做簇；

在Linux下如Ext4等文件系统中叫做块（block）。一般来说，一个块（block）包含连续的8个扇区，每个扇区512B，因此一个块大小为4096KB。

每个簇或者块可以包括2、4、8、16、32、64…2的n次方个扇区。

② 文件系统简介
文件系统：文件或数据的存储和管理。目前，正在使用的UNIX文件系统有多种实现。

传统的基于BSD的UNIX文件系统（称为UFS）。UFS是以Berkeley快速文件系统为基础的。本节讨论该文件系统。
读、写DOS格式软盘的文件系统（称为PCFS）
读CD的文件系统（称为HSFS）
我们可以把一个磁盘分成一个或多个分区。每个分区可以包含一个文件系统。



i节点即为inode结构体的数组
数据块一般被分成了大小为4KB的块（block）
i节点图（i节点位图）：用来判断inode的空闲与占用情况
块位图：用来判断数据块的占用与空闲情况
对于普通文件，如下图：



i节点结构体中通常包含15个指针来指向数据块，最后三个指针为一二三级指针，用于扩充文件的大小；图中的i节点指向了三个数据块。
在图中有两个目录项（两个不同文件名的文件，但是inode编号相同）指向同一个i节点（此时称为硬链接，即目录项就是硬链接的同义词）。每个i节点中都有一个硬链接计数st_nlink，其值是指向该i节点的目录项数。只有当链接计数减少至0时，才可删除该文件（也就是可以释放该文件占用的数据块）。
i节点包含了文件有关的所有信息∶文件类型、文件访问权限位、文件长度和指向文件数据块的指针等。stat 结构中的大多数信息都取自i节点。只有两项重要数据存放在目录项中∶文件名和i节点编号。
对于目录，目录也是一种文件，它的属性也需要inode结构体存储，它的物理存储也需要通过inode中的指针来指向的数据块（此时的数据块就是目录块）来存储；

目录块存储的内容非常的简单，由目录项组成，每条目录项有包含的文件名以及该文件名对应的inode编号。

如下图所示：



编号为2549的i节点（testdir），其类型字段st_mode表示它是一个目录（因此它指向一个特殊的数据块——目录块），链接计数为2。任何一个叶目录（不包含任何其他目录的目录）的链接计数总是2，数值2来自于命名该目录（testdir）的目录项以及在该目录中的.项。
编号为1267的i节点，其类型字段st_mode表示它是一个目录，链接计数大于或等于3。它大于或等于3的原因是，至少有3个目录项指向它∶一个是命名它的目录项，第二个是在该目录中的.项，第三个是在其子目录 testdir 中的..项。注意，在父目录中的每一个子目录都使该父目录的链接计数增加1。
③ 链接
链接分为硬链接和符号链接（注意不是软链接）。

创建链接的命令：

BASH
1
ln src dest # 创建src的硬链接为dest
BASH
1
ln -s src dest # 创建src的符号链接为dest
硬链接对比：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
[root@HongyiZeng tmp]# ln bigfile bigfile_link
[root@HongyiZeng tmp]# stat bigfile
  File: ‘bigfile’
  Size: 5368709120      Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26199       Links: 2
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:26:39.166784814 +0800
Modify: 2022-12-05 16:19:40.734216438 +0800
Change: 2022-12-05 20:29:33.176048702 +0800
 Birth: -
[root@HongyiZeng tmp]# stat bigfile_link 
  File: ‘bigfile_link’
  Size: 5368709120      Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26199       Links: 2
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:26:39.166784814 +0800
Modify: 2022-12-05 16:19:40.734216438 +0800
Change: 2022-12-05 20:29:33.176048702 +0800
 Birth: -
字段Link即为文件的硬链接数，硬链接数为2，说明有两个目录项指向了这个inode，并且注意两个文件的inode编号相同，说明同时指向了这个inode；

将源文件bigfile删除后，bigfile_link仍然存在：

BASH
1
2
3
4
5
6
7
8
9
10
[root@HongyiZeng tmp]# rm -rf bigfile
[root@HongyiZeng tmp]# stat bigfile_link 
  File: ‘bigfile_link’
  Size: 5368709120      Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26199       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:26:39.166784814 +0800
Modify: 2022-12-05 16:19:40.734216438 +0800
Change: 2022-12-05 20:33:03.459331364 +0800
 Birth: -
此时硬链接数Link变为1；

符号链接对比：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
[root@HongyiZeng tmp]# ln -s bigfile_link bigfile_s
[root@HongyiZeng tmp]# stat bigfile_link 
  File: ‘bigfile_link’
  Size: 5368709120      Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d    Inode: 26199       Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 16:26:39.166784814 +0800
Modify: 2022-12-05 16:19:40.734216438 +0800
Change: 2022-12-05 20:33:03.459331364 +0800
 Birth: -
[root@HongyiZeng tmp]# stat bigfile_s
  File: ‘bigfile_s’ -> ‘bigfile_link’
  Size: 12              Blocks: 0          IO Block: 4096   symbolic link
Device: fd01h/64769d    Inode: 26201       Links: 1
Access: (0777/lrwxrwxrwx)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2022-12-05 20:34:11.189456100 +0800
Modify: 2022-12-05 20:34:11.189456100 +0800
Change: 2022-12-05 20:34:11.189456100 +0800
 Birth: -
[root@HongyiZeng tmp]# ll bigfile_s
lrwxrwxrwx 1 root root 12 Dec  5 20:34 bigfile_s -> bigfile_link
可以看到硬链接数并未改变；此外符号链接文件的大小为12字节，物理占用的块甚至为0；文件类型标识也变为了symbolic link和l；

删除原始文件后，发现符号链接文件的链接已成非法链接（红色部分）：



相关的系统调用：

link 函数专门用来创建硬链接的，功能和 ln 命令一样。

C
1
2
#include <unistd.h>
int link(const char *oldpath, const char *newpath);
unlink函数删除一个文件的目录项并减少它的链接数，若成功则返回0，否则返回-1，错误原因存于error。

C
1
2
#include <unistd.h>
int unlink(const char *pathname)
对比：硬链接不能给分区建立，不能给目录建立，而符号链接可以。

4.2.6 杂项
文件的删除、重命名和移动

remove
C
1
2
#include <stdio.h>
int remove(const char *pathname);
rename：是mv命令的系统调用
C
1
2
#include <stdio.h>
int rename(const char *oldpath, const char *newpath);
更改文件的时间

utime：可更改文件的最后读的时间和最后修改的时间
C
1
2
3
#include <sys/types.h>
#include <utime.h>
int utime(const char *filename, const struct utimbuf *times);
更改当前工作路径

chdir：是cd命令的系统调用
C
1
2
3
#include <unistd.h>
int chdir(const char *path);
int fchdir(int fd);
getcwd：是pwd命令的系统调用
C
1
2
3
4
#include <unistd.h>
char *getcwd(char *buf, size_t size);
char *getwd(char *buf);
char *get_current_dir_name(void);
4.2.7 glob
该节内容为分析目录和读取目录内容。

glob函数原型（模型匹配函数）：

C
1
2
3
4
5
6
#include <glob.h>
int glob(const char *pattern, int flags,
         int (*errfunc) (const char *epath, int eerrno),
         glob_t *pglob);
// 释放glob函数调用的空间
void globfree(glob_t *pglob);
pattern： 通配符，要分析的pattern，如/*表示匹配根文件下的所有文件（不包括隐藏文件）
flags：flags参数可以设置特殊要求，如无特殊要求置为0
errfunc：函数指针，glob函数执行出错会执行的函数，出错的路径会回填到epath中，出错的原因回填到eerrno中。如不关注错误可设置为NULL
pglob：解析出来的结果放在这个参数里，是一个结构体指针
返回值：成功返回0，错误返回非0
其中，glob_t是一个结构体：

C
1
2
3
4
5
6
typedef struct {
    // pathc与pathv类似于main函数参数argc与argv
    size_t    gl_pathc;    //匹配到的数量
    char    **gl_pathv;    //匹配到的元素放在这里
    size_t    gl_offs; 
} glob_t;
程序实例

打印出/etc/*.conf的文件名。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
#include <stdio.h>
#include <stdlib.h>
#include <glob.h>
// 通配符
#define PAT "//etc//*.conf"

static int errfunc(const char *epath, int eerrno) {
    puts(epath);
    fprintf(stderr, "ERROR: %d", eerrno);
    return 1;
}

int main() {
    glob_t globres;
    int err;
    err = glob(PAT, 0, errfunc, &globres);

    if(err) {
        printf("ERROR CODE = %d\n", err);
        exit(1);
    }

    int i;

    for(i = 0; i < globres.gl_pathc; i++) {
        puts(globres.gl_pathv[i]);
    }
    // 释放空间
	globfree(&globres);
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
[root@HongyiZeng fs]# ./glob 
/etc/GeoIP.conf
/etc/asound.conf
/etc/chrony.conf
/etc/dat.conf
其余略
一组对目录操作的函数

和对文件操作的函数基于FILE结构体类似，对目录的操作基于名为DIR的结构体。

下面是常用的对目录进行操作的函数，他们的功能可以被glob替代。

暂略

5 进程环境
本节对应第七章——进程环境；

5.1 main函数
C程序总是从main函数开始执行，从main函数结束执行。即main是程序的入口和出口。

当内核执行C程序时（使用一个exec函数），在调用main前先调用一个特殊的启动例程。可执行程序文件将此启动例程指定为程序的起始地址———这是由连接编辑器设置的，而连接编辑器则由C编译器调用。

启动例程从内核取得命令行参数和环境变量值，然后为按上述方式调用main函数做好安排。

5.2 进程终止
5.2.1 终止方式
共有8种方式让进程终止。其中5种为正常退出：

从main返回
调用exit（C库函数）
调用_exit或_Exit（系统调用）
最后一个线程从其启动例程返回
从最后一个线程调用pthread_exit
异常终止有3种方式：

调用abort
接到一个信号
最后一个线程对取消请求做出响应
5.2.2 main函数的返回值
main函数的返回值给main函数的父进程。

假设一个程序名为main_test，则在终端上执行该程序时，父进程为shell：

BASH
1
./main_test
可通过$?（表示显示最后命令的退出状态）查看该返回值。

C
1
2
3
4
5
6
7
#include <stdio.h>
#include <stdlib.h>

int main() {
    printf("Hello World!\n");
    return 0;
}
执行结果：

BASH
1
2
3
4
[root@HongyiZeng fs]# ./main1 
Hello World!
[root@HongyiZeng fs]# echo $?
0
如果将程序更改为：

C
1
2
3
4
5
6
#include <stdio.h>
#include <stdlib.h>

int main() {
    printf("Hello World!\n");
}
则执行结果为：

BASH
1
2
3
4
[root@HongyiZeng fs]# ./main1 
Hello World!
[root@HongyiZeng fs]# echo $?
13
此时会将printf的返回值（打印字符的个数）作为main函数的返回值给父进程shell。

5.2.3 钩子函数atexit
按照ISO C的规定，一个进程可以登记多至32个函数，这些函数将由exit自动调用。我们称这些函数为终止处理程序（exit handler），并调用 atexit 函数来登记这些函数。

C
1
int atexit(void (*func)(void));
atexit当程序正常终止时，调用指定的函数（终止处理程序） func。可以在任何地方注册终止函数，但它会在程序终止的时候被调用。先注册的后调用。

func — 在程序终止时被调用的函数，该函数无参且无返回值，它是一个函数指针，因此传入的参数应该是一个函数的地址，即函数名（函数名就是函数的首地址）。
如果函数成功注册，则该函数返回零，否则返回一个非零值。
程序实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
#include <stdio.h>
#include <stdlib.h>

// 终止处理程序
static void f1() {
    puts("f1() is working!");
}

// 终止处理程序
static void f2() {
    puts("f2() is working!");
}

// 终止处理程序
static void f3() {
    puts("f3() is working!");
}

int main() {
    puts("Begin");
    // 先注册的后被调用
    // 钩子函数的书写顺序并不是实际执行顺序，atexit会在程序终止时被调用
    // atexit参数用指针来接收，因此需要传入地址，而函数名就是函数的地址
    atexit(f1);
    atexit(f2);
    atexit(f3);

    puts("End");
    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
4
5
Begin
End
f3() is working!
f2() is working!
f1() is working!
5.2.4 exit和_exit
exit是库函数，而_exit是系统调用，前者使用了后者。

除此之外，_exit()执行后会立即返回给内核，而exit()要先执行一些清除和终止操作，然后将控制权交给内核。



5.3 命令行参数
暂略

5.4 环境变量
5.4.1 简介
环境变量的含义：程序（操作系统命令和应用程序）的执行都需要运行环境，这个环境是由多个环境变量组成的。

按变量的周期划为永久变量和临时性变量2种：

永久变量：通过修改配置文件，配置之后变量永久生效。
临时性变量：使用命令如export等命令设置，设置之后马上生效。当关闭shell的时候失效（这种主要用于测试比较多）。
按照影响范围分为用户变量和系统变量2种：

用户变量（局部变量）：修改的设置只对某个用户的路径或执行起作用；
系统变量（全局变量）：影响范围是整个系统；
环境变量本质上是一个kv键值对。

5.4.2 查看环境变量
在Shell下，用env命令查看当前用户全部的环境变量。

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
[root@HongyiZeng fs]# env
XDG_SESSION_ID=139874
HOSTNAME=HongyiZeng
TERM=xterm-256color
SHELL=/bin/bash
HISTSIZE=3000
SSH_TTY=/dev/pts/0
USER=root
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/local/java/jdk1.8.0_161/bin:/root/bin:/root/bin
PWD=/usr/local/linux_c/fs
JAVA_HOME=/usr/local/java/jdk1.8.0_161
LANG=en_US.UTF-8
NEXUS_HOME=/usr/local/nexus/nexus-3.20.1-01
SHLVL=1
HOME=/root
LOGNAME=root
CLASSPATH=/usr/local/java/jdk1.8.0_161/lib/
SSH_CONNECTION=81.69.102.136 35390 10.0.24.5 22
LESSOPEN=||/usr/bin/lesspipe.sh %s
PROMPT_COMMAND=history -a; history -a; printf "\033]0;%s@%s:%s\007" "${USER}" "${HOSTNAME%%.*}" "${PWD/#$HOME/~}"
XDG_RUNTIME_DIR=/run/user/0
HISTTIMEFORMAT=%F %T 
_=/usr/bin/env
OLDPWD=/usr/local/linux_c
export命令显示当前系统定义的所有环境变量。

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
[root@HongyiZeng fs]# export
declare -x CLASSPATH="/usr/local/java/jdk1.8.0_161/lib/"
declare -x HISTSIZE="3000"
declare -x HISTTIMEFORMAT="%F %T "
declare -x HOME="/root"
declare -x HOSTNAME="HongyiZeng"
declare -x JAVA_HOME="/usr/local/java/jdk1.8.0_161"
declare -x LANG="en_US.UTF-8"
declare -x LESSOPEN="||/usr/bin/lesspipe.sh %s"
declare -x LOGNAME="root"
declare -x MAIL="/var/spool/mail/root"
declare -x NEXUS_HOME="/usr/local/nexus/nexus-3.20.1-01"
declare -x OLDPWD="/usr/local/linux_c"
declare -x PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/local/java/jdk1.8.0_161/bin:/root/bin:/root/bin"
declare -x PROMPT_COMMAND="history -a; history -a; printf \"\\033]0;%s@%s:%s\\007\" \"\${USER}\" \"\${HOSTNAME%%.*}\" \"\${PWD/#\$HOME/~}\""
declare -x PWD="/usr/local/linux_c/fs"
declare -x SHELL="/bin/bash"
declare -x SHLVL="1"
declare -x SSH_CLIENT="81.69.102.136 35390 22"
declare -x SSH_CONNECTION="81.69.102.136 35390 10.0.24.5 22"
declare -x SSH_TTY="/dev/pts/0"
declare -x TERM="xterm-256color"
declare -x USER="root"
declare -x XDG_RUNTIME_DIR="/run/user/0"
declare -x XDG_SESSION_ID="139874"
查看某个环境变量的值：

BASH
1
echo $KEY
例如，查看PATH的值：

BASH
1
2
[root@HongyiZeng fs]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/usr/local/java/jdk1.8.0_161/bin:/root/bin:/root/bin
5.4.3 设置环境变量
在用户的家目录/home/用户名下，有几个特别的文件：

.bash_profile（推荐首选）：当用户登录时执行，每个用户都可以使用该文件来配置专属于自己的环境变量。
.bashrc：当用户登录时以及每次打开新的Shell时该文件都将被读取，不推荐在里面配置用户专用的环境变量，因为每开一个Shell，该文件都会被读取一次，效率肯定受影响。
.bash_logout：当每次退出系统（退出bash shell）时执行该文件。
.bash_history：保存了当前用户使用过的历史命令
5.4.4 环境表
每个程序都接收到一张环境表。与参数表一样，环境表也是一个字符指针数组，其中每个指针包含一个以null结束的C字符串的地址。

全局变量environ则包含了该指针数组的地址∶

C
1
extern char **environ;
例如，如果该环境包含5个字符串，那么它看起来如图中所示。其中，每个字符串的结尾处都显式地有一个null字节。我们称 environ 为环境指针，指针数组为环境表，其中各指针指向的字符串为环境字符串。



代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
#include <stdio.h>
#include <stdlib.h>

// 引用声明外部的变量
extern char **environ;

int main(void) {
    int i;
    for(i = 0; environ[i] != NULL; i++) {
        puts(environ[i]);
    }
    exit(0);
}
相关库函数：

C
1
2
3
4
#include <stdlib.h>

// 根据环境变量的键name来获取环境变量的值
char *getenv(const char *name);
C
1
2
3
4
5
6
7
8
#include <stdlib.h>

// 键name，值value，overwrite是否覆盖
// 当name存在时，且overwrite为1，表示改变环境变量
int setenv(const char *name, const char *value, int overwrite);

// 根据key删除环境变量
int unsetenv(const char *name);
程序实例

C
1
2
3
4
5
6
7
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    puts(getenv("PATH"));
    exit(0);
}
5.5 共享库
共享库使得可执行文件中不再需要包含公用的库函数，而只需在所有进程都可引用的存储区中保存这种库例程的一个副本。

程序第一次执行或者第一次调用某个库函数时，用动态链接方法将程序与共享库函数相链接。这减少了每个可执行文件的长度，但增加了一些运行时间开销。这种时间开销发生在该程序第一次被执行时，或者每个共享库函数第一次被调用时。

共享库的另一个优点是可以用库函数的新版本代替老版本而无需对使用该库的程序重新连接编辑（假定参数的数目和类型都没有发生改变）。

动态库的相关库函数

C
1
2
3
4
5
6
7
8
9
10
11
12
13
// dladdr,  dlclose,  dlerror,  dlopen,  dlsym, dlvsym - programming interface to dynamic linking loader
#include <dlfcn.h>
// 该函数将打开一个新库，并把它装入内存。该函数主要用来加载库中的符号，这些符号在编译的时候是不知道的。这种机制使得在系统中添加或者删除一个模块时，都不需要重新进行编译
void *dlopen(const char *filename, int flag);

// 返回一个描述最后一次调用dlopen、dlsym，或dlclose的错误信息的字符串
char *dlerror(void);

// 在打开的动态库中查找符号的值
void *dlsym(void *handle, const char *symbol);

// 关闭动态库
int dlclose(void *handle);
5.6 函数间跳转
补充：goto语句

C 语言中的 goto 语句允许把控制无条件转移到同一函数内的被标记的语句。

语法：

C
1
2
3
4
goto label;
..
.
label: statement;
在这里，label 可以是任何除 C 关键字以外的纯文本，它可以设置在 C 程序中 goto 语句的前面或者后面。



代码示例：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
int main() {
   /* 局部变量定义 */
   int a = 10;
 
   /* do 循环执行 */
   LOOP:
   do {
      if( a == 15) {
         /* 跳过迭代 */
         a = a + 1;
         goto LOOP;
      }
      printf("a 的值： %d\n", a);
      a++;
   } while(a < 20);
 
   return 0;
}
执行结果：

PLAINTEXT
1
2
3
4
5
6
7
8
9
a 的值： 10
a 的值： 11
a 的值： 12
a 的值： 13
a 的值： 14
a 的值： 16
a 的值： 17
a 的值： 18
a 的值： 19
setjmp和longjmp可以实现非局部控制转移，即从一个函数到另外一个函数的跳转。

函数原型：

C
1
2
3
#include <setjmp.h>
int setjmp(jmp_buf buf);
void longjmp(jmp_buf env, int val);
setjmp函数用于记录当前位置，保存调用函数的栈环境在结构体jmp_buf buf（相当于保护现场）。函数输入参数为jmp_buf类型（这个结构体类似于goto的跳转标识），返回整型。当第一次调用时（设置跳转点），它的值为0；当第二次调用时（从别处跳转回来，即调用longjmp时）返回非零值；总之执行一次，返回两次，因此，setjmp函数后常常跟上分支语句。

longjmp的作用是使用setjmp保存在buf中的栈环境信息返回到setjmp的位置，也就是当执行longjmp时程序又回到setjmp处（相当于恢复现场）。形参val是调用longjmp时setjmp函数返回的值，为非零值，如果故意设置为0，也会被修改为1；

程序实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
#include <stdio.h>
#include <stdlib.h>

static void d(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}

static void c(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call d().\n", __FUNCTION__);
    d();
    printf("%s():d() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}


static void b(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call c().\n", __FUNCTION__);
    c();
    printf("%s():c() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}


static void a(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call b().\n", __FUNCTION__);
    b();
    printf("%s():b() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}

int main(void) {

    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call a().\n", __FUNCTION__);
    a();
    printf("%s():a() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
main():Begin.
main():Call a().
a():Begin.
a():Call b().
b():Begin.
b():Call c().
c():Begin.
c():Call d().
d():Begin.
d():End.
c():d() returned.
c():End.
b():c() returned.
b():End.
a():b() returned.
a():End.
main():a() returned.
main():End.
注：ANSI C 定义了许多宏。在编程中可以使用这些宏，但是不能直接修改这些预定义的宏。

例如：

PLAINTEXT
1
2
3
4
5
__DATE__ 当前日期，一个以 “MMM DD YYYY” 格式表示的字符串常量。
__TIME__ 当前时间，一个以 “HH:MM:SS” 格式表示的字符串常量。
__FILE__ 这会包含当前文件名，一个字符串常量。
__LINE__ 这会包含当前行号，一个十进制常量。
__FUNCTION__ 程序预编译时预编译器将用所在的函数名，返回值是字符串;
现在改写程序，在函数a进行setjmp，函数d进行longjmp：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
#include <stdio.h>
#include <stdlib.h>
#include <setjmp.h>

// 跳转点的现场环境
static jmp_buf save;

static void d(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Jump now!.\n", __FUNCTION__);
    // 向save跳转，并携带返回值为6
    longjmp(save, 6);
    printf("%s():End.\n", __FUNCTION__);
}

static void c(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call d().\n", __FUNCTION__);
    d();
    printf("%s():d() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}


static void b(void) {
    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call c().\n", __FUNCTION__);
    c();
    printf("%s():c() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
}


static void a(void) {
    // 返回值
    int ret;
    printf("%s():Begin.\n", __FUNCTION__);
    // 设置跳转点
    // setjmp一次调用，两次返回
    ret = setjmp(save);
    if(ret == 0) {
        printf("%s():Call b().\n", __FUNCTION__);
        b();
        printf("%s():b() returned.\n", __FUNCTION__);
    } else {
        printf("%s():Jumped back here with code %d.\n", __FUNCTION__, ret);
    }
    printf("%s():End.\n", __FUNCTION__);
}

int main(void) {

    printf("%s():Begin.\n", __FUNCTION__);
    printf("%s():Call a().\n", __FUNCTION__);
    a();
    printf("%s():a() returned.\n", __FUNCTION__);
    printf("%s():End.\n", __FUNCTION__);
    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
main():Begin.
main():Call a().
a():Begin.
a():Call b().
b():Begin.
b():Call c().
c():Begin.
c():Call d().
d():Begin.
d():Jump now!.
a():Jumped back here with code 6.
a():End.
main():a() returned.
main():End.
5.7 资源的获取和控制
获取或设置资源使用限制：linux下每种资源都有相关的软硬限制，软限制是内核强加给相应资源的限制值，硬限制是软限制的最大值。

非授权调用的进程只能将其软限制指定为0~硬限制范围中的某个值，同时能不可逆转地降低其硬限制。

授权进程（root用户）可以任意改变其软硬限制。

函数原型：

C
1
2
3
4
5
#include <sys/time.h>
#include <sys/resource.h>

int getrlimit(int resource, struct rlimit *rlim);
int setrlimit(int resource, const struct rlimit *rlim);
rlimit结构体定义如下：

C
1
2
3
4
struct rlimit {
　　rlim_t rlim_cur; // 软限制
　　rlim_t rlim_max; // 硬限制
};
resource的选择有：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
RLIMIT_AS //进程的最大虚内存空间，字节为单位。
RLIMIT_CORE //内核转存文件的最大长度。
RLIMIT_CPU //最大允许的CPU使用时间，秒为单位。当进程达到软限制，内核将给其发送SIGXCPU信号，这一信号的默认行为是终止进程的执行。然而，可以捕捉信号，处理句柄可将控制返回给主程序。如果进程继续耗费CPU时间，核心会以每秒一次的频率给其发送SIGXCPU信号，直到达到硬限制，那时将给进程发送 SIGKILL信号终止其执行。
RLIMIT_DATA //进程数据段的最大值。
RLIMIT_FSIZE //进程可建立的文件的最大长度。如果进程试图超出这一限制时，核心会给其发送SIGXFSZ信号，默认情况下将终止进程的执行。
RLIMIT_LOCKS //进程可建立的锁和租赁的最大值。
RLIMIT_MEMLOCK //进程可锁定在内存中的最大数据量，字节为单位。
RLIMIT_MSGQUEUE //进程可为POSIX消息队列分配的最大字节数。
RLIMIT_NICE //进程可通过setpriority() 或 nice()调用设置的最大完美值。
RLIMIT_NOFILE //指定比进程可打开的最大文件描述词大一的值，超出此值，将会产生EMFILE错误。
RLIMIT_NPROC //用户可拥有的最大进程数。
RLIMIT_RTPRIO //进程可通过sched_setscheduler 和 sched_setparam设置的最大实时优先级。
RLIMIT_SIGPENDING //用户可拥有的最大挂起信号数。
RLIMIT_STACK //最大的进程堆栈，以字节为单位。
返回值：

成功执行时，返回0。失败返回-1，errno被设为以下的某个值
EFAULT：rlim指针指向的空间不可访问
EINVAL：参数无效
EPERM：增加资源限制值时，权能不允许
6 进程控制
该节对应第八章——进程控制。

6.1 进程标识
每个进程都有一个非负整型表示的唯一进程ID。因为进程ID标识符总是唯一的，常将其用作其他标识符的一部分以保证其唯一性。例如，应用程序有时就把进程 ID 作为名字的一部分来创建一个唯一的文件名。

进程标识符的类型为pid_t，其本质上是一个无符号整型（unsigned int）的类型别名。

进程ID是可复用的。当一个进程终止后，其进程ID就成为复用的候选者。大多数 UNIX 系统实现延迟复用算法，使得赋予新建进程的 ID不同于最近终止进程所使用的ID。这防止了将新进程误认为是使用同一ID的某个已终止的先前进程。

系统中有一些专用进程，但具体细节随实现而不同。

ID为0的进程通常是调度进程，常常被称为交换进程（swapper）。该进程是内核的一部分，它并不执行任何硬盘上的程序，因此也被称为系统进程。
进程ID1通常是 init 进程，在自举过程结束时由内核调用。该进程的程序文件在UNIX的早期版本中是/etc/init，在较新版本中是/sbin/init。此进程负责在自举内核后启动一个UNIX系统。init 进程决不会终止。它是一个普通的用户进程（与交换进程不同，它不是内核中的系统进程），但是它以超级用户特权运行。
常用系统调用

C
1
2
3
4
5
6
7
8
#include <sys/types.h>
#include <unistd.h>

// 返回该函数进程的父进程标识符
pid_t getppid(void);

// 返回当前进程标识符
pid_t getpid(void);
补充：ps命令

Linux 中的 ps 命令是 Process Status 的缩写。ps 命令用来列出系统中当前正在运行的那些进程，就是执行 ps 命令的那个时刻的那些进程的快照。

参数：

参数	含义
-e	显示所有进程
-f	全格式
-l	长格式
a	显示终端上的所有进程，包括其他用户的进程
r	只显示正在运行的进程
x	显示没有控制终端的进程
常用组合：

BASH
1
ps aux # 查看全部进程,以用户为主的格式显示进程情况


BASH
1
ps ef # 显示出linux机器所有详细的进程信息


BASH
1
ps aux | grep bash


6.2 进程产生
6.2.1 fork
init进程：pid为1，是所有进程的祖先进程，注意不是父进程。

一个现有的进程可以调用fork函数创建一个新进程：

C
1
2
3
#include <unistd.h>

pid_t fork(void);
由fork创建的新进程被称为子进程（child process）。

返回值：fork函数被调用一次，但返回两次。子进程的返回值是0，父进程的返回值则是新建子进程的进程PID。如果失败则返回-1，并设置errno。和setjmp类似，fork语句后常常跟上分支语句进行判断。

子进程和父进程继续执行fork调用之后的指令。子进程是父进程的副本。例如，子进程获得父进程数据空间、堆、栈和缓冲区和文件描述符的副本。注意，这是子进程所拥有的副本。父进程和子进程并不共享这些存储空间部分，除了读时共享的部分。

fork后父子进程的区别

fork返回值不同

两个进程的pid不同

两个进程的ppid也不同，父进程的ppid是它的父进程pid，而子进程的ppid是创建它的进程的pid

父进程的未决信号和文件锁不继承

子进程的资源利用量归零

程序实例1——fork的使用

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>

int main(void) {
    pid_t pid;
    printf("[%d]:Begin!\n", getpid());
    pid = fork();
    // ---------------------------
    // 父进程调用fork后，父子进程都从这里开始执行
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    
    if(pid == 0) { // 如果返回值pid为0，则为子进程
        printf("[%d]:Child is working!\n", getpid());
    } else { // 如果返回值pid大于0，则为父进程
        printf("[%d]:Parent is working!\n", getpid());
    }

    printf("[%d]:End!\n", getpid());
    exit(0);
}
运行结果：（可能形式）

PLAINTEXT
1
2
3
4
5
6
[root@HongyiZeng proc]# ./fork1 
[16023]:Begin!
[16023]:Parent is working!
[16023]:End!
[root@HongyiZeng proc]# [16024]:Child is working!
[16024]:End!
一般来说，在fork之后是父进程先执行还是子进程先执行是不确定的，这取决于内核所使用的调度算法。

如果在main程序返回前添加一行：

C
1
getchar();
使得父子进程都暂停，再使用命令：

BASH
1
ps axf
可以看到两个进程与bash的关系如下：



程序实例2——fflush的重要性

对于上述程序的结果，注意到Begin只打印了一次：

BASH
1
2
3
4
5
6
[root@HongyiZeng proc]# ./fork1 
[16023]:Begin!
[16023]:Parent is working!
[16023]:End!
[16024]:Child is working!
[16024]:End!
如果将该打印信息重定向至某个文件：

BASH
1
./fork1 > /tmp/out
再查看该文件的内容：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng proc]# cat /tmp/out
[18060]:Begin!
[18060]:Parent is working!
[18060]:End!
[18060]:Begin!
[18061]:Child is working!
[18061]:End!
注意到Begin打印了两次。

原因：对于重定向至文件，采用的是全缓冲（除标准输出和标准错误输出），只有进程结束或者缓冲满的时候才刷新缓冲区（遇到换行符不刷新），将缓冲区的内容写入到文件。因此，父进程fork时，尚未刷新缓冲区，因此缓冲区的内容[18060]:Begin!（注意进程号已经固定了！）被复制到子进程的缓冲区中，当父子进程执行结束时，强制刷新，输出两次[18060]:Begin!。

为防止缓冲区内容被复制，父进程在fork之前需要强制刷新所有已经打开的流：

C
1
2
3
4
5
6
7
8
9
10
11
12
int main(void) {
    pid_t pid;
    printf("[%d]:Begin!\n", getpid());
    
    // 强制刷新所有打开的流!!!
    fflush(NULL);
    // 再调用fork
    pid = fork();
    // ---------------------------
    // 父进程调用fork后，父子进程都从这里开始执行
    // ...
}
此时，只打印了一句Begin：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng proc]# ./fork1 > /tmp/out
[root@HongyiZeng proc]# cat /tmp/out
[19853]:Begin!
[19853]:Parent is working!
[19853]:End!
[19854]:Child is working!
[19854]:End!
程序实例3——找质数

需求：找出30000000~30000200的所有质数。

单进程版：
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
#include <stdio.h>
#include <stdlib.h>
#define LEFT 30000000
#define RIGHT 30000200

int main(void) {
    int i, j, mark;
    for(i = LEFT; i <= RIGHT; i++) {
        mark = 1;
        for(j = 2; j < i/2; j++) {
            if(i % j == 0) {
                mark = 0;
                break;
            }
        }
        if(mark)
            printf("%d is a primer.\n", i);
    }
    exit(0);
}
打印结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
[root@HongyiZeng proc]# time ./primer0 
30000001 is a primer.
30000023 is a primer.
30000037 is a primer.
30000041 is a primer.
30000049 is a primer.
30000059 is a primer.
30000071 is a primer.
30000079 is a primer.
30000083 is a primer.
30000109 is a primer.
30000133 is a primer.
30000137 is a primer.
30000149 is a primer.
30000163 is a primer.
30000167 is a primer.
30000169 is a primer.
30000193 is a primer.
30000199 is a primer.

real    0m0.967s
user    0m0.950s
sys     0m0.001s
多进程协同：
一个错误的程序：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
#include <stdio.h>
#include <stdlib.h>
#define LEFT 30000000
#define RIGHT 30000200

int main(void) {
    int i, j, mark;
    pid_t pid;
    for(i = LEFT; i <= RIGHT; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) { // child
            mark = 1;
            for(j = 2; j < i/2; j++) {
                if(i % j == 0) {
                    mark = 0;
                    break;
                }
            }
            if(mark)
                printf("%d is a primer.\n", i);
        }
    }
    exit(0);
}
分析：子进程执行完pid==0的分支后，又会执行for循环的部分，此时会再次fork，导致进程数量指数式的增长，超出可用内存。

更正：在执行完pid==0的分支后面（完成了对某个数i的判断的任务），需要正常退出exit(0)：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
#include <stdio.h>
#include <stdlib.h>
#define LEFT 30000000
#define RIGHT 30000200

int main(void) {
    int i, j, mark;
    pid_t pid;
    for(i = LEFT; i <= RIGHT; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) { // child
            mark = 1;
            for(j = 2; j < i/2; j++) {
                if(i % j == 0) {
                    mark = 0;
                    break;
                }
            }

            if(mark)
                printf("%d is a primer.\n", i);
            // 子进程退出
            exit(0);
        }
    }
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
[root@HongyiZeng proc]# time ./primer1
30000037 is a primer.
30000071 is a primer.
30000059 is a primer.
30000079 is a primer.
30000083 is a primer.
30000049 is a primer.
30000023 is a primer.
30000137 is a primer.
30000149 is a primer.
30000041 is a primer.
30000167 is a primer.
30000193 is a primer.
30000109 is a primer.
30000001 is a primer.
30000199 is a primer.
30000169 is a primer.
30000163 is a primer.
30000133 is a primer.

real    0m0.048s
user    0m0.001s
sys     0m0.008s
程序实例4——孤儿进程和僵尸进程

修改1：在子进程在退出前，先睡眠1000s，这样父进程会先执行完毕而退出。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
int main(void) {
    int i, j, mark;
    pid_t pid;
    for(i = LEFT; i <= RIGHT; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) {
            mark = 1;
            for(j = 2; j < i/2; j++) {
                if(i % j == 0) {
                    mark = 0;
                    break;
                }
            }
            if(mark)
                printf("%d is a primer.\n", i);
            // 子进程睡眠1000s
            sleep(1000);
            exit(0);
        }
    }
    exit(0);
}
再使用命令ps axf查看：



此时201个子进程的状态为S（可中断的睡眠状态），且父进程为init进程（每个进程以顶格形式出现）。这里的子进程在init进程接管之前就是孤儿进程。

孤儿进程：一个父进程退出，而它的一个或多个子进程还在运行，那么这些子进程将成为孤儿进程。孤儿进程将被 init 进程所收养，并由 init 进程对它们完成状态收集工作，孤儿进程并不会有什么危害。

修改2：在父进程退出之前，先休眠1000s，再查看进程状态。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
int main(void) {
    int i, j, mark;
    pid_t pid;
    for(i = LEFT; i <= RIGHT; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) {
            mark = 1;
            for(j = 2; j < i/2; j++) {
                if(i % j == 0) {
                    mark = 0;
                    break;
                }
            }
            if(mark)
                printf("%d is a primer.\n", i);
            exit(0);
        }
    }
    // 父进程睡眠1000s再退出
    sleep(1000);
    exit(0);
}
执行结果：



可以看到子进程状态为Z，即为僵尸状态。

僵尸进程：一个进程使用 fork 创建子进程，如果子进程退出，而父进程并没有调用 wait 或 waitpid 获取子进程的状态信息（收尸），那么子进程的进程描述符仍然保存在系统中，这种进程称之为僵尸进程。

僵尸进程虽然不占有任何内存空间，但如果父进程不调用 wait() / waitpid() 的话，那么保留的信息就不会释放，其进程号就会一直被占用，而系统所能使用的进程号是有限的，如果大量的产生僵死进程，将因为没有可用的进程号而导致系统不能产生新的进程，此即为僵尸进程的危害。

避免产生僵尸进程的方式

僵尸进程和危害：当一个进程已经结束，但是系统没有把它的进程的数据结构完全释放，此时用 ps 察看它的状态是defunt。 僵尸进程占据进程表的空间，而且不能被kill掉因为它已经死了，所以在开发多进程尤其是守护进程时注意要避免产生僵尸进程。

产生原因：子进程先于父进程退出，且父进程没有给子进程收尸。

和孤儿进程的区别：就是爸爸（父进程）和儿子（子进程）谁先死的问题
如果当儿子还在世的时候，爸爸去世了，那么儿子就成孤儿了，这个时候儿子就会被init收养，换句话说，init进程充当了儿子的爸爸，所以等到儿子去世的时候，就由init进程来为其收尸。
如果当爸爸还活着的时候，儿子死了，这个时候如果爸爸不给儿子收尸，那么儿子就会变成僵尸进程。
SIGCHLD信号：当子进程退出时发送给父进程，默认动作是忽略。

避免产生僵尸进程的方式：

方式1：父进程调用wait/waitpid等函数等待子进程结束，如果尚无子进程退出wait会导致父进程阻塞。waitpid可以通过传递WNOHANG使父进程不阻塞立即返回。该方式详见6.3节。

方式2：通过两次调用fork。父进程首先调用fork创建一个子进程然后waitpid等待子进程退出，子进程再fork一个孙进程后退出。这样子进程退出后会被父进程等待回收，而对于孙子进程其父进程已经退出所以孙进程成为一个孤儿进程，孤儿进程由init进程接管，孙进程结束后，init会等待回收。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
int main(void) {
    pid_t pid;
    pid = fork();
    if(pid < 0) {
        perror("fork");
        exit(1);
    }
    if(pid == 0) { // 子进程
        pid_t newpid;
        newpid = fork();
        // 检错...
        if(newpid == 0) { // 孙子进程
            // 做自己的事情
        }
        exit(0); // 子进程退出
    } else { // 父进程
        waitpid(pid, NULL, 0); // 阻塞等待子进程退出
    }
    exit(0);
}
方式3：通过signal通知内核表明父进程对子进程的结束不关心，由内核回收。如果不想让父进程挂起，可以在父进程中加入一条语句：signal(SIGCHLD,SIG_IGN);表示父进程忽略SIGCHLD信号，该信号是子进程退出的时候向父进程发送的。该方式不会阻塞父进程。

C
1
2
3
4
5
6
7
8
9
10
11
12
int main(void) {	
   	int i;
  	pid_t pid;
	signal(SIGCHLD, SIG_IGN); // 显式的忽略SIGCHLD信号
	for(i = 0; i < 10; i++) 
	{
   		if ((pid = fork()) == 0)
 			_exit(0);
    }
   	sleep(10);
   	exit(0);
}
方式4：对子进程进行wait，释放它们的资源，但是父进程一般没工夫在那里守着，等着子进程的退出，所以，一般使用信号的方式来处理，在收到SIGCHLD信号的时候，在信号处理函数中调用wait操作来释放他们的资源。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
void avoid_zombies_handler(int signo) {
	pid_t pid;
	int exit_status;
	int saved_errno = errno;
	while ((pid = waitpid(-1, &exit_status, WNOHANG)) > 0) {
		/* do nothing */
	}
	errno = saved_errno;
}
int main(void) {
	pid_t pid;
	struct sigaction child_act;
	memset(&child_act, 0, sizeof(struct sigaction));
	
	child_act.sa_handler = avoid_zombies_handler; // 信号注册函数
	child_act.sa_flags = SA_RESTART | SA_NOCLDSTOP;
	sigemptyset(&child_act.sa_mask);
	if(sigaction(SIGCHLD, &child_act, NULL) == -1) { // 注册失败
		perror("sigaction error");
		_exit(EXIT_FAILURE);
	}
	while(1) {
		if ((pid = fork()) == 0)  {/* child process */
			_exit(0);
		}else if (pid > 0); /* parent process */
	}
	_exit(EXIT_SUCCESS);
}
方式5：使用sigaction对SIGCHLD注册信号处理函数，并设置sa_flags标志位为SA_NOCLDWAIT，这样，当子进程终止时，子进程不会被设置为僵尸进程。

SA_NOCLDWAIT在man手册中的描述：

PLAINTEXT
1
2
If signum is SIGCHLD, do not transform children into zombies when they terminate. See also waitpid(2).  This flag is meaningful only when establishing a  handler  for  SIGCHLD,  or when setting that signal's disposition to SIG_DFL.
如果信号是SIGCHLD，不会在孩子终止时将他们变成僵尸。另请参见waitpid（2）。该标志仅在为SIGCHLD建立处理程序或将信号的处理设置为SIG_DFL时才有意义。
C
1
2
3
4
5
6
7
8
9
10
11
12
13
// 父进程
int main(void) {
    struct sigaction sa, osa;
    sigset_t set, oset;
	
    // 避免子进程成为僵尸进程
    sa.sa_handler = SIG_DFL; // 或者自己定义的处理程序
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = SA_NOCLDWAIT;
    sigaction(SIGCHLD, &sa, &osa);
    
    // ...
}
父子进程之间的文件共享

fork的一个特性是父进程的所有打开文件描述符都被复制到子进程中。我们说“复制”是因为对每个文件描述符来说，就好像执行了dup函数。父进程和子进程每个相同的打开描述符共享一个文件表项。

考虑下述情况，一个进程具有3个不同的打开文件，它们是标准输入、标准输出和标准错误。在从fork返回时，我们有了如图8-2中所示的结构。



重要的一点是，父进程和子进程共享同一个文件偏移量。

考虑下述情况：一个进程 fork 了一个子进程，然后等待子进程终止。假定，作为普通处理的一部分，父进程和子进程都向标准输出进行写操作。如果父进程的标准输出已重定向（很可能是由 shell 实现的），那么子进程写到该标准输出时，它将更新与父进程共享的该文件的偏移量。在这个例子中，当父进程等待子进程时，子进程写到标准输出：而在子进程终止后，父进程也写到标准输出上，并且知道其输出会追加在子进程所写数据之后。如果父进程和子进程不共享同一文件偏移量，要实现这种形式的交互就要困难得多，可能需要父进程显式地动作。

如果父进程和子进程写同一描述符指向的文件，但又没有任何形式的同步（如使父进程等待子进程），那么它们的输出就会相互混合（假定所用的描述符是在fork之前打开的）。

在fork之后处理文件描述符有以下两种常见的情况：

父进程等待子进程完成。在这种情况下，父进程无需对其描述符做任何处理。当子进程终止后，它曾进行过读、写操作的任一共享描述符的文件偏移量已做了相应更新。
父进程和子进程各自执行不同的程序段。在这种情况下，在fork之后，父进程和子进程各自关闭它们不需使用的文件描述符，这样就不会干扰对方使用的文件描述符。这种方法是网络服务进程经常使用的。
6.2.2 vfork
考虑这样一个场景，父进程使用了一个占用内存很大的数据，此时它fork了一个子进程，而子进程仅仅打印一个字符串就退出了，此时这块很大的数据复制到子进程的内存空间中，造成了很大的内存浪费。

为了解决这个问题，在fork实现中，增加了读时共享，写时复制（Copy-On-Write，COW）的机制。写时复制可以避免拷贝大量根本就不会使用的数据（地址空间包含的数据多达数十兆）。因此可以看出写时复制极大提升了Linux系统下fork函数运行的性能。

写时复制指的是子进程的页表项指向与父进程相同的物理页，这也只需要拷贝父进程的页表项就可以了，不会复制整个内存地址空间，同时把这些页表项标记为只读。

读时共享：如果父子进行都不对页面进行操作或只读，那么便一直共享同一份物理页面。

写时复制：只要父子进程有一个尝试进行修改某一个页面（写时），那么就会发生缺页异常。那么内核便会为该页面创建一个新的物理页面，并将内容复制到新的物理页面中，让父子进程真正地各自拥有自己的物理内存页面，并将页表中相应地页表项标记为可写。

写时复制父子进程修改某一个页面前后变化如下图所示：



在fork还没实现copy on write之前。Unix设计者很关心fork之后立刻执行exec所造成的地址空间浪费，所以引入了vfork系统调用。而现在vfork已经不常用了。

vfork和fork的区别/联系：vfork函数和 fork函数一样都是在已有的进程中创建一个新的进程，但它们创建的子进程是有区别的。
父子进程的执行顺序
fork： 父子进程的执行次序不确定。
vfork：保证子进程先运行，在它调用 exec/exit之后，父进程才执行
是否拷贝父进程的地址空间
fork： 子进程写时拷贝父进程的地址空间，子进程是父进程的一个复制
vfork：子进程共享父进程的地址空间
调用vfork函数，是为了执行exec函数；如果子进程没有调用 exec/exit，程序会出错
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
int main(int argc, char *argv[]){
	pid_t pid;
	pid = vfork();	// 创建进程
	if(pid < 0){
		perror("vfork");
	}
	if(0 == pid){  
		sleep(3); // 延时 3 秒
		printf("i am son\n");
		
		_exit(0); // 退出子进程，必须
	}
	else if(pid > 0){ // 父进程
		printf("i am father\n");
	}
}
执行结果：已经让子进程延时 3 s，结果还是子进程运行结束后，父进程才执行

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
static int a = 10;

int main(void){
	pid_t pid;
	int b = 20;
	pid = vfork();
	if(pid < 0){
		perror("vfork()");
        exit(1);
	}
	if(0 == pid){
		a = 100, b = 200;
		printf("son: a = %d, b = %d\n", a, b);
		_exit(0);  
	}
	else if(pid > 0){ 
		printf("father: a = %d, b = %d\n", a, b);	
	}
    exit(0);
}
执行结果：子进程先执行，修改完a，b的值后，由于父子进程共享内存空间，因此会影响父进程

PLAINTEXT
1
2
son: a = 100, b = 200
father: a = 100, b = 200
如果采用fork的话，会有写时复制，此时父子进程的变量无关：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

static int a = 10;
int main(void) {
    pid_t pid;
    int b = 20;
    fflush(NULL);
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) {
        a = 100;
        b = 200;
        printf("son: a = %d, b = %d.\n", a, b);
        _exit(0);
    } else {
        printf("father: a = %d, b = %d.\n", a, b);
    }
    exit(0);
}
执行结果：

BASH
1
2
3
[root@HongyiZeng proc]# ./vfork 
father: a = 10, b = 20.
son: a = 100, b = 200.
6.3 wait和waitpid
wait系统调用：等待进程改变状态。

进程一旦调用了wait，就立即阻塞自己，由wait自动分析是否当前进程的某个子进程已经退出，如果让它找到了这样一个已经变成僵尸的子进程，wait就会收集这个子进程的信息，并把它彻底销毁后返回；如果没有找到这样一个子进程，wait就会一直阻塞在这里，直到有一个出现为止。

wait函数原型如下：

C
1
2
3
4
#include <sys/types.h>
#include <sys/wait.h>

pid_t wait(int *status);
status用来保存子进程退出时的一些状态。如果不在意子进程的退出状态，可以设定status为NULL。如果参数status的值不是NULL，wait就会把子进程退出时的状态取出，并存入其中。可以使用下列的宏函数来处理status：
WIFEXITED(status)：用来指出子进程是否为正常退出，如果是，则会返回一个非零值。
WEXITSTATUS(status)：当WIFEXITED返回非零值时，可以用这个宏来提取子进程的返回值。
如果执行成功，wait会返回子进程的PID；如果没有子进程，则wait返回-1。
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

int main (void) {
    pid_t pc, pr;
    pc = fork();

    if(pc < 0) {
        printf ("error ocurred!\n");
    }else if (pc == 0) { /* 如果是子进程 */
        printf("This is child process with pid of %d\n", getpid());
        sleep (10); /* 睡眠10秒钟 */
    }else { /* 如果是父进程 */
        pr = wait(NULL); /* 在这里阻塞，收尸 */
        printf ("I catched a child process with pid of %d\n", pr);
    }
    exit(0);
}
打印结果：

C
1
2
3
This is child process with pid of 298
等待10秒
I catched a child process with pid of 298
程序实例2

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int status;
    pid_t pc, pr;
    pc = fork();

    if(pc < 0) {
        printf( "error ocurred!\n" );
    }
    if(pc == 0) { /* 子进程 */
        printf("This is child process with pid of %d\n", getpid());
        exit(3); /* 子进程返回3 */
    if(pc > 0) { /* 父进程 */
        pr = wait(&status);
        if(WIFEXITED(status)) { /* 如果WIFEXITED返回非零值 */
            printf("the child process %d exit normally\n", pr);
            printf("the return code is %d\n", WEXITSTATUS(status ));
        } else { /* 如果WIFEXITED返回零 */
            printf("the child process %d exit abnormally\n", pr);
        }
    }
    exit(0);
}
打印结果：

C
1
2
3
This is child process with pid of 308
the child process 308 exit normally
the return code is 3
waitpid函数原型如下：

C
1
2
3
4
5
6
7
8
#include <sys/types.h>
#include <sys/wait.h>

pid_t waitpid(pid_t pid, int *status, int options);

// 下面两者等价：
wait(&status);
waitpid(-1, &status, 0);
从本质上讲，waitpid和wait的作用是完全相同的，但waitpid多出了两个可以由用户控制的参数pid和options：

pid：当pid取不同的值时，在这里有不同的意义：
取值	意义
> 0	只等待进程ID等于pid的子进程
=-1	等待任何一个子进程退出，此时waitpid和wait的作用一模一样
= 0	等待同一个进程组process group id中的任何子进程
<-1	等待一个指定进程组中的任何子进程，这个进程组的ID等于pid的绝对值
options：是一个位图，可以通过按位或来设置，如果不设置则置为0即可。最常用的选项是WNOHANG，作用是即使没有子进程退出，它也会立即返回，此时waitpid不同于wait，它变成了非阻塞的函数。
waitpid的返回值有如下几种情况：
当正常返回时，waitpid返回子进程的PID。
如果设置了WNOHANG，而waitpid没有发现已经退出的子进程，则返回0。
如果waitpid出错，则返回-1。例如参数pid指示的子进程不存在，或此进程存在，但不是调用进程的子进程。
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#define LEFT 30000000
#define RIGHT 30000200

int main(void) {
    int i, j, mark;
    pid_t pid;
    pid_t pid_child;
    for(i = LEFT; i <= RIGHT; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) {
            mark = 1;
            for(j = 2; j < i/2; j++) {
                if(i % j == 0) {
                    mark = 0;
                    break;
                }
            }

            if(mark)
                printf("[%d]:%d is a primer.\n", getpid(), i);
            exit(0);
        }
    }
    // 循环201次，给201个子进程收尸
    for(i = LEFT; i <= RIGHT; i++) {
        pid_child = wait(NULL);
        printf("Child process with pid: %d.\n", pid_child);
    }
    exit(0);
}
执行结果：省略了没有打印质数的输出

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
[32444]:30000023 is a primer.
Child process with pid: 32444.
[32462]:30000041 is a primer.
Child process with pid: 32462.
[32458]:30000037 is a primer.
Child process with pid: 32458.
[32422]:30000001 is a primer.
Child process with pid: 32422.
[32470]:30000049 is a primer.
Child process with pid: 32470.
[32480]:30000059 is a primer.
Child process with pid: 32480.
[32530]:30000109 is a primer.
Child process with pid: 32530.
[32504]:30000083 is a primer.
Child process with pid: 32504.
[32614]:30000193 is a primer.
Child process with pid: 32614.
[32590]:30000169 is a primer.
Child process with pid: 32590.
[32492]:30000071 is a primer.
Child process with pid: 32492.
[32620]:30000199 is a primer.
Child process with pid: 32620.
[32500]:30000079 is a primer.
Child process with pid: 32500.
[32588]:30000167 is a primer.
Child process with pid: 32588.
[32554]:30000133 is a primer.
Child process with pid: 32554.
[32558]:30000137 is a primer.
Child process with pid: 32558.
[32584]:30000163 is a primer.
Child process with pid: 32584.
[32570]:30000149 is a primer.
Child process with pid: 32570.
6.4 exec函数族
6.4.1 简介
fork函数是用于创建一个子进程，该子进程几乎是父进程的副本，而有时我们希望子进程去执行另外的程序，exec函数族就提供了一个在进程中启动另一个程序执行的方法。它可以根据指定的文件名或目录名找到可执行文件，并用它来取代原调用进程的数据段、代码段和堆栈段，在执行完之后，原调用进程的内容除了进程号外，其他全部被新程序的内容替换了。这里的可执行文件既可以是二进制文件，也可以是Linux下任何可执行脚本文件。

当进程调用一种exec函数时，该进程执行的程序完全替换为新程序，而新程序则从其main函数开始执行。因为调用exec并不创建新进程，所以前后的进程ID并未改变。exec只是用磁盘上的一个新程序替换了当前进程的正文段、数据段、堆段和栈段。

为什么需要exec函数

fork子进程是为了执行新程序（fork创建了子进程后，子进程和父进程同时被OS调度执行，因此子进程可以单独的执行一个程序，这个程序宏观上将会和父进程程序同时进行）；
可以直接在子进程的if中写入新程序的代码（参见6.2.1节的做法）。这样可以，但是不够灵活，因为我们只能把子进程程序的源代码贴过来执行（必须知道源代码，而且源代码太长了也不好控制），譬如说我们希望子进程来执行ls -la命令就不行了（没有源代码，只有编译好的可执行程序/usr/bin/ls）；
使用exec族运行新的可执行程序（exec族函数可以直接把一个编译好的可执行程序直接加载运行）；
我们有了exec族函数后，典型的父子进程程序是这样的：子进程需要运行的程序被单独编写、单独编译连接成一个可执行程序（叫hello），（项目是一个多进程项目）主程序为父进程，fork创建了子进程后在子进程中exec来执行hello，达到父子进程分别做不同程序同时（宏观上）运行的效果；
6.4.2 使用
有多种不同的exec函数可供使用，它们常常被统称为exec函数。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
#include <unistd.h>

extern char **environ;

// 直达
int execl(const char *path, const char *arg, ...);
// 从$PATH里找
int execlp(const char *file, const char *arg, ...);
// 直达
int execle(const char *path, const char *arg, ..., char * const envp[]);

// 直达
int execv(const char *path, char *const argv[]);
// 从$PATH里找
int execvp(const char *file, char *const argv[]);
// 从$PATH里找
int execvpe(const char *file, char *const argv[], char *const envp[]);
以上函数成功执行时不返回，失败时返回-1并设值errno
后缀含义

l：以list形式传入参数
v：以vector形式传入参数
p：在$PATH中查找可执行程序
e：在envp[]中查找可执行程序
execl和execv：这两个函数是最基本的exec，都可以用来执行一个程序，区别是传参的格式不同：

execl是把参数列表（本质上是多个字符串，必须以NULL结尾）依次排列而成
execv是把参数列表事先放入一个字符串数组中（必须以NULL结尾），再把这个字符串数组传给execv函数，类似于char **argv
path：完整的文件目录路径
execlp和execvp：这两个函数在上面2个基础上加了p

file：文件名，系统就会自动从环境变量$PATH所指出的路径中进行查找该文件。如果包含/，则视为路径名path。
execle和execvpe：这两个函数较基本exec来说加了e

envp：自己指定的环境变量。在envp[]中指定当前进程所使用的环境变量替换掉该进程继承的环境变量$PATH。


代码示例——环境变量

myexec.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    
    pid_t pid;
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    } else if(pid == 0) { // 子进程
        // 参数
        char * const param[] = {"myHello", "-a", "-l", NULL};
        // 自己设置的环境变量
        char * const envp[] = {"AA=aaa", "BB=bbb", NULL};
        // 执行同目录下的hello
        execvpe("./hello", param, envp);
        perror("execvpe()");
        exit(1);
    } else { // 父进程
        wait(NULL);
    }
    exit(0);
}
hello.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv, char **env) {

    printf("argc = %d\n", argc);

    int i;

    for(i = 0; argv[i] != NULL; i++) {
        printf("argv[%d]: %s\n", i, argv[i]);
    }

    for(i = 0; env[i] != NULL; i++) {
        printf("env[%d]: %s\n", i, env[i]);
    }

    exit(0);
}
编译链接为hello

执行结果：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng proc]# ./myexec 
argc = 3
argv[0]: myHello
argv[1]: -a
argv[2]: -l
env[0]: AA=aaa
env[1]: BB=bbb
代码示例——程序名称

补充：argv第一个参数为程序名称，后面的参数为命令行参数。程序名称可以任意设置，一般来说，如果一个源码文件的名称为XXX.c，则编译生成的可执行程序为XXX，此时运行，程序名称（argv[0]）就是XXX

使用gcc默认编译链接得到的可执行文件名称为a.out，此时程序名称（argv[0]）就是a.out。

使用exec族函数实现date +%s命令打印时间戳的功能。

BASH
1
2
[root@HongyiZeng proc]# date +%s
1670902531
这里的参数依次是程序名，+%s，NULL，注意第一个参数代表的是程序的名称，可以任意设置，类似于argv[0]，之后的参数才是重要的命令行参数。

代码实现：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    puts("Begin!");
	// 注意这里的程序名称给了一个myDate
    execl("/bin/date", "myDate", "+%s", NULL);
    perror("execl()");
    exit(1);

    puts("End!");
    exit(0);
}
或者使用execv：

C
1
2
3
4
5
6
7
8
9
10
11
int main(void) {
    puts("Begin!");
    
	char * const param[] = {"myDate", "+%s", NULL};
    execv("/bin/date", param);
    perror("execl()");
    exit(1);

    puts("End!");
    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
[root@HongyiZeng proc]# ./ex 
Begin!
1670902607
为什么不打印End!：执行exec后，原进程映像被替换成新的进程映像（即/bin/date程序），从main函数开始执行/bin/date的代码了。

我不再是我，我已成新的我。

让子进程睡眠1000s：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    pid_t pid;
    printf("[%d]Begin!\n", getpid());
    fflush(NULL);
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) {
        // 注意这里的程序名称给了一个httpd
        execl("/bin/sleep", "httpd", "1000", NULL);
        perror("execl()");
        exit(1);
    }

    wait(NULL);
    printf("[%d]End!\n", getpid());
    exit(0);
}
执行后查看：

BASH
1
ps axf


这里子进程运行时执行的是sleep程序，但是程序名称却被设置成了httpd，这实际上是一种低级的木马程序隐藏的办法。

代码示例——刷新缓冲区的重要性

在讲fork的时候提到过，在fork之前，最好将强制刷新所有已经打开的流，这里的exec也不例外，例如使用上面的程序，将结果重定向到/tmp/out中：

BASH
1
2
3
[root@HongyiZeng proc]# ./ex > /tmp/out
[root@HongyiZeng proc]# cat /tmp/out
1670902720
发现Begin!不见了，原因就在于重定向是全缓冲，当执行完puts("Begin!")后，该进程的缓冲区内容为Begin!\n，并不刷新到文件中，此时执行exec后，进程映像被替换成新的进程映像（即/bin/date程序），除了原进程的进程号外，其他全部（包括缓冲区）被新程序的内容替换了，之后新程序的缓冲区内容为时间戳，程序结束后，强制刷新到文件。

因此需要在执行exec之前强制刷新所有打开的流：

C
1
2
3
4
5
6
7
8
9
10
11
12
int main(void) {
    puts("Begin!");
	
    fflush(NULL);
    
    execl("/bin/date", "date", "+%s", NULL);
    perror("execl()");
    exit(1);

    puts("End!");
    exit(0);
}
再次执行：

PLAINTEXT
1
2
3
4
[root@HongyiZeng proc]# ./ex > /tmp/out
[root@HongyiZeng proc]# cat /tmp/out
Begin!
1670903209
代码示例——fork，exec和wait结合使用

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    pid_t pid;
    printf("[%d]Begin!\n", getpid());
    fflush(NULL);
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) { // 子进程
        execl("/bin/date", "date", "+%s", NULL);
        perror("execl()");
        exit(1);
    }
    // 等待子进程结束，收尸
    wait(NULL);
    printf("[%d]End!\n", getpid());
    exit(0);
}
执行结果：

C
1
2
3
[17301]Begin! // 父进程打印
1670903917 // 子进程打印，子进程和父进程完全是不同的程序了
[17301]End! // 父进程打印
至此，UNIX系统进程控制原语更加完善。用fork可以创建新进程，用exec可以初始执行新的程序。exit函数和wait函数处理终止和等待终止。这些是我们需要的基本的进程控制原语。

6.5 shell外部命令实现
内部命令和外部命令

内部命令指的是集成在Shell里面的命令，属于Shell的一部分。这些命令由shell程序识别并在shell程序内部完成运行，通常在linux系统加载运行时shell就被加载并驻留在系统内存中，比如cd命令等，这些命令在磁盘上看不见。
外部命令是linux系统中的实用程序部分，因为实用程序的功能通常都比较强大，所以其包含的程序量也会很大，在系统加载时并不随系统一起被加载到内存中，而是在需要时才将其调用内存。通常外部命令的实体并不包含在shell中，但是其命令执行过程是由shell程序控制的。shell程序管理外部命令执行的路径查找（PATH环境变量中）、加载存放，并控制命令的执行。这些命令的二进制可执行文件在磁盘上可见。
外部命令执行流程：

shell建立（fork）一个新的子进程，此进程即为Shell的一个副本
在子进程里，在PATH变量内所列出的目录中，寻找特定的命令。
/bin:/usr/bin:/usr/X11R6/bin:/usr/local/bin为PATH变量典型的默认值。 当命令名称包含有斜杠（/）符号时，将略过路径查找步骤。
在子进程里，以所找到的新程序取代（exec）子程序并执行。
父进程shell等待（wait）程序完成后（子进程exit），父进程Shell会接着从终端读取下一条命令或执行脚本里的下一条命令
相关命令：

BASH
1
2
type # 判断是外部命令还是内部命令
which # 查看命令所在的文件路径
示例：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng ~]# type cd
cd is a shell builtin
[root@HongyiZeng ~]# type mkdir
mkdir is /usr/bin/mkdir
[root@HongyiZeng ~]# which ls
alias ls='ls --color=auto'
        /usr/bin/ls
之前在终端上执行primer1.c时，出现下列情况：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
[root@HongyiZeng proc]# ./primer1
[root@HongyiZeng proc]# 30000037 is a primer.
30000001 is a primer.
30000041 is a primer.
30000023 is a primer.
30000079 is a primer.
30000133 is a primer.
30000137 is a primer.
30000049 is a primer.
30000109 is a primer.
30000083 is a primer.
30000071 is a primer.
30000059 is a primer.
30000193 is a primer.
30000169 is a primer.
30000167 is a primer.
30000199 is a primer.
30000163 is a primer.
30000149 is a primer.
发现终端先于子程序打印。

原因：在终端上执行primer1时，父进程（终端，即shell）fork了一个子进程，然后exec了primer1程序，并且wait到primer1退出，所以当primer1退出时，就立刻出现了终端，此时primer1fork的子进程还在运行打印结果，所以出现了终端先于子进程的结果出现。

重要！！！（外部命令执行流程）：一般的，当shell执行某个程序时，首先fork一个子进程，然后该子进程exec那个执行程序，shell此时wait该程序退出exit。

shell伪代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
int main(void) {
    // 死循环，shell不断接收用户命令
    while(1) {
        // 终端提示符
        prompt();
        
        // 获取命令
        getline();
        
        // 解析命令
        parse();
        
        if(内部命令) {
            // ...
        } else { // 外部命令
            fork();
            if(pid < 0) {
                // 异常处理...
            }
            if(pid == 0) { // 子进程
                exec(); // 将子进程替换为待执行程序
                // 异常处理...
            }
            if(pid > 0) { // shell父进程
                wait(NULL); // 等待子进程结束
            }
        }
        
    }
    exit(0);
}
代码实现

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <glob.h>

// 分隔符：空 制表符 换行符
#define DELIMS " \t\n"

struct cmd_st {
    glob_t globres;
};

static void prompt(void) {
    printf("mysh-0.1$ ");
}

static void parse(char *line, struct cmd_st *res) {
    char *tok;
    int i = 0;
    while(1) {
        tok = strsep(&line, DELIMS);
        // 分割完毕
        if(tok == NULL)
            break;
        if(tok[0] == '\0')
            continue;
       	// 选项解释
        // NOCHECK：不对pattern进行解析，直接返回pattern（这里是tok），相当于存储了命令行参数tok在glob_t中
        // APPEND：以追加形式将tok存放在glob_t中，第一次时不追加，因为globres尚未初始化，需要系统来自己分配内存，因此乘上i（乘法优先于按位或）
        glob(tok, GLOB_NOCHECK|GLOB_APPEND*i, NULL, &res->globres);
        // 置为1，使得追加永远成立
        i = 1;
    }
}


int main(void) {
    // getline的参数要初始化
    char *linebuf = NULL;
    size_t linebuf_size = 0;
    struct cmd_st cmd;
    pid_t pid;
    while(1) {
        prompt();
        // getline函数参见2.10节
        if(getline(&linebuf, &linebuf_size, stdin) < 0) {
            break;
        }
        // 解析命令
        parse(linebuf, &cmd);
        if(0) { // 内部命令，暂不做实现

        } else { // 外部命令
            pid = fork();
            if(pid < 0) {
                perror("fork()");
                exit(1);
            }
            if(pid == 0) {
                execvp(cmd.globres.gl_pathv[0], cmd.globres.gl_pathv);
                perror("execvp()");
                exit(1);
            } else {
                wait(NULL);
            }
        }
    }
    exit(0);
}
程序分析：

strsep函数原型：

C
1
2
3
#include <string.h>

char * strsep(char **stringp, const char *delim);
strsep实现字符串的分割，把stringp里面出现的delim替换成'\0'，后将 stringp 更新指向到'\0'符号的下一个字符地址，函数的返回值指向原来的 stringp 位置。直到分割完毕返回NULL。

代码执行流程分析：

例如：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
[root@HongyiZeng proc]# ./mysh 
mysh-0.1$ ls -l
total 144
-rwxr-xr-x 1 root root 8600 Dec 13 11:46 ex
-rw-r--r-- 1 root root  213 Dec 13 11:46 ex.c
-rwxr-xr-x 1 root root 8552 Dec 13 12:52 exv
-rw-r--r-- 1 root root  232 Dec 13 12:52 exv.c
-rwxr-xr-x 1 root root 8752 Dec 13 11:58 few
-rw-r--r-- 1 root root  361 Dec 13 11:58 few.c
-rwxr-xr-x 1 root root 8656 Dec 12 10:19 fork1
-rw-r--r-- 1 root root  402 Dec 12 10:19 fork1.c
-rwxr-xr-x 1 root root 8912 Dec 14 12:06 mysh
-rw-r--r-- 1 root root  953 Dec 14 12:06 mysh.c
-rwxr-xr-x 1 root root 8448 Dec 12 10:28 primer0
-rw-r--r-- 1 root root  313 Dec 12 10:28 primer0.c
-rwxr-xr-x 1 root root 8552 Dec 14 11:05 primer1
-rw-r--r-- 1 root root  437 Dec 14 11:04 primer1.c
-rwxr-xr-x 1 root root 8656 Dec 13 10:02 primer2
-rw-r--r-- 1 root root  652 Dec 13 10:02 primer2.c
-rwxr-xr-x 1 root root 8672 Dec 12 11:56 vfork
-rw-r--r-- 1 root root  372 Dec 12 11:56 vfork.c
getline得到字符串ls -l

parse解析该字符串，将分割结果存在globres中，其中：

C
1
2
3
globres.gl_pathv[0] = "ls";
globres.gl_pathv[1] = "-l";
globres.gl_pathv[2] = NULL;
子进程execvp(cmd.globres.gl_pathv[0], cmd.globres.gl_pathv);

第一个参数为要执行的可执行程序的名字，为ls，从环境变量PATH中找到/usr/bin/路径下的ls程序
第二个参数为指针数组，为ls和-l，第一个为程序名，任意，第二个和后面的为命令的参数，重要，这里的参数为-l
6.6 用户权限和组权限
在UNIX系统中，特权（如能改变当前日期的表示法）以及访问控制（如能否读、写一个特定文件），是基于用户ID和组ID的。当程序需要增加特权，或需要访问当前并不允许访问的资源时，我们需要更换自己的用户ID或组ID，使得新ID具有合适的特权或访问权限。与此类似，当程序需要降低其特权或阻止对某些资源的访问时，也需要更换用户ID或组ID，新ID不具有相应特权或访问这些资源的能力。

6.6.1 UID和GID
Linux采用一个32位的整数记录和区分不同的用户。这个区分不同用户的数字被称为User ID，简称UID。Linux系统中用户分为3类，即普通用户、根用户root、系统用户。

普通用户是指所有使用Linux系统的真实用户，通常UID>500；
根用户即root用户，UID为0。
系统用户是指系统运行必须有的用户，但并不是真实使用者。UID为1~499。对于系统用户，可能还不能理解是什么。比如，在Redhat或CentOS下运行网站服务时，需要使用系统用户Apache来运行httpd，而运行MySQL数据库服务时，需要使用系统用户mysql来运行mysqld进程。这就是系统用户。
可以使用id [用户名]命令查看uid，gid和组名：

BASH
1
2
3
4
[root@HongyiZeng proc]# id
uid=0(root) gid=0(root) groups=0(root)
[root@HongyiZeng proc]# id lighthouse
uid=1000(lighthouse) gid=1000(lighthouse) groups=1000(lighthouse),991(docker)
要确认自己所属的用户组，可以使用groups命令：

BASH
1
2
[root@HongyiZeng proc]# groups
root
系统用来记录用户名、密码的最重要两个文件是/etc/passwd和/etc/shadow，详见4.1.2节。

6.6.2 SUID和SGID
内核为每个进程维护的三个UID值（和三个GID值，是对应关系，略），这三个UID分别是：

RUID：(Real UID，实际用户ID)，我们当前以哪个用户登录，我们运行程序产生进程的RUID就是这个用户的UID。
EUID：(Effective UID，有效用户ID)，指当前进程实际以哪个UID来运行。一般情况下EUID等于RUID；但如果进程对应的可执行文件具有SUID权限（也就是rws的s），那么进程的EUID是该文件属主的UID，鉴权看的就是这个ID。
SUID：(Saved Set-user-ID，保存的设置用户ID)，EUID的一个副本，与SUID权限有关。
特殊权限

文件和目录权限除了普通权限rwx外，还有三个特殊权限：

SUID：在属主的x位以s标识，全称SetUID
SGID：在属组的x位以s标识，全称SetGID
STIKCY：黏着位，详见4.2.4.③节
BASH
1
2
[root@HongyiZeng proc]# ll /usr/bin/passwd 
-rwsr-xr-x 1 root root 27856 Apr  1  2020 /usr/bin/passwd
上面第4位的s就是特殊权限SUID，属主为root，其uid为0。当普通用户执行该命令时，会以root的身份去执行该命令。

下面将由五个问题来说明什么是SUID：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
# 1.普通用户可不可以修改密码？
可以，修改自己的密码

# 2./etc/shadow文件的作用？
存储用户密码的文件

# 3./etc/shadow文件的权限？
[root@localhost ~]# ll /etc/shadow
----------1 root root 16404 Apr  8 11:41 /etc/shadow

# 4.普通用户，是否可以修改/etc/shadow文件？
不可以，/etc/shadow文件，对于普通用户没有任何权限，所以不能读取，也不能写入内容。

普通用户的信息保存在 /etc/passwd文件中，与用户的密码在 /etc/shadow 文件中，也就是说，普通用户在更改自己密码时，修改了 /etc/shadow 文件中的加密密码，但是文件权限显示，普通用户对这两个文件都没有写权限。

# 5.那么普通用户，为什么可以修改密码？
1）因为使用了passwd这个命令
2）passwd命令在属主权限位上，原本是x权限，变成了s权限
3）s权限在属主权限位，又叫做SetUID权限，SUID
4）作用：普通用户在使用有SUID权限的文件或命令时，会以该文件的属主身份去执行该命令，换句话说，普通用户在执行passwd命令时，切换成了passwd属主即root的身份去执行passwd命令。
从进程控制的角度来说，当非root用户执行passwd这个可执行文件的时候，产生的进程的EUID，就是root用户的UID。换言之，这种情况下，产生的进程，实际以root用户的ID来运行二进制文件。

相关命令

BASH
1
2
chmod u+s 文件名/目录名 # 对文件给予用户s权限，则此用户暂时获得这个文件的属主权限
chmod g+s 文件名/目录名 # 对文件给予用户组s权限，则此用户暂时获得这个文件的属组权限
从进程控制的角度看命令的执行

UNIX系统产生的第一个进程是init进程，其三个uid为root的uid，即res为0 0 0，以init(0 0 0)表示；
init进程fork和exec产生getty(0 0 0)进程，此进程等待用户输入用户名；
用户回车输入了用户名后，getty进程存储用户名，exec产生login(0 0 0)进程，等待用户输入密码并验证口令（查找用户名和密码/etc/passwd）；
如果验证成功，login进程则fork并exec产生shell(r e s)进程，即终端，此时的res就是登录用户的UID，即固定了用户产生的进程的身份；
如果验证失败，则返回继续验证；
当用户执行某个命令时，shell进程fork并exec该命令对应的程序，例如ls(r e s)，并wait该程序，ls进程退出时，又返回到shell(r e s)终端（因为shell是一个死循环，参见6.5节）；
可以看出，整个UNIX的世界就是由fork，exec，wait和exit的进程控制原语搭建起来的
整个过程的图示如下：



又如执行passwd命令时图如下，变化的只有EUID和SUID：



6.6.3 相关系统调用
下面的系统调用是特殊权限实现所需的函数。

获取：
C
1
2
3
4
5
6
7
8
9
10
11
#include <unistd.h>
#include <sys/types.h>

// 返回当前进程的ruid
uid_t getuid(void);

// 返回当前进程的euid
uid_t geteuid(void);

gid_t getgid(void);
gid_t getegid(void);
设置：
C
1
2
3
4
5
6
7
8
#include <sys/types.h>
#include <unistd.h>

// 设置当前进程的euid
int setuid(uid_t uid);

// 设置当前进程的egid
int setgid(gid_t gid);
交换：
C
1
2
3
4
5
6
#include <sys/types.h>
#include <unistd.h>

// 交换当前进程的ruid和euid
int setreuid(uid_t ruid, uid_t euid);
int setregid(gid_t rgid, gid_t egid);
代码示例

实现任意用户用0号用户（即root）的身份查看/etc/shadow文件的功能：

BASH
1
./mysu 0 cat /etc/shadow
exec参数：

C
1
2
cat -> main的argv[2]：所需要执行的程序;
cat /etc/shadow -> main的argv[2]之后：程序名cat 命令行参数/etc/shadow
代码实现：（以普通用户lighthouse编译链接）

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char **argv) {
    pid_t pid;
    if(argc < 3) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }

    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }

    if(pid == 0) {
        setuid(atoi(argv[1])); // 将字符串转成int
        execvp(argv[2], argv + 2);
        perror("execvp()");
        exit(1);
    }
    wait(NULL);
    exit(0);
}
查看该文件的属性：

BASH
1
2
[lighthouse@HongyiZeng proc]$ ll mysu
-rwxr-xr-x 1 lighthouse lighthouse 8800 Dec 16 12:32 mysu
直接运行，权限不够：

BASH
1
2
[lighthouse@HongyiZeng proc]$ ./mysu 0 cat /etc/shadow
cat: /etc/shadow: Permission denied
切换到root用户，将mysu属主更改为root，并给予该文件s权限：

BASH
1
2
3
4
[root@HongyiZeng proc]# chown root mysu
[root@HongyiZeng proc]# chmod u+s mysu
[root@HongyiZeng proc]# ll mysu
-rwsr-xr-x 1 root lighthouse 8800 Dec 16 12:32 mysu
然后切换到lighthouse，再执行即可：



6.7 解释器文件
解释器文件也叫脚本文件。脚本文件包括：shell脚本，python脚本等；

脚本文件的后缀可自定义，一般来说shell脚本的后缀名为.sh，python脚本的后缀名为.py。

解释器文件的执行过程：当在linux系统的shell命令行上执行一个可执行文件时，系统会fork一个子进程，在子进程中内核会首先将该文件当做是二进制机器文件来执行，但是内核发现该文件不是机器文件（看到第一行为#!）后就会返回一个错误信息，收到错误信息后进程会将该文件看做是一个解释器文件，然后扫描该文件的第一行，获取解释器程序（本质上就是可执行文件）的名字，然后执行exec该解释器，并将该解释器文件当做解释器的一个参数，然后开始由解释器程序从头扫描整个解释器文件，执行每条语句（如果指定解释器为shell，会跳过第一条语句，因为#是注释）。如果其中某条命令执行失败了也不会影响后续命令的执行。

解释器文件的格式：

SHELL
1
2
3
#!pathname [optional-argument]

内容...
pathname：一般是绝对路径（它不会使用$PATH做路径搜索），对这个文件识别是由内核做为exec系统调用处理的。
optional-argument：相当于提供给exec的参数
内核exec执行的并不是解释器文件，而是第一行pathname指定的文件。一定要将解释器文件（本质是一个文本文件，以 #!开头）和解释器（由pathname指定）区分开。

代码示例1

以普通用户创建脚本test.sh：

SHELL
1
2
3
4
5
#!/bin/bash
ls
whoami
cat /etc/shadow
ps
这个文件没有执行权限，需要添加：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
[lighthouse@HongyiZeng proc]$ ll test.sh
-rw-r--r-- 1 lighthouse lighthouse 46 Dec 16 15:09 test.sh
[lighthouse@HongyiZeng proc]$ chmod u+x test.sh 
[lighthouse@HongyiZeng proc]$ ./test.sh
ex    exv.c  fork1    hello.c   mysh    mysu.c     primer1    primer2.c  test.sh
ex.c  few    fork1.c  myexec    mysh.c  primer0    primer1.c  sleep      vfork
exv   few.c  hello    myexec.c  mysu    primer0.c  primer2    sleep.c    vfork.c
lighthouse
cat: /etc/shadow: Permission denied
  PID TTY          TIME CMD
14857 pts/3    00:00:00 bash
19087 pts/3    00:00:00 test.sh
19091 pts/3    00:00:00 ps
shell执行./test.sh时，fork了一个子进程，该进程看到该文件为解释器文件，于是读取第一行，得到解释器程序的PATH，并exec该解释器程序（/bin/bash），然后重新执行这个解释器文件。

可以看出bash跳过了第一句，因为#在bash程序中被看成了注释，cat命令没有权限，但后面的ps命令仍然继续执行。

代码示例2

SHELL
1
2
3
4
5
#!/bin/cat
ls
whoami
cat /etc/shadow
ps
执行该脚本：

BASH
1
2
3
4
5
6
[root@HongyiZeng proc]# ./test.sh 
#!/bin/cat
ls
whoami
cat /etc/shadow
ps
发现这次是打印了该脚本文件的所有内容。过程同上，只是这次子进程exec的程序为/bin/cat程序。

代码示例3——自定义解释器程序

解释器程序（或解释器）本质上就是一个可执行文件。解释器文件是一个文本文件。

echoarg.c
C
1
2
3
4
5
6
7
8
9
10
11
#include <stdio.h>
#include <stdlib.h>
 
int main(int argc, char* argv[]) {
	int i;
	
	for(i = 0; i < argc; i++) {
		printf("argv[%d]: %s \n", i, argv[i]);
	}
	exit(0);
}
编译为echoarg，并存放在/usr/local/linux_c/proc/下。

echoarg.sh
SHELL
1
#!/usr/local/linux_c/proc/echoarg foo1 foo2 foo3
执行结果：

BASH
1
2
3
4
[root@HongyiZeng proc]# ./echoarg.sh
argv[0]: /usr/local/linux_c/proc/echoarg
argv[1]: foo1 foo2 foo3
argv[2]: ./echoarg.sh
6.8 system函数
函数原型：

C
1
2
3
#include <stdlib.h>

int system(const char *command);
作用：该函数实际上调用的是/bin/sh -c command，实质上是对fork+exec+wait的封装。

程序实例

C
1
2
3
4
5
6
7
8
9
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    
    system("date +%s > /tmp/out");
    
    exit(0);
}
该程序实质上执行的命令为：

BASH
1
/bin/sh -c date +%s > /tmp/out
在执行该命令的时候，system函数代码类似于：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    pid_t pid;
    
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    
    if(pid == 0) {
        // 实际上在 exec /bin/sh程序
        execl("/bin/sh", "sh", "-c", "date +%s > /tmp/out", NULL);
        perror("execl()");
        exit(1);
    }
    
    wait(NULL);
    exit(0);
}
6.9 守护进程
6.9.1 简介
守护进程也叫做精灵进程（Daemon），是运行在后台的一种特殊进程，它独立于控制终端并且可以周期性的执行某种任务或者等待处理某些发生的事件。

守护进程常常在系统引导装入时启动，在系统关闭时终止。

守护进程是非常有用的进程，在Linux当中大多数服务器用的就是守护进程。比如Web服务器httpd等，同时守护进程完成很多系统的任务。当Linux系统启动的时候，会启动很多系统服务，这些进程服务是没有终端的，也就是说把终端关闭了，这些系统服务是不会停止的。

特点

生存周期长[不是必须]：一般是操作系统启动的时候他启动，操作系统关闭的时候他才关闭
守护进程和终端没有关联，也就是说他们没有控制终端，所以控制终端退出也不会导致守护进程退出
守护进程是在后台运行不会占着终端，终端可以执行其它命令
6.9.2 进程组与会话
进程组：进程除了有PID之外还有一个进程组id，进程组是由一个进程或者多个进程组成。
通常进程组与同一作业相关联，可以收到同一终端的信号：这个信号可以使同一个进程组中的所有进程终止，停止或者继续运行
进程组id就是组长进程的pid，只要在某个进程组中还有一个进程存在，则该进程组就存在
会话：会话是有一个或者多个进程组组成的集合
每打开一个控制中断，或者在用户登录时，系统就会创建新会话
在该会话中允许的第一个进程称作会话首进程，通常这个首进程就是shell
通常，一个会话开始于用户登录，终止于用户退出，在此期间该用户运行的所有进程都属于这个会话


字段含义：

PPID：父进程pid
PID：当前进程pid
PGID：进程组id
SID TTY：当前进程的会话id
TPGID：进程组和终端的关系，-1表示没有关系
STAT：进程状态
UID：启动（exec）该进程的用户的id
TIME：进程执行到目前为止经历的时间
COMMAND：启动该进程时的命令
6.9.3 创建守护进程
相关系统调用：

C
1
2
3
#include <unistd.h>
// creates a session and sets the process group ID 错误返回-1
pid_t setsid(void);
作用：创建一个新的会话，并让执行的进程称为该会话组的组长

创建流程：

创建自己并被init进程接管：在父进程中执行fork并exit退出，让子进程被init进程接管，从而脱离终端进程shell的控制；
创建新进程组和新会话：在子进程中调用setsid函数创建新的会话和进程组；
修改子进程的工作目录：在子进程中调用chdir函数，让根目录 / 成为子进程的工作目录；
修改子进程umask：在子进程中调用umask函数，设置进程的umask为0；
在子进程中关闭任何不需要的文件描述符
由于守护进程和终端没有关系，所以需要将子进程的标准输入和标准输出重定向到dev/null（空设备当中去）
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
#include <iostream>
#include <unistd.h>
#include <signal.h>
#include <sys/stat.h>
#include <cstdlib>
#include <fcntl.h>

void main(void) {
    int fd;
    pid_t pid;
    
    pid = fork();
    
    if(pid < 0) { // 出错
        perror("fork()");
        exit(1);
    } else if(pid == 0) { // 子进程
        //只有子进程才会走到这里
        if(setsid() == -1) {
            perror("setsid()");
            exit(1);
        }
        umask(0); //设置权限掩码
        fd = open("/dev/null",O_RDWR); //打开黑洞设备以读写方式打开
        if(fd == -1) {
             perror("open");
            exit(1);
        }
        if(fd > 3) { // 关闭继承的文件描述符
            if(close(fd) == -1) {
                perror("close()");
                exit(1);
            }
        }
        // 重定向...
        
        for(;;) {
            // 守护进程要完成的任务...
        }
    } else if(pid > 0) {
        exit(0); // 父进程退出
    }
    // nerver reach...
    exit(0);
}
执行结果：



注意：进程守护化以后，只能使用kill命令杀掉该进程

6.9.4 后台进程和守护化
使用& ，可以将程序执行在后台：

BASH
1
./test >> out.txt 2>&1 &
在命令的末尾加个&符号后，程序可以在后台运行，但是一旦当前终端关闭，该程序就会停止运行，这就是后台进程。

后台进程和守护进程的区别

守护进程与终端无关，是被init进程收养的孤儿进程；而后台进程的父进程是终端，仍然可以在终端打印
守护进程在关闭终端时依然存在；而后台进程会随用户退出而停止
守护进程改变了会话、进程组、工作目录和文件描述符，后台进程直接继承父进程（shell）的
将进程守护化

可以使用nohup(no hang up)命令结合&将进程守护化：

BASH
1
nohup [进程名] [参数] 可执行文件 [重定向] &
例如：

BASH
1
nohup ./test >> out.txt &
将./test守护化，并将缓冲区的内容重定向至out.txt

BASH
1
nohup python -u test.py > nohup.out 2>&1 &
执行python程序，-u为python的参数，意为不启用缓冲，将内存中的内容直接写入到磁盘文件中。

BASH
1
nohup java -jar demo.jar
执行java程序。

6.10 系统日志
暂略

7 信号
该节对应第十章——信号。

7.1 前置概念
并发和并行

并发（concurrency）：把任务在不同的时间点交给处理器进行处理。在同一时间点，任务并不会同时运行。
并行（parallelism）：把每一个任务分配给每一个处理器独立完成。在同一时间点，任务一定是同时运行。


同步和异步

进程同步：按照一定的顺序协同进行（有序进行），而不是同时。即一组进程为了协调其推进速度，在某些地方需要相互等待或者唤醒，这种进程间的相互制约就被称作是进程同步。这种合作现象在操作系统和并发式编程中属于经常性事件。
例如，在主线程中，开启另一个线程。另一个线程去读取文件，主线程等待该线程读取完毕，那么主线程与该线程就有了同步关系。
进程异步：当一个异步过程调用发出后，调用者不能立刻得到结果。实际处理这个调用的部件在完成后，通过状态、通知和回调来通知调用者。
异步事件的处理：查询法（发生频率高）和通知法（发生频率低）

7.1 信号的概念
信号是软件层面的中断，是进程之间相互传递消息的一种方法，信号全称为软中断信号，也有人称作软中断，从它的命名可以看出，它的实质和使用很像中断。

进程之间可以通过调用kill库函数发送软中断信号。Linux内核也可能给进程发送信号，通知进程发生了某个事件（例如内存越界）。

每个信号都有一个名字。这些名字都以3个字符SIG开头。头文件<signal.h>中，信号名都被定义为正整数常量（信号编号）。

通过命令kill -l可以列出所有可用信号：

信号值 1 ~ 31 为不可靠信号（标准信号），信号会丢失；信号值 34 ~ 64 为可靠信号（实时信号），信号不会丢失。

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
[root@HongyiZeng signal]# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
信号名	信号值	发出信号的原因
SIGHUP	1	终端挂起或者控制进程终止
SIGINT	2	键盘中断Ctrl+c
SIGQUIT	3	键盘的退出键被按下
SIGILL	4	非法指令
SIGABRT	6	由abort(3)发出的退出指令
SIGFPE	8	浮点异常
SIGKILL	9	采用kill -9 进程编号 强制杀死程序。
SIGSEGV	11	无效的内存引用
SIGPIPE	13	管道破裂:写一个没有读端口的管道
SIGALRM	14	由alarm(2)发出的信号
SIGTERM	15	采用“kill 进程编号”或“killall 程序名”通知程序。
SIGUSR1	30,10,16	用户自定义信号1
SIGUSR2	31,12,17	用户自定义信号2
SIGCHLD	20,17,18	子进程结束信号
SIGCONT	19,18,25	进程继续（曾被停止的进程）
SIGSTOP	17,19,23	终止进程
SIGTSTP	18,20,24	控制终端（tty）上按下停止键
SIGTTIN	21,21,26	后台进程企图从控制终端读
SIGTTOU	22,22,27	后台进程企图从控制终端写
信号是异步事件的经典实例。产生信号的事件对进程而言是随机出现的。进程不能简单地测试一个变量（如 errno）来判断是否发生了一个信号，而是必须告诉内核”在此信号发生时，请执行下列操作”。

当某个信号出现时，可以告诉内核按下列三种方式之一进行处理，称之为信号的处理或与信号相关的工作：

忽略此信号
捕捉信号
执行系统默认工作
下图列出了所有信号的名字，说明了哪些系统支持此信号和信号对应的系统默认工作。可以看出C标准库支持的信号是最少的。大部分的信号的默认操作是终止进程。



core是在进程当前工作目录的core文件中复制了该进程的内存映像，core文件记录了进程终止时的错误报告，大多数UNIX系统调试程序都使用core文件检查进程终止时的状态。

补充：kill命令

BASH
1
kill [参数] [进程号]
常用参数：

-l：信号编号
如果是kill -l，则列出全部的信号名称
如果是kill -信号编号 pid，则将该编号对应的信号发送给指定pid的进程
默认为15，对应发出终止信号，例如kill 23007
其他常用编号：

信号编号	信号名	含义
0	EXIT	程序退出时收到该信息。
1	HUP	挂掉电话线或终端连接的挂起信号，这个信号也会造成某些进程在没有终止的情况下重新初始化。
2	INT	表示结束进程，但并不是强制性的，常用的 “Ctrl+C” 组合键发出就是一个 kill -2 的信号。
3	QUIT	退出。
9	KILL	杀死进程，即强制结束进程。
11	SEGV	段错误。
15	TERM	正常结束进程，是 kill 命令的默认信号。
7.3 signal函数
补充：typedef的用法总结

定义一种类型的别名：基本用法
C
1
2
typedef char* PCHAR;
PCHAR pa, pb;
C
1
2
3
4
5
6
typedef struct tagPOINT {
    int x;
    int y;
}POINT;

POINT p1, p2;
定义与平台无关的类型：当跨平台时，只要改下 typedef 本身就行，不用对其他源码做任何修改。标准库就广泛使用了这个技巧，比如size_t，pid_t。另外，因为typedef是定义了一种类型的新别名，不是简单的字符串替换，所以它比宏更稳健。
C
1
2
3
typedef long double REAL; // 平台1
typedef double REAL; // 平台2
// ...
为复杂声明定义一个新的简单的别名。
例如：

C
1
2
3
4
5
6
7
// 相当于给返回值为int，形参为int的这一类函数起了一个别名FUNC
typedef int FUNC(int);

// 定义了返回值为int，形参为int的函数f
FUNC f;  // 相当于：int f(int);

int a = f(1); // 使用
C
1
2
3
4
5
6
// 给返回值为int*，形参为int的指针函数（指针函数：返回指针的函数，本质是函数；函数指针：指向函数的指针，本质是指针）起了一个别名FUNCP，本质是一个函数
typedef int* FUNCP(int);

FUNCP p; // 相当于 int* p(int);

int* a = p(1); // 使用
C
1
2
3
4
5
6
// 给指向返回值为int*，形参为int的指针函数的指针其一个别名FUNCP，本质是一个指针
typedef int* (*FUNCP)(int);

FUNCP p; // 相当于 int* (*p)(int);

int* a = (*p)(1); // 使用
又如：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
// 原始声明，该声明本质上是一个函数指针
void (* signal(int signum, void (*func)(int)))(int);

// 最外层是一个函数指针，形式为：
void (*)(int);

// 内层是一个返回指针，形参为int，函数指针的函数signal
// 注意到signal的形参也有一个函数指针void (*func)(int)，这两个函数指针指向的都是同一种函数，即返回值为void，形参为int
void (*)(int);

// 因此给公共部分起一个别名sighandler_t，sighandler_t本质上是一个指针
typedef void (*sighandler_t)(int);

// 简化后的形式：
sighandler_t signal(int signum, sighandler_t func);
UNIX系统信号机制最简单的接口是signal函数，函数原型如下：

C
1
2
3
4
5
6
7
8
9
10
// CONFORMING TO C89, C99, POSIX.1-2001.
#include <signal.h>

typedef void (*sighandler_t)(int);

sighandler_t signal(int signum, sighandler_t handler);

// 注意，typedef没有定义在头文件中，因此必须要写出，否则按照下面的形式给出signal函数，APUE上的就是这种形式：

void (* signal(int signum, void (*func)(int)))(int);
signum参数是上图中的信号名，常用宏名来表示，例如SIGINT
func参数是下面的一种：
SIG_IGN：向内核忽略此信号，除了SIGKILL和SIGSTOP
SIG_DFL：执行系统默认动作
当接到此信号后要调用的函数的地址：在信号发生时，调用该函数；称这种处理为捕捉该信号，称此函数为信号处理程序或信号捕捉函数
作用：signal函数为signum所代表的信号设置一个信号处理程序func，换句话说，signal就是一个注册函数。
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
int main(void) {
    int i;
    // 忽略SIGINT信号
    signal(SIGINT, SIG_IGN);
    for(i = 0; i < 10; i++) {
        // 每1s向终端打印1个*
        write(1, "*", 1);
        sleep(1);
    }
    exit(0);
}
信号SIGINT产生的方式就是快捷键CTRL + C。

代码示例2

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>

static void int_handler(int s) {
    // 向终端打印!
    write(1, "!", 1);
}

int main(void) {
    int i;
    // 函数名就是函数的地址
    signal(SIGINT, int_handler);
    for(i = 0; i < 10; i++) {
        write(1, "*", 1);
        sleep(1);
    }
    exit(0);
}
每按1次CTRL + C，终端就打印1个!

代码示例3——阻塞和非阻塞

上述程序，如果一直按着CTRL + C，程序会小于10S就会结束。

原因在于：信号会打断阻塞的系统调用。这里的阻塞是write和sleep函数。

分析：进程运行到sleep(1)的时候，由运行态进入阻塞态，此时如果有信号到来，例如SIGINT，会打断阻塞（唤醒进程），让进程进入就绪态，获得时间片进入运行态，此时进程还没阻塞到1s，就进入了就绪态，即信号会打断阻塞的系统调用。

阻塞：为了完成一个功能，发起一个调用，如果不具备条件的话则一直等待，直到具备条件则完成
非阻塞：为了完成一个功能，发起一个调用，具备条件直接输出，不具备条件直接报错返回
此前学习过的所有IO函数，都是阻塞IO，即阻塞的系统调用。

以open为例，进程调用open时，进入阻塞态，等待IO设备打开，如果IO设备打开时间过长，此时有一个信号到来，就会打断open调用，使其打开设备失败。

因此，在设备打开失败的时候，需要判断是因为open自身引发的错误，还是因为信号打断而没有打开，对于前者，以以往的方式处理错误，而对于后者应该尝试再次打开设备，而不是报错后退出程序。

注意：对于所有的阻塞系统调用，都要处理是因为自身调用出现的真错，还是因为信号中断导致的假错。

在宏中，有一个名为EINTR的errno，即为被信号中断而引发的错误。当进程在执行一个阻塞的系统调用时捕捉到一个信号，则被中断不再执行该系统调用，该系统调用返回错误，errno就会被设置为EINTR。



以前面的一个程序为例，修改后的代码为：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

#define BUFSIZE 1024 // 缓冲区大小

int main(int argc, char **argv) {
    int sfd, dfd;
    char buf[BUFSIZE];
    int len, ret, pos;

    if(argc < 3) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }
	
    do {
        if((sfd = open(argv[1], O_RDONLY)) < 0) {
            if(errno != EINTR) {
                // 不是信号导致的错误，就退出
                perror("open()");
                exit(1);
            }
        }
    } while(sfd < 0)
	
    do {
        if((dfd = open(argv[2], O_WRONLY|O_CREAT|O_TRUNC, 0600)) < 0) {			
            if(errno != EINTR) {
                close(sfd);
                perror("open()");
                exit(1);
            }
    	}
    }while(dfd < 0)
    	

    while(1) {
        if((len = read(sfd, buf, BUFSIZE)) < 0) {
            if(errno == EINTR)
                continue;
            perror("read()");
            break;
        }
		
        if(len == 0)
            break;

        pos = 0;
        while(len > 0) {
            if((ret = write(dfd, buf + pos, len)) < 0) {
                if(errno == EINTR)
                    continue;
                perror("write()");
                exit(1);
            }
            pos += ret;
            len -= ret;
        }
    }
    close(dfd);
    close(sfd);

    exit(0);
}
7.4 不可靠的信号
信号处理程序由内核调用，在执行该程序时，内核为该处理程序布置现场，此时如果又来一个信号，内核再次调用信号处理程序，可能会冲掉第一次调用布置的现场。

7.5 可重入函数
可重入函数（即可以被中断的函数）可以被一个以上的任务调用，而不担心数据破坏。可重入函数在任何时候都可以被中断，而一段时间之后又可以恢复运行，而相应的数据不会破坏或者丢失。

不可重入的函数由于使用了一些系统资源，比如全局变量区，中断向量表等，所以它如果被中断的话，可能会出现问题，这类函数是不能运行在多任务环境下的。I/O代码通常不是可重入的，因为它们依赖于像磁盘这样共享的，单独的资源。

所有的系统调用都是可重入的，一部分库函数也是可重入的，例如memcpy。

函数不可重入的条件

函数内使用了静态的数据。
函数内使用了malloc或者free函数
函数内调用了标准的I/O函数
例如：

C
1
2
3
4
5
6
int temp;
void swap(int *ex1, int *ex2) {
    temp = *ex1; //(1)
    *ex1 = *ex2;
    *ex2 = temp;
}
分析：该函数中的全局变量temp是的函数变成了一个不可重入的函数，因为在多任务系统中，假如在任务1中调用swap函数，而程序执行到（1）处时被中断，进而执行其它的任务2，而刚好任务2也调用了swap函数，则temp里存的值则会被任务2改变。从而回到任务1被中断处继续执行的时候，temp里存的值已经不再是原来存的temp值了，进而产生了错误。

使得函数可重入的方法

不要使用全局变量，防止别的代码覆盖这些变量的值。
调用这类函数之前先关掉中断，调用完之后马上打开中断。防止函数执行期间被中断进入别的任务执行。
使用信号量（互斥条件）
7.6 信号的响应过程
补充：用户态和内核态的切换

Linux系统中的内核态本质是内核，是一种特殊的软件程序，用于控制计算机的硬件资源，例如协调CPU资源，分配内存资源，并且提供稳定的环境供应用程序运行。0-4G范围的虚拟空间地址都可以操作，尤其是对3-4G范围的高位虚拟空间地址必须由内核态去操作。

用户态提供应用程序运行的空间，为了使应用程序访问到内核管理的资源，例如CPU，内存，I/O等。用户态只能受限的访问内存，且不允许访问外设(硬盘、网卡等);内核态CPU可以访问内存所有数据，包括外设，且可以将自己从一个程序切换到另一个程序。

用户态切换到内核态的三种方式：

发生系统调用时：（主动）这是处于用户态的进程主动请求切换到内核态的一种方式。用户态的进程通过系统调用申请使用操作系统提供的系统调用服务例程来处理任务。而系统调用的机制，其核心仍是使用了操作系统为用户特别开发的一个中断机制来实现的，即软中断。
产生异常时：（被动）当CPU执行运行在用户态下的程序时，发生了某些事先不可知的异常，这时会触发由当前运行的进程切换到处理此异常的内核相关的程序中，也就是转到了内核态，如缺页异常。
外设产生中断时：（被动）当外围设备完成用户请求的操作后，会向CPU发出相应的中断信号，这时CPU会暂停执行下一条即将要执行的指令转而去执行与中断信号对应的处理程序，如果先前执行的指令是用户态下的程序，那么这个转换的过程自然也就发生了由用户态到内核态的切换。比如硬盘读写操作的完成，系统会切换到硬盘读写的中断处理程序中执行后续操作等。
信号在内核中的表示

信号递达（Delivery）：实际执行信号处理的动作。
信号未决（Pending）：信号从产生到递达之间的状态。
信号阻塞（Block）：被阻塞的信号产生时将保持在未决状态，直到进程解除对此信号的阻塞，才执行递达的动作。
有如下结论：

信号从收到到响应有一个不可避免的延迟。
标准信号的响应没有严格顺序。
内核为每个进程都维护了两个位图，一般为32位，两个位图进行与操作：

信号屏蔽字 mask ：用来屏蔽信号，mask初始值一般全部都是1，表示不屏蔽全部信号
位图 pending： 用来记录当前进程收到哪些信号，一般初始值全部都是0，表示没有收到信号，未决信号集。
信号响应过程

以打印*和!的程序为例。

在没有其他中断的情况下，当时间片耗尽时，进程被计时器中断，使得该进程被切换至内核态，在内核态中运行计时器中断的处理函数（handler），进程调度算法就发生在计时器中断的处理函数中（在这个处理函数中，系统会根据任务调度算法，从就绪队列里选择下一个运行的进程）。该进程等待调度。
进程当前的状态和信息被压入到内核栈中，其中包括程序的返回地址。
进程被内核调度时，进程从内核态切换至用户态，从内核栈中保存的返回地址继续运行
进程从内核态切换至用户态时（也只有在这个时刻），会执行mask & pending，确认自己是否收到信号。对于这种情况，按位与结果为每位都为0，表示进程没有收到信号。
当进程在某一刻收到SIGINT信号，此时将pending第2位置为1，表示收到SIGINT信号，但此时进程并不会被该信号中断（即响应该信号）。当时间片耗尽，一直到被调度，进程从内核态切换至用户态，执行mask & pending时，发现只有第2位结果为1，此时进程才响应该信号。因此说，信号从收到到响应有一个不可避免的延迟。
响应信号时的操作：内核将该位的mask和pending都置为0，并将程序返回地址修改为int_handler，即信号响应程序的地址，此时在用户态执行信号响应程序。换句话说，信号的收到的时刻是随机的，而信号的响应总是在从内核态切换到用户态时进行的；
信号响应程序执行完毕后，从用户态切换至内核态，内核将返回地址修改为程序的返回地址，并将mask的该位置为1；
再次被调度到时，切换至用户态，执行mask & pending，发现均为0，说明刚才的信号已经被响应了，则继续向下执行程序；
如何忽略一个信号？

注意到func（信号处理程序）可以为宏SIG_ING，实质上是将mask的位置为0，表示屏蔽该信号。

注意：我们能做的是不响应信号，即不对信号做处理（屏蔽），而不是阻止信号产生和到来。信号的随时都会到来，将pending置为1。

标准信号为什么不可靠，或者说标准信号为什么会丢失？

信号响应时，位图为(m0 p0)，此时又来多个信号，则p被反复置为1，即结果总是为(m0 p1)；信号响应完毕时，将m置为1，此时为(m1 p1)；返回用户态时，发现该位仍然为1，说明又来一个信号（注意不是多个），则继续响应…所以多个信号到来时，只有最后一个信号能够被响应，前面的信号都被丢失了。

总结：mask和pending的变化情况

C
1
2
3
4
5
6
7
8
9
mask pending
1		0	// 初始化
			// 进程处理自己的任务...
1		1	// 某一时刻信号到来
            // 进程处理自己的任务...,直到重新被调度时
0		0	// 信号响应 内核态->用户态时
    		// 进入到信号处理函数中执行
1		0	// 信号响应完毕
     		// 进程继续处理自己的任务
7.7 常用函数Ⅰ
7.7.1 kill
kill函数用于向进程发送信号，注意不是杀死进程。

C
1
2
3
4
#include <sys/types.h>
#include <signal.h>

int kill(pid_t pid, int sig);
参数：

pid：向哪个进程发送信号
pid > 0：发送信号给指定进程
pid = 0：发送信号给跟调用kill函数的那个进程处于同一进程组的进程，相当于组内广播。
pid < -1：发送信号给该绝对值所对应的进程组id的所有组员，相当于组内广播。
pid = -1：发送信号给所有权限发送的所有进程。
sig：待发送的信号
sig = 0：没有信号发送，但会返回-1并设置errno，用来检测某个进程id或进程组id是否存在。注意返回-1时并不能表明该id不存在，而是要根据errno来判断，详见下面的返回值。
返回值：

成功返回0
失败返回-1，并设值errno
EINVAL：无效的信号sig
EPERM：调用进程没有权限给pid的进程发送信号
ESRCH：进程或进程组不存在
7.7.2 raise
raise函数用于向调用进程发送信号，即自己给自己发送信号。

C
1
2
3
4
5
6
#include <signal.h>

int raise(int sig);

// 相当于
kill(getpid(), sig);
7.7.3 alarm
alarm函数

C
1
2
3
#include <unistd.h>

unsigned int alarm(unsigned int seconds);
作用：设置定时器。在指定seconds后，内核会给当前进程发送SIGALRM信号（定时器超时）。进程收到该信号，默认动作终止。每个进程都有且只有唯一的一个定时器，所以多个alarm函数共同调用时，后面设置的时钟会覆盖掉前面的时钟。

返回值：返回0或剩余的秒数，无失败。

代码示例

C
1
2
3
4
5
6
7
8
9
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    alarm(5);
    while(1);
    exit(0);
}
执行结果：

BASH
1
2
[root@HongyiZeng signal]# ./alarm 
Alarm clock
7.7.4 pause
pause函数用于等待信号。

C
1
2
3
#include <unistd.h>

int pause(void);
进程调用pause函数时，会造成进程主动挂起（处于阻塞状态，并主动放弃CPU），并且等待信号将其唤醒。

代码示例

C
1
2
3
4
5
6
7
8
9
10
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    alarm(5);
    while(1)
        pause();
    exit(0);
}
当调用到pause()时，该进程挂起，此时不再占用CPU，5s过后，接收到SIGALRM信号，采取默认动作终止。

信号的处理方式有三种：

默认动作
忽略处理
捕捉
进程收到一个信号后，会先处理响应信号，再唤醒pause函数。于是有下面几种情况：

如果信号的默认处理动作是终止进程，则进程将被终止，也就是说一收到信号进程就终止了，pause函数根本就没有机会返回，例如上面的例子
如果信号的默认处理动作是忽略，则进程将直接忽略该信号，相当于没收到这个信号，进程继续处于挂起状态，pause函数不返回
如果信号的处理动作是捕捉，则进程调用完信号处理函数之后，pause返回-1，errno设置为EINTR，表示“被信号中断”
pause收到的信号不能被屏蔽，如果被屏蔽，那么pause就不能被唤醒
sleep = alarm + pause

代码示例

需求：让程序等待5s

使用time
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

int main(void) {
    time_t end;
    int64_t count = 0;

    end = time(NULL) + 5;

    while(time(NULL) <= end) {
        count++;
    }

    printf("%lld\n", count);

    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
[root@HongyiZeng signal]# time ./pause > /tmp/out
real    0m5.142s
user    0m4.950s
sys     0m0.005s
[root@HongyiZeng signal]# time ./pause > /tmp/out
real    0m5.181s
user    0m5.045s
sys     0m0.008s
使用alarm
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>

// 循环跳出的标志
static int loop = 1;

static void alrm_handler(int s) {
    loop = 0;
}

int main(void) {
    int64_t count = 0;
    // 对信号SIGALRM注册信号处理函数
    signal(SIGALRM, alrm_handler);
    alarm(5);
    while(loop)
        count++;
    printf("%lld\n", count);
    exit(0);
}
运行结果：

BASH
1
2
3
4
5
6
7
8
[root@HongyiZeng signal]# time ./5sec_sig > /tmp/out
real    0m5.029s
user    0m4.821s
sys     0m0.012s
[root@HongyiZeng signal]# time ./5sec_sig > /tmp/out
real    0m5.017s
user    0m4.864s
sys     0m0.006s
当对上述程序进行编译优化时：

BASH
1
gcc 5sec_sig.c -O1
再次执行，发现程序一直在运行。

原因：编译优化时，gcc看到下面的循环体中没有出现loop，所以gcc认为loop是不变的，将loop的值存储在高速缓存（CPU的寄存器）中，每次读取loop的值时，从高速缓存中读取，而不是在loop实际存放的内存地址中读取，因此一直在此处循环。

C
1
2
while(loop)
    count++;
解决方法：用volatile修饰loop，此时编译器认为该变量易变，每次读取时从实际内存地址中读取：

C
1
static volatile int loop = 1;
从汇编的角度来看：

BASH
1
gcc -S 5sec_sig.c -O1
未加易变修饰前：（省略不重要的代码）

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
main:
.LFB24:
        .cfi_startproc
        subq    $8, %rsp
        .cfi_def_cfa_offset 16
        movl    $5, %edi
        movl    $0, %eax
        call    alarm
        movl    $alrm_handler, %esi
        movl    $14, %edi
        call    signal
        cmpl    $0, loop(%rip) # 判断loop
        jne     .L5 # 跳转到循环体中
        movl    $0, %esi
        movl    $.LC0, %edi
        movl    $0, %eax
        call    printf
        movl    $0, %edi
        call    exit
.L5:
        jmp     .L5 # 死循环
        .cfi_endproc
加上易变修饰后：

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
main:
.LFB24:
        .cfi_startproc
        subq    $8, %rsp
        .cfi_def_cfa_offset 16
        movl    $5, %edi
        movl    $0, %eax
        call    alarm
        movl    $alrm_handler, %esi
        movl    $14, %edi
        call    signal
        movl    loop(%rip), %eax
        testl   %eax, %eax # 判断loop
        je      .L5 # 跳入循环
        movl    $0, %esi
.L4:
        addq    $1, %rsi
        movl    loop(%rip), %eax
        testl   %eax, %eax
        jne     .L4
        jmp     .L3
.L5:
        movl    $0, %esi
.L3:
        movl    $.LC0, %edi
        movl    $0, %eax
        call    printf
        movl    $0, %edi
        call    exit
        .cfi_endproc
7.7.5 漏桶
leaky bucket也叫漏桶，就是将请求先放到一个桶中进行排队，然后按一个固定的速率来处理请求，即所谓的漏出。

桶具有一定的容量，即最大容量的请求数，当排队请求的数量超过桶的容量时，再进来的请求就直接过滤掉，不再被处理。换句话说就是请求先在桶中排队，系统或服务只以一个恒定的速度从桶中将请求取出进行处理。如果排队的请求数量超过桶能够容纳的最大数量，即桶装满了，则直接丢弃。

漏桶算法(Leaky Bucket)是网络世界中流量整形（Traffic Shaping）或速率限制（Rate Limiting）时经常使用的一种算法，它的主要目的是控制数据注入到网络的速率，平滑网络上的突发流量。

需求：实现一个复制文本到标准输出的程序，要求10字符10字符的复制，且不能让CPU空转。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <signal.h>
#include <errno.h>

#define CPS     10
#define BUFSIZE CPS

static volatile int loop = 0;

static void alrm_handler(int s) {
    alarm(1);
    loop = 1;
}

int main(int argc, char **argv) {
    int sfd, dfd = 1;
    char buf[BUFSIZE];
    int len, ret, pos;

    if(argc < 2) {
        fprintf(stderr, "Usage...\n");
        exit(1);
    }

    signal(SIGALRM, alrm_handler);
    alarm(1);
	
    do {
        if((sfd = open(argv[1], O_RDONLY)) < 0) {
            if(errno != EINTR) {
                perror("open()");
                exit(1);
            }
        }
    } while(sfd < 0);
	
    while(1) {

        while(!loop)
            // 防止CPU空转
            pause();
        loop = 0;
        while((len = read(sfd, buf, BUFSIZE)) < 0) {
            if(errno == EINTR)
                continue;
            perror("read()");
            break;
        }
		
        if(len == 0)
            break;

        pos = 0;
        while(len > 0) {
            if((ret = write(dfd, buf + pos, len)) < 0) {
                if(errno == EINTR)
                    continue;
                perror("write()");
                exit(1);
            }
            pos += ret;
            len -= ret;
        }
    }
    close(sfd);
    exit(0);
}
7.7.6 令牌桶
系统会以一个恒定的速度往桶里放入令牌，而如果请求需要被处理，则需要先从桶里获取一个令牌，当桶里没有令牌可取时，则拒绝服务。 当桶满时，新添加的令牌被丢弃或拒绝。

令牌桶算法是一个存放固定容量令牌（token）的桶，按照固定速率往桶里添加令牌。令牌桶算法基本可以用下面的几个概念来描述：

令牌将按照固定的速率被放入令牌桶中。比如每秒放10个。
桶中最多存放b个令牌，当桶满时，新添加的令牌被丢弃或拒绝。
当一个n个字节大小的数据包到达，将从桶中删除n个令牌，接着数据包被发送到网络上。
如果桶中的令牌不足n个，则不会删除令牌，且该数据包将被限流（要么丢弃，要么缓冲区等待）。
令牌桶和漏桶的区别

主要区别在于漏桶算法能够强行限制数据的传输速率，而令牌桶算法在能够限制数据的平均传输速率外，还允许某种程度的突发传输。在令牌桶算法中，只要令牌桶中存在令牌，那么就允许突发地传输数据直到达到用户配置的门限，因此它适合于具有突发特性的流量。

代码示例

暂略

7.8 信号集
信号集是一个能够表示多个信号的数据类型。

POSIX.1定义数据类型sigset_t来表示一个信号集（本质为整型），并且定义了下列5个处理信号集的函数：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
#include <signal.h>

// 置空一个信号集
int sigemptyset(sigset_t *set);

// 填充满一个信号集
int sigfillset(sigset_t *set);

// 将一个信号加入信号集
int sigaddset(sigset_t *set, int signum);

// 将一个信号从信号集删除
int sigdelset(sigset_t *set, int signum);

// 上述函数成功返回0，失败返回-1

// 检查一个信号集中是否有这个信号，存在返回1，不存在返回0
int sigismember(const sigset_t *set, int signum);
7.9 sigprocmask函数
sigprocmask函数可以检测或更改，或同时进行检测和更改进程的信号屏蔽字（阻塞信号集）。注：对信号来说，阻塞和屏蔽是一个意思。

函数原型：

C
1
2
3
#include <signal.h>
// 成功返回0，失败返回-1
int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
how：用于指定信号修改的方式，有三种选择：
SIG_BLOCK：该进程新的信号屏蔽字是其当前信号屏蔽字和set指向信号集的并集。即set包含了希望阻塞的附加信号；
SIG_UNBLOCK：该进程新的信号屏蔽字是其当前信号屏蔽字和set所指向信号集补集的交集。即set包含了希望解除阻塞的信号；
SIG_SETMASK：该进程新的信号屏蔽是set指向的值；
*set：和how结合使用
*oldset：进程的当前信号屏蔽字通过oset返回（oldset），如果不关心旧的信号集设置，可设置为NULL
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>

static void int_handler(int s) {
	write(1, "!", 1);
}

int main(void) {
	int i;
	sigset_t set;

	signal(SIGINT, int_handler);
    // 置空一个信号集set
    sigemptyset(&set);
    // 将SIGINT加入到信号集set中
    sigaddset(&set, SIGINT);
    while(1) {
        // 设置信号屏蔽字
        // 将SIGINT屏蔽
        sigprocmask(SIG_BLOCK, &set, NULL);
        for(i = 0; i < 5; i++) {
            write(1, "*", 1);
            sleep(1);
	    }
        write(1, "\n", 1);
        // 打印5个*后，将SIGINT解除屏蔽
        sigprocmask(SIG_UNBLOCK, &set, NULL);
    }
	exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
[root@HongyiZeng signal]# ./block
*****
*^C****
!*^C^C^C^C*^C^C^C^C^C^C^C^C^C***
!***^C^C^C**
!****^\Quit
解析：第三行发送SIGINT信号时，SIGINT的屏蔽字位为0，pending位置为1，当打印完成后，解除屏蔽，进程响应该信号，在第四行打印了!；第四行多次发送SIGINT信号，也只是对pending位反复置为1，在第五行也只打印一次!；

代码示例2

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
int main(void) {
	int i;
	sigset_t set, oset;
	signal(SIGINT, int_handler);
    sigemptyset(&set);
    sigaddset(&set, SIGINT);
    while(1) {
        // 将SIGINT阻塞，并将原始的屏蔽字存储在oset中，且原始屏蔽字都是1
        sigprocmask(SIG_BLOCK, &set, &oset);
        for(i = 0; i < 5; i++) {
            write(1, "*", 1);
            sleep(1);
	    }
        write(1, "\n", 1);
        // 将屏蔽字设置为oset信号集的值，即全1，没有屏蔽
        sigprocmask(SIG_SETMASK, &oset, NULL);
    }
	exit(0);
}
功能和代码示例1相同。

代码示例3

为了保证在进入模块和退出模块时进程的信号屏蔽字是一致的，需要修改代码为：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
int main(void) {
	int i;
	sigset_t set, oset, saveset;
	signal(SIGINT, int_handler);
    sigemptyset(&set);
    sigaddset(&set, SIGINT);
    // 将进程起始的信号屏蔽字保存在saveset中
    sigprocmask(SIG_UNBLOCK, &set, &saveset);
    while(1) {
        sigprocmask(SIG_BLOCK, &set, &oset);
        for(i = 0; i < 5; i++) {
            write(1, "*", 1);
            sleep(1);
	    }
        write(1, "\n", 1);
        sigprocmask(SIG_SETMASK, &oset, NULL);
    }
    // 退出时，还原信号屏蔽字
    sigprocmask(SIG_SETMASK, &saveset, NULL);
	exit(0);
}
7.10 sigpending函数
略

7.11 常用函数Ⅱ
7.11.1 sigsuspend函数
该函数通过将进程的屏蔽字替换为由参数mask给出的信号集，然后将进程挂起（阻塞）。

C
1
2
3
#include <signal.h>

int sigsuspend(const sigset_t *mask);
功能描述上和pause函数相同，即在等待信号的时候让进程挂起。

区别：sigsuspend实际是将sigprocmask和pause结合起来原子操作。

代码示例

需求：先打印一排*，等待信号，然后打印一排*，以此类推。

使用pause
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
static void int_handler(int s) {
	write(1, "!", 1);
}

int main(void) {
	int i;
	sigset_t set, oset, saveset;
	signal(SIGINT, int_handler);
    sigemptyset(&set);
    sigaddset(&set, SIGINT);
    sigprocmask(SIG_UNBLOCK, &set, &saveset);
    while(1) {
        sigprocmask(SIG_BLOCK, &set, &oset);
        for(i = 0; i < 5; i++) {
            write(1, "*", 1);
            sleep(1);
	    }
        write(1, "\n", 1);
        sigprocmask(SIG_SETMASK, &oset, NULL);
        pause();
    }
    sigprocmask(SIG_SETMASK, &saveset, NULL);
	exit(0);
}
执行结果：

BASH
1
2
3
4
5
[root@HongyiZeng signal]# ./susp 
*****
^C!*****
^C!**^C^C^C***
!
第二行开始处：当内层循环执行完毕后，到pause处挂起，此时SIGINT到来，首先处理信号，然后唤醒进程，继续执行内层循环打印。

注意第四行，当在内层循环执行时，有多个SIGINT信号到来，由于被屏蔽，所以不打印叹号，打印星号结束后，代码第十九行解除屏蔽（注意，代码第十九行和第二十行之间也会存在多个中断），响应信号（第五行的第一个叹号），再执行到代码第二十行处时，此时没有信号到来，所以一直挂起。

使用sigsuspend
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
static void int_handler(int s) {
	write(1, "!", 1);
}

int main(void) {
	int i;
	sigset_t set, oset, saveset;
	signal(SIGINT, int_handler);
    sigemptyset(&set);
    sigaddset(&set, SIGINT);
    // 保存初始屏蔽字
    sigprocmask(SIG_UNBLOCK, &set, &saveset);
    // 设置阻塞
    sigprocmask(SIG_BLOCK, &set, &oset);
    while(1) {
        for(i = 0; i < 5; i++) {
            write(1, "*", 1);
            sleep(1);
	    }
        write(1, "\n", 1);
        // 挂起
        sigsuspend(&oset);
        /*
        sigset_t tmpset;
        sigprocmask(SIG_SETMASK, &oset, &tmpset);
        pause();
        sigprocmask(SIG_SETMASK, &tmpset, NULL);
        */
    }
    sigprocmask(SIG_SETMASK, &saveset, NULL);
	exit(0);
}
7.11.2 sigaction函数
sigaction函数是升级版的signal函数。

sigaction函数的功能是检查或修改与指定信号相关联的处理动作（可同时两种操作）。

C
1
2
3
#include <signal.h>

int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
signum参数指出要捕获的信号类型
act参数指定新的信号处理方式
oldact参数输出先前信号的处理方式（如果不为NULL的话）
struct sigaction结构体定义如下：

C
1
2
3
4
5
6
7
struct sigaction {
    void (*sa_handler)(int);
    void (*sa_sigaction)(int, siginfo_t *, void *);
    sigset_t sa_mask;
    int sa_flags;
    void (*sa_restorer)(void);
}
sa_handler：此参数和signal()的参数handler相同，代表新的信号处理函数
sa_mask：用来设置在处理该信号时暂时将 sa_mask 指定的信号集屏蔽
sa_flags：用来设置信号处理的其他相关操作，下列的数值可用。
SA_RESETHAND：当调用信号处理函数时，将信号的处理函数重置为缺省值SIG_DFL
SA_RESTART：如果信号中断了进程的某个系统调用，则系统自动启动该系统调用
SA_NODEFER ：一般情况下， 当信号处理函数运行时，内核将阻塞该给定信号。但是如果设置了SA_NODEFER标记， 那么在该信号处理函数运行时，内核将不会阻塞该信号
SA_SIGINFO：调用信号处理器程序时携带了额外参数，其中提供了关于信号的深入信息
sa_sigaction：这是一个三个参数的sa_handler函数版本。如果设置了SA_SIGINFO标志位，则会使用sa_sigaction处理函数，否则使用sa_handler处理函数。其中参数siginfo_t是一个结构体类型
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
// 需求：三种信号SIGQUIT，SIGTERM，SIGINT共用一个信号处理函数，且该函数中释放了重要的资源，因此不可重入。

static void daemon_exit(int s) {
    // 释放相关资源
    // ...
    exit(0);
}

int main() {
    struct sigaction sa;
    // 设置信号处理函数
    sa.sa_handler = daemon_exit;
    sigemptyset(&sa.sa_mask);
    // 将SIGQUIT，SIGTERM，SIGINT信号阻塞，防止重入
    sigaddset(&sa.sa_mask, SIGQUIT);
    sigaddset(&sa.sa_mask, SIGTERM);
    sigaddset(&sa.sa_mask, SIGINT);
    sa.sa_flags = 0;
    
    // 要捕获SIGQUIT，SIGTERM，SIGINT三种信号
    sigaction(SIGINT, &sa, NULL);
    sigaction(SIGTERM, &sa, NULL);
    sigaction(SIGQUIT, &sa, NULL);
    // ...
    exit(0);
}
代码示例2

暂略

7.12 实时信号
暂略

7.13 信号实现原理
该节是对7.6节信号的响应过程的补充扩展。

7.13.1 未决和阻塞信号集
在 PCB 中有两个非常重要的信号集。一个称之为 阻塞信号集 mask，另一个称之为 未决信号集 pending。这两个信号集体现在内核中就是两张表。

但是操作系统不允许我们直接对这两个信号集进行任何操作，而是需要自定义另外一个集合，借助信号集操作函数（详见7.8-7.10节）来对 PCB 中的这两个信号集进行修改。

信号的 “未决（pending）” 是一种状态，指的是从信号的产生到信号被处理前的这一段时间。
信号的 “阻塞（mask）” 是一个开关动作，指的是阻止信号被处理，但不是阻止信号产生。
阻塞信号集和未决信号集在内核中的结构是相同的，它们都是一个整形数组 (被封装过的)，一共 128 字节 （int [32] == 1024 bit），1024 个标志位，其中前 31 个标志位，每一个都对应一个 Linux 中的标准信号，通过标志位的值来标记当前信号在信号集中的状态。

7.13.2 原理介绍
① 信号处理相关的数据结构
在进程管理结构 task_struct（详见8.1.2.①节） 中有几个与信号处理相关的字段，如下：

C
1
2
3
4
5
6
7
8
9
struct task_struct {
    ...
    int sigpending; // 
    ...
    struct signal_struct *sig; // 信号的处理方法
    sigset_t blocked; // 阻塞信号集
    struct sigpending pending; // 未决信号集
    ...
}
sigpending：表示进程是否有信号需要处理（1表示有，0表示没有）
blocked：表示被屏蔽的信息，每个位代表一个被屏蔽的信号
sig：表示信号相应的处理方法，其类型是 struct signal_struct，定义如下：
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#define  _NSIG  64

struct signal_struct {
	atomic_t		count;
	struct k_sigaction	action[_NSIG];
	spinlock_t		siglock;
};

typedef void (*__sighandler_t)(int);

// sigaction详见7.11.2节
struct sigaction {
	__sighandler_t sa_handler;
	unsigned long sa_flags;
	void (*sa_restorer)(void);
	sigset_t sa_mask;
};

struct k_sigaction {
	struct sigaction sa;
};
pending：其类型为 struct sigpending，存储着进程接收到的信号队列，定义如下：
C
1
2
3
4
5
6
7
8
9
struct sigqueue {
	struct sigqueue *next;
	siginfo_t info;
};

struct sigpending {
	struct sigqueue *head, **tail;
	sigset_t signal;
};
当进程接收到一个信号时，就需要把接收到的信号添加 pending 这个队列中。

② 发送信号
暂略

8 线程
本节对应APUE第十一、十二章内容

8.1 线程概念
线程本质：一个正在运行的函数。

进程本质：加载到内存的程序。

进程是操作系统分配资源的单位，线程是调度的基本单位，线程之间共享进程资源。

典型的UNIX进程可以看成只有一个控制线程：一个进程在某一时刻只能做一件事情。有了多个控制线程以后，在程序设计时就可以把进程设计成在某一时刻能够做不止一件事，每个线程处理各自独立的任务。

每个线程都包含有表示执行环境所必需的信息，其中包括进程中标识线程的线程ID、一组寄存器值、栈、调度优先级和策略、信号屏蔽字、errno变量以及线程私有数据。

一个进程的所有信息对该进程的所有线程都是共享的，包括可执行程序的代码、程序的全局内存和堆内存、栈以及文件描述符。

8.1.1 POSIX线程接口
POSIX线程（英语：POSIX Threads，常被缩写为Pthreads）是POSIX的线程标准，定义了创建和操纵线程的一套API。

Pthreads定义了一套C语言的类型、函数与常量，它以pthread.h头文件和一个线程库实现。Pthreads API中大致共有100个函数调用，全都以pthread_开头，并可以分为四类：

线程管理，例如创建线程，等待(join)线程，查询线程状态等。
互斥锁（Mutex）：创建、摧毁、锁定、解锁、设置属性等操作
条件变量（Condition Variable）：创建、摧毁、等待、通知、设置与查询属性等操作
使用了互斥锁的线程间的同步管理
因此在编译时需要makefile的编译和链接选项：

MAKEFILE
1
2
CFLAGS+=-pthread # 编译选项
LDFLAGS+=-pthread # 链接选项
BASH
1
cc -pthread  -pthread  create.c   -o create
8.1.2 进程和线程
① 进程控制块
进程控制块是用于保存一个进程信息的结构体，称之为PCB（process control block），相当于进程的身份证；PCB是进程存在的唯一标识。

在Linux中PCB的信息存放在task_struct结构体中。结构体的主要成员如下：

进程状态
进程执行时，它会根据具体情况改变状态。进程状态是调度和对换的依据。Linux中的进程主要有如下状态：

字段名	含义
TASK_RUNNING	可运行
TASK_INTERRUPTIBLE	可中断的等待状态
TASK_UNINTERRUPTIBLE	不可中断的等待状态
TASK_ZOMBIE	僵死
TASK_STOPPED	暂停
TASK_SWAPPING	换入/换出
进程调度信息
调度程序利用这部分信息决定系统中哪个进程最应该运行，并结合进程的状态信息保证系统运转的公平和高效。这一部分信息通常包括进程的类别（普通进程还是实时进程）、进程的优先级等等。

字段名	含义
need_resched	调度标志
Nice	静态优先级
Counter	动态优先级
Policy	调度策略
rt_priority	实时优先级
标识符
每个进程有进程标识符、用户标识符、组标识符。

域名	含义
Pid	进程标识符
Uid、gid	用户标识符、组标识符
Euid、egid	有效用户标识符、有效组标识符
Suid、sgid	备份用户标识符、备份组标识符
Fsuid、fsgid	文件系统用户标识符、文件系统组标识符
进程通信有关信息
为了使进程能在同一项任务上协调工作，进程之间必须能进行通信即交流数据。

Linux支持多种不同形式的通信机制。它支持典型的Unix通信机制（IPC Mechanisms）：信号（Signals）、管道（Pipes），也支持System V通信机制：共享内存（Shared Memory）、信号量和消息队列（Message Queues）

进程链接信息
程序创建的进程具有父/子关系。因为一个进程能创建几个子进程，而子进程之间有兄弟关系，在task_struct结构中有几个成员来表示这种关系。

时间和定时器信息
一个进程从创建到终止叫做该进程的生存期（lifetime）。进程在其生存期内使用CPU的时间，内核都要进行记录，以便进行统计、计费等有关操作。

进程耗费CPU的时间由两部分组成：一是在用户模式（或称为用户态）下耗费的时间，一是在系统模式（或称为系统态）下耗费的时间。每个时钟滴答，也就是每个时钟中断，内核都要更新当前进程耗费CPU的时间信息，例如将进程的时间片减一。

域名	含义
Start_time	进程创建时间
Per_cpu_utime	进程在某个CPU上运行时在用户态下耗费的时间
Per_cpu_stime	进程在某个CPU上运行时在系统态下耗费的时间
Counter	进程剩余的时间片
进程中的定时器如下图所示：



其余略

② 线程共享和私有内容
私有资源：所属线程的栈区、程序计数器、栈指针以及函数运行使用的寄存器是线程私有的。这些资源又称为线程上下文。

除此之外的所有资源均由线程共享。

8.2 线程标识
就像每个进程有一个进程ID一样，每个线程也有一个线程ID。进程ID在整个系统中是唯一的，但线程ID不同，线程ID只有在它所属的进程上下文中才有意义。

进程ID是用pid_t数据类型来表示的，是一个非负整数。线程ID是用pthread_t 数据类型来表示的，实现的时候可以用一个结构来代表pthread_t数据类型，所以可移植的操作系统实现不能把它作为整数处理。

因此需要一个函数来对两个线程ID进行比较：

C
1
2
3
4
#include <pthread.h>

// 相等返回非零，不等返回0
int pthread_equal(pthread_t t1, pthread_t t2);
获取自身的线程id：

C
1
2
3
#include <pthread.h>

pthread_t pthread_self(void);
8.3 线程创建
在传统 UNIX进程模型中，每个进程只有一个控制线程。从概念上讲，这与基于线程的模型中每个进程只包含一个线程是相同的。在POSIX线程（pthread）的情况下，程序开始运行时，它也是以单进程中的单个控制线程启动的。在创建多个控制线程以前，程序的行为与传统的进程并没有什么区别。

新增的线程可以通过调用pthread_create函数创建。

C
1
2
3
#include <pthread.h>
// 成功返回0，失败返回errno
int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);
thread：事先创建好的pthread_t类型的参数。成功时thread指向的内存单元被设置为新创建线程的线程ID。
attr：用于定制各种不同的线程属性。APUE的12.3节讨论了线程属性。通常直接设为NULL。
start_routine：新创建线程从此函数开始运行，无参数时arg设为NULL即可。形参是函数指针（该函数返回值和形参均为void*），因此需要传入函数地址。
arg：start_rtn函数的参数。无参数时设为NULL即可。有参数时输入参数的地址。当多于一个参数时应当使用结构体传入。
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

// 线程执行的函数
static void* func(void* p) {
    puts("Thread is working!");
    return NULL;
}

int main(void) {
    pthread_t tid;
    int err;

    puts("Begin!");

    err = pthread_create(&tid, NULL, func, NULL);
    if(err) {
        fprintf(stderr, "pthread_create():%s\n", strerror(err));
        exit(1);
    }

    puts("End!");
    exit(0);
}
打印结果：

BASH
1
2
3
[root@HongyiZeng pthread]# ./create 
Begin!
End!
分析：线程的调度取决于调度器策略。创建线程后，新的线程还没来得及被调度，main线程就执行了exit(0)使得进程退出，所以新的线程并没有被执行就退出了。

8.4 线程终止
8.4.1 终止方式
如果进程中的任意线程调用了 exit、_Exit 或者 _exit，那么整个进程就会终止。与此相类似，如果默认的动作是终止进程，那么，发送到线程的信号就会终止整个进程。

单个线程可以通过3种方式退出，因此可以在不终止整个进程的情况下，停止它的控制流：

线程可以简单地从启动例程中返回，返回值是线程的退出码
线程可以被同一进程中的其他线程取消
线程调用pthread_exit
C
1
2
3
#include <pthread.h>

void pthread_exit(void *rval_ptr);
例如：

C
1
2
3
4
5
static void *func(void *p) {
    puts("Thread is working!");
    pthread_exit(NULL);
    // return NULL;
}
函数pthread_join用来等待一个线程的结束。相当于进程控制中的wait。

C
1
2
3
#include <pthread.h>

int pthread_join(pthread_t thread, void **retval);
thread：为被等待的线程标识符
retval：为用户定义的指针，它可以用来存储被等待线程的返回值，即pthread_exit的参数。这是一个二级指针，因此传入的参数为一级指针的地址，如果不关心返回值则用NULL
代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
 #include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

static void *func(void *p) {
    puts("Thread is working!");
    return NULL;
}


int main(void) {

    pthread_t tid;
    int err;

    puts("Begin!");

    err = pthread_create(&tid, NULL, func, NULL);
    if(err) {
        fprintf(stderr, "pthread_create():%s\n", strerror(err));
        exit(1);
    }
    // join
    pthread_join(tid, NULL);
    puts("End!");
    exit(0);
}
执行结果：

BASH
1
2
3
4
[root@HongyiZeng pthread]# ./create 
Begin!
Thread is working!
End!
代码示例2

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

static void *thr_fn1(void *arg) {
    printf("thread1 is working!\n");
    pthread_exit((void *)1);
}

static void *thr_fn2(void *arg) {
    printf("thread2 is working!\n");
    pthread_exit((void *)2);
}


int main(void) {

    printf("main thread is working!\n");

    int err;
    pthread_t tid1, tid2;
    void *tret;

    err = pthread_create(&tid1, NULL, thr_fn1, NULL);
    if(err) {
        fprintf(stderr, "pthread_create():%s\n", strerror(err));
        exit(1);
    }

    err = pthread_create(&tid2, NULL, thr_fn2, NULL);
    if(err) {
        fprintf(stderr, "pthread_create():%s\n", strerror(err));
        exit(1);
    }

    pthread_join(tid1, &tret);
    printf("thread1 exit code %ld\n", (long)tret);

    pthread_join(tid2, &tret);
    printf("thread2 exit code %ld\n", (long)tret);

    printf("main thread exit!\n");

    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng pthread]# ./test
main thread is working!
thread1 is working!
thread1 exit code 1
thread2 is working!
thread2 exit code 2
main thread exit!
补充：void*

void*是一个未指定跳跃力的指针。

void*可以指向任何类型的地址。
C
1
2
3
4
float f = 5.5;
float* pf = &f; // pf是指向float的指针
void* pv = pf; // pv可以指向float类型的地址
float* pf2 = pv; //编译错误，有类型的指针变量不能指向void*变量
void*指针只有强制类型转换以后才可以解引用
C
1
2
3
4
5
6
7
8
9
10
11
12
13
int main() {
    
    float f = 5.5;
    float* pf = &f;
    void* pv;
    
    pv = pf; //这句是可以的
    
    cout<<*pv<<endl;  //编译错误，这样直接对pv取值是错误的
    cout<<*(float*)pv<<endl;  //强制类型转换后可以取值
    
    return 0;
}
void*指针变量和普通指针一样可以通过等于0或者NULL来初始化，表示一个空指针
C
1
2
void* pv = 0; 
void* pv2 = NULL; // 指针保存的地址为空或0
当void*指针作为函数的输入和输出时，表示可以接受任意类型的输入指针和输出任意类型的指针
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
#include <stdio.h>
#include <stdlib.h>

void* test(void* a) {
    printf("test: %d\n", *(int *)a);
    return a;
}

int main() {
    int a = 5;
    int* pi = &a;
    
    printf("%p\n", pi);
    printf("%p\n", test(pi));
    printf("main: %d\n", *(int *)test(pi));

    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
4
5
0x7ffcd39fa0d4
test: 5
0x7ffcd39fa0d4
test: 5
main: 5
8.4.2 栈的清理
线程可以安排它退出时需要调用的函数，这与进程在退出时可以用atexit函数安排退出是类似的。这样的函数称为线程清理处理程序（thread cleanup handler）。一个线程可以建立多个清理处理程序。处理程序记录在栈中，也就是说，它们的执行顺序与它们注册时相反。

C
1
2
3
4
#include <pthread.h>

void pthread_cleanup_push(void (*routine)(void *), void *arg);
void pthread_cleanup_pop(int execute);
当线程执行以下动作时，清理函数routine是由pthread_cleanup_push函数调度的，调用时只有一个参数arg：

调用pthread_exit时；
响应取消请求时；
用非零execute参数调用pthread_cleanup_pop 时。如果 execute 参数设置为0，清理函数将不被调用。不管发生上述哪种情况，pthread_cleanup_pop都将删除上次 pthread_cleanup_push调用建立的清理处理程序。
注意：这些函数有一个限制，由于它们可以实现为宏，所以必须在与线程相同的作用域中以匹配对的形式使用。pthread_cleanup_push 的宏定义可以包含字符{，这种情况下，在 pthread cleanup_pop 的定义中要有对应的匹配字符}。

代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

static void cleanup_func(void *p) {
    puts(p);
}

static void *func(void *p) {
    puts("Thread is working!");
    pthread_cleanup_push(cleanup_func, "cleanup:1");
    pthread_cleanup_push(cleanup_func, "cleanup:2");
    pthread_cleanup_push(cleanup_func, "cleanup:3");
    puts("push over!");
    // 成对出现
    pthread_cleanup_pop(1);
    pthread_cleanup_pop(0);
    pthread_cleanup_pop(0);
    pthread_exit(NULL);
}

int main(void) {
    pthread_t tid;
    int err;

    puts("Begin!");

    err = pthread_create(&tid, NULL, func, NULL);
    if(err) {
        fprintf(stderr, "pthread_create(): %s\n", strerror(err));
        exit(1);
    }
    pthread_join(tid, NULL);
    puts("End!");
    exit(0);
}
执行结果：

PLAINTEXT
1
2
3
4
5
Begin!
Thread is working!
push over!
cleanup:3
End!
8.4.3 线程的取消
多线程程序中，一个线程可以借助 pthread_cancel() 函数向另一个线程发送“终止执行”的信号，从而令目标线程结束执行。

pthread_cancel调用并不等待线程终止，它只提出请求。线程在取消请求发出后会继续运行，直到到达某个取消点(CancellationPoint)。取消点是线程检查是否被取消并按照请求进行动作的一个位置。

与线程取消相关的函数有：

C
1
2
// 发送终止信号给thread线程，如果成功则返回0，否则为非0值。发送成功并不意味着thread会终止。
int pthread_cancel(pthread_t thread);
C
1
2
// 设置本线程取消动作的执行时机
int pthread_setcanceltype(int type, int *oldtype)  
C
1
2
// 在不包含取消点，但是又需要取消点的地方创建一个取消点，以便在一个没有包含取消点的执行代码线程中响应取消请求
void pthread_testcancel(void)
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>

//线程执行的函数
void * thread_Fun(void * arg) {
    printf("新建线程开始执行\n");
    sleep(10);
}

int main(void) {
    pthread_t myThread;
    void * mess;
    int value;
    int res;
    //创建 myThread 线程
    res = pthread_create(&myThread, NULL, thread_Fun, NULL);
    if (res != 0) {
        printf("线程创建失败\n");
        return 0;
    }
    sleep(1);
    //向 myThread 线程发送 Cancel 信号
    res = pthread_cancel(myThread);
    if (res != 0) {
        printf("终止 myThread 线程失败\n");
        return 0;
    }
    //获取已终止线程的返回值
    res = pthread_join(myThread, &mess);
    if (res != 0) {
        printf("等待线程失败\n");
        return 0;
    }
    //如果线程被强制终止，其返回值为 PTHREAD_CANCELED
    if (mess == PTHREAD_CANCELED) {
        printf("myThread 线程被强制终止\n");
    }
    else {
        printf("error\n");
    }
    return 0;
}
8.5 线程同步
8.5.1 概念和例子
线程竞争的实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define LEFT  30000000
#define RIGHT 30000200
#define THRNUM (RIGHT-LEFT+1) // 线程个数

static void *thr_prime(void *p) {
    int i, j, mark;
    i = *(int *)p;
    mark = 1;
    for(j = 2; j < i/2; j++) {
        if(i % j == 0) {
            mark = 0;
            break;
        }
    }
    if(mark)
        printf("%d is a primer.\n", i);
	pthread_exit(NULL);
}

int main(void) {
	int i, err;
    pthread_t tid[THRNUM];
    for(i = LEFT; i <= RIGHT; i++) {
        err = pthread_create(tid+(i-LEFT), NULL, thr_prime, &i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for (i = LEFT; i <= RIGHT; i++)
        pthread_join(tid[i-LEFT], NULL);

    exit(0);
}
执行结果：发现每次都不一样，并且每次的结果都是相同的。

原因：线程发生了竞争。

创建线程时，main线程传递给函数thr_prime的参数&i是同一个地址，但是地址保存的值不相同：

C
1
err = pthread_create(tid+(i-LEFT), NULL, thr_prime, &i);
后面线程执行时，会对这个地址进行解引用：

C
1
i = *(int *)p;
注意：main线程和创建的线程的调度是由调度算法决定，因此会出现，在线程解引用之前，main线程就将该地址上的i值进行了修改，所以后面线程得到的i值都是同一个值。

解决竞争

定义一个结构体，成员为要计算判断的数，然后每次动态分配内存，将地址作为线程函数的参数即可。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define LEFT  30000000
#define RIGHT 30000200
#define THRNUM (RIGHT-LEFT+1)

struct thr_arg_st {
    int n;
};

static void *thr_prime(void *p) {
    int i, j, mark;
    // 先将void*强转为struct thr_arg_st *
    i = ((struct thr_arg_st *)p) -> n;
    mark = 1;
    for(j = 2; j < i/2; j++) {
        if(i % j == 0) {
            mark = 0;
            break;
        }
    }
    if(mark)
        printf("%d is a primer.\n", i);
    // 将p作为返回值
	pthread_exit(p);
}

int main(void) {
	int i, err;
    pthread_t tid[THRNUM];
    struct thr_arg_st *p;
    void * ret;
    for(i = LEFT; i <= RIGHT; i++) {
        // 动态分配内存
        p = malloc(sizeof(*p));
        if(p == NULL) {
            perror("malloc()");
            exit(1);
        }
        p -> n = i; 
        err = pthread_create(tid+(i-LEFT), NULL, thr_prime, p);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for (i = LEFT; i <= RIGHT; i++) {
        // 用ret来接收（一级指针的地址）
        pthread_join(tid[i-LEFT], &ret);
        // 释放动态分配的内存
        free(ret);
    }
    exit(0);
}
当多个控制线程共享相同的内存时，需要确保每个线程看到一致的数据视图。如果每个线程使用的变量都是其他线程不会读取和修改的，那么就不存在一致性问题。同样，如果变量是只读的，多个线程同时读取该变量也不会有一致性问题。但是，当一个线程可以修改的变量，其他线程也可以读取或者修改的时候，我们就需要对这些线程进行同步，确保它们在访问变量的存储内容时不会访问到无效的值。

当一个线程修改变量时，其他线程在读取这个变量时可能会看到一个不一致的值。在变量修改时间多于一个存储器访问周期的处理器结构中，当存储器读与存储器写这两个周期交叉时，这种不一致就会出现。当然，这种行为是与处理器体系结构相关的，但是可移植的程序并不能对使用何种处理器体系结构做出任何假设。

图11-7描述了两个线程读写相同变量的假设例子。在这个例子中，线程A读取变量然后给这个变量赋予一个新的数值，但写操作需要两个存储器周期。当线程B在这两个存储器写周期中间读取这个变量时，它就会得到不一致的值。



为了解决这个问题，线程不得不使用锁，同一时间只允许一个线程访问该变量。图11-8描述了这种同步。如果线程B希望读取变量，它首先要获取锁。同样，当线程A更新变量时，也需要获取同样的这把锁。这样，线程B在线程A释放锁以前就不能读取变量。



8.5.2 互斥量
可以使用 pthread 的互斥接口来保护数据，确保同一时间只有一个线程访问数据。互斥量（mutex）从本质上说是一把锁，在访问共享资源前对互斥量进行设置（加锁），在访问完成后释放（解锁）互斥量。

对互斥量进行加锁以后，任何其他试图再次对互斥量加锁的线程都会被阻塞直到当前线程释放该互斥锁。如果释放互斥量时有一个以上的线程阻塞，那么所有该锁上的阻塞线程都会变成可运行状态，第一个变为运行的线程就可以对互斥量加锁，其他线程就会看到互斥量依然是锁着的，只能回去再次等待它重新变为可用。在这种方式下，每次只有一个线程可以向前执行。

只有将所有线程都设计成遵守相同数据访问规则的，互斥机制才能正常工作。操作系统并不会为我们做数据访问的串行化。如果允许其中的某个线程在没有得到锁的情况下也可以访问共享资源，那么即使其他的线程在使用共享资源前都申请锁，也还是会出现数据不一致的问题。

互斥变量是用 pthread_mutex_t 数据类型表示的。在使用互斥变量以前，必须首先对它进行初始化，可以把它设置为常量PTHREAD_MUTEX_INITIALIZER（只适用于静态分配的互斥量），也可以通过调用pthread_mutex_init函数进行初始化。如果动态分配互斥量（例如，通过调用malloc函数），在释放内存前需要调用pthread_mutex_destroy。

相关函数：

初始化和销毁：
C
1
2
3
4
5
6
7
8
#include <pthread.h>
// 销毁互斥量
int pthread_mutex_destroy(pthread_mutex_t *mutex);

int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);

// 静态分配互斥量
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
加锁和解锁
C
1
2
3
4
5
6
7
8
#include <pthread.h>

// 阻塞加锁
int pthread_mutex_lock(pthread_mutex_t *mutex);
// 非阻塞加锁
int pthread_mutex_trylock(pthread_mutex_t *mutex);
// 解锁
int pthread_mutex_unlock(pthread_mutex_t *mutex);
代码示例——20个线程读写一个文件

先向/tmp/out下入1，然后创建20个线程来读这个文件内容并加1，然后写入。期望内容为21

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define FNAME "/tmp/out"
#define THRNUM 20
#define LINESIZE 1024

static void *thr_add(void *p) {
    FILE *fp;
    char linebuf[LINESIZE];

    fp = fopen(FNAME, "r+");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }

    fgets(linebuf, LINESIZE, fp);
    // 读完后将文件指针指向文件起始处
    fseek(fp, 0, SEEK_SET);
    sleep(1);
    // 向文件写入内容
    fprintf(fp, "%d\n", atoi(linebuf) + 1);
    fclose(fp);

    pthread_exit(NULL);
}

int main(void) {
    pthread_t tid[THRNUM];
    int i, err;

    for(i = 0; i < THRNUM; i++) {
        err = pthread_create(tid + i, NULL, thr_add, NULL);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for(i = 0; i < THRNUM; i++)
        pthread_join(tid[i], NULL);

    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
[root@HongyiZeng posix]# echo 1 > /tmp/out
[root@HongyiZeng posix]# cat /tmp/out
1
[root@HongyiZeng posix]# ./add
[root@HongyiZeng posix]# cat /tmp/out
2
分析：由于调度和竞争，线程读到文件内容1后，休眠1s，然后一起写入2，所以结果就为2；

代码示例——互斥量的使用

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define FNAME "/tmp/out"
#define THRNUM 20
#define LINESIZE 1024

// 互斥锁
static pthread_mutex_t mut = PTHREAD_MUTEX_INITIALIZER;

static void *thr_add(void *p) {
    FILE *fp;
    char linebuf[LINESIZE];

    fp = fopen(FNAME, "r+");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }
    
    // 在进入临界区前加上互斥锁
    pthread_mutex_lock(&mut);
    fgets(linebuf, LINESIZE, fp);
    // 读完后将文件指针指向文件起始处
    fseek(fp, 0, SEEK_SET);
    sleep(1);
    // 向文件写入内容
    fprintf(fp, "%d\n", atoi(linebuf) + 1);
    fclose(fp);
    // 退出临界区后解锁
    pthread_mutex_unlock(&mut);

    pthread_exit(NULL);
}

int main(void) {
    pthread_t tid[THRNUM];
    int i, err;

    for(i = 0; i < THRNUM; i++) {
        err = pthread_create(tid + i, NULL, thr_add, NULL);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for(i = 0; i < THRNUM; i++)
        pthread_join(tid[i], NULL);

    // 销毁互斥锁
    pthread_mutex_destroy(&mut);

    exit(0);
}
执行结果：

C
1
2
3
4
[root@HongyiZeng posix]# echo 1 > /tmp/out
[root@HongyiZeng posix]# ./add
[root@HongyiZeng posix]# cat /tmp/out
21
代码示例3——使用互斥锁实现线程同步

需求：四个线程依次打印abcd

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define THRNUM 4

static pthread_mutex_t mut[THRNUM];

// 返回下一个线程的编号0~3，3的下一个为0
static int next(int n) {
    return (n + 1) == THRNUM ? 0 : n + 1;
}

static void *thr_func(void *p) {
    int n = (int)p; // 线程编号
    int c = 'a' + (int)p; // 线程打印的字符

    while(1) {
        // 加锁
        pthread_mutex_lock(mut + n);
        // 向终端打印字符
        write(1, &c, 1);
        // 释放下一个线程的锁
        pthread_mutex_unlock(mut + next(n));
    }

    pthread_exit(NULL);
}

int main(void) {
    int i, err;
    pthread_t tid[THRNUM];

    for(i = 0; i < THRNUM; i++) {
        // 初始化锁
        pthread_mutex_init(mut + i, NULL);
        // 加锁
        pthread_mutex_lock(mut + i);
        err = pthread_create(tid + i, NULL, thr_func, (void *)i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }
    // 释放打印a的线程的锁
    pthread_mutex_unlock(mut + 0);
    // 运行3s终止进程
    alarm(3);
    for(i = 0; i < THRNUM; i++)
        pthread_join(tid[i], NULL);

    exit(0);
}
8.5.3 线程池
线程数是有一定限制的，8.5.1节用201个线程来检测质数，本节利用线程池来解决。

假设线程池提供4个线程来检测201个质数。设置临界区资源num，当：

num = 0：当前没有任务
num = -1：当前任务已经全部完成
num = 300000~3000200：当前有一个任务，需要一个线程来接受任务
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

#define LEFT  30000000
#define RIGHT 30000200
#define THRNUM 4 // 假设线程池中有4个线程

// 临界区资源
static int num = 0;
// 互斥锁
static pthread_mutex_t mut_num = PTHREAD_MUTEX_INITIALIZER;

static void *thr_prime(void *p) {
    int i, j, mark;
    // 死循环：领取任务
    while(1) {
        pthread_mutex_lock(&mut_num);
        // 循环检测是否有任务
        while(num == 0) { // 当前没有任务
            pthread_mutex_unlock(&mut_num);
            sched_yield();
            pthread_mutex_lock(&mut_num);
        }
        // 拿到任务，判断是否为-1，即任务已经结束
        if(num == -1) {
            // 要释放锁，防止其他线程死锁
            pthread_mutex_unlock(&mut_num);
            break;
        }
        // 拿到任务
        i = num;
        // 将num设置为0，即没有任务
        num = 0;
        pthread_mutex_unlock(&mut_num);
        //---------线程做任务：检测是否为质数-------
        mark = 1;
        for(j = 2; j < i/2; j++) {
            if(i % j == 0) {
                mark = 0;
                break;
            }
        }
        if(mark)
            printf("[thread-%d]%d is a primer.\n", (int)p, i);
    }
    pthread_exit(NULL);
}

int main(void) {
	int i, err;
    pthread_t tid[THRNUM];
    // 启动线程池
    for(i = 0; i < THRNUM; i++) {
        err = pthread_create(tid+i, NULL, thr_prime, (void *)i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }
	
    // 主线程下发任务
    for(i = LEFT; i <= RIGHT; i++) {
        pthread_mutex_lock(&mut_num);
        // 循环检测任务是否被线程领走
        while(num != 0) { // 没有领走任务
            pthread_mutex_unlock(&mut_num);
            // 让出CPU
            sched_yield();
            pthread_mutex_lock(&mut_num);
        }
        // 设置num，即下发任务
        num = i;
        pthread_mutex_unlock(&mut_num);
    } // 下发任务完毕
    
    // 设置num为-1，代表任务全部结束
    pthread_mutex_lock(&mut_num);
    // 循环检测最后一个任务是否被完成
    while(num != 0) { // 没有完成
        pthread_mutex_unlock(&mut_num);
        sched_yield();
        pthread_mutex_lock(&mut_num);
    }
    // 已完成，则设置为-1
    num = -1;
    pthread_mutex_unlock(&mut_num);
	// 收尸
    for (i = 0; i < THRNUM; i++) {
        pthread_join(tid[i], NULL);
    }
	// 销毁互斥锁
    pthread_mutex_destroy(&mut_num);
    exit(0);
}
打印结果：

PLAINTEXT
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
[thread-0]30000001 is a primer.
[thread-2]30000041 is a primer.
[thread-3]30000023 is a primer.
[thread-1]30000037 is a primer.
[thread-1]30000079 is a primer.
[thread-0]30000059 is a primer.
[thread-3]30000071 is a primer.
[thread-2]30000049 is a primer.
[thread-0]30000083 is a primer.
[thread-1]30000109 is a primer.
[thread-3]30000137 is a primer.
[thread-2]30000133 is a primer.
[thread-3]30000163 is a primer.
[thread-1]30000149 is a primer.
[thread-0]30000167 is a primer.
[thread-2]30000169 is a primer.
[thread-0]30000199 is a primer.
[thread-3]30000193 is a primer.
不足：该程序存在盲等，即查询法的不足，上游main线程一直在循环查看任务是否被领走，而下游一直在循环查看是否有任务。

通知法：上游将设置任务后，唤醒下游来处理任务。如果没有领走任务，则阻塞自己，等待下游来唤醒。

下游发现有任务，则领走任务，并唤醒上游；没有任务，则阻塞，等待上游来唤醒。

8.5.4 线程令牌桶
暂略

8.5.5 条件变量
条件变量是线程可用的另一种同步机制。条件变量给多个线程提供了一个会合的场所。条件变量与互斥量一起使用时，允许线程以无竞争的方式等待特定的条件发生。

条件本身是由互斥量保护的。线程在改变条件状态之前必须首先锁住互斥量。其他线程在获得互斥量之前不会察觉到这种改变，因为互斥量必须在锁定以后才能计算条件。

在使用条件变量之前，必须先对它进行初始化。由pthread_cond_t数据类型表示的条件变量可以用两种方式进行初始化。可以把常量PTHREAD_COND_INITTALIZER赋给静态分配的条件变量但是如果条件变量是动态分配的，则需要使用pthread_cond_init函数对它进行初始化。在释放条件变量底层的内存空间之前，可以使用pthread_cond_destroy函数对条件变量进行反初始化。

相关函数和作用：

初始化条件变量

C
1
2
3
4
5
6
7
8
9
#include <pthread.h>
// 销毁
int pthread_cond_destroy(pthread_cond_t *cond);

// 动态初始化
int pthread_cond_init(pthread_cond_t *restrict cond, const pthread_condattr_t *restrict attr);

// 静态初始化
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
阻塞当前线程，等待条件成立

C
1
2
3
4
5
6
7
8
9
10
#include <pthread.h>

// 有时间的条件等待
int pthread_cond_timedwait(pthread_cond_t *restrict cond,
                           pthread_mutex_t *restrict mutex,
                           const struct timespec *restrict abstime);

// 条件等待
int pthread_cond_wait(pthread_cond_t *restrict cond,
                      pthread_mutex_t *restrict mutex);
cond：已初始化好的条件变量
mutex：与条件变量配合使用的互斥锁
abstime：阻塞线程的时间
调用两个函数之前，我们必须先创建好一个互斥锁并完成加锁操作，然后才能作为实参传递给 mutex 参数。两个函数会完成以下两项工作：

阻塞线程，直至接收到条件成立的信号
当线程被添加到等待队列上时，将互斥锁解锁，即释放mutex
也就是说，函数尚未接收到“条件成立”的信号之前，它将一直阻塞线程执行。注意，当函数接收到“条件成立”的信号后，它并不会立即结束对线程的阻塞，而是先完成对互斥锁的“加锁”操作，然后才解除阻塞。

两个函数都以“原子操作”的方式完成“阻塞线程+解锁”或者“重新加锁+解除阻塞”这两个过程。

解除线程的“阻塞”状态

C
1
2
3
4
5
6
#include <pthread.h>
// 唤醒所有的阻塞线程
int pthread_cond_broadcast(pthread_cond_t *cond);

// 唤醒所有正在的至少一个线程
int pthread_cond_signal(pthread_cond_t *cond);
对于被上面两个函数阻塞的线程，我们可以借助如上两个函数向它们发送“条件成立”的信号，解除它们的“阻塞”状态。

由于互斥锁的存在，解除阻塞后的线程也不一定能立即执行。当互斥锁处于“加锁”状态时，解除阻塞状态的所有线程会组成等待互斥锁资源的队列，等待互斥锁“解锁”。

代码示例1——查询法转通知法

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>

#define LEFT  30000000
#define RIGHT 30000200
#define THRNUM 4

static int num = 0;
static pthread_mutex_t mut_num = PTHREAD_MUTEX_INITIALIZER;
static pthread_cond_t cond_num = PTHREAD_COND_INITIALIZER;

static void *thr_prime(void *p) {
    int i, j, mark;
    while(1) {
        pthread_mutex_lock(&mut_num);
        // 查看是否有任务
        while(num == 0) { // 没有任务
            // 则阻塞自己，释放互斥锁
            pthread_cond_wait(&cond_num, &mut_num);
        }
        if(num == -1) {
            pthread_mutex_unlock(&mut_num);
            break;
        }
        // 领走任务
        i = num;
        num = 0;
        // 唤醒所有阻塞线程
        pthread_cond_broadcast(&cond_num);
        pthread_mutex_unlock(&mut_num);
        mark = 1;
        for(j = 2; j < i/2; j++) {
            if(i % j == 0) {
                mark = 0;
                break;
            }
        }
        if(mark)
            printf("[thread-%d]%d is a primer.\n", (int)p, i);
    }
    pthread_exit(NULL);
}

int main(void) {
	int i, err;
    pthread_t tid[THRNUM];
    for(i = 0; i < THRNUM; i++) {
        err = pthread_create(tid+i, NULL, thr_prime, (void *)i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for(i = LEFT; i <= RIGHT; i++) {
        pthread_mutex_lock(&mut_num);
        // 查看任务是否领走
        while(num != 0) { // 任务没有领走
            // 阻塞自己，并释放互斥锁
            pthread_cond_wait(&cond_num, &mut_num);
        }
        // 已经被领走，则设置任务
        num = i; // 设置任务
        // 唤醒任何一个阻塞的线程
        pthread_cond_signal(&cond_num);
        pthread_mutex_unlock(&mut_num);
    }
    pthread_mutex_lock(&mut_num);
    while(num != 0) {
        pthread_mutex_unlock(&mut_num);
        sched_yield();
        pthread_mutex_lock(&mut_num);
    }
    num = -1;
    // 唤醒所有线程
    pthread_cond_broadcast(&cond_num);
    pthread_mutex_unlock(&mut_num);

    for (i = 0; i < THRNUM; i++) {
        pthread_join(tid[i], NULL);
    }

    pthread_mutex_destroy(&mut_num);
    pthread_cond_destroy(&cond_num);
    exit(0);
}
代码示例2——打印abcd

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define THRNUM 4

static int num = 0;
static pthread_mutex_t mut = PTHREAD_MUTEX_INITIALIZER;
static pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

static int next(int n) {
    return (n + 1) == THRNUM ? 0 : n + 1;
}

static void *thr_func(void *p) {
    int n = (int)p;
    int c = 'a' + (int)p;

    while(1) {
        pthread_mutex_lock(&mut);
        // 向终端打印字符
        while(num != n) {
            // 如果num不是自己的编号，则阻塞并释放锁
            pthread_cond_wait(&cond, &mut);
        }
        write(1, &c, 1);
        num = next(num); // 将num修改为下一个线程的编号
        // 广播唤醒所有阻塞的线程
        pthread_cond_broadcast(&cond);
        pthread_mutex_unlock(&mut);
    }

    pthread_exit(NULL);
}

int main(void) {
    int i, err;
    pthread_t tid[THRNUM];

    for(i = 0; i < THRNUM; i++) {
        err = pthread_create(tid + i, NULL, thr_func, (void *)i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }
    alarm(3);
    for(i = 0; i < THRNUM; i++)
        pthread_join(tid[i], NULL);
    pthread_mutex_destroy(&mut);
    pthread_cond_destroy(&cond);
    exit(0);
}
8.5.6 信号量
使用互斥量和条件变量可以实现信号量的功能。

需求：产生4个线程（4个资源）来筛选质数，任务完成后线程退出，然后又产生线程。这里的共享资源就是4个线程。

mysem.h
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
#ifndef __MYSEM_H__
#define __MYSEM_H__

// 为void取别名为mysem_t
typedef void mysem_t;

mysem_t *mysem_init(int initval);

int mysem_add(mysem_t *, int);

int mysem_sub(mysem_t *, int);

int mysem_destroy(mysem_t *);

#endif
mysem.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include "mysem.h"

// 信号量的结构体
struct mysem_st {
    int value; // 现有资源数（相当于信号量）
    pthread_mutex_t mut; // 互斥量
    pthread_cond_t cond; // 条件变量
};

// 信号量初始化，initval为资源总数
mysem_t *mysem_init(int initval) {
    struct mysem_st *me;
    me = malloc(sizeof(*me));
    if(me == NULL) {
        return NULL;
    }
    me->value = initval;
    pthread_mutex_init(&me->mut, NULL);
    pthread_cond_init(&me->cond, NULL);

    return me;
}

// 增加信号量
int mysem_add(mysem_t *ptr, int n) {
    // 将void*的指针指向mysem_st*
    struct mysem_st *me = ptr; 
    pthread_mutex_lock(&me->mut);
    me->value += n;
    // 广播唤醒所有等待线程
    pthread_cond_broadcast(&me->cond);
    pthread_mutex_unlock(&me->mut);
    return n;
}

// 减少信号量，索要n个资源
int mysem_sub(mysem_t *ptr, int n) {
    struct mysem_st *me = ptr;
    pthread_mutex_lock(&me->mut);
    while(me->value < n) { // 当资源总数少于需要的n
        // 条件等待
        pthread_cond_wait(&me->cond, &me->mut);
    }
    me->value -= n;
    pthread_mutex_unlock(&me->mut);
    return n;
}

// 销毁信号量
int mysem_destroy(mysem_t *ptr) {
    struct mysem_st *me = ptr;
    pthread_mutex_destroy(&me->mut);
    pthread_cond_destroy(&me->cond);
    free(me);
    return 0;
}
main.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>
#include "mysem.h"

#define LEFT  30000000
#define RIGHT 30000200
#define THRNUM (RIGHT-LEFT+1)
#define N 4 // 资源总量（可创建的线程总数）

static mysem_t *sem;

static void *thr_prime(void *p) {
    int i, j, mark;
    i = (int)p;
    mark = 1;
    for(j = 2; j < i/2; j++) {
        if(i % j == 0) {
            mark = 0;
            break;
        }
    }
    if(mark)
        printf("%d is a primer.\n", i);
   	// 假设任务需要5s完成
    sleep(5);
    // 增加信号量
    mysem_add(sem, 1);
	pthread_exit(NULL);
}

int main(void) {
	int i, err;
    pthread_t tid[THRNUM];
    
    sem = mysem_init(N);
    if(sem == NULL) {
        fprintf(stderr, "mysem_init() failed!\n");
        exit(1);
    }
    for(i = LEFT; i <= RIGHT; i++) {
        // 减少信号量
        mysem_sub(sem, 1);
        err = pthread_create(tid+(i-LEFT), NULL, thr_prime, (void *)i);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", strerror(err));
            exit(1);
        }
    }

    for (i = LEFT; i <= RIGHT; i++) {
        pthread_join(tid[i-LEFT], NULL);
    }
    mysem_destroy(sem);
    exit(0);
}
makefile
MAKEFILE
1
2
3
4
5
6
7
8
9
10
CFLAGS+=-pthread
LDFLAGS+=-pthread

all: mysem

mysem:main.o mysem.o
	gcc $^ -o $@ $(CFLAGS) $(LDFLAGS)

clean:
	rm -rf *.o mysem
执行结果

使用命令，查看线程关系：

BASH
1
ps ax -L
由下图所示，主线程main[3134]创建了4个线程来筛选质数：



当某个线程完成任务后，增加信号量，并唤醒阻塞线程，下图表示原来的4个线程已经完成任务退出，主线程又创建了4个新的线程来筛选质数：



同理，图表示原来的4个线程已经完成任务退出，主线程又创建了4个新的线程来筛选质数：



8.6 线程属性
pthread 接口允许我们通过设置每个对象关联的不同属性来细调线程和同步对象的行为。通常，管理这些属性的函数都遵循相同的模式。

在所有调用pthread_create函数的实例中，传入的参数都是空指针，而不是指向pthread_attr_t结构的指针。可以使用pthread_attr_t结构修改线程默认属性，并把这些属性与创建的线程联系起来。

可以使用pthread_attr_init函数初始化 pthread_attr_t 结构。在调用 pthread attr_init 以后，pthread_attr_t 结构所包含的就是操作系统实现支持的所有线程属性的默认值。

初始化和销毁

C
1
2
3
4
#include <pthread.h>

int pthread_attr_init(pthread_attr_t *attr);
int pthread_attr_destroy(pthread_attr_t *attr);
下图总结了 POSIX.1 定义的线程属性。POSIX.1 还为线程执行调度（Thread Execution Scheduling）选项定义了额外的属性，用以支持实时应用。下图同时给出了各个操作系统平台对每个线程属性的支持情况。



线程分离状态属性

线程分离：在我们使用默认属性创建一个线程的时候，线程是 joinable 的。 joinable 状态的线程，必须在另一个线程中使用 pthread_join() 等待其结束， 如果一个 joinable 的线程在结束后，没有使用 pthread_join() 进行操作， 这个线程就会变成”僵尸线程”。可以使用pthread_detach函数让线程分离。

当线程被设置为分离状态后，线程结束时，它的资源会被操作系统自动的回收， 而不再需要在其它线程中对其进行 pthread_join() 操作。

C
1
2
3
4
5
#include <pthread.h>
// 设置状态
int pthread_attr_setdetachstate(pthread_attr_t *attr, int detachstate);
// 获取状态
int pthread_attr_getdetachstate(pthread_attr_t *attr, int *detachstate);
detachstate：可以设置为以下属性
PTHREAD_CREATE_DETACHED：线程分离状态
PTHREAD_CREATE_JONINABLE：线程可joinable状态
代码示例：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
static void *fn(void *p) {
    // ...
}

int main(void) {
    int err;
    pthread_t tid;
    pthread_attr_t attr;
    // 初始化
    pthread_attr_init(&attr);
    // 设置创建的线程为分离状态
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
    // 创建线程
    err = pthread_create(&tid, &attr, fn, NULL);
    if(err) {
        //...
        exit(1);
    }
    // 销毁
    pthread_attr_destroy(&attr);
    exit(0);
}
线程的栈和栈大小

可以使用下列函数设置线程的栈属性。

C
1
2
3
4
#include <pthread.h>

int pthread_attr_setstack(pthread_attr_t *attr, void *stackaddr, size_t stacksize);
int pthread_attr_getstack(pthread_attr_t *attr, void **stackaddr, size_t *stacksize);
对于进程来说，虚地址空间的大小是固定的。因为进程中只有一个栈，所以它的大小通常不是问题。但对于线程来说，同样大小的虚地址空间必须被所有的线程栈共享。如果应用程序使用了许多线程，以致这些线程栈的累计大小超过了可用的虚地址空间，就需要减少默认的线程栈大小。另一方面，如果线程调用的函数分配了大量的自动变量，或者调用的函数涉及许多很深的栈帧（stack frame），那么需要的栈大小可能要比默认的大。

如果线程栈的虚地址空间都用完了，那可以使用malloc或者mmap来为可替代的栈分配空间，并用pthread_attr_setstack函数来改变新建线程的栈位置。由stackaddr 参数指定的地址可以用作线程视的内容范围中的最低可寻找地址，该地址与处理器结构相应的边界应对齐。当然，这要假设malloc和mmap所用的虚地址范围与线程栈当前使用的虚地址范围不同。

stackaddr线程属性被定义为栈的最低内存地址，但这并不一定是栈的开始位置。对于一个给定的处理器结构来说，如果栈是从高地址向低地址方向增长的，那么 stackaddr线程属性将是栈的结尾位置，而不是开始位置。

应用程序也可以通过下列函数读取或设置线程属性stacksize。

C
1
2
3
4
#include <pthread.h>

int pthread_attr_setstacksize(pthread_attr_t *attr, size_t stacksize);
int pthread_attr_getstacksize(pthread_attr_t *attr, size_t *stacksize);
如果希望改变默认的栈大小，但又不想自己处理线程栈的分配问题，这时使用pthread_attr_setstacksize函数就非常有用。设置stacksize属性时，选择的stacksize不能小于PTHREAD_STACK_MIN。

代码示例——测试线程数量的上限：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

static void *func(void *p) {
    while(1);
    pthread_exit(NULL);
}

int main(void) {
    int i, err;
    pthread_t tid;
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    // 定义线程的栈大小为10M
    pthread_attr_setstacksize(&attr, 1024 * 1024);
    for(i = 0; i ; i++) {
        err = pthread_create(&tid, NULL, func, NULL);
        if(err) {
            fprintf(stderr, "pthread_create(): %s\n", stderror(err));
            break;
        }
    }
    printf("%d\n", i);
    pthread_attr_destroy(&attr);
    exit(0);
}
8.7 同步属性
线程的同步对象也具有属性。

8.7.1 互斥量属性
互斥量属性是用 pthread_mutexattr_t 结构表示的。在8.5.2节每次对互斥量进行初始化时，都是通过使用PTHREAD_MUTEX_INITTALIZER 常量或者用指向互斥量属性结构的空指针作为参数调用 pthread_mutex_init 函数，得到互斥量的默认属性。

对于非默认属性，可以使用下列函数进行初始化和销毁：

C
1
2
3
4
#include <pthread.h>

int pthread_mutexattr_destroy(pthread_mutexattr_t *attr);
int pthread_mutexattr_init(pthread_mutexattr_t *attr);
互斥量的三个主要属性：

进程共享属性
健壮属性（略）
类型属性
进程共享

在进程中，多个线程可以访问同一个同步对象。正如在8.5.2节看到的，这是默认的行为。在这种情况下，进程共享互斥量属性需设置为PTHREAD_PROCESS_PRIVATE。

但也存在这样的机制：允许相互独立的多个进程把同一个内存数据块映射到它们各自独立的地址空间中。就像多个线程访问共享数据一样，多个进程访问共享数据通常也需要同步。如果进程共享互斥量属性设置为PTHREAD_PROCESS_SHARED，从多个进程彼此之间共享的内存数据块中分配的互斥量就可以用于这些进程的同步。

相关函数调用：

C
1
2
3
4
#include <pthread.h>

int pthread_mutexattr_getpshared(const pthread_mutexattr_t * restrict attr, int *restrict pshared);
int pthread_mutexattr_setpshared(pthread_mutexattr_t *attr, int pshared);
pshared：这里的p指的就是进程process
PTHREAD_PROCESS_PRIVATE：进程独占互斥量
PTHREAD_PROCESS_SHARED：进程共享互斥量
类型属性

类型互斥量属性控制着互斥量的锁定特性。POSIX.1定义了4种类型。

PTHREAD_MUTEX_NORMAL：一种标准互斥量类型，不做任何特殊的错误检查或死锁检测。
PTHREAD_MUTEX_ERRORCHECK：此互斥量类型提供错误检查。
PTHREAD_MUTEX_RECURSIVE ：此互斥量类型允许同一线程在互斥量解锁之前对该互斥量进行多次加锁。递归互斥量维护锁的计数，在解锁次数和加锁次数不相同的情况下，不会释放锁。所以，如果对一个递归互斥量加锁两次，然后解锁一次，那么这个互斥量将依然处于加锁状态，对它再次解锁以前不能释放该锁。
PTHREAD_MUTEX_DEFAULT：此互斥量类型可以提供默认特性和行为。操作系统在实现它的时候可以把这种类型自由地映射到其他互斥量类型中的一种。


上图不占用解锁是指解锁不是自己加的锁（解锁别人加的锁），例如打印abcd的程序。

相关函数：

C
1
2
3
4
#include <pthread.h>

int pthread_mutexattr_gettype(const pthread_mutexattr_t *restrict attr, int *restrict type);
int pthread_mutexattr_settype(pthread_mutexattr_t *attr, int type);
8.7.2 条件变量属性
相关函数：

C
1
2
3
4
5
6
7
#include <pthread.h>

int pthread_condattr_destroy(pthread_condattr_t *attr);
int pthread_condattr_init(pthread_condattr_t *attr);

int pthread_condattr_getpshared(const pthread_condattr_t *restrict attr, int *restrict pshared);
int pthread_condattr_setpshared(pthread_condattr_t *attr,int pshared);
8.8 线程安全IO
此前介绍过的IO都是线程安全的IO，即在当多个线程操作IO时，都必须对缓冲区进行加锁和解锁，防止出现对缓冲区进行竞争的现象。

例如三个线程，分别向标准输出终端打印连续字符aaa，bbb和ccc，如果使用线程安全的io，例如puts，可能出现的情况有：

C
1
2
3
4
aaabbbccc
aaacccbbb
cccbbbaaa
// ...
但绝不会出现下面这种情况：

C
1
2
3
abcabcabc
aabbccabc
// ... 
原因就在于，puts是线程安全的，在对缓冲区操作前，需要加锁。

也存在线程不安全的IO调用，当考虑到效率问题（省去加锁和解锁的时间），并且确保只有单线程操作缓冲区时，可以使用下面的这些函数，这些函数在后面都加上了_unlocked，表示不加锁。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
#include <stdio.h>
int getc_unlocked(FILE *stream);
int getchar_unlocked(void);
int putc_unlocked(int c, FILE *stream);
int putchar_unlocked(int c);

void clearerr_unlocked(FILE *stream);
int feof_unlocked(FILE *stream);
int ferror_unlocked(FILE *stream);
int fileno_unlocked(FILE *stream);
int fflush_unlocked(FILE *stream);
int fgetc_unlocked(FILE *stream);
int fputc_unlocked(int c, FILE *stream);
size_t fread_unlocked(void *ptr, size_t size, size_t n,
                      FILE *stream);
size_t fwrite_unlocked(const void *ptr, size_t size, size_t n,
                       FILE *stream);

char *fgets_unlocked(char *s, int n, FILE *stream);
int fputs_unlocked(const char *s, FILE *stream);
8.9 线程和信号
对多线程的进程而言，只有进程级别的未决信号集pending，没有信号屏蔽字mask，而每个线程都有自己的pending和mask（线程级别）。

进程向进程发送信号，改变的是进程级别的pending，线程向线程发送信号，改变的是线程级别的pending。对于线程级别的信号响应，使用当前线程的pending和mask进行按位与。对于进程级别的信号响应，使用当前工作线程的mask和进程级别的pending进行按位与。

此前讨论了进程如何使用 sigprocmask 函数来阻止信号发送。然而，sigprocmask 的行为在多线程的进程中并没有定义，线程必须使用pthread_sigmask。

C
1
2
3
4
5
6
7
#include <signal.h>

// 修改线程级别的信号屏蔽字
int pthread_sigmask(int how, const sigset_t *set, sigset_t *oldset);

// 向指定线程发送信号
int pthread_kill(pthread_t thread, int sig);
线程可以调用sigwait等待一个或多个信号的出现：

C
1
2
3
4
#include <signal.h>

// 等待信号集set
int sigwait(const sigset_t *set, int *sig);
8.10 线程与fork
暂略

9 高级IO
本节对应第十四章高级IO

IO模型分为五种：

阻塞io
非阻塞io
信号驱动
多路转接
异步io
9.0 IO过程
我们要将内存中的数据写入到磁盘的话，主体会是什么呢？主体可能是一个应用程序，比如一个Java进程（假设网络传来二进制流，一个Java进程可以把它写入到磁盘）。

操作系统负责计算机的资源管理和进程的调度。应用程序要把数据写入磁盘，或者从磁盘读取数据，只能通过调用操作系统开放出来的API来操作。

应用程序的IO操作分为两种动作：IO调用和IO执行。IO调用是由进程（应用程序的运行态）发起，而IO执行是操作系统内核的工作。此时所说的IO是应用程序对操作系统IO功能的一次触发，即IO调用。

应用程序发起的一次IO操作包含两个阶段：

IO调用：应用程序进程向操作系统内核发起调用。
IO执行：操作系统内核完成IO操作。
操作系统内核完成IO操作还包括两个过程：

准备数据阶段：内核等待I/O设备准备好数据
拷贝数据阶段：将数据从内核缓冲区（内核空间）拷贝到用户进程缓冲区（用户空间）


其实IO就是把进程的内部数据转移到外部设备，或者把外部设备的数据迁移到进程内部。外部设备一般指硬盘、socket通讯的网卡。

9.1 BIO和NIO
阻塞IO：当资源不可用的时候，应用程序就会挂起。当资源可用的时候，唤醒任务。

阻塞IO图示如下，应用程序调用 read 函数从设备中读取数据，当设备不可用或数据未准备好的时候就会进入到休眠态。等设备可用的时候就会从休眠态唤醒，然后从设备中读取数据返回给应用程序。



之前学习过的IO都是阻塞IO。阻塞IO要阻塞一次，即等待数据和拷贝数据这个过程。

优缺点

优点：应用的程序开发非常简单；在阻塞等待数据期间，用户线程挂起。在阻塞期间，用户线程基本不会占用CPU资源。

缺点：一般情况下，会为每个连接配备一个独立的线程；反过来说，就是一个线程维护一个连接的IO操作。在并发量小的情况下，这样做没有什么问题。但是，当在高并发的应用场景下，需要大量的线程来维护大量的网络连接，内存、线程切换开销会非常巨大。因此，基本上阻塞IO模型在高并发应用场景下是不可用的。

非阻塞IO：当资源不可用的时候，应用程序轮询查看，或放弃，会有超时处理机制。

非阻塞IO图示如下，可以看出，应用程序使用非阻塞访问方式从设备读取数据，当设备不可用或数据未准备好的时候会立即向内核返回一个错误码，表示数据读取失败。应用程序会再次重新读取数据，这样一直往复循环，直到数据读取成功。

非阻塞io也要阻塞一次，等待数据不用阻塞（内核马上返回未准备好），而从内核拷贝数据到用户区需要阻塞。



缺点：依然存在性能问题，即频繁的轮询，导致频繁的系统调用，同样会消耗大量的CPU资源。可以考虑IO复用模型，去解决这个问题。

9.2 有限状态机
需求：有左右两个设备，第一个任务为读左设备，写右设备，第二个任务读右设备，写左设备。



代码实现

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h>

#define TTY1 "/dev/tty3"
#define TTY2 "/dev/tty4"
#define BUFSIZE 1024

// 有限状态机的状态枚举
enum {
    STATE_R = 1, // 读
    STATE_W, // 写
    STATE_Ex, // 异常
    STATE_T // 终止
};

struct fsm_st {
    int state;
    int sfd;
    int dfd;
    int len; // 读取到的数据
    int pos; // buf的偏移量
    char buf[BUFSIZE]; // 缓冲区
    char *errstr; // 报错信息
};

/* 状态机驱动 */
static void fsm_driver(struct fsm_st *fsm) {
    int ret;
    // 根据当前状态，决定下一步动作
    switch(fsm->state) {
        // 当前状态为读状态
        case STATE_R:
            fsm->len = read(fsm->sfd, fsm->buf, BUFSIZE);
            if(fsm->len == 0) { // 读完文件
                fsm->state = STATE_T;
            }else if(fsm->len < 0) {
                if(errno == EAGAIN) { // 数据没有准备好
                    fsm->state = STATE_R;
                } else { // 真错
                    fsm->errstr = "read()";
                    fsm->state = STATE_Ex;
                }
            }else { // 转换为写状态
                fsm->pos = 0;
                fsm->state = STATE_W;
            }
            break;
        // 当前状态为写状态
        case STATE_W:
            ret = write(fsm->dfd, fsm->buf + fsm->pos, fsm->len);
            if(ret < 0) {
                if(errno == EAGAIN) {
                    fsm->state = STATE_W;
                } else {
                    fsm->errstr = "write()";
                    fsm->state = STATE_Ex;
                }
            }else {
                fsm->pos += ret;
                fsm->len -= ret;
                if(fsm->len == 0) { // 写够len个字节
                    fsm->state = STATE_R;
                } else { // 没有写够len个字节
                    fsm->state = STATE_W;
                }
            }
            break;
        case STATE_Ex:
            perror(fsm->errstr);
            fsm->state = STATE_T;
            break;
        case STATE_T:
            break;
        default:
            abort();
            break;
    }
}

static void relay(int fd1, int fd2) {
    int fd1_save, fd2_save;
    struct fsm_st fsm12, fsm21;
    // 获取文件状态选项
    fd1_save = fcntl(fd1, F_GETFL);
    // 设置文件状态选项，添加非阻塞模式
    fcntl(fd1, F_SETFL, fd1_save|O_NONBLOCK);

    fd2_save = fcntl(fd2, F_GETFL);
    fcntl(fd2, F_SETFL, fd2_save|O_NONBLOCK);
    // 设置状态机
    fsm12.state = STATE_R;
    fsm12.sfd = fd1;
    fsm12.dfd = fd2;

    fsm21.state = STATE_R;
    fsm21.sfd = fd2;
    fsm21.dfd = fd1;
	// 轮询
    while(fsm12.state != STATE_T || fsm21.state != STATE_T) {
        fsm_driver(&fsm12);
        fsm_driver(&fsm21);
    }

    // 恢复用户设置的文件状态
    fcntl(fd1, F_SETFL, fd1_save);
    fcntl(fd2, F_SETFL, fd2_save);
}

// 模拟用户的设置
int main(void) {
    int fd1, fd2; // 左设备和右设备
    // 假设用户不以非阻塞的方式打开文件
    if((fd1 = open(TTY1, O_RDWR)) < 0) {
        perror("open()");
        exit(1);
    }
    write(fd1,"TTY1\n",5);
    // 假设用户以非阻塞的方式打开文件
    if((fd2 = open(TTY1, O_RDWR|O_NONBLOCK)) < 0) {
        perror("open()");
        exit(1);
    }
    write(fd2,"TTY2\n",5);
    relay(fd1, fd2);
    close(fd2);
    close(fd1);

    exit(0);
}
上述代码存在忙等现象，会使得CPU利用率占满，原因在于：

进入循环后：

C
1
2
3
4
while(fsm12.state != STATE_T || fsm21.state != STATE_T) {
    fsm_driver(&fsm12);
    fsm_driver(&fsm21);
}
如果设备没有准备好数据，则进入fsm_driver后，执行read调用时，内核立即会返回（非阻塞），是一个假错，执行：

C
1
2
3
if(errno == EAGAIN) { // 通常在执行非阻塞io时引发EAGAIN，这意味着“现在没有可用的数据，以后再试一次” 。
    fsm->state = STATE_R;
}
状态不变，跳出case语句和驱动函数后，继续循环，所以导致cpu利用率高。

9.3 linux终端
9.3.1 终端，控制台和tty


tty全称teletypewriter，即是电传打字机，它通过两根电缆连接计算机，一根用于向计算机发送指令，一根用于接收计算机的输出，输出结果是打印在纸上的。它是最早出现的一种终端设备。

最初tty是指连接到Unix系统上的物理或者虚拟终端。终端是一种字符型设备，通常使用tty来统称各种类型的终端设备。随着时间的推移，当通过串行口能够建立起终端连接后，这个名字也用来指任何的串口设备。它还有多种类，例如串口（ttySn、ttySACn、ttyOn）、USB到串口的转换器（ttyUSBn）等。tty虚拟设备支持虚拟控制台，它能通过键盘及网络连接或者通过xterm会话登录到计算机上。

终端（terminal）为主机提供了人机接口，每个人都通过终端使用主机的资源。终端有字符终端和图形终端两种，一台主机可以连很多终端。
控制台（console）是一种特殊的人机接口，是人控制主机的第一人机接口，而主机对于控制台的信任度高于其他终端。
个人计算机只有控制台，没有终端。当然愿意的话，可以在串口上连一两台字符哑终端。但是linux按POSIX标准把个人计算机当成小型机来用，在控制台上通过软件虚拟了六个字符哑终端（或者叫虚拟控制台终端tty1-tty6）和一个图型终端，在虚拟图形终端中又可以通过软件再虚拟无限多个伪终端（pts/0等）。但这全是虚拟的，虽然用起来一样，但实际上没有物理实体。所以在个人计算机上，只有一个实际的控制台，没有终端，所有终端都是在控制台上用软件模拟的。要把个人计算机当主机再通过串口或网卡外连真正的物理终端也可以，论成本，谁会怎么做呢。



linux的终端设备一般分为以下几种：



9.3.2 控制台
① 系统控制台
/dev/console是系统控制台，是与操作系统交互的设备。

② 当前控制台
/dev/tty是当前控制台，它会映射到当前设备（使用命令tty可以查看它具体对应哪个实际物理控制台设备）。

如果在控制台界面下（即字符界面下）那么dev/tty就是映射到dev/tty1-6之间的一个，但是如果现在是在图形界面（Xwindows），那么你会发现现在的/dev/tty映射到的是/dev/pts的伪终端上。



③ 虚拟控制台
/dev/ttyn是进程虚拟控制台，他们共享同一个真实的物理控制台。

在PC上，用户可以使用alt+Fn切换控制台，看起来感觉存在多个屏幕，这种虚拟控制台对应tty1~n，其中 ，/dev/tty1等代表第一个虚拟控制台。

例如当使用ALT+F2进行切换时，系统的虚拟控制台为/dev/tty2 ，当前控制台（/dev/tty）则指向/dev/tty2；

比较特殊的是/dev/tty0，他代表当前虚拟控制台，是当前所使用虚拟控制台的一个别名。因此不管当前正在使用哪个虚拟控制台（注意：这里是虚拟控制台，不包括伪终端），系统信息都会发送到/dev/tty0上。

9.3.3 伪终端
伪终端（Pseudo Terminal，或者pty, pseudo-tty）是终端的发展，为满足现在需求（比如网络登陆、xwindow窗口的管理）。它是成对出现的逻辑终端设备（即master和slave设备，对master的操作会反映到slave上），多用于模拟终端程序，是远程登陆（telnet、ssh、xterm等）后创建的控制台设备。

在XWindow下打开的终端或使用telnet 或ssh等方式登录Linux主机，此时均通过pty设备。例如，如果某人在网上使用telnet程序连接到计算机上，则telnet程序就可能会打开/dev/ptmx设备获取一个fd。此时一个getty程序就应该运行在对应的/dev/pts/*上。当telnet从远端获取了一个字符时，该字符就会通过ptmx、pts/*传递给getty程序，而getty程序就会通过pts/*、ptmx和telnet程序往网络上返回login:字符串信息。这样，登录程序与telnet程序就通过“伪终端”进行通信。

9.3.4 串口终端
略

9.4 IO多路转接
IO多路转接也称为IO多路复用。

IO复用模型核心思路：系统给我们提供一类函数（select、poll、epoll函数），它们可以同时监控多个fd的操作，任何一个返回内核数据就绪，应用进程再发起recvfrom系统调用。

9.4.1 select
应用进程通过调用select函数，可以同时监控多个fd，在select函数监控的fd中，只要有任何一个数据状态准备就绪了，select函数就会返回可读状态，这时应用进程再发起recvfrom请求去读取数据。

图示1



图示2



IO多路转接需要阻塞两次，第一次是在select处阻塞（内核为我们轮询fd的变化情况），第二次是select返回后，拷贝数据发起系统调用（不管是读还是写）时需要再次阻塞。

函数原型：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
/* According to POSIX.1-2001 */
#include <sys/select.h>

/* According to earlier standards */
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>

int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);

// 删除 set 中的给定的文件描述符
void FD_CLR(int fd, fd_set *set);
// 测试文件描述符 fd 是否在 set 集合中
int  FD_ISSET(int fd, fd_set *set);
// 将文件描述符 fd 添加到 set 中
void FD_SET(int fd, fd_set *set);
// 清空 set 中的文件描述符
void FD_ZERO(fd_set *set);
注意select调用本身是阻塞的。

select参数含义：

nfds：最大的文件描述符 + 1；
readfds：需要监视的输入文件描述符集合，底层采用数组存储。
writefds：需要监视的输出文件描述符集合；
exceptfds：需要监视的会发生异常的文件描述符集合；
timeout：等待的超时时间，如果时间超时依然没有文件描述符状态发生变化那么就返回。设置为 0 会立即返回，设置为 NULL 则一直阻塞等待，不会超时。
返回值：错误返回-1，超时返回0。当关注的事件返回时，返回大于0的值，该值是发生事件的文件描述符数。

代码示例

利用select重构9.2小节的代码：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <errno.h>
#include <sys/select.h>

#define TTY1 "/dev/tty3"
#define TTY2 "/dev/tty4"
#define BUFSIZE 1024

// 有限状态机的状态枚举
enum {
    STATE_R = 1, // 读
    STATE_W, // 写
    STATE_AUTO,
    STATE_Ex, // 异常
    STATE_T // 终止
};

static void relay(int fd1, int fd2) {
    int fd1_save, fd2_save;
    struct fsm_st fsm12, fsm21;
    fd_set rset, wset;
    // 获取文件状态选项
    fd1_save = fcntl(fd1, F_GETFL);
    // 设置文件状态选项，添加非阻塞模式
    fcntl(fd1, F_SETFL, fd1_save|O_NONBLOCK);

    fd2_save = fcntl(fd2, F_GETFL);
    fcntl(fd2, F_SETFL, fd2_save|O_NONBLOCK);
    // 设置状态机
    fsm12.state = STATE_R;
    fsm12.sfd = fd1;
    fsm12.dfd = fd2;

    fsm21.state = STATE_R;
    fsm21.sfd = fd2;
    fsm21.dfd = fd1;

    while(fsm12.state != STATE_T || fsm21.state != STATE_T) {
        // 布置监视任务，清空fd_set
        FD_ZERO(&rset);
        FD_ZERO(&wset);
        if(fsm12.state == STATE_R) { // 当前为读态，即读sfd
            FD_SET(fsm12.sfd, &rset); // 我们关心sfd是否可读，因此将其加入到rset中
        }
        if(fsm12.state == STATE_W) { // 当前为写态，即写dfd
            FD_SET(fsm12.dfd, &wset);// 我们关心dfd是否可写，因此将其加入到wset中
        }
        if(fsm21.state == STATE_R) {
            FD_SET(fsm21.sfd, &rset);
        }
        if(fsm21.state == STATE_W) {
            FD_SET(fsm21.dfd, &wset);
        }
        // 监视
        if(fsm12.state < STATE_AUTO || fsm21.state < STATE_AUTO) {
            // 阻塞监视
            if(select(max(fd1, fd2) + 1, &rset, &wset, NULL, NULL) < 0) {
                if(errno == EINTR) { // 阻塞调用被信号打断，假错
                    continue;
                }
                perror("select()"); // 真错
                exit(1);
            }
        }
        // 查看监视结果
        if(FD_ISSET(fd1, &rset) || FD_ISSET(fd2, &wset) || fsm12.state > STATE_AUTO) { // 读fd1准备好 或 写fd2准备好 或 其他状态
            fsm_driver(&fsm12);
        }
        if(FD_ISSET(fd2, &rset) || FD_ISSET(fd1, &wset) || fsm21.state > STATE_AUTO) { // 读fd2准备好 或 写fd1准备好 或 其他状态
            fsm_driver(&fsm21);
        }
    }

    // 恢复用户设置的文件状态
    fcntl(fd1, F_SETFL, fd1_save);
    fcntl(fd2, F_SETFL, fd2_save);
}
上面的代码不会出现盲等现象，原因在于：

调用select函数后会在此处阻塞，同时内核监控rset和wset中的文件描述符的变化（准备数据）。

当发生变化时（数据准备好时），唤醒线程，select函数返回值大于0，继续向下执行，推动状态机运行（进行读写时还会再次阻塞，例如read或write，因为明确了数据已经准备好）。

综上，不会产生盲等。

select的缺点：

监听的IO最大连接数有限，在Linux系统上一般为1024。
select函数返回后，是通过遍历fdset，找到就绪的描述符fd。（仅知道有I/O事件发生，却不知是哪几个流，所以遍历所有流） ，如果同时连接的大量客户端，在一时刻可能只有极少处于就绪状态，伴随着监视的描述符数量的增长，效率也会线性下降。
内存拷贝：需要维护一个用来存放大量fd的数据结构，这样会使得用户空间和内核空间在传递该结构时复制开销大。
9.4.2 poll
select 和 poll 系统调用的本质一样，poll 的机制与 select 类似，与 select 在本质上没有多大差别，管理多个描述符也是进行轮询，根据描述符的状态进行处理，但是 poll 没有最大文件描述符数量的限制（数量过大后性能也是会下降）。poll 和 select 同样存在一个缺点就是，包含大量文件描述符的数组被整体复制于用户态和内核的地址空间之间，而不论这些文件描述符是否就绪，它的开销随着文件描述符数量的增加而线性增大。

函数原型：

C
1
2
3
4
5
6
7
8
9
10
11
// poll - wait for some event on a file descriptor

#include <poll.h>

int poll(struct pollfd *fds, nfds_t nfds, int timeout);

struct pollfd {
    int   fd;         /* 需要监视的文件描述符 */
    short events;     /* 要监视的事件 */
    short revents;    /* 该文件描述符发生了的事件 */
};
参数含义：

fds：实际上是一个结构体数组的首地址，因为 poll 可以帮助我们监视多个文件描述符，而一个文件描述放到一个 struct pollfd 结构体中，多个文件描述符就需要一个数组来存储了（一个文件描述符对应一个结构体）。底层采用链表存储。
nfds：fds 这个数组的长度。
timeout：阻塞等待的超时时间。传入 -1 则始终阻塞，不超时。
返回值：

成功时，poll 返回结构体中 revents 域不为 0 的文件描述符个数；如果在超时前没有任何事件发生，poll返回 0；
失败时，poll 返回 -1，并设置 errno
结构体中的事件events和revents可以指定下面七种事件，同时监视多个事件可以使用按位或（|）添加：

事件	描述
POLLIN	文件描述符可读
POLLPRI	可以非阻塞的读高优先级的数据
POLLOUT	文件描述符可写
POLLRDHUP	流式套接字连接点关闭，或者关闭写半连接。
POLLERR	已出错
POLLHUP	已挂断（一般指设备）
POLLNVAL	参数非法
程序实例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
static void relay(int fd1, int fd2) {
    int fd1_save, fd2_save;
    struct fsm_st fsm12, fsm21;
    struct pollfd pfd[2];
    // 获取文件状态选项
    fd1_save = fcntl(fd1, F_GETFL);
    // 设置文件状态选项，添加非阻塞模式
    fcntl(fd1, F_SETFL, fd1_save|O_NONBLOCK);

    fd2_save = fcntl(fd2, F_GETFL);
    fcntl(fd2, F_SETFL, fd2_save|O_NONBLOCK);
    // 设置状态机
    fsm12.state = STATE_R;
    fsm12.sfd = fd1;
    fsm12.dfd = fd2;

    fsm21.state = STATE_R;
    fsm21.sfd = fd2;
    fsm21.dfd = fd1;

    pfd[0].fd = fd1;
    pfd[1].fd = fd2;
    while(fsm12.state != STATE_T || fsm21.state != STATE_T) {
        // 布置监视任务
        pfd[0].events = 0; // 清空监视的事件
        pfd[1].events = 0;
        if(fsm12.state == STATE_R) {
            pfd[0].events |= POLLIN; // 监视是否可读
        }
        if(fsm12.state == STATE_W) {
            pfd[1].events |= POLLOUT;
        }
        if(fsm21.state == STATE_R) {
            pfd[1].events |= POLLIN;
        }
        if(fsm21.state == STATE_W) {
            pfd[0].events |= POLLOUT;
        }
        // 监视
        if(fsm12.state < STATE_AUTO || fsm21.state < STATE_AUTO) {
            if(poll(pfd, 2, -1) < 0) { // 阻塞监视
                if(errno == EINTR) {
                    continue;
                }
                perror("poll()");
                exit(1);
            }
        }
        // poll返回后，查看监视结果
        // 用按位与
        if(pfd[0].revents & POLLIN || pfd[1].revents & POLLOUT || fsm12.state > STATE_AUTO) {
            fsm_driver(&fsm12);
        }
        if(pfd[1].revents & POLLIN || pfd[0].revents & POLLOUT || fsm21.state > STATE_AUTO) {
            fsm_driver(&fsm21);
        }
    }    

    // 恢复用户设置的文件状态
    fcntl(fd1, F_SETFL, fd1_save);
    fcntl(fd2, F_SETFL, fd2_save);
}
同样不会出现盲等现象。

9.4.3 epoll
select和poll在需要我们在用户态创建监视文件描述符的集合（fd_set和pollfd，底层分别采用数组和链表存储，因此前者有大小限制，后者没有），调用时，需要将该集合复制到内核空间中，这样内核才能帮助我们轮询fd，这个过程具有一定开销。

epoll则只提供这个集合创建、控制相关的接口，调用时，直接在内核空间创建监视fd的集合，因此去除了复制过程开销。过程如下：



相关调用：epoll_create，epoll_ctl，epoll_wait

epoll_create

C
1
2
#include <sys/epoll.h>
int epoll_create(int size);
参数：调用 epoll_create 时最初 size 参数给传入多少，内核在建立数组的时候就是多少个元素。后来改进为只要 size 传入一个正整数即可，内核不会再根据传入的 size 直接作为数组的长度，因为内核是使用 hash 来管理要监视的文件描述符的。

作用和返回值：该函数会创建一个 epoll 实例（或epoll对象），同时返回一个引用该实例的文件描述符。返回的文件描述符仅仅指向对应的 epoll 实例，并不表示真实的磁盘文件节点。其他 API 如 epoll_ctl、epoll_wait 会使用这个文件描述符来操作相应的 epoll 实例，需要手动释放这个文件描述符。

一个epoll对象都有一个独立的eventpoll结构体，结构体如下：

C
1
2
3
4
5
6
7
8
struct eventpoll {
    ...
    /*红黑树的根节点，这颗树存储着所有添加到epoll中的需要监控的事件*/
    struct rb_root  rbr;
    /*双链表存储所有就绪的文件描述符*/
    struct list_head rdlist;
    ...
};
epoll 实例内部存储：

监听列表：所有要监听的文件描述符，使用红黑树，由 epoll_ctl 传来
就绪列表：所有就绪的文件描述符，使用双向链表
epoll_ctl

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
#include <sys/epoll.h>
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);

struct epoll_event {
       uint32_t events; /* Epoll 监视的事件，这些事件与 poll 能监视的事件差不多，只是宏名前面加了个E */
       epoll_data_t data; /* 用户数据，除了能保存文件描述符以外，还能让你保存一些其它有关数据，比如你这个文件描述符是嵌在一棵树上的，你在使用它的时候不知道它是树的哪个节点，则可以在布置监视任务的时候将相关的位置都保存下来。这个联合体成员就是 epoll 设计的精髓。 */
};

typedef union epoll_data {
    void        *ptr;
    int          fd;
    uint32_t     u32;
    uint64_t     u64;
} epoll_data_t;
参数和返回值：

epfd 即 epoll_create 返回的文件描述符，指向一个 epoll 实例
fd 表示要监听的目标文件描述符
event 表示要监听的事件（可读、可写、发送错误…）
op 表示要对 fd 执行的操作，有以下几种：

EPOLL_CTL_ADD：为 fd 添加一个监听事件 event
EPOLL_CTL_MOD：event 是一个结构体变量，这相当于变量 event 本身没变，但是更改了其内部字段的值
EPOLL_CTL_DEL：删除 fd 的所有监听事件，这种情况下 event 参数没用
返回值 0 或 -1，表示上述操作成功与否。

作用：epoll_ctl 会将文件描述符 fd 添加到 epoll 实例的监听列表里，同时为 fd 设置一个回调函数，并监听事件 event，如果红黑树中已经存在立刻返回。当 fd 上发生相应事件时，会调用回调函数，将 fd 添加到 epoll 实例的就绪队列上。

epoll_wait

C
1
int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);
参数和返回值：

epfd：要操作的 epoll 实例；
events 是一个数组，保存就绪状态的文件描述符，其空间由调用者负责申请
maxevents 指定 events 的大小
timeout 类似于 select 中的 timeout。如果没有文件描述符就绪，即就绪队列为空，则 epoll_wait 会阻塞 timeout 毫秒。如果 timeout 设为 -1，则 epoll_wait 会一直阻塞，直到有文件描述符就绪；如果 timeout 设为 0，则 epoll_wait 会立即返回
返回值表示 events 中存储的就绪描述符个数，最大不超过 maxevents。
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
static void relay(int fd1, int fd2) {
    int fd1_save, fd2_save;
    struct fsm_st fsm12, fsm21;
    int epfd; // epoll对象的文件描述符
    struct epoll_event ev;
    // 获取文件状态选项
    fd1_save = fcntl(fd1, F_GETFL);
    // 设置文件状态选项，添加非阻塞模式
    fcntl(fd1, F_SETFL, fd1_save|O_NONBLOCK);

    fd2_save = fcntl(fd2, F_GETFL);
    fcntl(fd2, F_SETFL, fd2_save|O_NONBLOCK);
    // 设置状态机
    fsm12.state = STATE_R;
    fsm12.sfd = fd1;
    fsm12.dfd = fd2;

    fsm21.state = STATE_R;
    fsm21.sfd = fd2;
    fsm21.dfd = fd1;

    epfd = epoll_create(10);
    if(epfd < 0) {
        perror("epoll_create()");
        exit(1);
    }
    ev.events = 0; // 清空监听的事件
    ev.data.fd = fd1; // 设置监听的文件描述符
    epoll_ctl(epfd, EPOLL_CTL_ADD, fd1, &ev); // 将fd1添加到epfd的监听列表里，同时为 fd 设置一个回调函数，并监听事件 event

    ev.events = 0;
    ev.data.fd = fd2;
    epoll_ctl(epfd, EPOLL_CTL_ADD, fd2, &ev);
    while(fsm12.state != STATE_T || fsm21.state != STATE_T) {
        // 布置监视任务
        ev.events = 0;
        ev.data.fd = fd1;
        if(fsm12.state == STATE_R) {
            ev.events |= EPOLLIN;
        }
        if(fsm21.state == STATE_W) {
            ev.events |= EPOLLOUT;
        }
        epoll_ctl(epfd, EPOLL_CTL_MOD, fd1, &ev);

        ev.events = 0;
        ev.data.fd = fd2;
        if(fsm12.state == STATE_W) {
            ev.events |= EPOLLOUT;
        }
        if(fsm21.state == STATE_R) {
            ev.events |= EPOLLIN;
        }
        epoll_ctl(epfd, EPOLL_CTL_MOD, fd2, &ev);
        // 监视
        if(fsm12.state < STATE_AUTO || fsm21.state < STATE_AUTO) {
            if(epoll_wait(epfd, &ev, 1, -1) < 0) { // 阻塞监视
                if(errno == EINTR) {
                    continue;
                }
                perror("epoll_wait()");
                exit(1);
            }
        }
        // 查看监视结果
        if(ev.data.fd == fd1 && ev.events & EPOLLIN \
            || ev.data.fd == fd2 && ev.events & EPOLLOUT \
            || fsm12.state > STATE_AUTO) {
            fsm_driver(&fsm12);
        }
        if(ev.data.fd == fd2 && ev.events & EPOLLIN \
            || ev.data.fd == fd1 && ev.events & EPOLLIN \
            || fsm12.state > STATE_AUTO) {
            fsm_driver(&fsm21);
        }
    }    

    // 恢复用户设置的文件状态
    fcntl(fd1, F_SETFL, fd1_save);
    fcntl(fd2, F_SETFL, fd2_save);
    close(epfd);
}
三者区别

select	poll	epoll
底层数据结构	数组存储文件描述符	链表存储文件描述符	红黑树存储监控的文件描述符，双链表存储就绪的文件描述符
如何从fd数据中获取就绪的fd	遍历fd_set	遍历链表	回调
时间复杂度	获得就绪的文件描述符需要遍历fd数组，O(n)	获得就绪的文件描述符需要遍历fd链表，O(n)	当有就绪事件时，系统注册的回调函数就会被调用，将就绪的fd放入到就绪链表中。O(1)
FD数据拷贝	每次调用select，需要将fd数据从用户空间拷贝到内核空间	每次调用poll，需要将fd数据从用户空间拷贝到内核空间	使用内存映射(mmap)，不需要从用户空间频繁拷贝fd数据到内核空间
最大连接数	有限制，一般为1024	无限制	无限制
9.5 其它IO函数
暂略

9.6 存储映射IO
暂略

10 进程间通信
对应APUE第十五章——进程间通信。

进程间通信（IPC，InterProcess Communication）分为：

PIPE（管道）
Socket（套接字）
XSI（System V）
消息队列
信号量数组
共享内存
这些手段都是用于进程间通讯的，只有进程间通讯才需要借助第三方机制，线程之间的通讯是不需要借助第三方机制的，因为线程之间的地址空间是共享的。

10.1 管道
管道是UNIX系统IPC的最古老形式，所有UNIX系统都提供此种通信机制，管道有以下两种局限性。

历史上，它们是半双工的（即数据只能在一个方向上流动），现在，某些系统提供全双工管道。
管道只能在具有公共祖先的两个进程之间使用。通常，一个管道由一个进程创建，在进程调用fork之后，这个管道就能在父进程和子进程之间使用了。
尽管有这两种局限性，半双工管道仍是最常用的IPC形式。每当在管道中键入一个命令序列，让shell执行时，shell都会为每一条命令单独创建一个进程，然后用管道将前一条命令进程的标准输出与后一条命令的标准输入相连接。

管道分为命名管道（FIFO）和匿名管道（PIPE），无论是哪种管道，都是由内核帮你创建和维护的。

10.1.1 匿名管道
匿名管道是通过调用pipe函数创建的。

C
1
2
#include <unistd.h>
int pipe(int pipefd[2]);
pipefd 是一个数组，表示管道的两端文件描述符，pipefd[0] 端作为读端，pipefd[1] 端作为写端。

pipe产生的是匿名管道，在磁盘的任何位置上找不到这个管道文件，而且匿名管道只能用于具有亲缘关系的进程之间通信。一般情况有亲缘关系的进程之间使用管道进行通信时，会把自己不用的一端文件描述符关闭。



注意：子进程打开的文件描述符与父进程的一致。

代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

#define BUFSIZE        1024

void main(void) {
    int pd[2];
    char buf[BUFSIZE];
    pid_t pid;
    int len;

    // 创建匿名管道
    if(pipe(pd) < 0) {
        perror("pipe()");
        exit(1);
    }

    // 创建子进程
    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) { // 子进程 读取管道数据
        // 关闭写端
        close(pd[1]);
        // 从管道中读取数据，如果子进程比父进程先被调度会阻塞等待数据写入
        len = read(pd[0], buf, BUFSIZE);
        puts(buf);
        // 管道是fork之前创建的，父子进程里都有一份，所以退出之前要确保管道两端都关闭
        close(pd[0]);
        exit(0);
    } else { // 父进程 向管道写入数据
        // 关闭读端
        close(pd[0]);
        write(pd[1], "Hello!", 6);
        close(pd[1]);
        // 等待子进程退出
        wait(NULL);
        exit(0);
    }
    // never reached
    exit(0);
}
10.1.2 命名管道
mkfifo函数用于创建命名管道，作用与匿名管道相同，不过可以在不同的进程之间使用，相当于对一个普通文件进行读写操作就可以了。

C
1
2
3
 #include <sys/types.h>
#include <sys/stat.h>
int mkfifo(const char *pathname, mode_t mode);
pathname：管道文件的路径和文件名。
mode：创建管道文件的权限。该mode还需要和umask做并运算来确定最后的管道文件权限。
返回值：成功返回 0，失败返回 -1 并设置 errno
当用mkfifo创建FIFO时，要用open来打开它。

FIFO有以下两种用途：

shell命令使用FIFO将数据从一条管道传送到另一条时，无需创建中间临时文件
客户进程-服务器进程应用程序中，FIFO 用作汇聚点，在客户进程和服务器进程二者之间传递数据。
可以使用命令，来创建管道文件：

BASH
1
mkfifo filename
例如：



由第一位p可知其为一个管道文件。

代码示例1

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

#include <sys/types.h>
#include <sys/stat.h>

#define PATHNAME    "/tmp/myfifo"
#define BUFSIZE 1024

int main (void) {
    pid_t pid;
    int fd = -1;
    char buf[BUFSIZE] = "";

    // 创建一个命名管道，用 ls -l 命令查看这个管道文件的属性
    if (mkfifo(PATHNAME, 0644) < 0) {
        perror("mkfifo()");
        exit(1);
    }

    fflush(NULL);
    pid = fork();
    if (pid < 0) {
        perror("fork()");
        exit(1);
    }
    if (pid > 0) { // 父进程
        pid = fork(); // 再次产生一个子进程
        if (pid < 0) {
            perror("fork()");
            exit(1);
        }
        if (pid > 0) { // 父进程
            // 两个子进程都创建完之后父进程直接退出，使两个子进程不具有亲缘关系。
            exit(0);
        }
        /* 子进程2 */
        /* 像操作普通文件一样对这个管道进行 open(2)、read(2)、write(2)、close(2) */
        fd = open(PATHNAME, O_RDWR);
        if (fd < 0) {
            perror("open()");
            exit(1);
        }
        read(fd, buf, BUFSIZE); // 读管道，如果没有数据会阻塞
        printf("%s", buf);
        write(fd, " World!", 8);
        close(fd);
        exit(0);
    } else { // 子进程1
        fd = open(PATHNAME, O_RDWR);
        if (fd < 0) {
            perror("open()");
            exit(1);
        }
        write(fd, "Hello", 6);
        sleep(1); // 刚写完管道不要马上读，等第二个进程读取完并且写入新数据之后再读。
        read(fd, buf, BUFSIZE);
        close(fd);
        puts(buf);
        // 肯定是这个进程最后退出，所以把管道文件删除，不然下次再创建的时候会报文件已存在的错误
        remove(PATHNAME);
        exit(0);
    }
    return 0;
}
子进程1在写完管道之后要先休眠，等待子进程2从管道的另一端把数据读入并写入新的数据，子进程1再醒来读出管道的内容。如果子进程1不休眠而是在写完之后马上读管道，很可能在它写完管道之后子进程2还没来得及调度，它自己就又把管道里的数据读出来了，这样不仅读出来的不是子进程2写入的数据，还会导致子进程2永远阻塞在 read ，因为管道中不会再有数据写入。

可能的过程：

PLAINTEXT
1
子进程2 read 阻塞 --> 子进程1写Hello --> 子进程1休眠 --> 子进程2读Hello --> 子进程1 read 阻塞 --> 子进程2写 World! --> 子进程1读 World!
代码示例2

comm.h
C
1
2
3
4
5
6
7
8
#include<string.h>
#include<stdio.h>
#include<sys/stat.h>
#include<sys/types.h>
#include<fcntl.h>
#include<unistd.h>

#define MY_FIFO "./fifo"
server.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
#include"comm.h"
#define BUFSIZE 1024
int main() {
    umask(0);
    if(mkfifo(MY_FIFO, 0666) < 0) { //创建命名管道
    	perror("mkfifo");
    	return 1;
    }

    // 只需要文件操作即可
    int fd = open(MY_FIFO, O_RDONLY);
    if(fd < 0) {
    	perror("open");
    	return 2;
    }

    // 业务逻辑
    while(1) {
        char buffer[BUFSIZE];
        ssize_t s = read(fd, buffer, BUFSIZE-1);
        if(s > 0) {
            buffer[s] = 0;
            printf("client-> %s\n", buffer);
        }else if(s == 0) {
            printf("client quit...\n");
            break;
        }else {
            perror("read");
            break;
        }
    }
    close(fd);
    return 0;
}
client.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
#include "comm.h"
#define BUFSIZE 1024
int main() {
    int fd = open(MY_FIFO, O_WRONLY);
    if(fd < 0) {
        perror("open");
        return 1;
    }
    
    // 业务逻辑
    while(1) {
   		printf("请输入-> ");
    	fflush(stdout);
    	char buffer[BUFSIZE];
    	ssize_t s = read(0, buffer, BUFSIZE-1);
    	if(s > 0) {
            buffer[s-1] = 0;
    		write(fd, buffer, strlen(buffer));
    	}
    }
    return 0;
}
执行结果：

BASH
1
2
3
4
5
6
7
8
9
[root@HongyiZeng proc]# ./server
client-> 1
client-> Hello World!


[root@HongyiZeng proc]# ./client 
请输入-> 1
请输入-> Hello World!
请输入-> 
10.2 XSI IPC
XSI IPC函数是紧密地基于System V的IPC函数的。

system V：同一主机内的进程间通信方案，在OS层面专门为进程间通信设计的方案。

system V标准下的三种通信方式：

共享内存
消息队列
信号量
10.2.1 相关命令
ipcs命令可以查看 XSI IPC 的使用情况。
ipcrm命令可以删除指定的 XSI IPC。
BASH
1
2
3
4
5
6
7
8
9
[root@HongyiZeng proc]# ipcs
------ Message Queues -------- # 消息队列
key        msqid      owner      perms      used-bytes   messages    

------ Shared Memory Segments -------- # 共享内存
key        shmid      owner      perms      bytes      nattch     status      

------ Semaphore Arrays -------- # 信号量数组
key        semid      owner      perms      nsems
通过 ipcs 命令可以看出来，命令的输出结果分为三个部分，第一部分是系统中当前开辟的共享内存（shm），第二部分是信号量数组（sem），第三部分是消息队列（msg）。

可以看到，不论是哪一部分，都有一列叫做key，使用 XSI IPC 通信的进程就是通过同一个 key 值操作同一个共享资源的。这个 key 是一个正整数，与文件描述符不同的是，生成一个新 key 值时它不采用当前可用数值中的最小值，而是类似生成进程 ID 的方式，key 值连续的加 1，直至达到一个整数的最大正值，然后再回转到 0 从头开始累加。

不同IPC的特征比较



10.2.2 标识符和键
每个内核中的IPC结构（消息队列、信号量或共享存储）都用一个非负整数的标识符（identifier）加以引用。例如，要向一个消息队列发送消息或者从一个消息队列取消息，只需要知道其队列标识符。标识符是IPC对象的内部名，为使多个合作进程能够在同一IPC对象上汇聚，需要提供一个外部命名方案。为此，每个 IPC对象都与一个键（key）相关联，将这个键作为该对象的外部名。

无论何时创建IPC结构（通过调用msgget、semget或shmget 创建），都应指定一个键。这个键的数据类型是基本系统数据类型key_t，通常在头文件<sys/types.h>中被定义为长整型。这个键由内核变换成标识符。

有多种方法使客户进程和服务器进程在同一IPC结构上汇聚。

方法1：服务器进程可以指定键IPC_PRIVATE创建一个新IPC结构，将返回的标识符存放在某处（如一个文件）以便客户进程取用。键IPC_PRIVATE保证服务器进程创建一个新IPC结构。这种技术的缺点是：文件系统操作需要服务器进程将整型标识符写到文件中，此后客户进程又要读这个文件取得此标识符。IPC_PRIVATE键也可用于父进程子关系。父进程指定IPC_PRIVATE创建一个新IPC结构，所返回的标识符可供fork后的子进程使用。接着，子进程又可将此标识符作为exec函数的一个参数传给一个新程序。

方法2：可以在一个公用头文件中定义一个客户进程和服务器进程都认可的键。然后服务器进程指定此键创建一个新的IPC结构。这种方法的问题是该键可能已与一个IPC结构相结合，在此情况下，get 函数（msgget、semget 或shmget）出错返回。服务器进程必须处理这一错误，删除已存在的IPC结构，然后试着再创建它。

方法3：客户进程和服务器进程认同一个路径名和项目ID（项目ID是0～255之间的字符值），接着，调用函数ftok将这两个值变换为一个键。然后在方法2中使用此键。ftok提供的唯一服务就是由一个路径名和项目ID产生一个键。
C
1
2
#include <sys/ipc.h>
key_t ftok(const chat *path, int id);
path参数必须引用一个现有的文件。当产生键时，只使用id参数的低8位。对于不同文件的两个路径名，如果使用同一项目ID，可能产生相同的键。

3个get 函数（msgget、semget和shmget）都有两个类似的参数：一个key和一个整型flag。在创建新的IPC结构（通常由服务器进程创建）时，如果key是IPC_PRIVATE或者和当前某种类型的IPC结构无关，则需要指明 flag 的IPC_CREAT标志位。为了引用一个现有队列（通常由客户进程创建），key必须等于队列创建时指明的key的值，并且IPC_CREAT必须不被指明。

注意，决不能指定IPC_PRIVATE作为键来引用一个现有队列，因为这个特殊的键值总是用于创建一个新队列。

如果希望创建一个新的IPC结构，而且要确保没有引用具有同一标识符的一个现有IPC结构，那么必须在flag中同时指定IPC_CREAT和IPC_EXCL位。这样做了以后，如果IPC结构已经存在就会造成出错，返回EEXTST。

10.2.3 消息队列
消息队列是消息的链接表，存储在内核中，由消息队列标识符标识。

msg、sem 和 shm 都有一系列函数遵循下面的命名规则：

xxxget()：创建或引用，将key转换为标识符id
xxxop()：相关操作
xxxctl()：其它的控制或销毁
每个队列都有一个msqid_ds结构体与之关联：

C
1
2
3
4
5
6
7
8
struct msqid_ds {
    struct ipc_perm msg_perm; // 保存ipc权限信息的结构体
    msgqnum_t msg_qnum;
    msglen_t msg_qbytes;
    pid_t msg_lspid;
    pid_t msg_lrpid;
	// ...
}
相关系统调用

C
1
2
3
4
5
6
7
// msgget - get a System V message queue identifier

#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>
// 函数的作用是创建或引用一个消息队列，消息队列是双工的，两边都可以读写。
int msgget(key_t key, int msgflg);
key：IPC内核标识符的外部方案实现，拥有相同 key 的双方才可以通信。key 值必须是唯一的，ftok 函数可以用于获取 key
msgflg：特殊要求，没有写0
返回：非负的队列ID或出错-1
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

// 将 msgp 指向的结构体存放到 msgid 的消息队列中，这段空间有 msgz 个字节大小，msgz 的值要减掉强制的成员 mtype 的大小（sizeof(long)）。
int msgsnd(int msqid, const void *msgp, size_t msgsz, int msgflg);

// 从 msgid 这个消息队列中取出 msgp 结构体数据，msgp 的大小是 msgsz，msgflg 是特殊要求，没有特殊要求可以写 0。 
ssize_t msgrcv(int msqid, void *msgp, size_t msgsz, long msgtyp,
               int msgflg);
// msgtyp可以指定接收哪一种信息“
// 0:  返回队列的第一个信息
// >0: 返回队列中消息类型为 type 的第一个消息。
// <0: 返回队列中消息类型值小于等于 type 绝对值的消息，如果这种消息有若干个，则取类型值最小的消息。


/* msgp 指向的结构体的成员定义要类似 msgbuf 这个结构体，第一个成员必须是 long 类型的 mtype，并且必须是 > 0 的值 */
struct msgbuf {
    long mtype;       /* 消息类型，必须 > 0 */
    char mtext[512];  /* 消息数据字段 */
};
C
1
2
3
4
5
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>
// msqctl函数对队列执行多种操作。它和另外两个与信号量及共享存储有关的函数（semctl 和shmctl）都是XSI IPC的类似于ioctl的函数（亦即垃圾桶函数）。
int msgctl(int msqid, int cmd, struct msqid_ds *buf);
cmd参数指定对msqid指定的队列要执行的命令：

IPC_STAT：取此队列的msqid_ds结构，并将它存放在buf指向的结构中。
IPC_RMID：从系统中删除该消息队列以及仍在该队列中的所有数据。这种删除立即生效。仍在使用这一消息队列的其他进程在它们下一次试图对此队列进行操作时，将得到EIDRM错误。此命令只能由下列两种进程执行：一种是其有效用户ID等于msg_perm.cuid或msg_perm.uid；另一种是具有超级用户特权的进程。
代码示例

proto.h：定义双方都需要使用的数据或对象
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
#ifndef __PROTO_H__
#define __PROTO_H__

#define NAMESIZE        32

/* 通讯双方生成 key 值共同使用的文件 */
#define KEYPATH            "/usr/local/linux_c/proc/key"
/* 通讯双方生成 key 值共同使用的 salt 值：0～255之间的字符值 */
#define KEYPROJ            'a'
/* 消息类型，只要是大于 0 的合法整数即可 */
#define MSGTYPE            10

/* 通讯双方约定的协议 */
struct msg_st {
    long mtype; // 消息类型
    char name[NAMESIZE]; // 消息数据
    int math; // 其他信息
    int chinese;
};
#endif
snder.c：客户端
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <string.h>
#include <unistd.h>
#include <time.h>

#include "proto.h"


int main() {
    key_t key;
    int msgid;
    struct msg_st sbuf;

    // 设置随机数种子
    srand(time(NULL));
    // 用与接收方相同的文件和 salt 生成一样的key，这样才可以通讯
    key = ftok(KEYPATH, KEYPROJ);
    if(key < 0) {
        perror("ftok()");
        exit(1);
    }

    // 取得消息队列
    msgid = msgget(key, 0);
    if(msgid < 0) {
        perror("msgget()");
        exit(1);
    }

    // 为要发送的结构体赋值
    sbuf.mtype = MSGTYPE;
    strcpy(sbuf.name, "Alan");
    sbuf.math = rand() % 100;
    sbuf.chinese = rand() % 100;

    // 发送结构体
    if(msgsnd(msgid, &sbuf, sizeof(sbuf)-sizeof(long), 0) < 0) {
        perror("msgsnd()");
        exit(1);
    }
    puts("ok!");
    // 消息队列不是发送方创建的，所以发送方不用负责销毁
    exit(0);
}
rcver.c：服务器
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>

#include "proto.h"

int main() {
    key_t key;
    int msgid;
    struct msg_st rbuf;

    // 通过文件和字符 'a' 生成唯一的 key，注意文件必须是真实存在的
    key = ftok(KEYPATH,KEYPROJ);
    if(key < 0) {
        perror("ftok()");
        exit(1);
    }

    // 接收端应该先启动，所以消息队列由接收端创建
    msgid = msgget(key, IPC_CREAT|0600);
    if(msgid < 0) {
        perror("msgget()");
        exit(1);
    }

    // 不停的接收消息
    while(1) {
        // 没有消息的时候会阻塞等待
        if(msgrcv(msgid, &rbuf, sizeof(rbuf)-sizeof(long), 0, 0) < 0) {
            perror("msgrcv");
            exit(1);
        }

        /* 用结构体中强制添加的成员判断消息类型，
         * 当然在这个例子中只有一种消息类型，所以不判断也可以。
         * 如果包含多种消息类型这里可以写一组 switch...case 结构
         */
        if(rbuf.mtype == MSGTYPE) {
            printf("Name = %s\n",rbuf.name);
            printf("Math = %d\n",rbuf.math);
            printf("Chinese = %d\n",rbuf.chinese);
        }
    }

    /* 谁创建谁销毁。
     * 当然这个程序是无法正常结束的，只能通过信号杀死。
     * 使用信号杀死之后大家可以用 ipcs(1) 命令查看一下，消息队列应该是没有被销毁的，
     * 大家可以使用上面我们提到的 ipcrm(1) 命令把它删掉。
     */
    msgctl(msgid, IPC_RMID, NULL);

    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
[root@HongyiZeng proc]# ./rcver 
Name = Alan
Math = 81
Chinese = 36

[root@HongyiZeng proc]# ./snder 
ok!
使用命令查看ipc并销毁：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
[root@HongyiZeng proc]# ipcs
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    
0x610125ef 0          root       600        0            0           

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status      

------ Semaphore Arrays --------
key        semid      owner      perms      nsems
[root@HongyiZeng proc]# ipcrm -q 0
[root@HongyiZeng proc]# ipcs
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status      

------ Semaphore Arrays --------
key        semid      owner      perms      nsems 
10.2.4 信号量
信号量与已经介绍过的IPC（管道、FIFO以及消息列队）不同。它是一个计数器，用于为多个进程提供对共享数据对象的访问。

为了获得共享资源，进程需要执行下列操作：

测试控制该资源的信号量。
若此信号量的值为正，则进程可以使用该资源。在这种情况下，进程会将信号量值减1，表示它使用了一个资源单位。
否则，若此信号量的值为0，则进程进入休眠状态，直至信号量值大于0。进程被唤醒后，它返回至步骤1。
当进程不再使用由一个信号量控制的共享资源时，该信号量值增1。如果有进程正在休眠等待此信号量，则唤醒它们。

为了正确地实现信号量，信号量值的测试及减1操作应当是原子操作。为此，信号量通常是在内核中实现的。

常用的信号最形式被称为二元信号量（binary semaphore）。它控制单个资源，其初始值为1。但是，一般而言，信号量的初值可以是任意一个正值，该值表明有多少个共享资源单位可供共享应用。

相关系统调用

C
1
2
3
4
5
6
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>

// 创建或获得一个信号量id
int semget(key_t key, int nsems, int semflg);
key：具有亲缘关系的进程之间可以使用一个匿名的 key 值，key 使用宏 IPC_PRIVATE 即可。
nsems：表示有多少个信号。信号量实际上是一个计数器，所以如果设置为 1 可以用来模拟互斥量。
semflg：IPC_CREAT 表示创建信号量，同时需要按位或一个权限，如果是匿名 IPC 则无需指定这个宏，直接给权限就行了。
成功返回 sem ID，失败返回 -1 并设置 errno。
C
1
2
3
4
5
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
// 用来控制或销毁信号量
int semctl(int semid, int semnum, int cmd, ...);
semid：信号量id
semnum：信号量数组的下标
C
1
2
3
4
5
6
7
8
9
10
11
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
// 操作信号量
int semop(int semid, struct sembuf *sops, unsigned nsops);

struct sembuf {
    unsigned short sem_num; /* 对第几个资源（数组下标）操作 */
    short sem_op; /* 取几个资源写负数几(不要写减等于)，归还几个资源就写正数几 */
    short sem_flg; /* 特殊要求 */
};
sops：结构体数组起始位置；
nsops：结构体数组长度；
返回值：成功返回0，失败返回-1并设置 errno。
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <errno.h>

#define PROCNUM        20
#define FNAME        "/tmp/out"
#define BUFSIZE        1024

// 多个函数都要使用这个信号量 ID，所以定义为全局变量
static int semid;

static void P(void) {
    struct sembuf op;

    op.sem_num = 0; // 只有一个资源，所以数组下标是 0
    op.sem_op = -1; // 取一个资源就减1
    op.sem_flg = 0; // 没有特殊要求
    while(semop(semid,&op,1) < 0) {
        // 出现假错就重试
        if(errno != EINTR && errno != EAGAIN) {
            perror("semop()");
            exit(1);
        }
    }

}

static void V(void) {
    struct sembuf op;

    op.sem_num = 0;
    op.sem_op = 1; // 归还一个资源
    op.sem_flg = 0;
    while(semop(semid,&op,1) < 0) {
        if(errno != EINTR && errno != EAGAIN) {
            perror("semop()");
            exit(1);
        }
    }
}

static void func_add() {
    FILE *fp;
    char buf[BUFSIZE];

    fp = fopen(FNAME,"r+");
    if(fp == NULL) {
        perror("fopen()");
        exit(1);
    }

    // 先取得信号量在操作文件，取不到就阻塞等待，避免发生竞争
    P();
    fgets(buf,BUFSIZE,fp);
    rewind(fp);
    sleep(1); // 调试用，为了放大竞争，更容易看出来互斥量发挥了作用
    fprintf(fp,"%d\n",atoi(buf)+1);
    fflush(fp);
    // 操作结束之后归还信号量，让其它进程可以取得信号量
    V();
    fclose(fp);

    return ;
}

int main() {
    int i;
    pid_t pid;

    // 在具有亲缘关系的进程之间使用，所以设置为 IPC_PRIVATE 即可。
    // 另外想要实现互斥量的效果，所以信号量数量设置为 1 个即可。
    semid = semget(IPC_PRIVATE, 1, 0600);
    if(semid < 0) {
        perror("semget()");
        exit(1);
    }

    // 将 union semun.val 的值设置为 1
    if(semctl(semid, 0, SETVAL, 1) < 0) {
        perror("semctl()");
        exit(1);
    }


    // 创建 20 个子进程
    for(i = 0 ; i < PROCNUM ; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) {   // child
        
            func_add();
            exit(0);
        }
    }

    for(i = 0 ; i < PROCNUM ; i++)
        wait(NULL);

    // 销毁信号量
    semctl(semid,0,IPC_RMID);

    exit(0);
}
10.2.5 共享存储
共享存储允许两个或多个进程共享一个给定的存储区。因为数据不需要在客户进程和服务器进程之间复制，所以这是最快的一种IPC。

使用共享存储时要掌握的唯一窍门是，在多个进程之间同步访问一个给定的存储区。若服务器进程正在将数据放入共享存储区，则在它做完这一操作之前，客户进程不应当去取这些数据。通常，信号量用于同步共享存储访问。（不过正如前节最后部分所述，也可以用记录锁或互斥量。）

我们已经看到了共享存储的一种形式，就是在多个进程将同一个文件映射到它们的地址空间的时候。XSI共享存储和内存映射的文件的不同之处在于，前者没有相关的文件。XSI共享存储段是内存的匿名段。

相关系统调用

C
1
2
3
4
#include <sys/ipc.h>
#include <sys/shm.h>

int shmget(key_t key, size_t size, int shmflg);
C
1
2
3
4
#include <sys/types.h>
#include <sys/shm.h>

void *shmat(int shmid, const void *shmaddr, int shmflg);78 int shmdt(const void *shmaddr);
C
1
2
3
4
#include <sys/ipc.h>
#include <sys/shm.h>

int shmctl(int shmid, int cmd, struct shmid_ds *buf);
代码示例

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <fcntl.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/wait.h>

// 申请的共享内存大小，单位是字节
#define MEMSIZE        1024

int main() {
    char *str;
    pid_t pid;
    int shmid;

    // 有亲缘关系的进程 key 参数可以使用 IPC_PRIVATE 宏，并且创建共享内存 shmflg 参数不需要使用 IPC_CREAT 宏
    shmid = shmget(IPC_PRIVATE, MEMSIZE, 0600);
    if(shmid < 0) {
        perror("shmget()");
        exit(1);
    }

    pid = fork();
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) { // 子进程
        // 关联共享内存
        str = shmat(shmid,NULL,0);
        if(str == (void *)-1) {
            perror("shmat()");
            exit(1);
        }
        // 向共享内存写入数据
        strcpy(str,"Hello!");
        // 分离共享内存
        shmdt(str);
        // 无需释放共享内存
        exit(0);
    }
    else { // 父进程
        // 等待子进程结束再运行，因为需要读取子进程写入共享内存的数据
        wait(NULL);
        // 关联共享内存
        str = shmat(shmid,NULL,0);
        if(str == (void *)-1) {
            perror("shmat()");
            exit(1);
        }
        // 直接把共享内存中的数据打印出来
        puts(str);
        // 分离共享内存
        shmdt(str);
        // 释放共享内存
        shmctl(shmid,IPC_RMID,NULL);
        exit(0);
    }
    exit(0);
}
11 网络套接字
本节对应APUE第十六章——网络IPC：套接字

11.1 引言
上一章考察了各种UNIX系统所提供的经典进程间通信机制（IPC）：管道、FIFO、消息队列、信号量以及共享存储。这些机制允许在同一台计算机上运行的进程可以相互通信。本章将考察不同计算机（通过网络相连）上的进程相互通信的机制：网络进程间通信（network IPC）。

在本章中，我们将描述套接字网络进程间通信接口，进程用该接口能够和其他进程通信，无论它们是在同一台计算机上还是在不同的计算机上。实际上，这正是套接字接口的设计目标之一：同样的接口既可以用于计算机间通信，也可以用于计算机内通信。尽管套接口可以采用许多不同的网络协议进行通信，但本章的讨论限制在因特网事实上的通信标准：TCP/IP协议栈。

11.2 套接字描述符
套接字是一种通信机制（通信的两方的一种约定），socket屏蔽了各个协议的通信细节，提供了tcp/ip协议的抽象，对外提供了一套接口，通过这个接口就可以统一、方便的使用tcp/ip协议的功能。这使得程序员无需关注协议本身，直接使用socket提供的接口来进行互联的不同主机间的进程的通信。我们可以用套接字中的相关函数来完成通信过程。



套接字是通信端点的抽象。正如使用文件描述符访问文件，应用程序用套接字描述符访问套接字。套接字描述符在UNIX系统中被当作是一种文件描述符。事实上，许多处理文件描述符的函数（如read和write）可以用于处理套接字描述符。

为创建一个套接字，调用socket函数：

C
1
2
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
domain：域，或者协议族，确定通信的特性。即采用什么协议来传输数据。
AF_UNIX、AF_LOCAL：本地协议；
AF_INET：IPV4 协议；
AF_INET6：IPV6 协议；
AF_IPX：是非常古老的操作系统，出现在 TCP/IP 之前；
AF_NETLINK：是用户态与内核态通信的协议；
AF_APPLETALK：苹果使用的一个局域网协议；
AF_PACKET：底层 socket 所用到的协议，比如抓包器所遵循的协议一定要在网卡驱动层，而不能在应用层，否则无法见到包封装的过程。再比如 ping 命令，想要实现 ping 命令就需要了解这个协议族。
type：确定套接字的类型，进一步确定通信特征。
SOCK_STREAM：流式套接字，特点是有序、可靠。有序、双工、基于连接的、以字节流为单位的。
可靠不是指不丢包，而是流式套接字保证只要你能接收到这个包，那么包中的数据的完整性一定是正确的。
双工是指双方都能收发。
基于连接的是指：通信双方是知道对方是谁。
字节流是指数据没有明显的界限，一端数据可以分为任意多个包发送。
SOCK_DGRAM：报式套接字（数据报套接字），无连接的，固定的最大长度，不可靠的消息。
SOCK_SEQPACKET：提供有序、可靠、双向、基于连接的数据报通信。
SOCK_RAW：原始的套接字，提供的是网络协议层的访问。
SOCK_RDM：数据层的访问，不保证传输顺序。
protocol：具体使用哪个协议。在 domain 的协议族中每一个对应的 type 都有一个或多个协议，使用协议族中默认的协议可以填写0。
返回值：如果成功，返回套接字描述符；如果失败，返回 -1，并设置 errno。
调用socket与调用open相类似。在两种情况下，均可获得用于I/O的文件描述符。当不再需要该文件描述符时，调用close来关闭对文件或套接字的访问，并且释放该描述符以便重新使用。

虽然套接字描述符本质上是一个文件描述符，但不是所有参数为文件描述符的函数都可以接受套接字描述符。图16-4总结了到目前为止所讨论的大多数以文件描述符为参数的函数使用套接字描述符时的行为。未指定和由实现定义的行为通常意味着该函数对套接字描述符无效。例如lseek不能以套接字描述符为参数，因为套接字不支持文件偏移量的概念。



11.3 制定协议
11.3.1 字节序
大端格式：低地址存放高位数据，高地址存放低位数据。

小端格式：低地址存放低位数据，高地址存放高位数据。

假设要存放的数据是 0x30313233，那么 33 是低位，30 是高位，在大端存储格式中，30 存放在低位，33 存放在高位；而在小端存储格式中，33 存放在低位，30 存放在高位。



主机字节序：host

网络字节序：network

网络协议指定了字节序，因此异构计算机系统能够交换协议信息而不会被字节序所混淆。TCP/IP协议栈使用大端字节序。应用程序交换格式化数据时，字节序问题就会出现。对于TCP/IP，地址用网络字节序来表示，所以应用程序有时需要在处理器的字节序与网络字节序之间转换它们。例如，以一种易读的形式打印一个地址时，这种转换很常见。

对于TCP/IP应用程序，有4个用来在处理器字节序和网络字节序之间实施转换的函数：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
#include <arpa/inet.h>

// 主机字节序转换为网络字节序
// 返回以网络字节序表示的32位整数
uint32_t htonl(uint32_t hostlong);

// 主机字节序转换为网络字节序
// 返回以网络字节序表示的16位整数
uint16_t htons(uint16_t hostshort);

uint32_t ntohl(uint32_t netlong);

uint16_t ntohs(uint16_t netshort);
h表示主机字节序，
n表示网络字节序。
l表示长（即4字节）整数
s表示短（即2字节）整数
11.3.2 对齐
以下面的结构体为例：

C
1
2
3
4
5
struct {
    int i;
    float f;
    char ch;
};
在32位的机器上，各占用4，4，1共9个字节的大小。但是编译器会将其自动对齐，此时为12个字节。

对齐原因：

平台原因： 不是所有的硬件平台都能访问任意地址上的任意数据的；某些硬件平台只能在某些地址处取某些特定类型的数据，否则抛出硬件异常。
性能原因： 数据结构(尤其是栈)应该尽可能地在自然边界上对齐。 原因在于，为了访问未对齐的内存，处理器需要作两次内存访问；而对齐的内存访问仅需要一次访问。
总体来说：结构体的内存对齐是拿空间来换取时间的做法。优点是提高了可移植性和cpu性能。

网络传输的结构体中的成员都是紧凑的，所以不能地址对齐，需要在结构体外面增加 __attribute__((packed))。例如：

C
1
2
3
4
5
struct msg_st {
     uint8_t name[NAMESIZE];
     uint32_t math;
     uint32_t chinese;
}__attribute__((packed));
11.3.3 类型长度问题
标准C并没有对int、char这样的基本数据类型占用多大字节做一个明确的规定，例如：

一个16位的机器上，int可能占2个字节；

一个32位的机器上，int可能占4个字节；

解决：使用int32_t、uint32_t、int64_t、int8_t、uint8_t等类型明确指定占用的位数。这些类型包含在头文件<stdint.h>中。

stdint.h

stdint.h是c99中引进的一个标准C库的头文件。

C的整数类型有：

类型	最小大小（bits）	说明
char	无要求	最小的可寻址单元，能包含基础字符集，大小无硬性要求，可能是signed或unsigned的，现在基本都是8 bits
short	16	
int	16	没有要求要大于short
long	32	
long long	64	C99 及以后支持
每个类型都有signed和unsigned之分。

C99中引入了固定大小的整数类型，和字节数有关的类型，其中包括：

定长类型(u)intN_t，比如 int16_t，uint64_t ，保证变量占用的内存空间一定，但是不保证能够无开销的进行无符号整数溢出，也不保证在任何平台和编译器中存在。
快速运算类型(u)int_fastN_t，比如int_fast32_t，会根据平台来选择运算速度最快的大于N比特的类型，通常在AMD64下当N大于等于16时都是 (u)int64_t，这样就没有必要保证完美的进行无开销的无符号整数溢出的行为，并且保证在任何编译器实现中都存在。通常它们都用于在寄存器中的运算，你绝对不会想把它们存在内存里，这就要看：
空间优先类型(u)int_leastN_t，比如int_least16_t，会从定长类型中选择满足最小的至少为N的类型，保证一定存在，不保证一定为N比特.
11.3.4 主动端和被动端


Socket中主动端（客户端）和被动端（服务器）需要做的工作有：

主动端——客户端

取得 Socket
给 Socket 关联绑定地址（可省略，不必与操作系统约定端口，由操作系统指定随机端口）
发/收消息
关闭 Socket
被动端——服务器

取得 Socket
给 Socket 关联地址
将Socket置为监听模式（如果是流式套接字）
接受连接（如果是流式套接字）
收/发消息
关闭 Socket
11.4 寻址
标识目标通信进程需要网络地址（IP）和端口号（port），前者标识网络上想与之通信的计算机，后者帮助标识特定的进程，因此需要将套接字与这两者进行绑定关联。

11.4.1 地址格式
一个地址（IP+PORT）标识一个特定通信域的套接字端点，地址格式与这个特定的通信域相关。为使不同格式地址能够传入到套接字函数，不同的地址结构会被强制转换成一个通用的地址结构 sockaddr：

C
1
2
3
4
5
struct sockaddr {
	sa_family_t sa_family; // 协议族
    char sa_data[]; // 变长地址
    // ...
};
例如，因特网地址定义在<netinet/in.h>头文件中。在IPv4因特网域（AF_INET）中，套接字的地址用结构 sockaddr_in表示：

C
1
2
3
4
5
6
7
8
9
10
// 地址
struct sockaddr_in { 
    sa_family_t sin_family; // 协议族
    in_port_t sin_port; // 端口号
    struct in_addr sin_addr; // ipv4地址
};

struct in_addr {
	uint32_t s_addr; // ipv4地址，是一个32位无符号的整数
};
有时，需要打印出能被人理解而不是计算机所理解的地址格式。BSD 网络软件包含函数inet_addr和inet_ntoa，用于二进制地址格式与点分十进制字符表示（a.b.c.d）之间的相互转换。但是这些函数仅适用于IPv4地址。有两个新函数inet_ntop和inet_pton具有相似的功能，而且同时支持IPv4地址和IPv6地址。

C
1
2
3
4
5
6
#include <arpa/inet.h>
// 将网络字节序的地址addr（一个uint32_t的大整数）转换为点分十进制的文本字符串格式str
const char *inet_ntop(int domain, const void *restrcit addr, char *restrict str, socklen_t size);

// 将点分十进制的文本字符串格式转换为网络字节序的地址（一个uint32_t的大整数），放在addr中
const char *inet_pton(int domain, const void *restrcit str, char *restrict addr);
domain：协议族，仅支持以下两种：
AF_INET：IPV4 协议
AF_INET6：IPV6 协议
addr：网络字节序的二进制地址
str和size：指定存放转换后地址的位置和缓冲区大小，size的大小可设置为下面两个常数
INET_ADDRSTRLEN：它定义了足够大的空间来存放一个表示IPv4地址的文本字符串
INET6_ADDRSTRLEN ：定义了足够大的空间来存放一个表示 IPv6 地址的文本字符串
11.4.2 套接字与地址关联
将一个客户端的套接字关联上一个地址没有多少新意，可以让系统选一个默认的地址。然而，对于服务器，需要给一个接收客户端请求的服务器套接字关联上一个众所周知的地址。客户端应有一种方法来发现连接服务器所需要的地址，最简单的方法就是服务器保留一个地址并且注册在/etc/services或者某个名字服务中。

使用bind函数来关联地址和套接字：

C
1
2
3
4
5
6
// bind - bind a name to a socket

#include <sys/types.h>          /* See NOTES */
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
sockfd：套接字描述符
addr：要绑定到套接字上的地址。该地址必须和创建套接字时的地址族所支持的格式相匹配。例如，创建套接字时指定的domain为AF_INET（ipv4），则传入的地址的结构体类型必须为sockaddr_in，详见11.4.1节
addrlen：addr 传递的地址结构体的长度。
可以调用 getsockname函数来发现绑定到套接字上的地址：

C
1
2
#include <sys/socket.h>
int getsockname(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict alenp);
如果套接字已经和对等方连接，可以调用 getpeername 函数来找到对方的地址：

C
1
2
#include <sys/socket.h>
int getpeername(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict alenp);
11.5 建立连接
如果要处理一个面向连接（流套接字）的网络服务，那么在开始交换数据以前，需要在请求服务的进程套接字（客户端）和提供服务的进程套接字（服务器）之间建立一个连接。

使用connect函数来建立连接：

C
1
2
3
#include <sys/socket.h>
int connect(int sockfd, const struct sockaddr *addr, socklen_t len);
// 成功返回0， 错误返回-1
在connect中指定的地址是我们想与之通信的服务器地址。如果sockfd没有绑定到一个地址，connect会给调用者绑定一个默认地址。

服务器调用 listen 函数来宣告它愿意接受连接请求。

C
1
2
3
#include <sys/socket.h>
int listen(int sockfd, int backlog);
// 成功返回0， 错误返回-1
参数 backlog 提供了一个提示，提示系统该进程所要入队的未完成连接请求数量。其实际值由系统决定，但上限由<sys/socket.h>中的SOMAXCONN指定。

一旦队列满，系统就会拒绝多余的连接请求，所以 backlog 的值应该基于服务器期望负载和处理量来选择，其中处理量是指接受连接请求与启动服务的数量。

一旦服务器调用了 listen，所用的套接字就能接收连接请求。

使用accept函数获得连接请求并建立连接。

C
1
2
3
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *restrict addr, socklen_t *restrict len);
// 成功返回套接字描述符，失败返回-1
函数accept所返回的文件描述符是套接字描述符，该描述符连接到调用connect的客户端。这个新的套接字描述符和原始套接字（sockfd）具有相同的套接字类型和地址族。但是传给 accept 的原始套接字没有关联到这个连接，而是继续保持可用状态并接收其他连接请求。

如果不关心客户端标识，可以将参数addr（对端地址）和len设为NULL。否则，在调用accept之前，将addr参数设为足够大的缓冲区来存放地址，并且将len指向的整数设为这个缓冲区的字节大小。返回时，accept会在缓冲区填充客户端的地址，并且更新指向len的整数来反映该地址的大小。

如果没有连接请求在等待，accept 会阻塞直到一个请求到来。如果 sockfd处于非阻塞模式，accept 会返回-1，并将 errno 设置为 EAGAIN。

如果服务器调用 accept，并且当前没有连接请求，服务器会阻塞直到一个请求到来，另外，服务器可以使用 poll或 select 来等待一个请求的到来。在这种情况下，一个带有等待连接请求的套接字会以可读的方式出现。

11.6 套接字选项
套接字机制提供了两个套接字选项接口来控制套接字行为。一个接口用来设置选项，另一个接口可以查询选项的状态。

C
1
2
3
#include <sys/socket.h>
// 成功返回0，失败返回-1
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
level： 标识了选项应用的协议。
如果选项是通用的套接字层次选项，则 level 设置成SOL_SOCKET。否则，level设置成控制这个选项的协议编号
对于TCP，level是IPPROTO_TCP
对于IP，level是IPPROTO_IP。
下图总结了通用套接字层次选项。


optname： 需设置的选项
optval：根据选项的不同指向一个数据结构或者一个整数。一些选项是on/off开关。如果整数非0，则启用选项。如果整数为0，则禁止选项。
optlen：指定了optval指向的对象的大小。
可以使用 getsockopt 函数来查看选项的当前值：

C
1
2
3
#include <sys/socket.h>
// 成功返回0，失败返回-1
int getsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen);
11.7 数据传输
11.7.1 发送和接收
发送数据，类似于write：

C
1
2
3
4
5
6
7
8
9
10
#include <sys/types.h>
#include <sys/socket.h>

// 用于有连接的流套接字
ssize_t send(int sockfd, const void *buf, size_t len, int flags);

// 可以在无连接的报式套接字上指定一个目标地址
ssize_t sendto(int sockfd, const void *buf, size_t len, int flags,
               const struct sockaddr *dest_addr, socklen_t addrlen);
// 返回值是真正发送出去的数据的长度；出现错误返回 -1 并设置 errno。
注意：即使 send 成功返回，也并不表示连接的另一端的进程就一定接收了数据。我们所能保证的只是当send成功返回时，数据已经被无错误地发送到网络驱动程序上。

接收数据，类似于read：

C
1
2
3
4
5
6
7
8
9
10
#include <sys/types.h>
#include <sys/socket.h>

// 流套接字
ssize_t recv(int sockfd, void *buf, size_t len, int flags);

// 报套接字
ssize_t recvfrom(int sockfd, void *buf, size_t len, int flags,
                 struct sockaddr *src_addr, socklen_t *addrlen);
// 返回读到的字节数，若无可读数据或对等方已经结束，返回0，出错返回-1
recv 函数一般用在流式（SOCK_STREAM）套接字中，而 recvfrom 则一般用在报式（SOCK_DGRAM）套接字中。

11.7.2 报式套接字示例
① 基本实现
proto.h
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
/* proto.h */
#ifndef __PROTO_H__
#define __PROTO_H__

#include <stdint.h>

#define RCVPORT "1989" // 服务器的端口号，使用到时用atoi进行转换，原因：没有单位的数字1989没有意义，因此用字符串来代替
#define NAMESIZE 13 //为了测试数据对齐的问题，这里选择一个一定不对齐的数字

// 传输的结构体
struct msg_st {
    uint8_t name[NAMESIZE];
    uint32_t math;
    uint32_t chinese;
}__attribute__((packed)); //告诉gcc编译器,不要对齐

#endif
rcver.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
/* rcver.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>

#include "proto.h"

#define IPSTRSIZE        64

int main(void) {
    // 套接字
    int sd;
    // laddr -- local address -- 本机地址
    // raddr -- remote address -- 对端地址
    struct sockaddr_in laddr,raddr;
    // 对端地址长度
    socklen_t raddr_len;
    // 接收到的结构体
    struct msg_st rbuf;
    // 存储点分十进制字符串的数组
    char ipstr[IPSTRSIZE];
	// 创建协议为ipv4的报式套接字，0为默认协议，即UDP
    sd = socket(AF_INET, SOCK_DGRAM, 0/*IPPROTO_UDP*/);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }
	// 本机地址的配置
    laddr.sin_family = AF_INET;
    // ip地址和网络端口号是要通过网络发送过去的,所以需要考虑字节序的问题,也就是htons
    laddr.sin_port = htons(atoi(RCVPORT));
    // 因为本机的ip地址有可能会变化,为了避免ip地址每一次变化,都要进来修改,所以给它匹配一个万能地址0.0.0.0
    // 对"0.0.0.0"的定义是any address.就是说在当前绑定阶段,本机的ip地址是多少,这四个0就会自动换成当前的ip地址.
    inet_pton(AF_INET, "0.0.0.0", &laddr.sin_addr.s_addr);
	// 关联地址和套接字
    if(bind(sd, (struct sockaddr *)&laddr, sizeof(laddr)) < 0) {
        perror("bind()");
        exit(1);
    }
	// 这里一定要初始化对端地址的大小！
    raddr_len = sizeof(raddr);
    while(1) {
        if(recvfrom(sd, &rbuf, sizeof(rbuf), 0, (void *)&raddr, &raddr_len) < 0) {
            perror("recvfrom()");
            exit(1);
        }
		// 整数转点分十进制的字符串
        inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
        printf("---MESSAGE FROM %s:%d---\n", ipstr, ntohs(raddr.sin_port));
        // 单字节传输不涉及到大端小端的存储情况
        printf("Name = %s\n",rbuf.name);
        printf("Math = %d\n",ntohl(rbuf.math));
        printf("Chinese = %d\n",ntohl(rbuf.chinese));
    }
	// 关闭套接字
    close(sd);
    exit(0);
}
运行编译好的代码，然后，重启一个终端，使用命令netstat -anu来查看，其中，u代表的是udp。

BASH
1
2
3
4
5
6
7
8
9
[root@HongyiZeng socket]# netstat -anu
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
udp        0      0 0.0.0.0:1989            0.0.0.0:*                          
udp        0      0 0.0.0.0:68              0.0.0.0:*                          
udp        0      0 10.0.24.5:123           0.0.0.0:*                          
udp        0      0 127.0.0.1:123           0.0.0.0:*                          
udp6       0      0 fe80::5054:ff:fe2f::123 :::*                               
udp6       0      0 ::1:123                 :::* 
snder.c：这端可以不用向操作系统绑定端口，发送数据的时候由操作系统为我们分配可用的端口即可，当然如果想要自己绑定特定的端口也是可以的。
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
/* snder.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>

#include "proto.h"


int main(int argc,char **argv) {
    int sd;
    // 发送的结构体
    struct msg_st sbuf;
    // 对端地址
    struct sockaddr_in raddr;

    if(argc < 2) {
        fprintf(stderr,"Usage...\n");
        exit(1);
    }
    
	// 创建套接字
    sd = socket(AF_INET, SOCK_DGRAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }

//    bind();    // 主动端可省略绑定端口的步骤
	memset(&sbuf, '\0', sizeof(sbuf));
    strcpy(sbuf.name, "Alan");
    sbuf.math = htonl(rand()%100);
    sbuf.chinese = htonl(rand()%100);
	// 对端地址的配置
    raddr.sin_family = AF_INET; // 对端协议
    raddr.sin_port = htons(atoi(RCVPORT)); // 对端端口
    inet_pton(AF_INET, argv[1], &raddr.sin_addr); // 对端ip地址
	// 发送数据
    if(sendto(sd, &sbuf, sizeof(sbuf), 0, (void *)&raddr, sizeof(raddr)) < 0) {
        perror("sendto()");
        exit(1);
    }
    puts("OK!");
    close(sd);
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
[root@HongyiZeng socket]# ./snder 127.0.0.1
OK!

[root@HongyiZeng socket]# ./rcver 
---MESSAGE FROM 127.0.0.1:46573---
Name = Alan
Math = 83
Chinese = 86
② 动态报式套接字
传输的结构体中含有变长的数组。例如结构体中name数组的大小是不固定，此时可以使用变长结构体。

变长结构体
变长结构体是由gcc扩展的一种技术，它是指其最后一个成员的长度不固定（flexible array member，也叫柔性数组）。

使用范围：数据长度不固定，例如协议对接中有固定的头结构体，数据结构体不固定。

问题引出：项目中用到数据包的处理，但包的大小是不固定的，其长度由包头的2字节决定。比如如下的包头：

PLAINTEXT
1
88 0f 0a ob cd ef 23 00
长度由头2个字节880f决定，考虑字节序，转为0f88，转为10进制为3976个字节的包长度。

这个时候存储包的时候，一方面可以考虑设定包的大小固定：如4K=4*1024=4096个字节，因为最大包长不可能超过4k，但该方法的有缺陷，存在一种极端就是包最小仅含包头不含数据域，此时包为8个字节，浪费了4096-8 =4088个字节的存储空间。

另一方面考虑有没有一种方法能根据长度进行存储，或者说初始不分配长度，计算出了长度后再分配存储呢。而实际项目中正是通过包头计算出了包的整体大小的。

这就引出了变长结构体的概念。例如：

C
1
2
3
4
5
6
struct Var_Len_Struct
{
    int nsize;
    char buffer[0];
    // 或者不指定大小 char buffer[];
};
那结构体是怎么实现可变长的呢？如上所示，请注意看结构体中的最后一个元素：一个没有元素的数组（柔性数组）。我们可以通过动态开辟一个比结构体大的空间，然后让buffer去指向那些额外的空间，这样就可以实现可变长的结构体了。更为巧妙的是，我们甚至可以用nsize存储字符串buffer的长度。

代码示例：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
#include <stdint.h>

struct packet_st {
	uint32_t packetHead; // 占4个字节
	uint8_t packetData[0];
}__attribute__((packed));


int main() {
    // 结构体长度
    int size;
    // 结构体指针
    struct packet_st *bufp;
	printf("sizeof(packet_st) = %d\n", sizeof(struct packet_st));

    // 要发送的数据
    char data[] = "123456";
    // 发送的结构体长度: 结构体定长 + 要发送数据的长度
    size = sizeof(struct packet_st) + strlen(data);
    // 以长度申请动态内存
    bufp = (struct packet_st*)malloc(size);
    // 设置结构体成员
    bufp->packetHead = 1;
    strcpy(bufp->packetData , data);

    // 发送数据的操作
    // ...

    printf("packetHead: %d\n", bufp->packetHead);
    printf("packetData: %s\n", bufp->packetData);
    free(bufp);
    return 0;
}
执行结果：

BASH
1
2
3
4
[root@HongyiZeng socket]# ./val_struct 
sizeof(packet_st) = 4
packetHead: 1
packetData: 123456
可以看到，packetData在结构体中虽然定义长度为0，但仍然可以填充数据。

之前的代码修改如下：

proto.h
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
/* proto.h */
#ifndef __PROTO_H__
#define __PROTO_H__

#include <stdint.h>

#define RCVPORT "1989" 
#define NAMEMAX (512-8-8) // 512为udp包推荐的字节数，8为udp的报头大小，8为结构体中固定长度的大小，即math和chinese

// 传输的变长结构体
struct msg_st {
    uint32_t math;
    uint32_t chinese;
    uint8_t name[1]; // 1仅为占位符，数组定义一定要放在最后
}__attribute__((packed));

#endif
snder.c：发送方发送的结构体大小和内容是动态的，根据用户的命令行参数来确定
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
/* snder.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>

#include "proto.h"


int main(int argc,char **argv) {
    int sd;
    // 结构体指针
    struct msg_st *sbufp;
    // 对端地址
    struct sockaddr_in raddr;
    // 发送结构体的大小
    int size;
	// Usage: ./snder IP NAME
    if(argc < 3) {
        fprintf(stderr,"Usage...\n");
        exit(1);
    }
    
    if(strlen(argv[2]) > NAMEMAX) {
        fprintf(stderr, "Name is to long!");
        exit(1);
    }
    
    sd = socket(AF_INET, SOCK_DGRAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }
    // 结构体定长和可变名字的长度
    size = sizeof(struct msg_st) + strlen(argv[2]);
    // 申请动态内存
	sbufp = (struct msg_st *)malloc(size);
    if(sbufp == NULL) {
        perror("malloc()");
        exit(1);
    }
    strcpy(sbufp->name, argv[2]);
    sbufp->math = htonl(rand()%100);
    sbufp->chinese = htonl(rand()%100);
	// 对端地址的配置
    raddr.sin_family = AF_INET; // 对端协议
    raddr.sin_port = htons(atoi(RCVPORT)); // 对端端口
    inet_pton(AF_INET, argv[1], &raddr.sin_addr); // 对端ip地址
	// 发送数据
    if(sendto(sd, sbufp, size, 0, (void *)&raddr, sizeof(raddr)) < 0) {
        perror("sendto()");
        exit(1);
    }
    puts("OK!");
    close(sd);
    exit(0);
}
rcver.c：接收方不知道发送方发送的内容大小
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
/* rcver.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>

#include "proto.h"

#define IPSTRSIZE 64

int main(void) {
    // 套接字
    int sd;
    // laddr -- local address -- 本机地址
    // raddr -- remote address -- 对端地址
    struct sockaddr_in laddr,raddr;
    // 对端地址长度
    socklen_t raddr_len;
    // 结构体指针
    struct msg_st *rbufp;
    // 存储点分十进制字符串的数组
    char ipstr[IPSTRSIZE];
    int size;

    size = sizeof(struct msg_st) + NAMEMAX - 1; // 1是name的占位大小
    rbufp = (struct msg_st*)malloc(size);
    
    sd = socket(AF_INET, SOCK_DGRAM, 0/*IPPROTO_UDP*/);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }
    laddr.sin_family = AF_INET;
    laddr.sin_port = htons(atoi(RCVPORT));
    inet_pton(AF_INET, "0.0.0.0", &laddr.sin_addr.s_addr);
    if(bind(sd, (struct sockaddr *)&laddr, sizeof(laddr)) < 0) {
        perror("bind()");
        exit(1);
    }
    raddr_len = sizeof(raddr);
    while(1) {
        if(recvfrom(sd, rbufp, size, 0, (void *)&raddr, &raddr_len) < 0) {
            perror("recvfrom()");
            exit(1);
        }
        inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
        printf("---MESSAGE FROM %s:%d---\n", ipstr, ntohs(raddr.sin_port));
        printf("Name = %s\n",rbufp->name);
        printf("Math = %d\n",ntohl(rbufp->math));
        printf("Chinese = %d\n",ntohl(rbufp->chinese));
    }
    close(sd);
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
[root@HongyiZeng socket]# ./snder 127.0.0.1 Mark
OK!

[root@HongyiZeng socket]# ./rcver 
---MESSAGE FROM 127.0.0.1:52874---
Name = Mark
Math = 83
Chinese = 86
③ 广播
在使用TCP/IP 协议的网络中，主机标识段host ID 为全1 的 IP 地址为广播地址。

广播数据有如下特点：

TCP/IP协议栈中，传输层只有UDP可以广播，TCP没有广播的概念.
UDP广播不需要经过路由器转发，因为路由器不会转发广播数据；
代码示例

snder.c：设置套接字，打开广播选项，并向广播地址255.255.255.255发送数据报
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
/* snder.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>

#include "proto.h"


int main(int argc,char **argv) {
    // ...

    int val = 1;
    // 设置该套接字，打开广播
    if(setsockopt(sd, SOL_SOCKET, SO_BROADCAST, &val, sizeof(val)) < 0) {
        perror("setsockopt()");
        exit(1);
    }
    // ...
    inet_pton(AF_INET, "255.255.255.255", &raddr.sin_addr); // 对端ip地址
    // ...
}
打印结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
[root@HongyiZeng dgram]# ./snder Mark
OK!
[root@HongyiZeng dgram]# ./snder Mary
OK!

[root@HongyiZeng dgram]# ./rcver 
---MESSAGE FROM 10.0.24.5:52621---
Name = Mark
Math = 83
Chinese = 86
---MESSAGE FROM 10.0.24.5:42769---
Name = Mary
Math = 83
Chinese = 86
④ 多播/组播
多播地址，也叫组播地址，组播报文的目的地址使用D类IP地址， D类地址不能出现在IP报文的源IP地址字段。组播地址可以分为四类：

224.0.0.0～224.0.0.255为预留的组播地址（永久组地址），地址224.0.0.0保留不做分配，其它地址供路由协议使用；
224.0.1.0～224.0.1.255是公用组播地址，可以用于Internet；
224.0.2.0～238.255.255.255为用户可用的组播地址（临时组地址），全网范围内有效；
239.0.0.0～239.255.255.255为本地管理组播地址，仅在特定的本地范围内有效。
代码示例

多播选项在IP协议中。相关数据结构和选项可以通过man 7 ip查看。

proto.h：设置一个约定的多播地址
C
1
2
3
// ...
#define MTGOURP "224.2.2.2" // 约定的多播组ip
// ...
snder.c：创建多播组
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include "proto.h"

int main(int argc,char **argv) {
    // ...

    // 创建多播组选项IP_MULTICAST_IF需要传入的数据结构
    struct ip_mreqn mreq;

    inet_pton(AF_INET, MTGOURP, &mreq.imr_multiaddr); // 多播地址
    inet_pton(AF_INET, "0.0.0.0", &mreq.imr_address); // 自己的地址
    mreq.imr_ifindex = if_nametoindex("eth0"); // 网络设备的索引号

    // 设置该套接字（协议为IP，即IPPROTO_IP），创建多播组
    if(setsockopt(sd, IPPROTO_IP, IP_MULTICAST_IF, &mreq, sizeof(mreq)) < 0) {
        perror("setsockopt()");
        exit(1);
    }
    // ...
    inet_pton(AF_INET, MTGOURP, &raddr.sin_addr); // 对端ip地址
	// ...
}
其中，可以使用命令查看网络设备的索引号：

BASH
1
ip ad sh


可以通过下列函数来获取网络设备名的索引编号：

C
1
2
#include <net/if.h>
unsigned int if_nametoindex(const char *ifname);
rcver.v：加入多播组
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
#include "proto.h"

#define IPSTRSIZE 64

int main(void) {
    // ...

    // 加入多播组选项IP_ADD_MEMBERSHIP需要传入的数据结构
    struct ip_mreqn mreq;

    inet_pton(AF_INET, MTGOURP, &mreq.imr_multiaddr); // 多播地址
    inet_pton(AF_INET, "0.0.0.0", &mreq.imr_address); // 自己的地址
    mreq.imr_ifindex = if_nametoindex("eth0"); // 网络设备的索引号

    // 设置该套接字（协议为IP，即IPPROTO_IP），加入多播组
    if(setsockopt(sd, IPPROTO_IP, IP_ADD_MEMBERSHIP, &mreq, sizeof(mreq)) < 0) {
        perror("setsockopt()");
        exit(1);
    }
    // ...
}
执行结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
13
14
[root@HongyiZeng mcast]# ./snder Mark
OK!
[root@HongyiZeng mcast]# ./snder Mike
OK!

[root@HongyiZeng mcast]# ./rcver 
---MESSAGE FROM 10.0.24.5:51081---
Name = Mark
Math = 83
Chinese = 86
---MESSAGE FROM 10.0.24.5:53772---
Name = Mike
Math = 83
Chinese = 86
多播中有一个特殊的 ip 地址（224.0.0.1），它表示，所有支持多播的地址默认都存在这个组当中，并且无法离开。如果 snder 方向这个 ip 地址发送信息，就相当于向 255.255.255.255 上发消息。

11.7.3 流式套接字示例
主动端和被动端的工作详见11.3.4小节

① 基本实现
proto.h
C
1
2
3
4
5
#ifndef __PROTO_H__
#define __PROTO_H__
    #define SERVERPORT "1989" // 服务器端口
    #define FMT_STAMP "%lld\r\n" // 格式化参数
#endif
server.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <time.h>

#include "proto.h"

#define IPSTRSIZE 40
#define BUFSIZE 1024

static void server_job(int sd) {
    char buf[BUFSIZE];
    int len;
    // 将格式化数据写入到buf中,返回写入的字符总数
    len = sprintf(buf, FMT_STAMP, (long long)time(NULL));
    if(send(sd, buf, len, 0) < 0) {
        perror("send()");
        exit(1);
    }
}

int main(void) {
    int sd;
    int new_sd;
    struct sockaddr_in laddr, raddr;
    socklen_t raddr_len;
    char ipstr[IPSTRSIZE];
    
    // 采用流式套接字SOCK_STREAM
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }
    
    int val = 1;
    if(setsockopt(sd, SOL_SOCKET, SO_REUSEADDR, &val, sizeof(val)) < 0) {
        perror("setsockopt()");
        exit(1);
    }

    laddr.sin_family = AF_INET;
    laddr.sin_port = htons(atoi(SERVERPORT));
    inet_pton(AF_INET, "0.0.0.0", &laddr.sin_addr.s_addr);

    // 绑定地址
    if(bind(sd, (struct sockaddr *)&laddr, sizeof(laddr)) < 0) {
        perror("bind()");
        exit(1);
    }

    // 监听连接
    if(listen(sd, 200) < 0) {
        perror("listen");
        exit(1);
    }

    raddr_len = sizeof(raddr);
    while(1) {
        // 接收连接
        new_sd = accept(sd, (void *)&raddr, &raddr_len);
        if(new_sd < 0) {
            perror("accept()");
            exit(1);
        }
        inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
        printf("Client:%s:%d\n", ipstr, ntohs(raddr.sin_port));
        server_job(new_sd);
        close(new_sd);
    }
    close(sd);
    exit(0);
}
使用命令，查看是否成功，t代表tcp

BASH
1
netstat -ant


发现已经处于监听状态。

可以使用nc命令来与服务器端建立连接：

BASH
1
nc 127.0.0.1 1989
执行结果：

BASH
1
2
3
4
5
[root@HongyiZeng basic]# nc 127.0.0.1 1989
1678513397

[root@HongyiZeng basic]# ./server 
Client:127.0.0.1:36270
现在实现客户端的功能，其实也就是nc命令的功能。这里采用将系统io转换为标准io的指针来实现，当然，也可以采用recv来实现。

client.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
/* client.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>

#include "proto.h"

int main(int argc, char*argv[]) {
    int sd;
    // 对端地址
    struct sockaddr_in raddr;
    FILE *fp;
    long long stamp;
    
	// Usage: ./client IP
    if(argc < 2) {
        fprintf(stderr,"Usage...\n");
        exit(1);
    }
    
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }

    // 对端地址的配置
    raddr.sin_family = AF_INET; // 对端协议
    raddr.sin_port = htons(atoi(SERVERPORT)); // 对端端口
    inet_pton(AF_INET, argv[1], &raddr.sin_addr); // 对端ip地址

    // 与对端建立连接
    if(connect(sd, (void *)&raddr, sizeof(raddr)) < 0) {
        perror("connect()");
        exit(1);
    }
	// 系统io转换为标准io
    // r+表示打开可读写的文件，且该文件必须存在
    fp = fdopen(sd, "r+");

    if(fp == NULL) {
        perror("dfopen()");
        exit(1);
    }
	
    // 根据数据格式FMT_STAMP从fp中读取数据到stamp中
    if(fscanf(fp, FMT_STAMP, &stamp) < 1) {
        fprintf(stderr, "Bad format!\n");
    } else {
        fprintf(stdout, "stamp = %lld\n", stamp);
    }
	// 按照标准io的方式关闭fp
    fclose(fp);
    exit(0);
}
fdopen的使用详见3.5节。

执行结果：

BASH
1
2
3
4
5
[root@HongyiZeng basic]# ./client 127.0.0.1
stamp = 1678514756

[root@HongyiZeng basic]# ./server 
Client:127.0.0.1:37820
② 并发实现
服务器端fork出子进程来执行任务。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <time.h>

#include "proto.h"

#define IPSTRSIZE 40
#define BUFSIZE 1024

static void server_job(int sd) {
    // 假设很耗时
    // ...
}

int main(void) {
    // ...
    pid_t pid;
    // ...
    while(1) {
        // 父进程只接收连接
        new_sd = accept(sd, (void *)&raddr, &raddr_len);
        if(new_sd < 0) {
            perror("accept()");
            exit(1);
        }
        pid = fork(); // 创建子进程
        if(pid) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) { // 子进程
            close(sd); // 关闭不需要的主套接字（监听套接字）
            inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
            printf("Client:%s:%d\n", ipstr, ntohs(raddr.sin_port));
            server_job(new_sd); // 干活
            close(new_sd);
            exit(0); // 干完活后退出
        }
        close(new_sd); // 关闭不需要的副套接字
    }
    close(sd);
    exit(0);
}
这里需要关闭不需要的套接字，详见6.2.1节的父子进程之间的文件共享。

原因在于 fork 执行之后，所有已经打开的套接字都被增加了引用计数，在其中任一个进程中都无法彻底关闭套接字，只能减少该文件的引用计数。因此，在 fork 之后，每个进程立即关闭不再需要的文件是个好的策略，否则很容易导致大量没有正确关闭的文件一直占用系统资源的现象。

③ 请求http服务实现
客户端功能：使用http的GET请求，下载服务器端上的一张图片。

服务器端准备
使用nginx作为web服务器，根目录一般位于usr/local/nginx。

将静态文件，例如test.jpg放置于根目录下的img文件夹（自己新建）中。

编写usr/local/nginx/conf/nginx.conf配置文件，在server块中新增映射关系，以响应请求静态文件的请求：

NGINX
1
2
3
location ~* \.(gif|jpg|jpeg|png)$  { # url为gif|jpg|jpeg|png结尾时
    root img; # 以root方式设置资源路径
}
例如，访问http://xxx.xxx.xxx.xxx:80/test.jpg时，匹配到末尾的jpg，nginx将其替换为/img/test.jpg。

client.c
C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
/* client.c */
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>

// #include "proto.h"
#define BUFSIZE 1024

int main(int argc, char*argv[]) {
    int sd;
    // 对端地址
    struct sockaddr_in raddr;
    FILE *fp;
    char rbuf[BUFSIZE];
    int len;
    
	// Usage: ./client IP
    if(argc < 2) {
        fprintf(stderr,"Usage...\n");
        exit(1);
    }
    
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }

    // 对端地址的配置
    raddr.sin_family = AF_INET; // 对端协议
    raddr.sin_port = htons(80); // 对端端口，http一般为80端口
    inet_pton(AF_INET, argv[1], &raddr.sin_addr); // 对端ip地址

    // 与对端建立连接
    if(connect(sd, (void *)&raddr, sizeof(raddr)) < 0) {
        perror("connect()");
        exit(1);
    }

    fp = fdopen(sd, "r+");

    if(fp == NULL) {
        perror("dfopen()");
        exit(1);
    }
    // 发送http请求
    fprintf(fp, "GET /test.jpg\r\n\r\n");
    // 发送后刷新流
    fflush(fp);

    while(1) {
        len = fread(rbuf, 1, BUFSIZE, fp);
        if(len == 0) {
            break;
        }
        // 打印在标准终端
        fwrite(rbuf, 1, len, stdout);
    }

    fclose(fp);
    exit(0);
}
执行结果：

BASH
1
[root@HongyiZeng basic]# ./webdl XXX.XXX.XXX.XXX > /tmp/out


④ 静态进程池套接字实现
静态进程池：父进程先fork出4个子进程，再由子进程来接收连接，处理任务。

代码实现：

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <time.h>

#include "proto.h"

#define IPSTRSIZE 40
#define BUFSIZE 1024
#define PROCNUM 4 // 静态进程池中进程的个数

static void server_job(int sd) {
    char buf[BUFSIZE];
    int len;
    // 将格式化数据写入到buf中,返回写入的字符总数
    len = sprintf(buf, FMT_STAMP, (long long)time(NULL));
    if(send(sd, buf, len, 0) < 0) { 
        perror("send()");
        exit(1);
    }
}

static void server_loop(int sd) {
    struct sockaddr_in raddr;
    socklen_t raddr_len;
    int new_sd;
    char ipstr[IPSTRSIZE];

    raddr_len = sizeof(raddr);
    // 不断接收连接，处理任务
    while(1) {
        // 接收连接
        new_sd = accept(sd, (void *)&raddr, &raddr_len);
        if(new_sd < 0) {
            perror("accept()");
            exit(1);
        }
        inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
        printf("[%d]Client:%s:%d\n", getpid(), ipstr, ntohs(raddr.sin_port));
        // 处理任务
        server_job(new_sd);
        close(new_sd);
    }
    // nerver reach
    close(sd);
}

int main(void) {
    int sd;
    struct sockaddr_in laddr;
    pid_t pid;
    
    // 采用流式套接字SOCK_STREAM
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }

    int val = 1;
    if(setsockopt(sd, SOL_SOCKET, SO_REUSEADDR, &val, sizeof(val)) < 0) {
        perror("setsockopt()");
        exit(1);
    }

    laddr.sin_family = AF_INET;
    laddr.sin_port = htons(atoi(SERVERPORT));
    inet_pton(AF_INET, "0.0.0.0", &laddr.sin_addr.s_addr);

    // 绑定地址
    if(bind(sd, (struct sockaddr *)&laddr, sizeof(laddr)) < 0) {
        perror("bind()");
        exit(1);
    }

    // 监听连接
    if(listen(sd, 200) < 0) {
        perror("listen");
        exit(1);
    }
    int i;
    for(i = 0;i < PROCNUM; i++) {
        pid = fork();
        if(pid < 0) {
            perror("fork()");
            exit(1);
        }
        if(pid == 0) { // 子进程
            server_loop(sd);
            exit(0);
        }
    }

    for(i = 0;i < PROCNUM; i++) {
        wait(NULL);
    }
    
    close(sd);
    exit(0);
}
执行结果：

BASH
1
2
3
4
5
6
7
8
9
10
11
12
[root@HongyiZeng pool_static]# ./server 
[2828]Client:127.0.0.1:59268
[2829]Client:127.0.0.1:59270
[2830]Client:127.0.0.1:59272
[2831]Client:127.0.0.1:59276
[2828]Client:127.0.0.1:59278
[2829]Client:127.0.0.1:59280
[2830]Client:127.0.0.1:59282
[2831]Client:127.0.0.1:59284
[2828]Client:127.0.0.1:59286
[2829]Client:127.0.0.1:59290
[2830]Client:127.0.0.1:59292
⑤ 动态进程池套接字实现
描述：进程池主进程最大可fork出20个进程来接收连接和处理任务，当一些进程完成任务后，阻塞等待连接，成为空闲进程，此时主进程需要控制空闲进程（一直阻塞等待连接）的数量，最大可空闲10个，否则多余的空闲进程由主进程杀死，减少服务器端的资源消耗，最小可空闲5个，小于5个时由主线程fork出，这一目的是提高服务器的并发性，在有多个客户端突发连接时能够得到有效处理。

主进程任务：初始化进程池，并控制进程池中进程的数量。

子进程任务：不断接收客户端连接，处理任务。

C
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
233
234
235
236
237
238
239
240
241
242
243
244
245
246
247
248
249
250
251
252
253
254
255
256
257
258
259
260
261
262
263
264
265
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <sys/socket.h>
#include <time.h>
#include <errno.h>
#include <sys/mman.h>
#include <signal.h>
#include <unistd.h>

#include "proto.h"

#define MINSPARESERVER 5 // 最小可空闲进程
#define MAXSPARESERVER 10 // 最大可空闲进程
#define MAXCLIENT 20 // 进程资源总量
#define SIG_NOTIFY SIGUSR2 // 自定义的信号
#define IPSTRSIZE 40
#define LINEBUFSIZE 1024

// 进程状态的枚举
enum {
    STATE_IDLE = 0, // 进程空闲
    STATE_BUSY // 进程忙碌
};

// 包含进程信息的结构体
struct server_st {
    pid_t pid; // 进程pid
    int state; // 进程状态
    // int reuse;
};

static struct server_st *serverpool; // server_st数组的首地址
static int idle_count = 0, busy_count = 0; // 计数器
static int sd; // 监听套接字

// 信号SIG_NOTIFY的处理函数
static void usr2_hanlder(int s) {
    return ;
}

// 子进程的任务：等待连接，处理任务
static void server_job(int pos) {
    struct sockaddr_in raddr;
    socklen_t raddr_len;
    char ipstr[IPSTRSIZE];
    time_t stamp;
    int len;
    char linebuf[LINEBUFSIZE];
    // 父进程pid
    int ppid = getppid();
    int client_sd; // 副套接字
    while(1) {
        // 没有连接前，状态为空闲
        serverpool[pos].state = STATE_IDLE;
        // 状态改变时，通知主进程：作用是打断主进程中的阻塞
        kill(ppid, SIG_NOTIFY);
        // 阻塞等待连接
        client_sd = accept(sd, (void *)&raddr, &raddr_len);
        if(client_sd < 0) {
            if(errno != EINTR || errno != EAGAIN) {
                perror("accept()");
                exit(1);
            }
        }
        // 完成连接，设置状态为忙碌
        serverpool[pos].state = STATE_BUSY;
        // 状态改变时，通知主进程
        kill(ppid, SIG_NOTIFY);
        inet_ntop(AF_INET, &raddr.sin_addr, ipstr, IPSTRSIZE);
        // printf("[%d]Client:%s:%d\n", getpid(), ipstr, ntohs(raddr.sin_port));
        stamp = time(NULL);
        len = snprintf(linebuf, LINEBUFSIZE, FMT_STAMP, stamp);
        send(client_sd, linebuf, len , 0);
        sleep(5);
        close(client_sd);
    }
}

// 创建一个子进程
static int add_1_server(void) {
    int slot;
    pid_t pid;
    if(idle_count + busy_count >= MAXCLIENT) { // 空闲和忙碌的进程数大于进程资源总量，则不添加
        return -1;
    }
    // 遍历serverpool数组，查找到可用的位置slot
    for(slot = 0; slot < MAXCLIENT; slot++) { 
        if(serverpool[slot].pid == -1) {  
            break;
        }
    }
    serverpool[slot].state = STATE_IDLE; // 新增进程的状态设置为空闲
    pid = fork(); // 创建子进程
    if(pid < 0) {
        perror("fork()");
        exit(1);
    }
    if(pid == 0) { // 子进程
        server_job(slot); // 干活
        exit(1);
    } else { // 父进程
        serverpool[slot].pid = pid; // 父进程负责设置子进程的pid
        idle_count ++; // 增加一个空闲资源
    }
    return 0;
}

static int del_1_server(void) {
    int i;
    pid_t pid;
    if(idle_count == 0) { // 空闲进程数为0，则不删除
        return -1;
    }
    // 遍历serverpool数组，查找到可释放的空闲进程
    for(i = 0; i < MAXCLIENT; i++) { 
        // 找到一个已分配的空闲进程
        if(serverpool[i].pid != -1 && serverpool[i].state == STATE_IDLE) { 
            // 主线程发送终止信号
            kill(serverpool[i].pid, SIGTERM);
            serverpool[i].pid = -1;
            idle_count--;
            break;
        }
    }
    return 0;
}

// 遍历进程池
static int scan_pool(void) {
    int i;
    int busy = 0;
    int idle = 0;
    for(i = 0; i < MAXCLIENT; i++) {
        if(serverpool[i].pid == -1) { // 进程不存在
            continue;
        }
        // kill(pid, 0)用于检测pid是否存在，存在返回0，不存在返回-1
        if(kill(serverpool[i].pid, 0) == -1) {
            serverpool[i].pid = -1;
            continue;
        }
        if(serverpool[i].state == STATE_IDLE) {
            idle++;
        } else if(serverpool[i].state == STATE_BUSY) {
            busy++;
        } else {
            fprintf(stderr, "Unknown state.\n");
            abort();
        }
    }
    idle_count = idle;
    busy_count = busy;
    return 0;
}

// 主进程
int main(void) {
    struct sigaction sa, osa;
    struct sockaddr_in laddr;
    sigset_t set, oset;
    int val = 1;
    int i;
    pid_t pid;
	
    // 避免子进程成为僵尸进程，详见6.2.1节
    sa.sa_handler = SIG_DFL;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = SA_NOCLDWAIT;
    sigaction(SIGCHLD, &sa, &osa);
	
    // 自定义信号SIG_NOTIFY的处理
    sa.sa_handler = usr2_hanlder;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIG_NOTIFY, &sa, &osa);
	
    // 对SIG_NOTIFY信号进行屏蔽
    sigemptyset(&set);
    sigaddset(&set, SIG_NOTIFY);
    sigprocmask(SIG_BLOCK, &set, &oset);

    // 为serverpool申请20个空间
    serverpool = mmap(NULL, sizeof(struct server_st) * MAXCLIENT, 
        PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);
    if(serverpool == MAP_FAILED) {
        perror("mmap()");
        exit(1);
    }
	
    // 赋初值，表示当前没有产生(fork)进程
    for(i = 0; i < MAXCLIENT; i++) {
        serverpool[i].pid = -1;
    }
	
    // 创建套接字
    sd = socket(AF_INET, SOCK_STREAM, 0);
    if(sd < 0) {
        perror("socket()");
        exit(1);
    }

    // 设置套接字选项
    if(setsockopt(sd, SOL_SOCKET, SO_REUSEADDR, &val, sizeof(val)) < 0) {
        perror("setsockopt()");
        exit(1);
    }

    // 本机地址配置
    laddr.sin_family = AF_INET;
    laddr.sin_port = htons(atoi(SERVERPORT));
    inet_pton(AF_INET, "0.0.0.0", &laddr.sin_addr.s_addr);

    // 绑定地址
    if(bind(sd, (struct sockaddr *)&laddr, sizeof(laddr)) < 0) {
        perror("bind()");
        exit(1);
    }

    // 监听连接
    if(listen(sd, 200) < 0) {
        perror("listen");
        exit(1);
    }
	
    // 初始化进程池，使其空闲进程数等于MINSPARESERVER
    for(i = 0 ;i < MINSPARESERVER; i++) {
        add_1_server();
    }

    while(1) {
        // 信号驱动：当子进程的状态发生变化时，向父进程发送信号，父进程相应作出一些动作
        // 解除对SIG_NOTIFY的屏蔽，并且阻塞在这里，直到子进程调用kill，发送给主进程一个信号来打断此阻塞，使得主进程能够向下执行
        sigsuspend(&oset);
        // 扫描进程池
        scan_pool();
        // 控制进程池：多退少补
        if(idle_count > MAXSPARESERVER) {
            for(i = 0; i < (idle_count - MAXSPARESERVER); i++) {
                del_1_server();
            }
        } else if(idle_count < MINSPARESERVER) {
            for(i = 0; i < (MINSPARESERVER - idle_count); i++) {
                add_1_server();
            }
        }
        // 输出进程池的状态
        for(i = 0; i < MAXCLIENT; i++) {
            if(serverpool[i].pid == -1) { // 尚未分配的进程
                putchar('x');
            } else if(serverpool[i].state == STATE_IDLE) { // 空闲进程
                putchar('.');
            } else { // 忙碌进程
                putchar('$');
            }
        }
        putchar('\n');
    }
	// 恢复信号屏蔽字
    sigprocmask(SIG_SETMASK, &oset, NULL);
    close(sd);
    exit(0);
}
代码测试：

启动服务器端：
BASH
1
2
3
4
5
6
[root@HongyiZeng pool_dynamic]# ./server 
.....xxxxxxxxxxxxxxx
.....xxxxxxxxxxxxxxx
.....xxxxxxxxxxxxxxx
.....xxxxxxxxxxxxxxx
.....xxxxxxxxxxxxxxx
这里出现5行的原因为初始化5个子进程。可以看到前五个为启用的进程，且是空闲的，后15个尚未启用。

服务器运行到：fork出子进程

C
1
2
3
for(i = 0 ;i < MINSPARESERVER; i++) {
    add_1_server();
}
子进程运行到：

C
1
2
3
4
5
6
while(1) {
    serverpool[pos].state = STATE_IDLE;
    kill(ppid, SIG_NOTIFY); // 设置主进程的SIG_NOTIFY的pending位为1
    client_sd = accept(sd, (void *)&raddr, &raddr_len); // 阻塞等待连接
    // ...
}
由于主进程对SIG_NOTIFY进行了屏蔽（mask=1），因此不会响应，但是pending位为1。

主进程创建完个子进程后，进入循环：

C
1
2
3
4
5
while(1) { 
    sigsuspend(&oset); // 解除对SIG_NOTIFY的屏蔽，同时阻塞
    scan_pool();
    // ...
}
主进程阻塞在sigsuspend，然后依次响应之前的信号（存疑）。

发送一个请求，服务器端打印的结果：
BASH
1
2
3
..$...xxxxxxxxxxxxxx
..$...xxxxxxxxxxxxxx
......xxxxxxxxxxxxxx
某个子进程接收到连接，改变状态为忙碌，同时发送信号给父进程。父进程响应，扫描进程池后发现空闲进程数量为（5-1=4<5），因此再次fork一个子进程（注意fork出的子进程阻塞在accept之前也要向父进程发送信号）。父进程打印第一二行，分别是对原子进程和新子进程的信号响应；5s后，原子进程完成任务，将状态修改为空闲，并向父进程发送信号，父进程再打印出第三行。此时空闲进程有6个了。

连续发送请求：
BASH
1
while true; do (./client 127.0.0.1 &); sleep 1; done # 每1s发送一个请求，每个请求的处理时间为5s
文章作者: Kisugi Takumi
文章链接: https://kisugitakumi.net
版权声明: 此文章版权归Kisugi Takumi所有，如有转载，请注明来自原作者
LinuxLinux系统编程

上一篇
Leetcode刷题笔记（简单）

下一篇
51单片机基础
相关推荐

 2023-04-08
Shell脚本编程学习笔记

 2023-03-22
Unix网络编程学习笔记

 2023-04-30
Linux内核学习笔记

 2023-04-12
轻量级Web服务器学习笔记
目录1
Linux系统编程学习笔记
1 前言
1.1 环境
1.2 GCC
1.3 make
1.4 makefile
