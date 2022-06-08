1、匿名变量
如果函数调用的参数不是左值或与相应的const引用参数的类型不匹配，则C++将创建正确的匿名变量
将函数调用的参数值传递给该匿名变量，并让参数来引用该变量

2、右值引用
右值引用可指向右值，使用&&声明

double &&jref = 10 * 100;

3、常规返回类型是右值，因为这种返回值位于临时内存单元中，运行到下一条语句时，它们可能不在存在

4、名称修饰——编译器为了区分函数重载，对重载函数的名称做了转换

5、将模板的名义放在头文件中，并在需要使用的时候包含头文件

6、模板的显式具体化和显式实例化
具体化：
	template<>
		void Swap<int>(int &, int &);
	或
	template<>
		void Swap(int &, int &);
实例化:
	template void Swap<int>(int &, int &);

7、decltype
decltype(expression) var;

8、后置返回类型

auto func(T1 x, T2 y) -> decltype(x + y)

->Type 称为后置返回类型
auto 自动类型识别
decltype 必须放在变量声明后使用

所以后置声明很好的解决了这个问题

9、thread_local声明
变量使用thread_local声明，则其生命周期与所属线程一样长


10、register寄存器变量
建议编译器使用CPU寄存器来存储自动变量
目前基本失去作用，与auto作用差不多


11、const, volatile, mutable
volatile: 程序代码没有对内存单元进行修改，其值也可能发生变化
		  避免编译对代码做的寄存器优化(将经常访问的变量存储在寄存器中)
mutable: 用来指出，即使结构（或类）变量为const，其某个成员也可以被修改

12、不要在头文件中使用 using 编译指令。首先，这样做掩盖了要让哪些名称可用；另外，包含头文
件的顺序可能影响程序的行为。如果非要使用编译指令 using，应将其放在所有预处理器编译指令
#include 之后

13、基本类型
（1）决定数据对象需要的内存数量
（2）决定如何解释内存中的位
（3）决定可使用数据对象执行的操作或方法


14、构造函数和析构函数


15、this指针
this指针本身是 ClassType * const，即指针常量
成员函数的const修饰的是this，所以对于const成员函数相当于使用 const ClassType * const指针去调用成员函数，不能修改成员变量












