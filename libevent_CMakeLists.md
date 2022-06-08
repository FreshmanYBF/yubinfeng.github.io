
direcotry:

root
	Documentation
	WIN32-Code
	cmake
	compat/sys
	extra
	include
	m4
	sample
	test-export
	test
	源码

# CMakeLists.txt
该CMakeLists兼容了各种操作系统，各种编译器

## CMake版本兼容——策略的使用

cmake_minimum_required(VERSION 3.1.2 FATAL_ERROR)

if (POLICY CMP0054)
    cmake_policy(SET CMP0054 NEW)
endif()
if (POLICY CMP0074)
    cmake_policy(SET CMP0074 NEW)
endif()
if (POLICY CMP0075)
    cmake_policy(SET CMP0075 NEW)
endif()

每个新发布版本一般都会引入一些新的策略，每个策略都会有一个标识号，格式CMP<NNNN>的标识符，<NNNN>对应四个0到9的整数。
每个策略都在文档中描述了OLD和NEW的行为，以及引入的原因
### 隐式设置策略
cmake_minimum_required(VERSION 3.1.2 FATAL_ERROR)
使用cmake_minimum_required会隐式调用cmake_policy(VERSION)
（1）限制版本号
cmake_policy(VERSION <min>...<max>)


### 显示设置策略
//使用NEW来声明后续代码依赖于此policy
cmake_policy(SET CMP<NNNN> NEW)
//使用OLD来向使用此policy的配置过程发出警告信息
cmake_policy(SET CMP<NNNN> OLD)

//使用GET来获取当前支持的policy
//OLD或NEW的行为值存放在variable中
cmake_policy(GET CMP<NNN> <variable>)

if (POLICY CMP<CMAKE_POLICY>)
使用此条件来判断当前的cmake是否支持某条policy

### 策略的POP和PUSH
CMake将策略设置保存在一个栈结构中
当进入一个项目的子目录时，就会在策略栈中入栈新的一层元素，离开子目录时就会出栈这层元素
在一个项目的子目录中设置策略并不会影响父目录以及同层路径，但会影响子目录

cmake_policy(PUSH)
cmake_policy(POP)

PUHS和POP必须成对出现


## 设置默认的构建类型——set default build type in CMakeLists.txt

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release
        CACHE STRING "Set build type to Debug or Release (default Release)" FORCE)
endif()

1、无效的设置CMAKE_BUILD_TYPE

SET(CMAKE_BUILD_TYPE debug)

2、使用set(.... CACHE ...)
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release" 
		CACHE 
		STRING "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)



## string操作

1、字符串查找
string(FIND <string> <substring> <out-var> [...])
string(REPLACE <match-string> <replace-string> <out-var> <input>...)
string(REGEX MATCH <match-regex> <out-var> <input>...)
string(REGEX MATCHALL <match-regex> <out-var> <input>...)
string(REGEX REPLACE <match-regex> <replace-expr> <out-var> <input>...)

2、字符串操作
//将<string>转成小写，并存在output_variable中
string(TOLOWER <string> <output_variable>)

//将<string>转成大写，并存在output_variable中
string(TOUPPER <string> <output_variable>)

//获取<string>的长度
string(LENGTH <string> <output_variable>)

string(SUBSTRING <string> <begin> <length> <output_variable>)

//将input都追加到字符串中
string(APPEND <string_variable> [<input>...])

//将input都前置到字符串中
string(PREPEND <string_variable> [<input>...])

//
string(CONCAT <output_variable> [<input>...])

//使用< glue >字符串将所有< input >参数连接在一起
string(JOIN <glue> <output_variable> [<input>...])


3、字符串比较

string(COMPARE LESS <string1> <string2> <output_variable>)
string(COMPARE GREATER <string1> <string2> <output_variable>)
string(COMPARE EQUAL <string1> <string2> <output_variable>)
string(COMPARE NOTEQUAL <string1> <string2> <output_variable>)
string(COMPARE LESS_EQUAL <string1> <string2> <output_variable>)
string(COMPARE GREATER_EQUAL <string1> <string2> <output_variable>)


4、Hash加密散列

string(<HASH_Alogrithm> <output_variable> <input>)

MD5，SHA1等

5、生成

//将所有数字转换为相应的ASCII字符
string(ASCII <number> [<number>...] <output_variable>)

//转成16进制表示
string(HEX <string> <output_variable>)

//返回给定< length >的随机字符串，由给定< alphabet >中的字符组成。默认长度是5个字符，默认字母是所有数字和大写字母和小写字母。如果给定一个整数RANDOM_SEED，它的值将用于为随机数生成器种子。
string(RANDOM [LENGTH <length>] [ALPHABET <alphabet>]
       [RANDOM_SEED <seed>] <output_variable>)

//将当前日期/时间的字符串表示形式写入< output_variable >
string(TIMESTAMP <output_variable> [<format_string>] [UTC])


## 设置生成库类型

set(EVENT__LIBRARY_TYPE DEFAULT CACHE STRING
    "Set library type to SHARED/STATIC/BOTH (default SHARED for MSVC, otherwise BOTH)")


## list——列表操作

list即对列表的一系列操作，cmake中的列表变量是以分号分隔的一组字符串
set可以用于创建列表，元素之间使用空格分开

读取，查找，修改，排序等

list (subcommand <list> [args...])



## include——载入并运行来自文件或模块的CMake代码

include(<file|module> [OPTIONAL] [RESULT_VARIABLE <VAR>] [NO_POLICY_SCOPE])



## macro和function——宏和函数

### macro
macro(<name> [<arg1> ...])
	<commands>
endmacro()

3.18支持使用cmake_language(CALL <name>)来调用macro


### function
function(<name> [<arg1> ...])
	<commands>
endfunction()

cmake_language(CALL <name>)


## option——控制编译流程

### option
option(<variable> "<help_text>" [value])

variable: 名称
help_text: 选项含义
value: ON或OFF

可用来控制编译流程

### cmake_dependent_option
cmake_dependent_option(<option> "<help_text>" <value> <depends> <force>)

当depends为真时，option为value，否则为force



## add_xxx

### add_definitions——添加预处理标志

add_definitions(-DFOO -DBAR ...)

增加 -D 标志到源码的编译中
添加预处理器定义

1、替代命令
（1）add_compile_definitions() —— add preprocessor definitions，添加预处理标志
（2）include_directories() —— add include directories
（3）add_compile_options() —— add other options. 
add_compile_options(-Wall -Wextra -pedantic -Werror)

### 



## find_xx

find_file —— 查找指定文件的完整路径
find_library —— 查找库
find_path —— 查找目录
find_program —— 查找程序
find_package —— 查找库
	使用Module模式，执行CMAKE_MODULE_PATH指定路径下的FindXXX.cmake文件，从而找到库
	使用Config模式，执行XXX_DIR路径下的XXXConfig.cmake文件，从而找到库


## cmake内置宏



## CHECK_XX

### CHECK_TYPE_SIZE

check_type_size(<type> <variable> [BUILTIN_TYPES_ONLY]
                                  [LANGUAGE <language>])
检查类型是否存在和它的大小

（1）HAVE_<variable>
Holds a true or false value indicating whether the type exists.

（2）<variable>
Holds one of the following values:

	<size>
		Type has non-zero size <size>.

	0
		Type has architecture-dependent size. 
		This may occur when CMAKE_OSX_ARCHITECTURES has multiple architectures. 
		In this case <variable>_CODE contains C preprocessor tests mapping from each architecture macro to the corresponding type size. 
		The list of architecture macros is stored in <variable>_KEYS, and the value for each key is stored in <variable>-<key>.

	"" (empty string)
		Type does not exist.

（3）<variable>_CODE
Holds C preprocessor code to define the macro <variable> to the size of the type, or to leave the macro undefined if the type does not exist.


The options are:
（4）BUILTIN_TYPES_ONLY
Support only compiler-builtin types. 
If not given, the macro checks for headers <sys/types.h>, <stdint.h>, and <stddef.h>, and saves results in HAVE_SYS_TYPES_H, HAVE_STDINT_H, and HAVE_STDDEF_H. 
The type size check automatically includes the available headers, thus supporting checks of types defined in the headers.

（5）LANGUAGE <language>
Use the <language> compiler to perform the check. Acceptable values are C and CXX.



### CHECK_PROTOTYPE_DEFINITION

检查我们期望的协议类型是否正确

check_prototype_definition(FUNCTION PROTOTYPE RETURN HEADER VARIABLE)

	FUNCTION - The name of the function (used to check if prototype exists)
	PROTOTYPE- The prototype to check.
	RETURN - The return value of the function.
	HEADER - The header files required.
	VARIABLE - The variable to store the result.
			   Will be created as an internal cache variable.


### CHECK_SYMBOL_EXISTS

检查符号是否存在

check_symbol_exists(<symbol> <files> <variable>)

check_cxx_symbol_exists(<symbol> <files> <variable>)


## source_group

source_group(<name> [FILES <src>...] [REGULAR_EXPRESSION <regex>])
source_group(TREE <root> [PREFIX <prefix>] [FILES <src>...])

	FILES
		任何明确指定的源文件都将放置在group<name>中。
		相对路径是相对于当前源目录进行解释的。

## configure_file

Copy a file to another location and modify its contents.
将文件拷贝到其他位置，并修改其内容

configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])


			   
	

## add_xxx
	
### add_executable

add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               [source1] [source2 ...])

构建一个名为name的目标程序

### add_library

add_library(<name> [STATIC | SHARED | MODULE]
            [EXCLUDE_FROM_ALL]
            [<source>...])

构建要给名为name的库


### add_test

add_test(NAME <name> COMMAND <command> [<arg>...]
         [CONFIGURATIONS <config>...]
         [WORKING_DIRECTORY <dir>]
         [COMMAND_EXPAND_LISTS])

添加一个名为name的测试。
使用ctest 执行


set_tests_properties

set_tests_properties(test1 [test2...] PROPERTIES prop1 value1 prop2 value2)


enable_testing
使用生成的test
enable_testing()


### add_custom_command

add_custom_command(OUTPUT output1 [output2 ...]
                   COMMAND command1 [ARGS] [args1...]
                   [COMMAND command2 [ARGS] [args2...] ...]
                   [MAIN_DEPENDENCY depend]
                   [DEPENDS [depends...]]
                   [BYPRODUCTS [files...]]
                   [IMPLICIT_DEPENDS <lang1> depend1
                                    [<lang2> depend2] ...]
                   [WORKING_DIRECTORY dir]
                   [COMMENT comment]
                   [DEPFILE depfile]
                   [JOB_POOL job_pool]
                   [VERBATIM] [APPEND] [USES_TERMINAL]
                   [COMMAND_EXPAND_LISTS])

增加用户构建规则到构建系统中

### add_custom_target

add_custom_target(Name [ALL] [command1 [args1...]]
                  [COMMAND command2 [args2...] ...]
                  [DEPENDS depend depend depend ... ]
                  [BYPRODUCTS [files...]]
                  [WORKING_DIRECTORY dir]
                  [COMMENT comment]
                  [JOB_POOL job_pool]
                  [VERBATIM] [USES_TERMINAL]
                  [COMMAND_EXPAND_LISTS]
                  [SOURCES src1 [src2...]])				   


				  
### add_dependencies

add_dependencies(<target> [<target-dependency>]...)
使顶级目标依赖于其他顶级目标，以确保它们在构建之前先构建
				  
				  
## target_xxx

### target_link_libraries
添加链接的库

target_link_libraries(<target> ... <item>... ...)
先生成目标然后再链接库			   
			   
			   
### target_compile_definitions			   
			   
target_compile_definitions(<target>
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])			   
			   
编译指定目标时添加的编译选项	
target必须被add_executable()或add_library()创建 
			  

## set_target_properties

set_target_properties(target1 target2 ...
                      PROPERTIES prop1 value1
                      prop2 value2 ...)
影响目标如何被构建

## install

install(TARGETS <target>... [...])
install(IMPORTED_RUNTIME_ARTIFACTS <target>... [...])
install({FILES | PROGRAMS} <file>... [...])
install(DIRECTORY <dir>... [...])
install(SCRIPT <file> [...])
install(CODE <code> [...])
install(EXPORT <export-name> [...])
install(RUNTIME_DEPENDENCY_SET <set-name> [...])		  
			   
生成安装规则
