---
layout: post
title: Google C++ CodeStyle
category : simple
tagline: "Hello world simple"
tags : [codestyle, google, C++]
---
{% include JB/setup %}

摘自 http://zh-google-styleguide.readthedocs.org/en/latest/google-cpp-styleguide/ 有改动

###原作者:

   * Benjy Weinberger
   * Craig Silverstein
   * Gregory Eitzmann
   * Mark Mentovai
   * Tashana Landray

###翻译:

   * YuleFox
   * brantyoung

### 项目主页:

   * [Google Style Guide](https://https://code.google.com/p/google-styleguide/)
   * [Google 开源项目风格指南 - 中文版](http://https://github.com/zh-google-styleguide/zh-google-styleguide)

## 1.头文件

   * 使用#define保护 所有头文件都应该使用 #define 防止头文件被多重包含, 命名格式应当是: &lt; `PROJECT`&gt;\_&lt; `PATH`&gt;\_&lt;`FILE`&gt;_H_
	
		#ifndef FOO_BAR_BAZ_H_

		#define FOO_BAR_BAZ_H
	 
	 	//...
	 
		#endif // FOO_BAR_BAZ_H_

   * 头文件依赖 能用前置声明的地方尽量不使用 #include. 减少头文件依赖
   * 内联函数 只有函数在10行以内时考虑使用内联，具体是不是内联由编译器决定
   * -inl.h文件 如果内联函数较复杂，考虑使用-inl.h文件 这个文件也要使用#define保护
   * 函数参数顺序 一般采用先输入后输出原则，不需要严格遵守
   * \#include路径顺序 使用标准的头文件包含顺序可增强可读性, 避免隐藏依赖: C 库, C++ 库, 其他库的 .h, 本项目内的 .h. 按字母顺序进行二次排序以方便进行查找

		1. cc/cpp文件对应头文件，父类头文件
		2. C系统文件
		3. C++系统文件
		4. ？ObjC系统文件？
		5. 其他库的.h文件
		6. 本项目内.h文件

## 2.作用域


   * 名字空间 在.cc文件中使用匿名名字空间避免冲突。具名名字空间科技与项目名或相对路径
      1.  匿名名字空间 namespace { enum { ... };  void func() {} }
      1.  具名名字空间 namespace mynamespace {  ... }
      1.  不要在std命名空间声明任何东西
      1.  在.cc文件.h文件函数方法或类中可以使用using关键字 using namespace mynamespace;
      1.  尽量使用using声明 using std::string; 而不是用using指示using namespace std;

   * 非成员函数、静态成员函数和全局函数
      * 使用静态成员函数或名字空间内的非成员函数，尽量不要使用裸的全局函数
      
   * 局部变量 将函数变量尽可能置于最小作用域并在变量声明时进行初始化
      * 如果在循环作用于中声明一个对象变量，则每次循环都会调用对象的构造函数和析构函数，如果可能将对象移到循环体外是个不错的选择
      
   * 静态和全局变量 禁止使用class类型的静态或全局变量， 他们会导致很难发现的bug和不确定的构造和析构函数调用顺序。
      * 静态生存周期的对象，都必须是原生数据类型（POD：Plain Old Data）以及POD类型的数组/结构体/指针
      * 永远不要使用函数返回值初始化静态变量。
      * 不要在多线程代码中使用非const的静态变量
      * C++标准并未明确定义静态变量的构造函数，析构函数以及初始化操作的调用顺序

## 3.类


   * 构造函数职责  使用类似objc的两段构造的方式进行初始化 
      * 构造函数中只进行那些没什么意义的 (trivial) 初始化, 可能的话, 使用 Init() 方法集中初始化有意义的 (non-trivial) 数据.

      * 在构造函数中执行操作引起的问题有
         * 构造函数中很难上报错误, 不能使用异常.
         * 操作失败会造成对象初始化失败，进入不确定状态.
         * 如果在构造函数内调用了自身的虚函数, 这类调用是不会重定向到子类的虚函数实现. 即使当前没有子类化实现, 将来仍是隐患.
         * 如果有人创建该类型的全局变量 (虽然违背了上节提到的规则), 构造函数将先 main() 一步被调用, 有可能破坏构造函数中暗含的假设条件.
   
   
   * 默认构造函数
      * 如果一个类定义了若干成员变量又没有其它构造函数, 必须定义一个默认构造函数. 否则编译器将自动生产一个很糟糕的默认构造函数.
      * new 一个不带参数的类对象时, 会调用这个类的默认构造函数. 用 new[] 创建数组时，默认构造函数则总是被调用.


   * 显式构造函数
      * 对单个参数的构造函数使用 C++ 关键字 explicit.避免不合时宜的变换
      
   * 拷贝构造函数 
      * 仅在代码中需要拷贝一个类对象的时候使用拷贝构造函数; 大部分情况下都不需要, 此时应使用 DISALLOW_COPY_AND_ASSIGN.
		
            // 禁止使用拷贝构造函数和 operator= 赋值操作的宏
		    // 应该类的 private: 中使用
		    
		    #define DISALLOW_COPY_AND_ASSIGN(TypeName) \
            TypeName(const TypeName&); \
            void operator=(const TypeName&)

      * 绝大多数情况下都应使用 DISALLOW_COPY_AND_ASSIGN 宏. 如果类确实需要可拷贝, 应在该类的头文件中说明原由, 并合理的定义拷贝构造函数和赋值操作. 注意在 operator= 中检测自我赋值的情况


   * 结构体 VS. 类 
      * 仅当只有数据时使用 struct, 其它一概使用 class.

   * 继承 
      * 使用组合 (composition) 常常比使用继承更合理. 如果使用继承的话, 定义为 public 继承.

      * 所有继承必须是 public 的. 如果你想使用私有继承, 你应该替换成把基类的实例作为成员对象的方式

      * 必要的话, 析构函数声明为 virtual. 如果你的类有虚函数, 则析构函数也应该为虚函数. 注意 数据成员在任何情况下都必须是私有的.

      * 当重载一个虚函数, 在衍生类中把它明确的声明为 virtual. 理论依据: 如果省略 virtual 关键字, 代码阅读者不得不检查所有父类, 以判断该函数是否是虚函数

      * 组合 > 实现继承 > 接口继承 > 私有继承, 子类重载的虚函数也要声明 virtual 关键字, 虽然编译器允许不这样做

   
   * 多重继承
      * 真正需要用到多重实现继承的情况少之又少. 只在以下情况我们才允许多重继承: 最多只有一个基类是非抽象类; 其它基类都是以 Interface 为后缀的 纯接口类
      * 多重继承允许子类拥有多个基类. 要将作为 纯接口 的基类和具有 实现 的基类区别开来


   * 接口
      * 接口是指满足特定条件的类, 这些类以 Interface 为后缀 (不强制).
      * 当一个类满足以下要求时, 称之为纯接口:
         * 只有纯虚函数 (“=0”) 和静态函数 (除了下文提到的析构函数).
         * 没有非静态数据成员.
         * 没有定义任何构造函数. 如果有, 也不能带有参数, 并且必须为 protected.
         * 如果它是一个子类, 也只能从满足上述条件并以 Interface 为后缀的类继承.
     
         
   * 运算符重载
      * 除少数特定环境外，不要重载运算符.
      * 一般不要重载运算符. 尤其是赋值操作 (operator=) 比较诡异, 应避免重载. 如果需要的话, 可以定义类似 Equals(), CopyFrom() 等函数

      * 下标运算符重载 int &operator[] (const size_t); //左值，没有const返回引用  const int &operator[] (const size_t) const; //右值 含有const，返回const引用且不改变对象的值
   
   
   * 存取控制
      * 将 所有 数据成员声明为 private, 并根据需要提供相应的存取函数. 例如, 某个名为 foo_ 的变量, 其取值函数是 foo(). 还可能需要一个赋值函数 set_foo().
      * 一般在头文件中把存取函数定义成内联函数.


   * 声明顺序 
      * 在类中使用特定的声明顺序: public: 在 private: 之前, 成员函数在数据成员 (变量) 前;
      * 类的访问控制区段的声明顺序依次为: public:, protected:, private:. 如果某区段没内容, 可以不声明.
      * 每个区段内的声明通常按以下顺序:
         * typedefs 和枚举
         * 常量
         * 构造函数
         * 析构函数
         * 成员函数, 含静态成员函数
         * 数据成员, 含静态数据成员
      * 总是将freined声明放在privite中
      * 宏 DISALLOW_COPY_AND_ASSIGN 的调用放在 private: 区段的末尾. 它通常是类的最后部分

   * 编写简短函数 倾向编写简短, 凝练的函数. 


## 4.来自Google的奇技

   * 智能指针
      *  如果确实需要使用智能指针的话, scoped_ptr 完全可以胜任. 你应该只在非常特定的情况下使用 std::tr1::shared_ptr （std::sharedptr）, 例如 STL 容器中的对象. 任何情况下都不要使用 auto_ptr.
      * 一般来说，我们倾向于设计对象隶属明确的代码, 最明确的对象隶属是根本不使用指针, 直接将对象作为一个作用域或局部变量使用. 另一种极端做法是, 引用计数指针不属于任何对象. 这种方法的问题是容易导致循环引用, 或者导致某个对象无法删除的诡异状态, 而且在每一次拷贝或赋值时连原子操作都会很慢.

   * cpplint
      * 使用cpplint.py检查风格错误.  cpplint.py是一个用来分析源文件, 能检查出多种风格错误的工具. 它不并完美, 甚至还会漏报和误报, 但它仍然是一个非常有用的工具. 用行注释 // NOLINT 可以忽略误报. 下载地址: [cpplint下载地址](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py)


## 5.其他C++特性

   * 引用参数
      * 所有按引用传递的参数必须加上 const.  这在 Google Code 是一个硬性约定: 输入参数是值参或 const 引用, 输出参数为指针. 输入参数可以是 const 指针, 但决不能是 非 const 的引用参数
   
   * 函数重载
      * 仅在输入参数类型不同, 功能相同时使用重载函数 (含构造函数). 不要用函数重载模拟 缺省函数参数
      * 如果你想重载一个函数, 考虑让函数名包含参数信息, 例如, 使用 AppendString(), AppendInt() 而不是 Append()

   * 缺省参数
      * 我们不允许使用缺省函数参数.除非在某些特殊情况下，在么某些情况下使用函数重载代替使用却上参数
      * 所有参数必须明确指定, 迫使程序员理解 API 和各参数值的意义, 避免默默使用他们可能都还没意识到的缺省参数.
      * 如果函数是`cpp`文件内部的静态方法或者是匿名空间中的方法，这种方法只能局部使用，可以使用缺省参数

   * 变长数组和 alloca().
      * 我们不允许使用变长数组和 alloca().

   * 友元
      * 我们允许合理的使用友元类及友元函数.
      * 友元扩大了 (但没有打破) 类的封装边界. 某些情况下, 相对于将类成员声明为 public, 使用友元是更好的选择, 尤其是如果你只允许另一个类访问该类的私有成员时. 当然, 大多数类都只应该通过其提供的公有成员进行互操作.

   * 异常
      * 不使用 C++ 异常

   * 运行时类型识别
      * 禁止使用 RTTI（） 除单元测试外, 不要使用 RTTI
      * 在运行时判断类型通常意味着设计问题. 如果你需要在运行期间确定一个对象的类型, 这通常说明你需要考虑重新设计你的类
      * 如果要实现根据子类类型来确定执行不同逻辑代码, 虚函数无疑更合适. 在对象内部就可以处理类型识别问题.
      * 如果要在对象外部的代码中判断类型, 考虑使用双重分派方案, 如访问者模式. 可以方便的在对象本身之外确定类的类型.
   
   * 类型转换
      * 使用 C++ 的类型转换, 如 static_cast<>(). 不要使用 int y = (int)x 或 int y = int(x) 等转换方式
      * 用 static_cast 替代 C 风格的值转换, 或某个类指针需要明确的向上转换为父类指针时.
      * 用 const_cast 去掉 const 限定符.
      * 用 reinterpret_cast 指针类型和整型或其它指针之间进行不安全的相互转换. 仅在你对所做一切了然于心时使用.
      * dynamic_cast 测试代码以外不要使用. 除非是单元测试, 如果你需要在运行时确定类型信息, 说明有 设计缺陷.
   
   * 流
      * 只在记录日志时使用
      * 如果不使用 printf 风格的格式化字符串, 某些格式化操作 (尤其是常用的格式字符串 `%.*s`) 用流处理性能是很低的. 流不支持字符串操作符重新排序 (`%1s`), 而这一点对于软件国际化很有用

      * 不要使用流, 除非是日志接口需要. 使用 printf 之类的代替

   * 前置自增和自减
      * 对于迭代器和其他模板对象使用前缀形式 (++i) 的自增, 自减运算符.
      * 定义前自增／前自减操作符

				class CheckedPtr {

				public:

    				CheckedPtr& operator++(); // prefix operators

    				CheckedPtr& operator--();

					// other members as before

				};
      * 定义后缀式操作符

				class CheckedPtr {
				public:
					// increment and decrement
					CheckedPtr& operator++(int); // postfix operators
					CheckedPtr& operator--(int);
					// other members as before
				};

   
   * const的使用 强烈建议你在任何可能的情况下都要使用 const
      * 在声明的变量或参数前加上关键字 const 用于指明变量值不可被篡改 (如 const int foo ). 为类中的函数加上 const 限定符表明该函数不会修改类成员变量的状态 (如 class Foo { int Bar(char c) const; };).
      * 如果函数不会修改传入的引用或指针类型参数, 该参数应声明为 const.
      * 尽可能将函数声明为 const. 访问函数应该总是 const. 其他不会修改任何数据成员, 未调用非 const 函数, 不会返回数据成员非 const 指针或引用的函数也应该声明成 const.
      * 如果数据成员在对象构造之后不再发生变化, 可将其定义为 const.
      * const位置 提倡const在前 
         * const int* cptr; //cptr may point to an int that is const
         * int *const cErr; // cErr is a constant pointer that point to an int
         * const 修饰typedef生命的类型要注意理解其含义，建议将const后置然后再理解
   
   * 整型 
      * C++ 内建整型中, 仅使用 int. 如果程序中需要不同大小的变量, 可以使用 &lt;stdint.h&gt; 中长度精确的整型, 如 int16_t
      * 头文件stdint.h 定义了 int16_t, uint32_t, int64_t 等整型, 在需要确保整型大小时可以使用它们代替
      * 如果已知整数不会太大, 我们常常会使用 int, 如循环计数. 在类似的情况下使用原生类型 int. 你可以认为 int 至少为 32 位, 但不要认为它会多于 32 位. 如果需要 64 位整型, 用 int64_t 或 uint64_t.
      * 不要使用 uint32_t 等无符号整型, 除非你是在表示一个位组而不是一个数值, 或是你需要定义二进制补码溢出.
      
   * 64位下可移植性
      * 代码应该对 64 位和 32 位系统友好. 处理打印, 比较, 结构体对齐时应切记
      

  