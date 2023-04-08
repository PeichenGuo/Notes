### Genral
c11新特性
```
[capture list] (params list) mutable exception-> return type { function body }
```
各项具体含义如下

1.  capture list：捕获外部变量列表
2.  params list：形参列表
3.  mutable指示符：用来说用是否可以修改捕获的变量
4.  exception：异常设定
5.  return type：返回类型
6.  function body：函数体

也可以省略部分
```
[capture list] (params list) -> return type {function body}
[capture list] (params list) {function body}
[capture list] {function body}
```

### 捕获
#### **1、值捕获**

值捕获和参数传递中的值传递类似，被捕获的变量的值在Lambda表达式创建时通过值**拷贝**的方式传入，因此随后对该变量的修改不会影响影响Lambda表达式中的值。
```c++
int main()
{
    int a = 123;
    auto f = [a] { cout << a << endl; }; 
    a = 321;
    f(); // 输出：123
}
```
#### **2、引用捕获**

使用引用捕获一个外部变量，只需要在捕获列表变量前面加上一个引用说明符&。
```c++
int main()
{
    int a = 123;
    auto f = [&a] { cout << a << endl; }; 
    a = 321;
    f(); // 输出：321
}
```
#### **3、隐式捕获**

上面的值捕获和引用捕获都需要我们在捕获列表中显示列出Lambda表达式中使用的外部变量。除此之外，我们还可以让编译器根据函数体中的代码来推断需要捕获哪些变量，这种方式称之为隐式捕获。隐式捕获有两种方式，分别是[=]和[&]。[=]表示以值捕获的方式捕获外部变量，[&]表示以引用捕获的方式捕获外部变量。
```c++
int main()
{
    int a = 123;
    auto f = [=] { cout << a << endl; };    // 值捕获
    f(); // 输出：123

	int a = 123;
    auto f = [&] { cout << a << endl; };    // 引用捕获
    a = 321;
    f(); // 输出：321
}
```

[]
不捕获任何外部变量

[变量名, …]

默认以值得形式捕获指定的多个外部变量（用逗号分隔），如果引用捕获，需要显示声明（使用&说明符）

[this]

以值的形式捕获this指针

[=]

以值的形式捕获所有外部变量

[&]

以引用形式捕获所有外部变量

[=, &x]

变量x以引用形式捕获，其余变量以传值形式捕获

[&, x]

变量x以值的形式捕获，其余变量以引用形式捕获

