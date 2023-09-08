####  Item5:优先考虑auto而非显式声明
#### Item6: 有时候auto推断会奇怪，此时要用显式声明

#### Item7：区分（）和{}
{}：统一化赋值/初始化
优点：可以在隐式变窄转换的时候报warning或者error
缺点：会和initialize_list搞混
```c++
class Widget {
public:

    Widget(int i, bool b);     //
    Widget(std::initializer_list<long double> il);      //
};
Widget w1(10, true); // 第一个构造函数

Widget w2{10, true}; //应该用第二个，但编译器会把大括号处理成initialize_list然后调用第二个构造函数，10和true都会被转换
```

#### prefer nullptr to 0 and NULL

#### prefer alias to typedef
alias的好处是可以用template，typedef用不了
```c++
// alias
template<typename T> using MyAllocList = std::list<T,MyAlloc<T>>;
MyAllocList<Widget> lw;
// typedef
template<typename T>
class Widget {
private:
    typename MyAllocList<T>::type list;
};
```
#### prefer 限域枚举 to 非限域枚举
旧的enum中成员的作用域和enum的作用域一样大
```c++
enum Color { black, white, red }; 
auto white = false; // ！white已被声明
```
因此可以用限域枚举
```c++
enum class Color { black, white, red }; 
```
值得一提的是，非限域枚举仍有一席之地：可以当做tuple field tag。比如，我有一个tuple三元组(first name, second name, middle name)程序员很难一直记得index和其内容的对应关系，此时可以用一个enum来标记field
```c++
enum NameField{FirstName, SecondName, MiddleName};
```
#### 隐藏函数的时候 prefer delete to 私有声明
```c++
// 98的写法
class c{
private:
	c(){}; //隐藏默认构造函数
}
// 11的写法
class c{
public:
	c() = delete; //隐藏默认构造函数
}
```

#### 函数重载要显示写override
下面任何一个“重载”都不会报错，因为编译器会以为是重写。因此要重载的时候一定要加override
```c++
class Base {
public:

    virtual void mf1() const;
    virtual void mf2(int x);
    virtual void mf3() &;
    void mf4() const;

};
class Derived: public Base {
public:

    virtual void mf1();
    virtual void mf2(unsigned int x);
    virtual void mf3() &&;
    void mf4() const;

};
```
#### prefer const_iterator to iterator
const_iterator不会更改it指向的内容，但是可以对容器进行操作。
```cpp
std::vector<int> values; //  
auto it =   cbeginstd::find(values.cbegin(),values.cend(), 1983); // cend 
values.insert(it, 1998);
```

#### 如果函数无异常，则使用noexcept
noexcept可以优化编译效率和程序效率
书中举了一个很有趣的例子。一个vector在不停pushback，当其空间不够的时候，c98会重新allocate一个地方然后进行复制操作，而c11会尝试进行move操作。但是这个move可能是不安全的，比如move到第n个的时候，突然出异常了，之前move的n-1个也不可能复原。因此在确保不会出exception前，不会用move操作。

#### 尽可能使用constexpr
constexpr表示：不仅是常量，而且**编译器可知**

这在变量中一定如此。constexpr的对象是编译期可知的const，而const有可能编译期不可知

在函数中，如果参数编译器不可知，constexpr就是普通函数，但如果参数编译期可知，constexpr会在编译期求出值
c11中constexpr只能有一行，c14中放宽了

c11中，constexpr函数也是隐式的const函数，不能修改对象。c14无所谓


#### item16: const 线程安全
*没太get这和const有啥关系*

#### item17：特殊成员函数
**特殊成员函数**是指C++自己生成的函数。C++98有四个：默认构造函数，析构函数，拷贝构造函数，拷贝赋值运算符。c11多两个：移动构造函数和移动赋值运算符

Rule of Three规则。这个规则告诉我们如果你声明了拷贝构造函数，拷贝赋值运算符，或者析构函数三者之一，你应该也声明其余两个。

声明析构和拷贝会关掉默认的移动，如果需要移动，则手动声明；vice versa。

声明后可以用= default使用默认实现
```cpp
class Base{
public: 
	virtual ~Base() = default; //使析构函数virtual 
	Base(Base&&) = default; //支持移动 
	Base& operator=(Base&&) = default; 
	Base(const Base&) = default; //支持拷贝 
	Base& operator=(const Base&) = default; 
};
```
