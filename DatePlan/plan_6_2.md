
# c++ 类与对象知识点
## 访问限定符
### ｜ public、protected、private
---
- public 修饰的成员可以在类外通过对象直接访问
- private、protected 只能在类中通过成员函数进行访问
- 访问限定符的作用域从上一个开始到下一个结束
- struct 的默认访问权限为公有， class 的默认访问权限为私有
- private: 只能由该类中的函数、其友元函数访问,不能被任何其他访问，该类的对象也不能访问
- protected: 可以被该类中的函数、子类的函数、以及其友元函数访问,但不能被该类的对象访问
- public: 可以被该类中的函数、子类的函数、其友元函数访问,也可以由该类的对象访问
- 注：友元函数包括两种：设为友元的全局函数，设为友元类中的成员函数
- C++的类和对象的权限1
权限	|  类内部	|  该类对象	 |  子类（派生类）  |	友元函数
private	 |  可访问　 |	不可访问　　|   不可访问	 |  可访问
public	 |  可访问	 |  可访问	 |  可访问	 |  可访问
protected	|  可访问	 |  不可访问	|   可访问  |	可访问


**注意** ： 访问限定符只在编译时有用，当数据映射到内存后，没有任何访问限定符上的区别

### ｜ struct与class 的区别
---
- struct 兼容c ,可以作为结构体使用
- struct 的默认访问权限为公有， class 的默认访问权限为私有

### ｜ 封装
---
- 将数据和操作数据的方法进行有机结合，隐藏对象的属性和实现细节，仅对外公开接口来和对象进行 交互。


### ｜ 对象内存存储
---
- 根据创建方式的不同，对象存储位置也不同，通过类名定义的创建的对象存储在栈上 使用new 创建的对象存储在堆上
```
class Student{
    //...
};

Student stu; //栈上
Student* stu = new Student; //堆上

```
- 成员函数存储在代码段(存储可执行代码、字符串常量)

![内存布局](https://github.com/Mjianjianjiao/review/blob/master/Picture/v2-0d8aeed026fcb865e5f228ee8cbe6d7e_720w.jpeg)

```
系统内核
环境变量/命令行参数
栈:局部变量、函数传参,由高向低
堆: 动态分配内存, 由低向高
未初始化数据区
初始化数据区:初始化数据、静态变量、全局常量
文本段: 字符串常量
代码段: 已编译的机器指令
```
 
#### malloc\relloc\realloc\alloca
```
int* p1 = (int*) malloc(sizeof(int));
int* p2 = (int*)calloc(4, sizeof(int));
int* p3 = (int*)realloc(p2, sizeof(int)*10);
char *p4 = (char *)alloca(100*sizeof(char));
```
- malloc: 错误返回空指针，成功返回void* 指针指向的连续存储空间, 只需传递申请大小，不进行初始化，需调用memset 进行初始化
- calloc:可以指定申请的元素个数，并且按位初始化为0 
- relloc: 对传入的指针进行扩容，返回的地址可能与传入的地址不一致，扩容时如果当前指针后的空间不够，可能会重新找一个满足条件的空间，将数据拷贝到新的地址，旧的地址空间释放。写代码时不要忘记将原来的指针和现在的指针不一致
- alloca :在栈上开辟空间，自动释放

#### new/delete
---
- new 操作符申请内存 分为两步，** 1.申请内存 2.调用构造函数初始化对象**
    - new 底层是通过operator new进行内存申请，而operator new 底层调用malloc申请内存
    - 会初始化， 做类型安全检查
    ```
    void *__CRTDECL operator new(size_t size) throw (std::bad_alloc)
    {
        // try to allocate size bytes
        void *p;
        while ((p = malloc(size)) == 0) //申请空间
        if (_callnewh(size) == 0) //若申请失败则调用处理函数
        {
        // report no memory
        static const std::bad_alloc nomem;
        _RAISE(nomem); //#define _RAISE(x) ::std:: _Throw(x) 抛出nomem的异常
        }   
        return (p);
    }
    ```
    - operator new 由多个版本，所以new可以重载
    ```
    // 这4个版本可能抛出异常
    void *operator new(size_t);			// 分配一个对象
    void *operator new[](size_t);			// 分配一个数组
    void *operator delete(void*) noexcept;		// 释放一个对象
    void *operator delete[](void*) noexcept;	// 释放一个数组
    // 这些版本承若不会抛出异常
    void *operator new(size_t, nothorw_t &);		// 分配一个对象
    void *operator new[](size_t, nothorw_t &);		// 分配一个数组
    void *operator delete(void*, nothorw_t &) noexcept;	// 释放一个对象
    void *operator delete[](void*, nothorw_t &) noexcept;	// 释放一个数组
    ```
        - new 操作：申请内存，调用构造。
            ^ 
            ｜
        - operator new : 申请内存，抛出异常
            ^
            ｜
        - malloc 申请内存

- delete 调用析构函数， 释放内存
    - 底层由operator delete 实现，operator delete (会抛出异常)底层调用 free 
    ```
    void operator delete(void *pUserData)
    void operator delete(void *pUserData)
    {
    _CrtMemBlockHeader * pHead;
    RTCCALLBACK(_RTC_Free_hook, (pUserData, 0));
    if (pUserData == NULL)
    return;

    _mlock(_HEAP_LOCK); /* block other threads */
     __TRY
     /* get a pointer to memory block header */
     pHead = pHdr(pUserData);
    /* verify block type */
     _ASSERTE(_BLOCK_TYPE_IS_VALID(pHead->nBlockUse));
    _free_dbg( pUserData, pHead->nBlockUse );
    __FINALLY
     _munlock(_HEAP_LOCK); /* release other threads */
    __END_TRY_FINALLY
    return;
    }
    - delete 也有多个版本，可以重载
    ```

#### 定位new 表达式 placement new
- 给定一个已有的内存地址，在上面调用构造函数进行初始化对象。不分配内存

- 用法

```
new (place_address) type
new (palce_address) type (initializer-list)
new(address) type[size];
new(address) type[size]{braced initializer list};

void *buffer = ::operator new(sizeof(string));
buffer = new(buffer) string(“abd”);
```
- place_address必须是个指针，指向已经分配好的内存。为了使用这种形式的new表达式，必须包含头文件<new>
- 在需要反复创建删除对象时，使用定位new 表达式可以减少内存分配释放的消耗
- 申请的内存不再局限于堆，可以在指定地址区域(栈区、堆区、静态区)构造对象
- 在删除定位new 的表达式生成的对象时，需要显示的调用析构函数，需要释放内存时也需要使用对应的方式释放
```
std::string *sp = new std::string("a value");
sp->~string(); //显示调用，但不会释放内存
sp = new std::string("new value"); // 重新使用 sp 所指的内存空间进行对象的构造

delete sp; //释放内存
```
### ｜ 类的大小
---

- 类的大小与 普通的成员变量有关，与成员函数（存储在代码段）、静态数据成员（存储在静态区）、静态成员函数无关。

- 类大小计算遵循结构体对齐规则
    - 第一个成员在与结构体偏移量为0的地址处 
    - 其他成员变量要对齐到某个数字(对齐数)与变量类型长度比较的较小值的整数倍的地址处, 
       - 对齐数：编译器默认对齐数与该成员大小的较小值，vs下默认的对齐数为8，linux 默认的对齐数为 4
       - 修改默认对齐数
       ```
       #pragma pack(8)//将默认对齐数改为8
       ```
    - 结构体的总大小为（每一个成员变量都有一个最大对齐数）最大对齐数的整数倍
    - 结构体进行嵌套时，嵌套的结构体对齐到自己最大对齐数的整数倍，结构体总的大小就是最大对齐数的整数倍（包含嵌套结构体的对齐数）
    
    - **为什么要进行内存对齐？**
        - **平台原因（移植原因）**：不是所有的平台都可以在任意地址位置读取数据，有的平台只能在特定位置读取特定类型的数据
        - **性能原因** ：指令去数据时按照固定指令长度读取数据，为了访问未对齐的内存，访问一块数据时可能要访问两次，并将他们拼起来。
        - 空间换时间
        - 将占用空间小的内存尽可能的放在一起，减少因边界对齐而带来的损失。
        ```
        struct seqlist
        {
            int a;
            int b;
            char c;
        }//12

        struct p
        {
            char b;
            struct seqlist s;
            int a;
        }//24
        ```
        - 通过offsetof 宏确定结构中成员的实际位置,结构体中某个成员相对于结构体起始位置的偏移量
        ```
        offsetof(type,member)

        type:结构体类型 member：成员名

        返回值 size_t 偏移量 这个指定的成员的实际位置距离结构体开始存储位置偏移了几个字节数
        ```
    
- 空类的大小是一个特殊情况,空类的大小为1,用于占位
-  c++ 类的大小与 虚函数、虚继承均有关、后面再谈

### ｜ this 指针
 - this 指针类型: 类类型* const
 - this指针是类成员函数的第一个隐藏的参数,将对象的地址传递给成员函数
 - this 不存储在对象中，this中存储的就是对象的地址，会在调用类方法时候给方法传入了eax寄存器，通过ecx寄存器自动传递
 - this 指针可以为空，只要我们在成员函数中不对空的this指针操作即可，是可以调用成功成员函数的。但是，不能访问成员变量，NULL->val 程序蹦溃
```
    class A{
    public:
        void function()
        {
         cout << "I can run" << endl;
        }
    };

    int main()
    {
        A* pa = NULL;
        pa->function();
        system("pause");
        return 0;
    }

    //可以运行成功，相当与在成员函数的第一个参数传递类一个NULL指针
```
### ｜ 类的默认默认成员函数
---
- 一个空类中会自动生成六个默认的成员函数
    - 构造函数
        - 构造函数用于对成员变量进行初始化，并不开辟空间创建对象
        - 名字与类名相同，创建对象时由编译器自动调用，在对象的生命周期中只调用一次
        - 无返回值
        - 可以重载
        - 当没有显示定义构造函数时，会自动生成无参的构造函数，一旦显示定义就不会再生成，此时由我们自己无参的构造
        - 无参构造函数和全缺省的构造函数都可认为时默认的构造函数
        ```
        class A{
            public:
            A(){}
            //A(int x = 0){}
            A(int x){
                i_x = x;
            }

            A(const A& a){
                i_x = a.i_x;
            } //拷贝构造

            A& operator=(const A& a){
                if(this != &a){
                    i_x = a.i_x;
                }

                return *this;
            }
            A* operator&(){
                return this;
            }

            const A* operator&() const{
                return this;
            }
            ～A(){

            }
            private:
            int i_x;
        };

        int main(){
            A a1;
            A a2(a1); //调用拷贝构造
            return 0 ;
        }
        ```
    - 析构函数
        - 对象在销毁时会调用析构函数完成资源的清理
        - 析构函数的命名是在类名前加～
        - 无参数 无返回值
        - 一个类只有一个析构函数，未显示定义式，生成默认的析构函数
        - 析构函数释放资源，不释放内存，由delete完成
    - 拷贝构造函数
        - **拷贝构造函数的参数只有一个用(一般常用const修饰)且必须使用引用传参，使用传值方式会引发无穷递归调用(如果在传递参数是传值，可能会不停调用拷贝构造)**。
        - 若未显示定义，系统生成默认的拷贝构造函数，是构造函数的重载。 默认的拷贝构造函数对象按内存存储按字节序完成拷 贝，这种拷贝我们叫做浅拷贝，或者值拷贝
            - 深拷贝是指在拷贝指针时，不拷贝指针的地址，而是将指针地址上存储的数据拷贝了一遍。如果浅拷贝，就仅是将指针的地址复制了一遍，此时俩个指针指向一个内存地址，释放时会释放两遍，此时就会报错。
    - 赋值操作符重载
        - c++ 中可以通过operator 进行重载
        - .* 、:: 、**sizeof**、?: 、. 注意以上5个运算符不能重载
        - 复制运算符 参数类型是自己本身类型＿回*this , 检测是否给自己赋值
    - 取地址运算符重载
    - const取地址操作符重载

### | 初始化列表
---
- 构造函数只能初始化一次，但是在构造函数体内可以赋值多次
- 初始化列表:以一个冒号开始，接着是一个以逗号分隔的数据成员列表，每个"成员变量"后面跟一个放在括 号中的初始值或表达式。
```
class Test{
    public:
        Test(int a, int b, int c)
        :_a(a)
        ,_b(b)
        ,_c(c){
        }

        ~Test(){}
    private:
        int _a;
        int _b;
        char _c;
};

```
**注意**：
- 每个成员变量在初始化列表中只能出现一次(初始化只能初始化一次)
- 引用成员变量、const成员变量、类类型成员(该类没有默认构造函数) 必须放在初始化列表位置进行初始化:
```

class Test2{
    public:
        Test2(int a, int b,  Test c)
        :_a(a)
        ,_b(b)
        ,_c(c){
        }

        ~Test2(){}
    private:
        const int _a = 0;
        int& _b = 0;
        Test _c;
};



```
- 成员变量在类中声明次序就是其在初始化列表中的初始化顺序，与其在初始化列表中的先后次序无关
- c++ 11 非静态成员变量可以在声明时直接初始化

### ｜ 内部类
---
- 内部类: 一个类定义在另一个类的内部，就叫内部类
```
class A{
    public:
        class B{
            public:
                //..
        };
};
```
- 内部类是外部类的友元类，B是A的友元类，B 可以通过A的对象访问到A的public、protected、private成员变量，而A无法访问B的成员变量
- 注意内部类可以直接访问外部类中的static、枚举成员，不需要外部类的对象/类名
- sizeof(外部类)=外部类，和内部类没有任何关系。
    
### ｜ 友元函数和友元类
---
- 
    

### leetcode
[无重复字符最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)
[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)
### 智力题
