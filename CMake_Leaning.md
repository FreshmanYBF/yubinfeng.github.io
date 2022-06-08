
cmake优点

（1）开源
（2）跨平台。Linux/Unix-->makefile；Apple-->xcode；Windows-->MSVC
（3）管理大型项目
（4）高效可扩展


1 构建方式

内部构建——工程和主CMakeLists.txt在同一目录，并在工程目录执行cmake
外部构建——源文件目录下编写CMakeLists.txt；build目录下运行cmake <CMakeLists.txt所在目录>

最好采用外部构建






2 外部构建

app
    bin
        xx.out
    build
    doc
        README
    src
        xxx.c
        CMakeLists.txt
    CMakeLists.txt
    install.sh
    
（1）在app下编写CMakeLists.txt

CMakeLists.txt

PROJECT(APP)               # PROJECT指令隐式生成两个变量PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR
ADD_SUBDIRECTORY(src bin)  # src，即app/src，该参数指定了源文件的位置
                           # bin，指定cmake和make执行过程中，二进制文件存放的位置
                           # 单纯的bin目录会在执行cmake的目录下创建（这里在build目录下执行cmake ..，处理app/CMakeLists.txt中的内容）

EXECUTABLE_OUTPUT_PATH和LIBRARY_OUTPUT_PATH变量用来指定最终目标二进制的位置

通过SET指令可以设置这两个变量的值，以此来指定它们的位置

（2）编写src下的CMakeLists.txt

CMakeLists.txt

# AUX_SOURCE_DIRECTORY(. SRC_LIST)  该指令可以搜索当前目录下所有源代码文件，并存储到SRC_LIST中
SET(SRC_LIST xxx1.c xxx2.c)      # SET可以显式定义一个变量
ADD_EXECUTABLE(DES ${SRC_LIST})  # 该指令生成一个可执行文件

（3）INSTALL指令——安装

安装就是安装生成的头文件，目标文件，doc等

安装指令在src所在目录的CMakeLists.txt中编写












3 静态库和动态库


app
    bin
    build
    doc
    lib
        CMakeLists.txt
    src
        CMakeLists.txt
    CMakeLists.txt
    install.sh


生成可执行目标文件使用ADD_EXECUTABLE

生成库文件使用ADD_LIBRARY(libname [SHARED | STATIC | MODULE] [EXCLUDE_FROM_ALL] src1 src2 ... srcN)


3.1 SET_TARGET_PROPERTIES

SET_TARGET_PROPERTIES(target1 target2 ...
            PROPERTIES prop1 value1
                       prop2 value2 ...)
设置目标属性，可以用来设置库文件等目标文件属性

3.2 GET_TARGET_PROPERTY

GET_TARGET_PROPERTY(VAR target property)

获取目标属性


3.3 动态库版本号

SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)

VERSION指代动态库版本，SOVERSION指代API版本

3.4 安装库文件

（1）安装.so文件
INSTAL(TARGETS hello hello_static
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)  # 注意，静态库要使用ARCHIVE


（2）安装.h文件

INSTALL(FILES hello.h DESTINATION include/hello)


3.4 使用库文件

3.4.1 包含头文件

INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)


3.4.2 包含库文件

LINK_DIRECTORIES的全部语法是：

    LINK_DIRECOTRISE(directory1 directory2 ...)

这个指令用于添加非标准的共享库搜索路径。

3.4.3 链接目标文件

TARGET_LINK_LIBRARIES的全部语法是

    TARGET_LINK_LIBRARIES(target library1 
                        <debug | optimized> library2
                        ...)











4 安装指令

安装的需要有两种，一种是从代码编译后直接make install安装，一种是打包时的指定目录安装

需要引入cmake指令INSTALL和一个变量CMAKE_INSTALL_PREFIX

INSTALL指令用于定义安装规则，安装的内容可以包括目标二进制、动态库、静态库以及文件、目录、脚本等

4.1 INSTALL指令

INSTALL指令包含了各种安装类型



目标文件的安装：

    INSTALL(TARGETS targets...
        [[ARCHIVE | LIBRARY | RUNTIME]
                    [DESTINATION <dir>]
                    [PERMISSIONS permissions...]
                    [CONFIGURATIONS
        [Debug|Release|...]]
                    [COMPONENT <component>]
                    [OPTIONAL]
                   ] [...])

（1）参数中的TARGETS后面跟的就是我们通过ADD_EXECUTABLE或者ADD_LIBRARY定义的目标文件，可能是可执行二进制、动态库、静态库

（2）目标类型也相对应三种，ARCHIVE指静态库，LIBRARY指动态库；RUNTIME指可执行目标二进制

（3）DESTINATION定义了安装的路径，如果路径以/开头，那么指的是绝对路径，这时CMAKE_INSTALL_PREFIX就无效了
如果你希望使用CMAKE_INSTALL_PREFIX来定义安装路径，就要写成相对路径
${CMAKE_INSTALL_PREFIX}/<DESTINATION定义的路径>

（4）例子

INSTALL(TARGETS myrun mylib mystaticlib
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION libstatic
    )



普通文件的安装

    INSTALL(FILES files...DESTINATION <dir>
        [PERMISSIONS permissions...]
        [CONFIGURATIONS [Debug | Release | ...]]
        [COMPONENT <component>]
        [RENAME <name>] [OPTIONAL])

可以用于安装一般文件，并可以指定访问权限，文件名是此指令所在路径下的相对路径。

如果不定义权限PERMISSIONS，安装后的权限为：
OWNER_WRITE，OWNER_READ，GROUP_READ，和WORLD_READ，即644权限



非目标文件的可执行程序安装（比如脚本之类）
    
    INSTALL(PROGRAMS files... DESTINATION <dir>
        [PERMISSIONS permissions...]
        [CONFIGURATIONS [Debug | Release | ...]]
        [COMPONENT <component>]
        [RENAME <name>] [OPTIONAL])



目录安装

    INSTALL(DIRECTORY dirs... DESTINATION <dir>
        [FILE_PERMISSIONS permissions...]
        [DIRECTORY_PERMISSIONS permissions...]
        [USE_SOURCE_PERMISSIONS]
        [CONFIGURATIONS [Debug | Release | ...]]
        [COMPONENT <component>]
        [[PATTERN <pattern> | REGEX <regex>]
          [EXCLUDE] [PERMISSIONS permission...]] [...])




5 cmake指令

5.1 常用

PROJECT，ADD_EXECUTABLE，INSTALL，ADD_SUBDIRECTORY，SET

INCLUDE_DIRECTORIES，LINK_DIRECTORIES，TARGET_LINK_LIBRARIES



5.2 其他


5.2.1 ADD_DEFINITIONS

向C/C++编译器添加-D定义


5.2.2 ADD_DEPENDENCIES

定义target依赖的其他target，确保在编译本target之前，其他的target已经被构建

ADD_DEPENDENCIES(target-name depend-target1 depend-target2 ...)


5.2.3 ADD_EXECUTABLE

定义生成的可执行文件


5.2.4 ADD_LIBRARY

定义生成的库文件


5.2.5 ADD_SUBDIRECTORY

定义源文件位置，和二进制文件存放位置


5.2.6 ADD_TEST与ENABLE_TESTING指令

（1）ENABLE_TESTING指令用来控制Makefile是否构建test目标，涉及工程所有目录。

一般情况这个指令放在工程的主CMakeLists.txt中，使用ENABLE_TESTING()

（2）ADD_TEST(testname Exename arg1 arg2 ...)

testname是自定义的test名称，Exename可以是构建的目标文件也可以是外部脚本等等

后面连接传递给可执行文件的参数。

如果没有在同一个CMakeLists.txt中打开ENABLE_TESTING()指令，任何ADD_TEST都是无效的

（3）示例

比如在之前的工程主CMakeLists.txt中添加
   
    ADD_TEST(mytest ${PROJECT_BINARY_DIR}/bin/main)
    ENABLE_TESTING()

生成Makefile后，就可以运行make test来执行测试了


5.2.6 AUX_SOURCE_DIRECTORY

AUX_SOURCE_DIRECTORY(dir VARIABLE)

作用是发现一个目录下所有源代码文件并将列表存储在一个变量中，这个指令临时被用来自动构建源文件列表

因为目前cmake还不能自动发现新添加的源文件


5.2.7 INCLUDE

用来载入CMakeLists.txt文件，也用于载入预定的cmake模块

    INCLUDE(file1 [OPTIONAL])
    INCLUDE(module [OPTIONAL])

OPTIONAL参数的作用是文件不存在也不会产生错误

你可以指定载入一个文件，如果定义的是一个模块，那么将在CMAKE_MODULE_PATH中搜索这个模块并载入

可以编写cmake文件来定义不同的源文件使用不同的标准来编译




5.3 控制指令

5.3.1 IF指令

    IF(expression)
         # THEN section.
         COMMAND1(ARGS ...)
         COMMAND2(ARGS ...)
         ...
    ELSE(expression)
         #ELSE section
         COMMAND1(ARGS ...)
         COMMAND2(ARGS ...)
         ...
     ENDIF(expression)

出现ELSEIF的地方，ENDIF是可选的；出现IF的地方一定要对应的ENDIF


5.3.1.1 expression的使用方法

IF(NOT VAR)

IF(var1 AND var2)

IF(var1 OR var2)

IF(COMMAND cmd)，当给定的cmd确实是命令并可以调用时为真

IF(EXISTS dir)或IF(EXISTS file)，当目录名或文件名存在时为真

IF(file1 IS_NEWER_THAN file2)，当file1比file2新，或者file1/file2其中有一个不能存在时为真（文件名使用完整路径）

IF(IS_DIRECTORY dirname)，当dirname是目录时，为真

IF(variable MATCHES regex)
IF(string MATCHES regex)
当给定的变量或字符串能够匹配正则表达式regex时为真
例如：
    IF("hello" MATCHES "ell")
         MESSAGE("true")
    ENDIF("hello" MATCHES "ell")

IF(variable LESS number)
IF(string LESS number)
IF(variable GREATER number)
IF(string GREATER number)
IF(variable EQUAL number)
IF(string EQUAL number)
数字比较表达式

IF(variable STRLESS string)
IF(string STRLESS string)
IF(variable STRGREATER string)
IF(string STRGREATER string)
IF(variable STREQUAL string)
IF(string STREQUAL string)
按照字母序的排列进行比较

IF(DEFINED variable)，如果变量被定义，为真


CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS开关，可以控制IF ELSE的写法





5.4 list

list(LENGTH <list><output variable>)                                   # 返回list的长度
list(GET <list> <elementindex> [<element index> ...]<output variable>) # 返回list中index的element到value中
list(APPEND <list><element> [<element> ...])                           # 添加新element到list中
list(FIND <list> <value><output variable>)                             # 返回list中element的index，没有找到返回-1
list(INSERT <list><element_index> <element> [<element> ...])           # 将新element插入到list中index的位置
list(REMOVE_ITEM <list> <value>[<value> ...])                          # 从list中删除某个element
list(REMOVE_AT <list><index> [<index> ...])                            # 从list中删除指定index的element
list(REMOVE_DUPLICATES <list>)                                         # 从list中删除重复的element
list(REVERSE <list>)                                                   # 将list的内容反转
list(SORT <list>)                                                      # 将list按字母顺序排序



















6 变量

6.1 cmake变量引用的方式

使用${}进行变量的引用

在IF等语句中，是直接使用变量名而不是通过${}取值




6.2 cmake自定义变量的方式

隐式定义和显式定义两种

6.2.1 隐式定义

PROJECT指令，它会隐士的定义<projectname>_BINARY_DIR和<projectname>_SOURCE_DIR两个变量

6.2.2 显式定义

使用SET指令，就可以构建一个自定义变量了





6.3 cmake常用变量

6.3.1 CMAKE_BINARY_DIR
    
    PROJECT_BINARY_DIR
    
    <projectname>_BINARY_DIR

这三个变量指代的内容是一致的。

如果是in source编译，指的就是工程顶层目录，如果是out of source编译，指的就是工程编译发生的目录


6.3.2 CMAKE_SOURCE_DIR
    
    PROJECT_SOURCE_DIR

    <project>_SOURCE_DIR

这三个变量指代的内容是一致的，不论采用何种编译方式，都是工程顶层目录


6.3.3 CMAKE_CURRENT_SOURCE_DIR

指的是当前处理的CMakeLists.txt所在的路径


6.3.4 CMAKE_CURRENT_BINARY_DIR

如果是in-source编译，它跟CMAKE_CURRENT_SOURCE_DIR一致，如果是out-of-source编译，它指的是target编译目录

（1）使用ADD_SUBDIRECTORY(src bin)可以更改这个变量的指

（2）使用SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对这个变量造成影响，它仅仅修改了最终目标文件存放的路径


6.3.5 CMAKE_CURRENT_LIST_FILE

输出调用这个变量的CMakeLists.txt的完整路径


6.3.6 CMAKE-CURRENT_LIST_LINE

输出这个变量所在的行


6.3.7 CMAKE_MODULE_PATH

这个变量用来定义自己的cmake模块所在的路径。

如果你的工程比较复杂，有可能会自己编写一些cmake模块，这些cmake模块是随你的工程发布的，
    为了让cmake在处理CMakeLists.txt时找到这些模块，你需要通过SET指令，将自己的cmake模块路径设置一下。

比如SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)


6.3.8 EXECUTABLE_OUTPUT_PATH和LIBRARY_OUTPUT_PATH

分别用来重新定义最终结果的存放目录

6.3.9 PROJECT_NAME

返回通过PROJECT指令定义的项目名称





6.4 cmake调用环境变量的方式

（1）使用$ENV{NAME}指令就可以调用系统的环境变量了

比如MESSAGE(STATUS "HOME dir : $ENV{HOME}")

（2）设置环境变量的方式是：

SET(ENV{变量名} 值)


6.4.1 CMAKE_INCLUDE_CURRENT_DIR

自动添加CMAKE_CURRENT_BINARY_DIR和CMAKE_CURRENT_SOURCE_DIR到当前处理的CMakeLists.txt

6.4.2 CMAKE_INCLUDE_DIRECTORIES_PROJECT_BEFORE

将工程提供的头文件目录始终置于系统头文件目录的前面，当你定义的头文件确实跟系统发生冲突时可以提供一些帮助

6.4.3 CMAKE_INCLUDE_PATH和CMAKE_LIBRARY_PATH



6.5 系统信息

CMAKE_MAJOR_VERSION，CMAKE主版本号，比如2.4.6中的2

CMAKE_MINOR_VERSION，CMAKE次版本号，比如2.4.6中的4

CMAKE_PATCH_VERSION，CMAKE补丁等级，比如2.4.6中的6

CMAKE_SYSTEM，系统名称，比如Linux-2.6.22

CMAKE_SYSTEM_NAME，不包含版本的系统名，比如Linux

CMAKE_SYSTEM_PROCESSOR，处理器名称，比如i686

UNIX，在所有的类UNIX平台为TRUE，包括OS X和cygwin

WIN32，在所有的win32平台为TRUE，包括cygwin




6.6 主要的开关选项

6.6.1 CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS，用来控制IF ELSE语句的书写方式

6.6.2 BUILD_SHARED_LIBS

这个开关用来控制默认的库编译方式，如果不进行设置，
    使用ADD_LIBRARY并没有指定库类型情况下，默认编译生成的库都是静态库

如果SET(BUILD_SHARED_LIBS ON)后，默认生成的为动态库


6.6.3 CMAKE_C_FLAGS

设置C编译选项，也可以通过指令ADD_DEFINITIONS()添加

6.6.4 CMAKE_CXX_FLAGS

设置C++编译选项，也可以通过指令ADD_DEFINITIONS()添加

