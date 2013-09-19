---
layout: post
title: C++函数参数传值与传引用测试
category : basic
tagline: ""
tags : [C++, 参数传递]
---
{% include JB/setup %}

# C++参数传值与传引用测试
下面的代码控制台CClog的输出是什么？共创建了几个对象？

        namespace test {
            static int TestObj_count;
            class TestObj {
              public:
                explicit TestObj(int i) : _i(i)
                {
                    TestObj_count++;
                    CCLog("constructor");
                }
                virtual ~TestObj()
                {
            
                }
                int getI() const { return _i; }
                void setI(int i) { _i = i; };
        
                static int getObjCount() { return TestObj_count; }
                static void resetObjCount() { TestObj_count = 0;}
            private:
                int _i;
            };
            void testPassByObj(TestObj obj)
            {
                CCLog("pass value is %d, %p", obj.getI(), &obj);
            }
    
            void testPassByRef(const TestObj& obj)
            {
                CCLog("pass ref is %d, %p", obj.getI(), &obj);
            }
        }

        int main()
        {
            test::TestObj::resetObjCount();
            test::TestObj obj(12);
            CCLog("gen object value is %d, %p", obj.getI(), &obj);
            test::testPassByObj(obj);
            CCLog("after, obj %d origin is 12, gen obj %d", obj.getI(), test::TestObj::getObjCount());
    
            test::TestObj::resetObjCount();
            test::TestObj obj2(120);
            CCLog("gen object value is %d, %p", obj2.getI(), &obj2);
            test::testPassByRef(obj2);
            CCLog("after, obj %d origin is 12, gen obj %d", obj2.getI(), test::TestObj::getObjCount());
        }

