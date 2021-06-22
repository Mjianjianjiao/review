# c++ 四种强制类型转换介绍

const_cast , static_cast , dynamic_cast , reinterpret_cast

## C风格的强制转换

C风格的强制转换(Type Cast)容易理解,不管什么类型的转换都可以使用使用下面的方式.

`TypeName b = (TypeName)a;`

当然,C++也是支持C风格的强制转换,但是C风格的强制转换可能带来一些隐患,让一些问题难以察觉.所以C++提供了一组可以用在不同场合的强制转换的函数.

## C++ 四种强制转换类型函数

### const_cast

- 常量指针被转化成非常量的指针，并且仍然指向原来的对象；
- 常量引用被转换成非常量的引用，并且仍然指向原来的对象；
- const_cast一般用于修改指针。如const char *p形式。

```c++
#include<iostream>

int main(){
    // 原始数组
    int ary[4] = { 1,2,3,4 };
    // 打印数据
    for (int i = 0; i < 4; i++)
        std::cout << ary[i] << "\t";
    std::cout << std::endl;

    // 常量化数组指针
    const int*c_ptr = ary;
    //c_ptr[1] = 233;   //error

    // 通过const_cast<Ty> 去常量
    int *ptr = const_cast<int*>(c_ptr);

    // 修改数据
    for (int i = 0; i < 4; i++)
        ptr[i] += 1;    //pass

    // 打印修改后的数据
    for (int i = 0; i < 4; i++)
        std::cout << ary[i] << "\t";
    std::cout << std::endl;
    return 0;
}
```

out print:

```
1   2   3   4
2   3   4   5
```

注意:对于在定义为常量的参数,使用const_cast可能会有不同的效果.类似代码如下

```c++
#include<iostream>
int main() {
    const int c_val = 233;  //声明为常量类型
    int &use_val = const_cast<int&>(c_val); //使用去const 引用
    int *ptr_val = const_cast<int*>(&c_val);//使用去const 指针
    use_val = 666;  //未定义行为
    std::cout << c_val << "\t" << use_val << "\t" << *ptr_val << std::endl;
    *ptr_val = 110; //未定义行为
    std::cout << c_val << "\t" << use_val << "\t" << *ptr_val << std::endl;
    return 0;
}
```

在 vs2017 下 输出为:

```
233 666 666
233 110 110
```


未定义行为:C++标准对此类行为没有做出明确规定.同一份代码在使用不同的编译器会有不同的效果.在 vs2017 下, 虽然代码中 c_val , use_val , ptr_val 看到的地址是一样的.但是c_val的值并没有改变.有可能在某种编译器实现后,这一份代码的c_val 会被改变.也有可能编译器对这类行为直接 error 或 warning.

### static_cast

static_cast 作用和C语言风格强制转换的效果基本一样，由于没有运行时类型检查来保证转换的安全性，所以这类型的强制转换和C语言风格的强制转换都有安全隐患。
用于类层次结构中基类（父类）和派生类（子类）之间指针或引用的转换。注意：进行上行转换（把派生类的指针或引用转换成基类表示）是安全的；进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。
用于基本数据类型之间的转换，如把int转换成char，把int转换成enum。这种转换的安全性需要开发者来维护。
static_cast不能转换掉原有类型的const、volatile、或者 __unaligned属性。(前两种可以使用const_cast 来去除)
在c++ primer 中说道：c++ 的任何的隐式转换都是使用 static_cast 来实现。

```c++
/* 常规的使用方法 */
float f_pi=3.141592f
int   i_pi=static_cast<int>(f_pi); /// i_pi 的值为 3

/* class 的上下行转换 */
class Base{
    // something
};
class Sub:public Base{
    // something
}

//  上行 Sub -> Base
//编译通过，安全
Sub sub;
Base *base_ptr = static_cast<Base*>(&sub);  

//  下行 Base -> Sub
//编译通过，不安全
Base base;
Sub *sub_ptr = static_cast<Sub*>(&base);    
```

### dynamic_cast

dynamic_cast强制转换,应该是这四种中最特殊的一个,因为他涉及到面向对象的多态性和程序运行时的状态,也与编译器的属性设置有关.所以不能完全使用C语言的强制转换替代,它也是最常有用的,最不可缺少的一种强制转换.



```c++
#include<iostream>
using namespace std;

class Base{
public:
    Base() {}
    ~Base() {}
    void print() {
        std::cout << "I'm Base" << endl;
    }
    virtual void i_am_virtual_foo() {}
};

class Sub: public Base{
public:
    Sub() {}
    ~Sub() {}
    void print() {
        std::cout << "I'm Sub" << endl;
    }
    virtual void i_am_virtual_foo() {}
};

int main() {
    cout << "Sub->Base" << endl;
    Sub * sub = new Sub();
    sub->print();
    Base* sub2base = dynamic_cast<Base*>(sub);
    if (sub2base != nullptr) {
        sub2base->print();
    }
    cout << "<sub->base> sub2base val is: " << sub2base << endl;
    cout << endl << "Base->Sub" << endl;
    Base *base = new Base();
    base->print();
    Sub  *base2sub = dynamic_cast<Sub*>(base);
    if (base2sub != nullptr) {
        base2sub->print();
    }
    cout <<"<base->sub> base2sub val is: "<< base2sub << endl;

    delete sub;
    delete base;
    return 0;
}
```

vs2017 输出为下：

```
Sub->Base
I'm Sub
I'm Base
<sub->base> sub2base val is: 00B9E080   // 注:这个地址是系统分配的,每次不一定一样

Base->Sub
I'm Base
<base->sub> base2sub val is: 00000000   // VS2017的C++编译器,对此类错误的转换赋值为nullptr
```

从上边的代码和输出结果可以看出:
对于从子类到基类的指针转换 ,dynamic_cast 成功转换,没有什么运行异常,且达到预期结果
而从基类到子类的转换 , dynamic_cast 在转换时也没有报错,但是输出给 base2sub 是一个 nullptr ,说明dynami_cast 在程序运行时对类型转换对“运行期类型信息”（Runtime type information，RTTI）进行了检查.
这个检查主要来自虚函数(virtual function) 在C++的面对对象思想中，虚函数起到了很关键的作用，当一个类中拥有至少一个虚函数，那么编译器就会构建出一个虚函数表(virtual method table)来指示这些函数的地址，假如继承该类的子类定义并实现了一个同名并具有同样函数签名（function siguature）的方法重写了基类中的方法，那么虚函数表会将该函数指向新的地址。此时多态性就体现出来了：当我们将基类的指针或引用指向子类的对象的时候，调用方法时，就会顺着虚函数表找到对应子类的方法而非基类的方法。因此注意下代码中 Base 和 Sub 都有声明定义的一个虚函数 ” i_am_virtual_foo” ,我这份代码的 Base 和 Sub 使用 dynami_cast 转换时检查的运行期类型信息,可以说就是这个虚函数

### reinterpret_cast

reinterpret_cast是强制类型转换符用来处理无关类型转换的，通常为操作数的位模式提供较低层次的重新解释！但是他仅仅是重新解释了给出的对象的比特模型，并没有进行二进制的转换！
他是用在任意的指针之间的转换，引用之间的转换，指针和足够大的int型之间的转换，整数到指针的转换，在在面的文章中将给出.
请看一个简单代码

```c++
#include<iostream>
#include<cstdint>
using namespace std;
int main() {
    int *ptr = new int(233);
    uint32_t ptr_addr = reinterpret_cast<uint32_t>(ptr);
    cout << "ptr 的地址: " << hex << ptr << endl
        << "ptr_addr 的值(hex): " << hex << ptr_addr << endl;
    delete ptr;
    return 0;
}
```

```
ptr 的地址: 0061E6D8
ptr_addr 的值(hex): 0061e6d8
```

上述代码将指针ptr的地址的值转换成了 unsigned int 类型的ptr_addr 的整数值.
提供下IBM C++ 对 reinterpret_cast 推荐使用的地方
A pointer to any integral type large enough to hold it （指针转向足够大的整数类型）
A value of integral or enumeration type to a pointer （从整形或者enum枚举类型转换为指针）
A pointer to a function to a pointer to a function of a different type （从指向函数的指针转向另一个不同类型的指向函数的指针）
A pointer to an object to a pointer to an object of a different type （从一个指向对象的指针转向另一个不同类型的指向对象的指针）
A pointer to a member to a pointer to a member of a different class or type, if the types of the members are both function types or object types （从一个指向成员的指针转向另一个指向类成员的指针！或者是类型，如果类型的成员和函数都是函数类型或者对象类型）

下面这个例子来自 MSDN 的一个哈希函数辅助

```c++
// expre_reinterpret_cast_Operator.cpp  
// compile with: /EHsc  
#include <iostream>  

// Returns a hash code based on an address  
unsigned short Hash(void *p) {
    unsigned int val = reinterpret_cast<unsigned int>(p);
    return (unsigned short)(val ^ (val >> 16));
}
using namespace std;
int main() {
    int a[20];
    for (int i = 0; i < 20; i++)
        cout << Hash(a + i) << endl;
}
```

## 结尾

在使用强制转换的时候,请先考虑清楚我们真的需要使用强制转换和我们应该使用那种强制转换。
