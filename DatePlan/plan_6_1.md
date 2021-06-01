
## 6-1 日复习计划
### C/C++ 
#### 关键字
#### const 
-----
1. c语言中的const
- const 修饰变量： 表明该变量是只读的，不能修改。在c中使用const进行修改时必须进行初始化，修饰函数形参除外。
- const修饰指针： 指针常量与常量指针
```
const int *p; //指针p所指向的变量不能变

int* const p; // 指针本身的地址不能变，改地址所存储的内容可变

const int* const p; //两者都不可变
```
2. c++ 中的const
- **const修饰变量：** 在定义const全局变量后，编译后可能会被对应的常量值替换，与宏定义类似，但是后有变量的类型检查
- **const 修饰类对象:** 该对象不能被改变，不能改变对象的成员函数，不能调用非const成员函数。
- **const 修饰成员变量:** const 修饰的成员变量必须使用构造函数的初始化列表进行初始化。static const 成员变量在类外定义初始化（c 语言中const 与 static 不能一起用，因为生命周期不同）
- **const 修饰类成员函数：** 一般在成员函数后加const 表示，该函数不应该修改成员变量的值，如果有修改成员变量的操作编译时会报错。但是，当该成员变量被mutable修饰时，可以在cosnt 成员函数中进行修改。

- const 对象可以调用非const成员函数吗?为什么？
```
不可以。因为对象在调用成员函数时，成员函数的第一个参数是隐藏的this 指针，如果是非const 成员函数，其this 指针的类型是 type* const this， 而const 对象对应传进的指针是const type*  ,无法完成匹配所以不能调用非const成员函数。而const 成员函数对应的this 形参为 const type* const this 与const对象的指针相对应，所以可以调用 
```
- 非const对象可以调用const成员函数吗？
```
可以。c++ 有最小权限的原则，非const对象可以调用const 成员函数。例如 int* p 可以转成 const int* p, 而const int* p 不能转成int* p 
```
----- 


####  static
---
- c语言中
    - **修饰变量：** 修饰全局变量，数据存储在静态区，文件之外不可见，未初始化时，初始化为0,作用域为全局。修饰局部变量，数据存储在静态区，生命周期随程序，作用域为局部。

    - **修饰函数：** 修饰函数改变其可见性，表明只能在本文件中使用，外部文件不可见
-  c++ 中
    - **修饰成员变量：** static 声明的成员变量，表明该变量是属于整个类的，而不属于某一个对象。在程序运行期间只有一个副本，所以不能在定义对象时对其初始化，不能通过构造函数进行初始化。只能在类外初始化。
    ```
    class test{

        private:
            static int i; //类中声明 
    };

    int test::i = 0; //类外定义

    //定义时须指明::类名表明属于哪一个类 ，此时不加static 防止与声明的static 的变量冲突

    //静态成员变量是为了解决数据共享的问题，可以节省内存，更新一次，所有的对象都可以获取。

    //调用方式
    1. 通过对象调用
    2. 通过类名:: 调用
    ```
    
    
    - **修饰成员函数：** 属于类的成员函数，不单独属于某一个对象。在成员函数前加 static 。在类的静态成员函数中不能使用非静态成员函数变量，因为静态成员函数中不含this 指针，所以与this 指针相关的都不可以。
    - 与静态成员变量相同，静态成员函数可以直接访问。
    
---

#### voliate

- 确保本条指令不会因为编译器的优化而省略，而且要求每次从内存中直接读取值。
    
    -  使用voliate 声明的变量，使系统总是从内存中读取数据，防止因为编译器的优化将值存进寄存器，从而读取到脏数据。当内存中有数据变动，而寄存器中的数据未更新时易读取到脏数据。

#### [explicit](https://zhuanlan.zhihu.com/p/52152355)

#### [extern](https://hellozhaozheng.github.io/z_post/Cpp-extern%E5%85%B3%E9%94%AE%E5%AD%97/)
    注意: extern声明不是定义，不分配存储空间
#### [friend](http://c.biancheng.net/cpp/biancheng/view/211.html)

#### final
- **修饰类：** 在类名后直接加final 表明该类禁止被继承
- **修饰虚函数：** 表明该函数禁止被重写

#### [inline](https://zhuanlan.zhihu.com/p/151995167)

#### [mutable](https://liam.page/2017/05/25/the-mutable-keyword-in-Cxx/)

#### [new/delete](https://blog.csdn.net/hazir/article/details/21413833?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control)
[与malloc/free区别](https://blog.csdn.net/u010510020/article/details/76266505)

#### [register](https://blog.csdn.net/M_jianjianjiao/article/details/80149790?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162251143716780271581655%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=162251143716780271581655&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-1-80149790.nonecase&utm_term=register&spm=1018.2226.3001.4450)

#### [this](https://blog.csdn.net/u011939264/article/details/51544129?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)

#### [reinterpret_cast、const_cast、static_cast、dynamic_cast](https://my.oschina.net/feistel/blog/3000199)

#### [override](https://blog.csdn.net/xiaoheibaqi/article/details/51272009) 

[参考1](https://blog.csdn.net/tostq/article/details/52718737?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-1&spm=1001.2101.3001.4242)

[参考2](https://blog.csdn.net/K346K346/article/details/53887827)

#### 面试题 
- c/c++ 中 struct 的区别？
- struct 和 class 的区别？
- c/c++ 中 static 和const 的区别？
- const char *p和char * const p; 的区别
-  delete与 delete []区别
- new 可以重载吗？
- new 和 malloc / free 和delete 的区别
- c++ 4种cast类型转换的区别
- volatile的作用
- const和#define有什么区别，大多数情况下那种更优？
- “extern C”、C++中引用编译过的C代码为什么需要“extern C”
- sizeof是函数还是关键字？
- new/delete和malloc/free会初始化内存和清空内存内容吗？
### leetcode
- [两数之和](https://leetcode-cn.com/problems/two-sum/)
- [两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

**注意**: 时间复杂度、空间复杂度

### 智力题
[一共12个一样的小球， 其中只有一个重量与其它不一样(未知轻重)，给你一个天平，找出那个不同重量的球？]()