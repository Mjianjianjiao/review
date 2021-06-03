####  只能在栈上创建对象的类
---
- 只能在栈上创建对象，就是只能使用静态的方式创建对象，不能使用new 在堆上创建对象
- new 创建对象需要调用operator new , 只需要将operator new  和 operator delete 设置为私有即可 
    - 方法1
    ```
    class OnlyStack{
        public:
            OnlyStack(){}
            ~OnlyStack(){}
        private: 
            void * operator new(size_t size){}
            void operator delete(void *ptr){}
    };
    ```
    - 方法2
    - 构造函数私有化
    ```
    class OnlyStack{
    public:
        static OnlyStack Create(){
            return OnlyStack();
        }
    private:
        OnlyStack(){}
    };
    ```
####  只能在堆上创建对象的类
---
- 不能在栈上创建对象，只能在堆上创建对象
- 方法1: 将构造函数私有化，提供只能用new静态的创建对象的接口
```
class OnlyHeap{
    public:
        static OnlyHeap* Create(){
            return new OnlyHeap();
        }
    private:
        OnlyHeap(){}
};
```
- 方法2: 将析构函数私有化，编译器在创建对象时会检查析构函数函数的访问权限看是否能够调用，如果不能调用就无法创建。 但是实现多态时需要将析构函数设置成virtul 以完成重写。如果设置为private就无法继承，所以设置成protected 
```
class OnlyHeap{
    public:
        static OnlyHeap* Create(){
            return new OnlyHeap();
        }

        void destory(){ //通过调用在类外释放内存
            delete this;
        }
    protected:
        ~OnlyHeap(){}
};
```

#### delete this 合法吗？
---
- 合法
- 但是要保证 this 对象是由new 分配的（new 与 delete配对）。
- 保证调用delete this的成员函数是最后一个调用，后面不会再调用其他的成员函数
- 后面不再操作delete this

### ｜ 内存泄漏
---
- 是指在内存申请后没有被释放导致该内存块被永远的占用。
- 几种内存泄漏的方式：
    - 1. 常发性内存泄漏。发生内存泄漏的代码会被多次执行到，每次被执行的时候都会导致一块内存泄漏。
    - 2. 偶发性内存泄漏。发生内存泄漏的代码只有在某些特定环境或操作过程下才会发生。
    - 3. 一次性内存泄漏。发生内存泄漏的代码只会被执行一次，或者由于算法上的缺陷，导致总会有一块仅且一块内存发生泄漏。
    - 4. 隐式内存泄漏。程序在运行过程中不停的分配内存，但是直到结束的时候才释放内存。严格的说这里并没有发生内存泄漏，因为最终程序释放了所有申请的内存。但是对于一个服务器程序，需要运行几天，几周甚至几个月，不及时释放内存也可能导致最终耗尽系统的所有内存。所以，我们称这类内存泄漏为隐式内存泄漏。
    - 内存泄漏堆积导致最后系统的内存被耗尽。

#### 检测内存泄漏
---
- linux 下使用mtrace 分析内存泄漏
    - mtrace是Glibc的一部分，无须特殊安装。只要在要检测的代码中添加mtrace 钩子函数，就会生成内存分配的调试跟踪信息
    ```
    #include <mcheck.h>

    void mtrace(void); // 开启内存分配跟踪
    void muntrace(void); //取消内存分配跟踪

    #include<stdio.h>
    #include<stdlib.h>
    #include<mcheck.h>

    int main(int argc, char **argv)
    {
        mtrace();

        char *p = malloc(16);

        free(p);

        p = malloc(32);

        muntrace();

        return 0;
    }
    ``` 
    - 先定义一个环境变量来指明生成日志的路径export MALLOC_TRACE=./memleak.log
    - 然后运行程序
    - 日志内容:记录了内存的分配与释放
    ```
    = Start
    @ ./demo_mtrace_memleak:[0x7f6ca1400738] + 0x7fffc93796a0 0x10
    @ ./demo_mtrace_memleak:[0x7f6ca1400748] - 0x7fffc93796a0
    @ ./demo_mtrace_memleak:[0x7f6ca1400752] + 0x7fffc93796c0 0x20
    = End
    ```
    - 使用mtrace 命令分析日志, mtrace这个工具需要至少两个参数，一个是生成的可执行程序文件的路径，还有一个是日志文件的路径
    ```
    # mtrace ./demo $MALLOC_TRACE

    Memory not freed:   
    -----------------
              Address     Size     Caller
    0x00007fffc93796c0     0x20  at 0x7f6ca1400752
    ```
    - 可以检测未释放内存、重复释放内存
- linux 下 valgrind 中的memcheck 检测内存错误
    - 可以检测内存泄漏
    - 可以检测内未释放内存的使用
    - 对已释放的内存的读写
    - 重复释放内存
    - 不匹配使用new/new[]/delete/ delete[]/ malloc/free
    - 使用野指针（未给指针分配内存却使用了，内存已经释放继续使用该指针）
    - 动态内存越界访问
- 使用方式 linux 安装valgrind
- g++编译程序时编译加-g  命令： valgrind --tool=memcheck ./a.out  选择memcheck 工具 执行可执行程序即可, 各种内存问题均有提示
![示例](https://user-images.githubusercontent.com/38885360/120576595-a128c500-c455-11eb-8876-23754675303c.png)

#### 避免内存泄露
---
- 

#### 内存溢出

### ｜ 指针与引用
---
#### 空指针与野指针

### ｜ C 与 c++ 的联系
--- 

### ｜ C 实现 C++ 类
--- 

### ｜ 继承
--- 

### ｜ 多态
--- 
