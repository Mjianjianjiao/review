[STL](#STL)
- [迭代器](#迭代器)
- [序列式容器](#序列式容器)
- [关联式容器](#关联式容器)
- [适配器](#适配器)
- [仿函数](#仿函数)
- [空间适配器](#空间适配器)
- [其他](#迭代器)
    - [string](#string)
    
# STL
## STL介绍

C++ STL（标准模板库）是一套功能强大的 C++ 模板类，提供了通用的模板类和函数，这些模板类和函数可以实现多种流行和常用的算法和数据结构，如向量、链表、队列、栈等。

STL的一个重要特点就是数据结构和算法的分离。例如，STL中sort()函数是完全通用的，你可以用它来操作几乎任何数据集合，包括链表，容器和数组。

STL另一个重要特性是它不是面向对象的，主要依赖于模版，而不是封装和继承

## 常用基本组件

**容器:** 容器是用来管理某一类对象的集合。C++ 提供了各种不同类型的容器，比如 deque、list、vector、map 等。容器分为序列式容器和关联式容器，STL提供了三个序列式容器：向量（vector）、双端队列（deque）、列表（list），此外你也可以把 string 和 array 当做一种序列式容器；STL提供了四个关联式容器：集合（set）、多重集合（multiset）、映射（map）和多重映射（multimap）。

**迭代器:** 迭代器提供了访问容器中对象的方法。例如，可以使用迭代器指定list或vector中的一定范围的对象。

**算法:** 算法是用来操作容器中的数据的模板函数。它们提供了执行各种操作的方式，包括对容器内容执行初始化、排序、搜索和转换等操作。由此可见，算法和迭代器组件都是作用于容器的。其中大部分算法都包含在头文件 <algorithm> 中，少部分位于头文件 <numeric> 中。

**适配器:** 可以使一个类的接口（模板的参数）适配成用户指定的形式，从而让原本不能在一起工作的两个类工作在一起。值得一提的是，容器、迭代器和函数都有适配器。

**仿函数:** 如果一个类将 () 运算符重载为成员函数，这个类就称为函数对象类，这个类的对象就是函数对象（又称仿函数）。

**空间适配器:** 为容器类模板提供自定义的内存申请和释放功能，由于往往只有高级用户才有改变内存分配策略的需求，因此内存分配器对于一般用户来说，并不常用。

---
## 迭代器
---
## 其他
### string
#### 常用函数
#### **成员函数**
-  构造函数
```c++
std::string s(4, '=');// "===="

std::string const other("Exemplary");
std::string s(other, 0, other.length()-1); // "Exemplar"
  
std::string s("C-style string", 7);//"C-style"

std::string s("C-style\0string");// "C-style"

char mutable_c_str[] = "another C-style string";
std::string s(std::begin(mutable_c_str)+8, std::end(mutable_c_str)-1);

std::string const other("Exemplar");
std::string s(other); // "Exemplar"

std::string s(std::string("C++ by ") + std::string("example"));// "C++ by example"

std::string s({ 'C', '-', 's', 't', 'y', 'l', 'e' }); // "C-style"

std::string s(3, std::toupper('a')); // "AAA"
```
- **assign :**  为字符串赋值
```c++
// assign(size_type count, CharT ch) 用count个ch来替换s
s.assign(4, '=');  // "====" 

std::string const c("Exemplary");
// assign(basic_string const& str) 将c 赋值给s
s.assign(c); //"Exemplary"

// assign(basic_string const& str, size_type pos, size_type count) 将str从pos开始的count个字符赋值给s
s.assign(c, 0, c.length()-1);//"Exemplar"

// assign(charT const* s, size_type count) 分配字符串的前count 的个字符给s
s.assign("C-style string", 7); // "C-style"

// assign(charT const* s) 会在\0出停止
s.assign("C-style\0string");// "C-style"

// assign(InputIt first, InputIt last) 使用迭代器来初始化
s.assign(std::begin(mutable_c_str), std::end(mutable_c_str)-1);

// assign(std::initializer_list<charT> ilist) 
s.assign({ 'C', '-', 's', 't', 'y', 'l', 'e' })
```
#### **元素访问**
- **at :**
```c++
//返回给定下标的引用,超出范围抛出异常
reference at( size_type pos );

string s("abcd");
s.at(1) = "x"; //s = "axcd"
```c++
- **operator[]** 返回对应位置字符引用
```c++
reference operator[]( size_type pos );
cout<<s[0]; //"a"
```
- **front** 返回首字符引用
```c++
s.front(); //"a"
```
- **back** 返回尾字符引用
```c++
s.back(); //"d"
```
- **data :** 返回底层数组指针
- **c_str :** 返回c类型的const字符串
c++11 后data 与 c_str 功能相同
#### **迭代器**
|迭代器|用法
|---:|---:|
| begin |返回指向字符开始的迭代器|
| end   |返回指向结尾的迭代器
| rbegin| 逆向迭代器
| rend  |
| cbegin/crbegin| 返回const 型迭代器
| cend/crend  |
![image](https://user-images.githubusercontent.com/38885360/122936763-fea09980-d3a3-11eb-8e34-5c93c8b3eb72.png)
#### **容量**
|函数|用法
|---:|---:|
| empty |s.empty() 字符串为空返回true，否则false|
| size  |s.size() 返回有效字符的长度
|length |s.length 与size相同
| max_size| s.max_size()返回当前系统 或 库所限制的最大元素个数
| reserve  |s.reserve(new_capacity) 重新分配对象存储空间,可扩容或缩容
| capacity| s.capacity()返回当前对象存储容量
| shrink_to_fit |s.shrink_to_fit()释放未使用长度，使得capacity 到size长度
#### **元素操作**
|函数|用法
|---:|---:|
| clear      |s.clear() 清理字符串字符，不释放内存|
| insert     | 向字符串中插入
```c++
(1)insert(size_type index, size_type count, char ch) //在index 位置插入count 个字符 ch

(2)insert(size_type index, const char* s)\insert(size_type index, string& s) //在index处插入字符串\string

(3)insert( size_type index, const CharT* s, size_type count ); //在位置 index 插入范围 [s, s+count) 中的字符。范围能含有空字符。

(4)
{
iterator insert( iterator pos, CharT ch ); //在pos 前插入ch
iterator insert( const_iterator pos, size_type count, CharT ch );
\\在pos前插入count 个ch
}

(5)insert(const_iterator pos, InputIt first, InputIt last)//在pos 前插入 [first,last]的字符
```
|    |   |
|---:|---:|
| erase   | 删除字符串中元素  |
```c++
(1)erase( size_type index = 0, size_type count = npos ); //删除从pos 起的count 个元素

(2)iterator erase( iterator position ); //删除迭代器位置

(3)iterator erase( iterator first, iterator last ); //删除迭代器范围
```
|||
|---:|---:|
| push_back  | s.push_back('a'); 向字符结尾插入一个字符|
| pop_back   | s.pop_back(); 移除字符串末尾字符
| append     | 向字符串后追加
```c++
(1)s.append(3, '*'); //向后追加3个*

(2)s.append(str);  //向后追一个string 或者 c字符串

(3)append( const basic_string& str,size_type pos, size_type count); //追加str 从 pos 开始的count个字符

(4)basic_string& append( const CharT* s, size_type count ); 追加C字符串的一部分，从头开始
output.append(1, ' ').append(carr, 4); //可以链式调用

(5)basic_string& append( InputIt first, InputIt last );//追加一个范围
```
|||
|---:|---:|
| operator+= | 字符串追加可以使用+= 的操作
| compare    | 
```c++
 - (1) s.compare(str);  s == str 返回值等于 0 , s < str 返回值小于0 , s > str 返回值大于0; 
 - (2) s.compare(pos, count, str); // s [pos, pos+count]的字串与str比较
 - (3) s.compare(pos1, count1, str, pos2, count2); //s 的子串 与 str 的子串相比较
 - str 可为string 或者c 字符串
```
|||
|---:|---:|
| substr     |  s.substr(pos,count);//从pos 开始 count 个字符的子串, pos默认值为0 |
| copy       |  size_type copy( CharT* dest, size_type count, size_type pos = 0 ) const; s.copy(dest,count); //拷贝s 字符串count个字符到dest目标字符串，默认位置从0 开始 
| replace    | 
| resize     | 重新设置string 的大小以含 count 个字符。
```c++
(1) void resize( size_type count );
s.resize(size); //若当前大小大于 count，则缩短至count字符
(2) void resize( size_type count, CharT ch );
s.resize(count,ch)//若当前大小小于 count ，则后附额外的字符。
```
|||
|---:|---:|
| swap       | 两string 进行交换 s.swap(str);

#### **查找**
- find: 查找字符串中的字符或者字串，返回第一个字符下标，未找到返回 npos
```
// 查找某一字符串
s.find(str);
//查找某一字符
s.find('a');
//从某一下标位置开始查找
s.find("is", pos);
```

#### **非成员函数**
- **operator+** ：字符串连接
```c++
s = s1 + s2;
```
- stoi\stol\stoll 字符串转整形
- stoul\stoull 转换字符串为无符号整数
- stof/stod/stold 字符串转换浮点型
```c++
string str = "3.25";
float f = stof(str);
double d = stod(str);
long double = stold(str);
```

- to_string 整数或浮点型转换为string
```c++
str = to_string(3.25);
```

---
## 序列式容器
### vector 
#### 底层实现
#### 常用函数
### array (c++11)
### 
---
## 关联式容器
---
## 适配器
---
## 仿函数
---
## 空间适配器

