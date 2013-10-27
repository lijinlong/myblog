---
layout: post
title: Google C++ CodeStyle
category : simple
tagline: "Hello world simple"
tags : [codestyle, google, C++]
---
{% include JB/setup %}

# {{page.title}}

<p>{{ page.date | date_to_string}}</p>

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
      * C++ 内建整型中, 仅使用 int. 如果程序中需要不同大小的变量, 可以使用 &lt;stdint.h&gt; 中长度精确的整型, 如 `int16_t`
      * 头文件`stdint.h` 定义了 `int16_t`, `uint32_t`, `int64_t` 等整型, 在需要确保整型大小时可以使用它们代替
      * 如果已知整数不会太大, 我们常常会使用 int, 如循环计数. 在类似的情况下使用原生类型 int. 你可以认为 int 至少为 32 位, 但不要认为它会多于 32 位. 如果需要 64 位整型, 用 `int64_t` 或 `uint64_t`.
      * 不要使用 `uint32_t` 等无符号整型, 除非你是在表示一个位组而不是一个数值, 或是你需要定义二进制补码溢出.

   * 64位下可移植性
      * 代码应该对 64 位和 32 位系统友好. 处理打印, 比较, 结构体对齐时应切记
      * 记住 `sizeof(void *) != sizeof(int)`. 如果需要一个指针大小的整数要用 `intptr_t`
      * 你要非常小心的对待结构体对齐, 尤其是要持久化到磁盘上的结构体 (yospaly 注: 持久化 - 将数据按字节流顺序保存在磁盘文件或数据库中). 在 64 位系统中, 任何含有 `int64_t`/`uint64_t` 成员的类/结构体, 缺省都以 8 字节在结尾对齐. 如果 32 位和 64 位代码要共用持久化的结构体, 需要确保两种体系结构下的结构体对齐一致. 大多数编译器都允许调整结构体对齐. gcc 中可使用 `__attribute__((packed))`. MSVC 则提供了 `#pragma pack()` 和 `__declspec(align())` (YuleFox 注, 解决方案的项目属性里也可以直接设置)
      * 创建 64 位常量时使用 LL 或 ULL 作为后缀, 如: `int64_t my_value = 0×123456789LL`
      
   * 预处理宏
      * 使用宏时要非常谨慎, 尽量以内联函数, 枚举和常量代替之
      * 宏意味着你和编译器看到的代码是不同的. 这可能会导致异常行为, 尤其因为宏具有全局作用域
      * 如果你要宏, 尽可能遵守:
         * 不要在 .h 文件中定义宏.
         * 在马上要使用时才进行 #define, 使用后要立即 #undef.
         * 不要只是对已经存在的宏使用#undef，选择一个不会冲突的名称；
         * 不要试图使用展开后会导致 C++ 构造不稳定的宏, 不然也至少要附上文档说明其行为
         * `带参数的宏将参数使用`（）`括起来，避免错误的替换`
         * `包含多条语句的宏使用 `do { … } while (0)` 的形式定义，是宏整体上看上去像一条语句`
         * `不要再宏调用处使用含有赋值、前缀/后缀递增/递减，输入输出和含有这些效果的函数调用，一般这不是你期待的行为`
         * `切记传递给红的变量可以在宏定义体内修改，这是宏与函数不同的地方`
   
   
   * 0 和 nullptr/NULL
      * 整数用 0, 实数用 0.0, 指针用 nullptr(NULL), 字符 (串) 用 '\0'.

   * sizeof
      * 尽可能用 sizeof(varname) 代替 sizeof(type).

   * auto
       * 在类型的含义十分明确的时候使用auto关键字（比如迭代器声明），这样可以是代码清晰
       * 当时用类型显示声明能使代码更清晰时，继续使用类型的显示声明
       * 只在局部变量中使用auto
       * 不要用braced initializer list初始化auto类型变量
       
    * Brace initialization 初始化列表
       * 允许使用这种初始化
       * C++03，聚集类型（数组或不带构造函数的struct）可以使用花括号初始化列表初始化
       
               struct Point { int x; int y; };
               Point p = {1, 2};
               
       * C++11将这种语法扩展到其他数据类型
               
               // Vector takes lists of elements.
                vector<string> v{"foo", "bar"};
                
                // The same, except this form cannot be used if the initializer_list
                // constructor is explicit. You may choose to use either form.
                vector<string> v = {"foo", "bar"};
                
                // Maps take lists of pairs. Nested braced-init-lists work.
                map<int, string> m = \{\{1, "one"\}, \{2, "2"\}\};
                
                // braced-init-lists can be implicitly converted to return types.
                vector<int> test_function() {
                  return {1, 2, 3};
                }
                
                // Iterate over a braced-init-list.
                for (int i : {-1, -2, -3}) {}
                
                // Call a function using a braced-init-list.
                void test_function2(vector<int> v) {}
                test_function2({1, 2, 3});
                
        * 用户数据类型可以定义使用initializer_list为参数的构造函数
                
                class MyType {
                    public:
                    // initializer_list is a reference to the underlying init list,
                    // so it can be passed by value.
                    MyType(initializer_list<int> init_list) {
                    for (int element : init_list) {}
                    }
                };
                MyType m{2, 3, 5, 7};
         
         * 没有initializer_list构造函数的类，花括号出事后会自动调用一般的构造函数
         
                 double d{1.23};
                // Calls ordinary constructor as long as MyOtherType has no
                // initializer_list constructor.
                class MyOtherType {
                 public:
                  explicit MyOtherType(string);
                  MyOtherType(int, string);
                };
                MyOtherType m = {1, "b"};
                // If the constructor is explicit, you can't use the "= {}" form.
                MyOtherType m{"b"};
         * 不要使用花括号初始化，auto类型局部变量
         
   * Boost库
      * 只使用 Boost 中被认可的库.
      * 只允许使用 Boost 一部分经认可的特性子集. 目前允许使用以下库:
         * Compressed Pair : boost/compressed_pair.hpp
         * Pointer Container : boost/ptr_container (序列化除外)
         * Array : boost/array.hpp
         * The Boost Graph Library (BGL) : boost/graph (序列化除外)
         * Property Map : boost/property_map.hpp
         * Iterator 中处理迭代器定义的部分 : boost/iterator/iterator_adaptor.hpp, boost/iterator/iterator_facade.hpp, 以及boost/function_output_iterator.hpp

   * C++11
      * 只使用被广泛认可的C++11函数库和语言扩展
      * 目前可以使用下面的C++11特性
         * auto （只用于局部变量）
         * constexpr （一般化的受保证的常量表达式， 编译时可以确定为常量的表达式）
         * 在模版参数中直接使用‘&gt;&gt;’中间不需要有空格， 如set&lt;list&lt;string&gt;&gt; 在C++03中需要一个空格set&lt;list&lt;string&gt; &gt;
         * Range-based for loops， for (int i : vec) {}
         * 使用LL或ULL后缀的数字字面值保证他们的类型至少有64bit
         * Variadic macros可变参数宏（但我们不推荐使用宏）
         * All of the new STL algorithms in the &lt;algorithm&gt; and &lt;numeric&gt; headers
         * Use of local types as template parameters 局部类型作为模板参数
         * nullptr and nullptr_t
         * static_assert. 静态（编译器）断言
         * Everything in &lt;array&gt;.
         * Everything in &lt;tuple&gt;.
         * Variadic templates.
         * Alias templates (the new using syntax) may be used. Don't use an alias declaration where a typedef will work.
         * unique_ptr, with restrictions (see below).
         * Brace initialization syntax. 初始化列表
         
  * unique_ptr
      * 在函数和类中自由使用unique_ptr，但不要将他的所有权转移到.cc/.h文件外
      
## 6.命名约定


   * 通用命名规则
      * 函数命名, 变量命名, 文件命名应具备描述性; 不要过度缩写. 类型和变量应该是名词, 函数名可以用 “命令性” 动词.
      * 类型和变量名一般为名词: 如 FileOpener, num_errors
      * 函数名通常是指令性的 (确切的说它们应该是命令), 如 OpenFile(), set_num_errors(). 取值函数是个特例 (在 函数命名 处详细阐述), 函数名和它要取值的变量同名
      * 除非该缩写在其它地方都非常普遍, 否则不要使用

   * 文件命名
      * 文件名要全部小写, 可以包含下划线 (_) 或连字符 (-). 按项目约定来
      * 不要使用已经存在于 /usr/include 下的文件名

   * 类型命名
      * 类型名称的每个单词首字母均大写, 不包含下划线: MyExcitingClass, MyExcitingEnum.
      * 所有类型命名 —— 类, 结构体, 类型定义 (typedef), 枚举 —— 均使用相同约定

   * 变量命名
      * 变量名一律小写, 单词之间用下划线连接. 类的成员变量以下划线结尾
      * 对全局变量没有特别要求, 少用就好, 但如果你要用, 可以用 g_ 或其它标志作为前缀, 以便更好的区分局部变量

   * 常量命名
      * 在名称前加 k 所有编译时常量, 无论是局部的, 全局的还是类中的, 和其他变量稍微区别一下. k 后接大写字母开头的单词
   
   * 函数命名
      * 常规函数使用大小写混合, 取值和设值函数则要求与变量名匹配
      * 常规函数:函数名的每个单词首字母大写, 没有下划线
      * 取值和设值函数:取值和设值函数要与存取的变量名匹配

   * 名字空间命名
      * 名字空间用小写字母命名, 并基于项目名称和目录结构

   * 枚举命名
      * 枚举的命名应当和 常量 或 宏 一致: kEnumName 或是 ENUM_NAME

   * 宏命名
      * 你并不打算 使用宏, 对吧? 如果你一定要用, 像这样命名: MY_MACRO_THAT_SCARES_SMALL_CHILDREN.


   * 命名规则特例
      * 如果你命名的实体与已有 C/C++ 实体相似, 可参考现有命名策略


## 7.注释


   * 注释风格
      * 使用 // 或 /\* \*/, 统一就好.

   * 文件注释
      * 在每一个文件开头加入版权公告, 然后是文件内容描述
      * 每个文件都应该包含以下项, 依次是:
         * 版权声明 (比如, Copyright 2008 Google Inc.)
         * 许可证. 为项目选择合适的许可证版本 (比如, Apache 2.0, BSD, LGPL, GPL)
         * 作者: 标识文件的原始作者.
      * 通常, .h 文件要对所声明的类的功能和用法作简单说明. .cc 文件通常包含了更多的实现细节或算法技巧讨论 不要简单的在 .h 和 .cc 间复制注释. 这种偏离了注释的实际意义.
  
   *  类注释
      * 每个类的定义都要附带一份注释, 描述类的功能和用法.
      * 如果你觉得已经在文件顶部详细描述了该类, 想直接简单的来上一句 “完整描述见文件顶部” 也不打紧, 但务必确保有这类注释
      * 如果类有任何同步前提, 文档说明之. 如果该类的实例可被多线程访问, 要特别注意文档说明多线程环境下相关的规则和常量使用.

   * 函数注释
      * 函数声明处注释描述函数功能; 定义处描述函数实现.
      * 函数声明处注释的内容:
         * 函数的输入输出.
         * 对类成员函数而言: 函数调用期间对象是否需要保持引用参数, 是否会释放这些参数.
         * 如果函数分配了空间, 需要由调用者释放.
         * 参数是否可以为 NULL.
         * 是否存在函数使用上的性能隐患.
         * 如果函数是可重入的, 其同步前提是什么?

   * 变量注释
      * 常变量名本身足以很好说明变量用途. 某些情况下, 也需要额外的注释说明.
      * 类数据成员: 每个类数据成员 (也叫实例变量或成员变量) 都应该用注释说明用途. 如果变量可以接受 NULL 或 -1 等警戒值, 须加以说明
      * 全局变量:和数据成员一样, 所有全局变量也要注释说明含义及用途

   * 实现注释 
      * 对于代码中巧妙的, 晦涩的, 有趣的, 重要的地方加以注释.
      * 代码前注释:巧妙或复杂的代码段前要加注释
      * 行注释:比较隐晦的地方要在行尾加入注释. 在行尾空两格进行注释
      * 如果实现中出现的nullptr/NULL, true/false, 1,2,3… 没有被很好地说明其含义，那么使用注释说明这些数值的具体含义。

   * 标点，拼写和语法
      * 注意标点, 拼写和语法; 写的好的注释比差的要易读的多.

   * TODO注释
      * 对那些临时的, 短期的解决方案, 或已经够好但仍不完美的代码使用 TODO 注释.
      * `TODO 注释要使用全大写的字符串 TODO后面紧跟冒号空格 之后是todo内容，这样可以方便查找todo的标记，而且某些IDE（如XCode，Eclipse）能够自动识别出todo的注释`,例如
      
      		//TODO: (yourname@gmail.com) sth todo
      
      * 类似的还有FIXME注释，可以用于标记代码的潜在问题
   
   * Deprecation comment 废弃代码注释
      * 在要废弃的接口（一般为函数）上添加DEPRECATED注释， 可以在废弃的接口的上边，后者是声明的行
      * 使用全大写的`DEPRECATED` 并在其后的圆括号中填写你的名字，Email或者其他标识
      * 使用废弃注释的地方必须提供，替换废弃方法的实例
      * 在接口上添加了废弃注释不能改变任何事情，如果向阻止用户继续使用废弃的接口，你需要手动的修改调用的代码。
      * 新添加的代码不用改使用废弃的接口，使用替换的新接口。
      

## 8.格式


   * 行长度
      * 每一行代码字符数不超过 80. 行长度不要太长便于代码的阅读，太长的函数调用语句可以分成几行来写
   
   * 非 ASCII 字符
      * 尽量不使用非 ASCII 字符, 使用时必须使用 UTF-8 编码.

   * 空格还是制表位
      * 只使用空格, 每次缩进 2 或4个空格. `我喜欢4个空格`
      * 调整正你的编辑其设置，使制表符占用为2或4个空格并自动替换为空格
      
   * 函数声明与定义
      * 返回类型和函数名在同一行, 参数也尽量放在同一行.
      * 注意以下几点:
         * 返回值总是和函数名在同一行;
         * 左圆括号总是和函数名在同一行;
         * 函数名和左圆括号间没有空格;
         * 圆括号与参数间没有空格;
         * 左大括号总在最后一个参数同一行的末尾处;
         * 右大括号总是单独位于函数最后一行;
         * 右圆括号和左大括号间总是有一个空格;
         * 函数声明和实现处的所有形参名称必须保持一致;
         * 所有形参应尽可能对齐;
         * 缺省缩进为 2 个空格;
         * 换行后的参数保持 4 个空格的缩进;
      * 如果函数声明成 const, 关键字 const 应与最后一个参数位于同一行
      * `如果有些参数没有用到, 在函数定义处将参数名注释起来 如果在需要使用参数时可以方便的去掉注释`


   * 函数调用
      * 尽量放在同一行, 否则, 将实参封装在圆括号中.
      * 如果同一行放不下, 可断为多行, 后面每一行都和第一个实参对齐, 左圆括号后和右圆括号前不要留空格
      
   * 初始化列表 Braced Initializer List
      * 尽量放在同一行， 如果放不下，保证{是本行左后一个字符，}单起一行最为第一个字符 
      
   * 条件语句if
      * 倾向于不在圆括号内使用空格. 关键字 else 另起一行
      * 注意所有情况下 if 和左圆括号间都有个空格. 右圆括号和左大括号之间也要有个空格
      * if语句必须使用大括号括起来，即使只有一条语句， 只有一条语句且没有else是可写在同一行
      
      		if (condition) { statment; }
      
      
   * 循环和开关选择语句
      * switch 语句可以使用大括号分段. 空循环体应使用 `{}` 或 `continue`.而不是一个简单的分号
     
      		switch (var) {
  			  case 0: {  // 2 space indent
    			...      // 4 space indent
    			break;
  			  }
  			  case 1: {
    			...
    			break;
 			  }
  			  default: {
    			assert(false);
    		}
			
	* `switch的case语句块如果没有其他跳转语句（如return或continue）结束就必需包含break语句，如果没有需要添加注释（如FALLTHROUGH）明确的说明这里不需要break`
	* `switch语句都要添加default语句块，可以参考上面的代码块儿填写，方便捕捉没有处理的case`
      
   * 指针和引用表达式
      * 句点或箭头前后不要有空格. 指针/地址操作符 (*, &) 之后不能有空格
      * 在声明指针变量或参数时, 星号与类型或变量名紧挨都可以，采用一种方式，至少做到文件内统一

   * 布尔表达式
     * 如果一个布尔表达式超过 标准行宽, 断行方式要统一一下

   * 函数返回值
      * return 表达式中不要用圆括号包围 return x；

   * 变量及数组初始化
      * 用 = 或 () 或 {} 均可.
      * 使用`{}`时注意如果类型优先使用带initializer_list的构造函数进行初始化，如果不想用使用`()`进行初始化

   * 预处理指令
      * 预处理指令不要缩进, 从行首开始.

   * 类格式
      * 访问控制块的声明依次序是 public:, protected:, private:, 每次缩进 `2` 个空格.

   * 初始化列表
      * 构造函数初始化列表放在同一行或按四格缩进并排几行 我喜欢每个变量占一行

   * 名字空间格式化
      * 名字空间内容不缩进 风格不错，但好像IDE对这种格式支持不太好

   * 水平留白
      * 水平留白的使用因地制宜. 永远不要在行尾添加没意义的留白.

   * 垂直留白
      * 少使用垂直留白
      * 两个函数定义之间的空行不要超过 2 行, 函数体首尾不要留空行, 函数体中也不要随意添加空行


## 9.规则特例


   * 对于现有不符合既定编程风格的代码可以网开一面.

   * Windows 程序员有自己的编程习惯, 主要源于 Windows 头文件和其它 Microsoft 代码. 我们希望任何人都可以顺利读懂你的代码, 所以针对所有平台的 C++ 编程只给出一个单独的指南.
   
   
