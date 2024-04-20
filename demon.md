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
一个工程中的源文件不计其数，其按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则来指定哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作。

![alt text](image.png)
#### 1.4.1 使用gcc的例子

- 创建文件
```BASH
touch main.c tool1.c tool1.h tool2.c tool2.h
```
可以使用vim * -p打开当前目录下的所有文件。-p是打开多个文件的选项。

在命令模式下，gt切换到下一个标签页，gT切换到上一个标签页。

退出多个标签时，可加上a，例如:qa或者:wqa等

- tool1.h和tool1.c

```C
#ifndef __TOOL1_H__
#define __TOOL1_H__
    void mytool1();
#endif
```
```c
#include <stdio.h>
#include "tool1.h" 
void mytool1() {
	printf("tool1 print...\n");
}
```
- tool2.h和tool2.c
```c
#ifndef __TOOL2_H__
#define __TOOL2_H__
    void mytool2();
#endif
```

```c
#include <stdio.h>
#include "tool2.h"

void mytool2() {
    printf("tool2 print...\n");
}
```
- main.c

```c
#include "tool1.h"
#include "tool2.h"

int main(void) {
    mytool1();
    mytool2();
    return 0;
}
```

对所有文件进行编译：
```BASH
gcc *.c
```
执行a.out

```BASH
[root@HongyiZeng makefile]# ./a.out 
tool1 print...
tool2 print...
```
#### 1.4.2 语法规则

```PLAINTEXT
目标 ... : 依赖 ...
	命令1
	命令2
	. . .
```

- 目标即要生成的文件。如果目标文件的更新时间晚于依赖文件更新时间，则说明依赖文件没有改动，目标文件不需要重新编译。否则会进行重新编译并更新目标文件。默认情况下Makefile的第一个目标为终极目标。
- 依赖：即目标文件由哪些文件生成。
- 命令：即通过执行命令由依赖文件生成目标文件。注意每条命令之前必须有一个tab保持缩进。

>       代码示例

-新建makefile，touch makefile
```MAKEFILE
mytool:main.o tool1.o tool2.o
    gcc main.o tool1.o tool2.o -o mytool
main.o:main.c
    gcc -c main.c -o main.o
tool1.o:tool1.c
    gcc -c tool1.c -o tool1.o
tool2.o:tool2.c
    gcc -c tool2.c -o tool2.o
```    
- 执行make即可。

---

makefile会监视哪些源文件发生了变化，一旦发生变化，则会在有这个变化文件的编译链上重新编译。

- 假设没有变化
```BASH
[root@HongyiZeng makefile]# make
make: `mytool' is up to date.
```
- 将tool1.c修改一点，再次make
```BASH
[root@HongyiZeng makefile]# make
gcc -c tool1.c -o tool1.o
gcc main.o tool1.o tool2.o -o mytool
[root@HongyiZeng makefile]# ./mytool 
tool1.c is updated...
tool2 print...
```
发现只有tool1.c影响到的地方被重新编译了，没有发生变化的地方则不会被重新编译。

---

代码清理clean：我们可以编译一条属于自己的clean语句，来清理make命令所产生的所有文件，例如：

---
- 变量：$符号表示取变量的值，当变量名多于一个字符时，使用()
-变量赋值：
    * =：最普通的等号，在Makefile中容易搞错赋值等号，使用 =进行赋值，变量的值是整个Makefile中最后被指定的值。
```MAKEFILE
    VIR_A = A
VIR_B = $(VIR_A) B
VIR_A = AA
```
经过上面的赋值后，最后VIR_B的值是AA B，而不是A B，在make时，会把整个Makefile展开，来决定变量的值
    *:=：表示直接赋值，赋予当前位置的值。
