---
layout: post
title: Cocod2d-x C++ CodeStyle
category : basic
tagline: ""
tags : [codestyle, cocos2d, C++]
---
{% include JB/setup %}

# {{page.title}}

<p>{{ page.date | date_to_string}}</p>

###原文链接：
[cocos2d-x C++ CodeStyle](http://www.cocos2d-x.org/projects/cocos2d-x/wiki/Cocos2d_C++_Coding_Style)

###Google C++ CodeStyle
原文[Google's C++ coding style rev. 3.260](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)

翻译,有删减，部分内容有改动
[Google C++ CodeStyle](http://lijinlong.github.io/myblog/2013/08/22/Google-Cplusplus-Code-Style.html)


cocos2d-x C++ coding style 基于 [Google's C++ coding style rev. 3.260](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)
主要区别有：缺省参数（允许cocos2d-x中形如`createXXX` 和 `initXXX` 的函数使用缺省参数），RTTI（使用RTTI）， boost（不适用boost）变量命名（命名规则与Google略有不同）注释（使用了doxygon，deprecation comment有所不同）下面详细列举这部分，采用章节符号与原文一致便于查找

## 5.其他C++特性

   * 缺省参数
      * （+）在cocos2d-x中`createXXX`和`initXXX`方法允许使用缺省参数

   * 运行时类型识别RTTI （Run-Time Type Information）
      * （+）为了能够编译和运行cocos2d-x使用了RTTI，你要小心和不要滥用RTTI
      
   * Boost库
      * （!）不使用boost库
      * （+）为了提高代码的可读性和尽量减少cocos2d-x的依赖关系，我们不使用boost库
      
## 6.命名约定
   将这一节整个重新翻译了一下
   
   * 通用命名规则
      * 函数命名, 变量命名, 文件命名应具备描述性; 不要过度缩写. 类型和变量应该是名词, 函数名可以用 “命令性” 动词.
      * 类型和变量名一般为名词: 如 FileOpener, num_errors
      * 函数名通常是指令性的 (确切的说它们应该是命令), 如 OpenFile(), set_num_errors(). 取值函数是个特例 (在 函数命名 处详细阐述), 函数名和它要取值的变量同名
      * 除非该缩写在其它地方都非常普遍, 否则不要使用

            // OK
            int priceCountReader;     // No abbreviation.
            int numErrors;            // "num" is a widespread convention.
            int numDNSConnections;    // Most people know what "DNS" stands for.
            
            // BAD
            int n;                     // Meaningless.
            int nerr;                  // Ambiguous abbreviation.
            int nCompConns;            // Ambiguous abbreviation.
            int wgcConnections;        // Only your group knows what this stands for.
            int pcReader;              // Lots of things can be abbreviated "pc".
            int cstmrId;               // Deletes internal letters.

   * 文件命名
      * (!)文件名首字母大写（CamelCasel），但cocos2d的类的文件使用CC前缀
      * 不要使用已经存在于 /usr/include 下的文件名
            
            CCSprite.cpp
            CCTextureCache.cpp
            CCTexture2D.cpp

   * 类型命名
      * 类型名称的每个单词首字母均大写, 不包含下划线: MyExcitingClass, MyExcitingEnum.
      * 所有类型命名 —— 类, 结构体, 类型定义 (typedef), 枚举 —— 均使用相同约定
      
            // classes and structs
            class UrlTable { ...
            class UrlTableTester { ...
            struct UrlTableProperties { ...
            
            // typedefs
            typedef hash_map<UrlTableProperties *, string> PropertiesMap;
            
            // enums
            enum UrlTableErrors { …

   * 变量命名
      * (!)变量名除第一个单词外首字母大写（camelCasel）, 类的成员变量以下划线开头
      * (!)允许全部使用小写的变量
      
      * 普通变量
      
            string tableName;  // OK - uses camelcase
            string tablename;   // OK - all lowercase.
            
            string table_name;   // Bad - uses underscore.
            string TableNname;   // Bad - starts with Uppercase
      
      * 类成员变量
      
            string _tableName;   // OK
            string _tablename;   // OK

      * 结构体变量 
      
            struct UrlTableProperties {
                string name;
                int numEntries;
            }
      
      * 对全局变量没有特别要求, 少用就好, 但如果你要用, 可以用 g_ 或其它标志作为前缀, 以便更好的区分局部变量

   * 常量命名
      * (!)常量全部大写使用下划线分割
      * (+)不要使用#define定义常量
      * 优先使用强类型枚举（strongly typed enum）而不是const变量
      * 所有编译器常量都是用全部大写下划线分割的形式，包括但不限于局部的，全局的，类中的常量
      
             const int MENU_DEFAULT_VALUE = 10;
             const float GRAVITY = -9.8;
             
             enum class Projection {
                 ORTHOGONAL,
                 PERSPECTIVE
             };
             
             enum class PixelFormat {
                RGBA_8888,
                RGBA_4444,
                RGBA_5551,
                RGB_565,
             };
   
   * 函数命名
      * 常规函数使用大小写混合, 取值和设值函数则要求与变量名匹配
      * (!)常规函数:函数名的小写字母开头，气候每个单词首字母大写, 没有下划线
      
            addTableEntry()
            deleteUrl()
            openFileOrDie()
            
      * 取值和设值函数:取值和设值函数要与存取的变量名匹配
      
            class MyClass {
              public:
               ...
                int getNumEntries() const { return _numEntries; }
                void setNumEntries(int numEntries) { _numEntries = numEntries; }
             
              private:
                int _numEntries;
            };

   * 名字空间命名
      * 名字空间用小写字母命名, 并基于项目名称和目录结构

   * 枚举命名
      * 枚举的命名应当和 常量或宏一致ENUM_NAME
   
            enum class UrlTableErrors {
                OK = 0,
                ERROR_OUT_OF_MEMORY,
               ERROR_MALFORMED_INPUT,
            };

   * 宏命名
      * 你并不打算 使用宏, 对吧? 如果你一定要用, 像这样命名: MY_MACRO_THAT_SCARES_SMALL_CHILDREN.


   * 命名规则特例
      * 如果你命名的实体与已有 C/C++ 实体相似, 可参考现有命名策略
      
##7.注释

   * (+)Doxygen
      * 在头文件中使用Doxygen字符串，`不要`在实现文件中使用Doxygen指示
      * 所有公共类（public class）都`必须`有Doxygen注释解释类的功能
      * 类的公有函数不需使用Doxygen注释，override的方法可以例外，因为其父类中已经有说明 
      * `建议`但不强制类的受保护成员和私有成员方法添加Doxygen注释
      * 类的是实例变量`不要`添加Doxygen注释，除非他们是共有的
      
            /** WorldPeace extends Node by adding enough power to create world peace.
             *
             * WorldPeace should be used only when the world is about to collapse.
             * Do not create an instance of WorldPeace if the Scene has a peace level of 5.
             * 
             */
            class WorldPeace : public Node
            {
             
              public:
                 /** creates a WorldPeace with a predefined number of preachers
                  */
                 static WorldPeace* create(int numberOfPreachers);
               
                 /** sets the number of preachers that will try to create the world peace.
                  The more the better. But be aware that corruption might appear if  the number if higher than the 20% of the population.
                 */
                 void setNumberOfPreachers(int numberOfPreachers);
                 
                /** displays an aura around the WorldPeace object */
                void displayAura();
                
                // Overrides
                virtual void addChild(Node * child) override;
                virtual void removeChild(Node* child, bool cleanup) override;
                
              protected:
                WorldPeace();
                virtual ~WorldPeace();
                bool init(int nubmerOfPreachers);
                
                int _nubmerOfPreachers;
            };
      
   * 废弃注释
       * (+)使用CC_DEPRECATED_ATTRIBUTE宏标记废弃的方法
       * 同时使用@deprecated@doxygen文档字符串在文档中进行标记
       * 使用废弃注释的地方必须提供，替换废弃方法的实例
       * 在接口上添加了废弃注释不能改变任何事情，如果向阻止用户继续使用废弃的接口，你需要手动的修改调用的代码。
       * 新添加的代码不用改使用废弃的接口，使用替换的新接口。
          