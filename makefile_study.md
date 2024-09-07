
make

target : dependeds

Tab command

Tab command
# 规则
## 1、make
执行makefile的第一个目标

## 2、特殊符号

\$<：表示dependeds的结果

\$@：表示target的名称

## 3、用户自定义变量

var=

在command中使用变量的方式：$(var)

## 4、makefile预定义变量

AR：生成静态库文件的程序名称，默认ar；ARFLAGS

AS：汇编编译器的名称，默认as；ASFLAGS

CC：C编译器名称，默认cc；CFLAGS

CPP：C预编译器名称，默认$(CC)-E；CPPFLAGS

CXX：C++编译器名称，默认g++；CXXFLAGS

FC：FORTRAN语言编译器名称，f77；FFLAGS

RM：删除文件程序的名称, 默认rm -f 
