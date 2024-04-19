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
