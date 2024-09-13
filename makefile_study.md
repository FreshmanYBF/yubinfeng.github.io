
make

target : dependeds

Tab command

Tab command
# 规则
## 1、make
执行makefile的第一个目标；make第一个目标的实现需要它的依赖项；当依赖项全都具备的时候，开始执行command；当依赖项不具备的时候，会生成对应的依赖项；如果依赖项已经具备，则不会执行后续生成依赖项中的命令

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

## 5、搜索路径

VPATH：指定需要搜索的目录，例如VPATH=path1:path2:...，make会从VPATH中去搜索目标和依赖项

## 6、自动推导规则

make进行编译时，会使用一个默认规则：按照默认规则完成对.c文件的编译，生成对应的.o文件

## 7、递归make


调用子目录的makefile

subobj:

	cd subdir && $(MAKE)
 
subobj:

	$(MAKE) -C subdir
	
目标只有一个，如果一个变量中包含多个目标，则只会生成第一个目标，所以最终目标需要依赖所有依赖项


## 8、makefile中的函数

wildcard：查找当前目录下所有符合PATTERN的文件名，返回值是以空格分割的、当前目录下的所有符合模式PATTERN的文件名列表。$(wildcard PATTERN)：$(wildcard *.c)

patsubst：查找字符串text中按照空格分开的单词，将符合模式pattern的字符串替换成replacement，返回值是替换后新字符串。$(patsubst pattern,replacement,text)：$(patsubst %.c,%.o,add.c)

foreach：$(foreach VAR,LIST,TEXT)，将LIST字符串中一个空格分隔的单词，先传给临时变量VAR，然后执行TEXT表达式，TEXT表达式处理结束后输出。返回值是空格分割表达式TEXT的计算结果
