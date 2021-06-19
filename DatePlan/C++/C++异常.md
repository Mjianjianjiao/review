### 异常
#### **c++异常处理方式**
> c++ 使用 try 、 throw 、 catch 来处理异常
- throw : 当程序出现异常时，通过throw抛出异常
- catch : catch捕捉异常 ，可以有多个catch来进行捕捉
- try : try 块中的代码标识将被激活的特定异常,它后面通常跟着一个或多个 catch 块。
```
try
{
   // 保护代码
}catch( ExceptionName e1 )
{
   // catch 块
}catch( ExceptionName e2 )
{
   // catch 块
}catch( ExceptionName eN )
{
   // catch 块
}
```

```
double division(int a, int b)
{
   if( b == 0 )
   {
      throw "Division by zero condition!";
   }
   return (a/b);
}
 
int main ()
{
   int x = 50;
   int y = 0;
   double z = 0;
 
   try {
     z = division(x, y); //可能会抛出异常的代码
     cout << z << endl;
   }catch (const char* msg) {
     cerr << msg << endl;
   }
 
   return 0;
}
```
- 异常通过抛出的对象而引发，**对象的类型**决定了应该激活那个catch的处理代码。如上抛出的是字符串就用const char* 来捕获。
- 被选中的处理代码是调用链中与该对象类型匹配且离抛出异常位置最近的那一个
- 抛出异常对象后，会生成一个异常对象的拷贝，因为抛出的异常对象可能是一个临时对象，所以会生成一个拷贝对象，这个拷贝的临时对象会在被catch以后销毁。（这里的处理类似于函数的传值返回）
- catch(…)可以捕获任意类型的异常，问题是不知道异常错误是什么
- 实际中抛出和捕获的匹配原则有个例外，并不都是类型完全匹配，可以抛出的派生类对象，使用基类捕获。

#### **函数调用链中异常的匹配规则**
1. 首先检查抛出异常的throw是否在try 内，如果在就找响应的catch匹配，匹配到了就在响应的catch 处理
2. 如果未匹配到相应的catch,就跳出当前函数栈，然后再进行catch 匹配
3. 如果到达main函数的栈，依旧没有匹配的，则终止程序
4. 实际操作中使用catch(…)捕获任意类型的异常, 如果异常没有被捕获会导致程序终止, 默认操作 terminate 是调用 abort

> 函数栈展开进行异常查找是在编译时期进行, 运行时查找会造成开销

##### 异常捕获后重新抛出
```
double Division(int a, int b)
{
// 当b == 0时抛出异常
if (b == 0)
{
throw "Division by zero condition!";
}
return (double)a / (double)b;
}
void Func()
{
// 这里可以看到如果发生除0错误抛出异常，另外下面的array没有得到释放。
// 所以这里捕获异常后并不处理异常，异常还是交给外面处理，这里捕获了再
// 重新抛出去。
int* array = new int[10];
try {
    int len, time;
    cin >> len >> time;
    cout << Division(len, time) << endl;
}
catch (...)  //捕获后重新抛出
{
    cout << "delete []" << array << endl;
    delete[] array;
    throw;
}
    // ...
    cout << "delete []" << array << endl;
    delete[] array;
}
int main()
{
    try
    {
    Func();
    }
catch (const char* errmsg)
{
    cout << errmsg << endl;
}
    return 0;
}
```
##### **异常安全处理**
- 尽量不要在构造函数中抛出异常，防止初始化不完整
- 尽量不要在析构函数中抛出异常，防止资源释放不彻底，造成内存泄漏
- 可能因为异常的抛出而导致的资源的泄漏问题
    - 在锁中抛出异常
    - 在文件open/close之间
    - 在内存new/delete 之间

##### 异常使用规范
- 在函数中加 throw()  （c++11后）noexcept / noexcept(true) 表明不会抛出异常
```
void fun() throw();
void fun() noexcept;
void fun() noexcept(true);
// 这里表示这个函数不会抛出异常
void* operator delete (std::size_t size, void* ptr) throw();
```
- 在函数中加 throw(type) 表示会抛出对应类型的异常
```
// 这里表示这个函数只会抛出bad_alloc的异常
void* operator new (std::size_t size) throw (std::bad_alloc);
```
- 若无异常接口声明 或者 使用throw(...)/noexcept(false)，则此函数可以抛掷任何类型的异常


##### c++标准异常
- C++语言本身或者标准库抛出的异常都是 exception 的子类，称为标准异常（Standard Exception）。你可以通过下面的语句来捕获所有的标准异常：
```
try{
    //可能抛出异常的语句
}catch(exception &e){
    //处理异常的语句
}
```
- exception 类位于 <exception> 头文件中，它被声明为：
```
class exception{
public:
    exception () throw();  //构造函数
    exception (const exception&) throw();  //拷贝构造函数
    exception& operator= (const exception&) throw();  //运算符重载
    virtual ~exception() throw();  //虚析构函数
    virtual const char* what() const throw();  //虚函数
}
```


|异常|描述
|---:|---:|
std::exception |	该异常是所有标准 C++ 异常的父类。
std::bad_alloc |	该异常可以通过 new 抛出。
std::bad_cast  |该异常可以通过 dynamic_cast 抛出。
std::bad_exception	|这在处理 C++ 程序中无法预期的异常时非常有用。
std::bad_typeid	| 该异常可以通过 typeid 抛出。
std::logic_error | 	理论上可以通过读取代码来检测到的异常。
std::domain_error |	当使用了一个无效的数学域时，会抛出该异常。
std::invalid_argument	| 当使用了无效的参数时，会抛出该异常。
std::length_error |	当创建了太长的 std::string 时，会抛出该异常。
std::out_of_range |  该异常可以通过方法抛出，例如 std::vector 和 std::bitset<>::operator[]()。
std::runtime_error |	理论上不可以通过读取代码来检测到的异常。
std::overflow_error	|  当发生数学上溢时，会抛出该异常。
std::range_error |	当尝试存储超出范围的值时，会抛出该异常。
std::underflow_error | 	当发生数学下溢时，会抛出该异常。

##### **自定义异常体系**
- c++ 中一般使用继承体系来处理异常，通过抛出派生类异常对象，catch 中基类接收对象。
- 我们可以通过继承和重载 exception 类来定义新的异常
```
#include <iostream>
#include <exception>
using namespace std;

struct MyException : public exception
{
  const char * what () const throw ()
  {
    return "C++ Exception";
  }
};
 
int main()
{
  try
  {
    throw MyException();
  }
  catch(MyException& e)
  {
    std::cout << "MyException caught" << std::endl;
    std::cout << e.what() << std::endl;
  }
  catch(std::exception& e)
  {
    //其他的错误
  }
}
```