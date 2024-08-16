## 进入modern cpp
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
书中举了一个很有趣的例子。一个vector在不停pushback，当其空间不够的时候，c98会重新allocate一个地方然后进行复制操作，而c11会尝试进行move操作。但是这个move可能是不安全的，比如move到第n个的时候，突然出异常了，之前move的n-1个也不可能复原。因此在确保不会出exception前，不会用m  ove操作。

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

## 智能指针
#### Item17：unique pointer
unique pointer可以自定义析构函数，此时㤇把这个方法传入unique ptr的参数列表里std::unique_ptr<Investment, decltype(delInvmt)>
decltype可以避免去算delInvmt的类型
```cpp
auto delInvmt = [](Investment* pInvestment) //自定义删除器 
{ //（lambda表达式） 
	makeLogEntry(pInvestment); delete pInvestment; 
};
template<typename... Ts> std::unique_ptr<Investment, decltype(delInvmt)> //更改后的返回类型 
makeInvestment(Ts&&... params) { 
	std::unique_ptr<Investment, decltype(delInvmt)> 
	pInv(nullptr, delInvmt); //应返回的指针 
	return pInv; 
}
```
自定义析构器会增大unique指针大小，变成2个word
`std::unique_ptr`是轻量级、快速的、只可移动（_move-only_）的管理专有所有权语义资源的智能指针

unique转shared
```cpp
std::shared_ptr<Investment> sp =            //将std::unique_ptr
    makeInvestment(arguments);              //转为std::shared_ptr
```

#### shared ptr
shared ptr通过引用计数实现，所以存在以下三个问题：
- shared ptr大小是普通ptr的两倍以上
- shared ptr需要给引用计数动态分配内存
- shared ptr增减计数的时候需要原子操作，比较慢

![[Pasted image 20230910102531.png]]
shared ptr在内存的样子，其中绿色部分是记录引用计数和自定义析构器等东西的控制块

值得注意的是，控制块在用原始指针构造shared ptr时，会创建控制块。因此，**不能用同一个东西的原始指针创建多个shared ptr**
当类中需要用shared ptr管理this的时候，需要让类继承std::enable_shared_from_this

==`std::shared_ptr`不能处理的数组==

#### weak ptr
weak ptr是shared ptr的加强版，为了解决一些shared ptr不得不面临的悬空问题
weak ptr无法解引用，也无法测试是否为nullptr
访问weak ptr的办法是把其转化为shared ptr，这个操作是原子的。
方法1：
```cpp
std::shared_ptr<Widget> spw1 = wpw.lock();  //如果wpw过期，spw1就为空
 											
auto spw2 = wpw.lock();                     //同上，但是使用auto

```
方法2：
```CPP
std::shared_ptr<Widget> spw3(wpw);          //如果wpw过期，抛出std::bad_weak_ptr异常

```

#### 优先考虑make shared和make unique而非new

## 左值与右值
#### move和forward
move和forward的本质都是cast。move是把一个左值变成右值，foreward把一个右值初始化的参数变成右值

当希望用move达成移动构造的时候，不要使用const，const很有可能隐式转成拷贝构造函数。

理论上我们可以全用forward，但move好写一点

#### 区分通用引用和右值引用
有时候&&也可以左值引用，此时称为通用引用。
两种情况：函数模板和auto声明。存在类型推导的时候存在通用引用
````cpp 
template <typename T> void f(T&& param); 
auto&& val2 = var1;
````
#### 右值引用用move，通用引用用forward
move可能会导致左值消失
```cpp
class Base{
public:
	string name;
	template <typename T>
	void set_name(T&& n){
		this->name = forward<T>(n); // 如果这里是move main中的s会在使用会变回未定义
	}
};
int main(){
	Base b;
	string s = "abc";
	b.set_name(s);
	cout << b.name << endl;
	cout << s << endl;
	return 0;
}
```
#### 避免通用引用使用重载
通用引用用到了模板，但是其使用时与模板无关（比如上节中我是明确知道且只期望set_name改变一个string变量），只是希望编译器推断。
因此重载通用引用函数非常危险。比如：
```cpp
std::string name;
std::vector<string> names;
std::string get_name_from_idx(int i){
	return names[i];
}
template <typename T>
void set_name(T&& n){
	name = forward<T>(n); 
}

void set_name(int idx){
	name = forward<T>(get_name_from_idx(idx)); 
}
```
此时如果传入了一个short，这个参数不会隐式转换调用函数2，而是调用函数1

解决方案：
- 不用重载
- 用const T&这种c98经典方式
- 按值传递
- tag dispatch。类似下面
```cpp
template<typename T>
void logAndAdd(T&& name)
{

  logAndAddImpl(std::forward<T>(name),
	  std::is_instegral<typename std::remove_reference<T>::type>());
}
```

```cpp
template<typename T>
void logAndAddImpl(T&& name, std::false_type) //
{
  auto now = std::chrono::system_clock::now();
  log(now, "logAndAdd");
  names.emplace(std::forward<T>(name));
}
```
其中std::false_type是一个tag，是一个编译时就能决定true false的tag，从而可以提前编译

#### 引用折叠
编译器有时候会搞出来引用的引用，此时会自动折叠。右值会变成右值引用，左值会变成左值引用。

#### move操作可能不廉价或没有被使用

## Lambda表达式
#### 避免使用默认捕获模式
