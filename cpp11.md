# cpp11

### 类型推导

#### auto

auto的自动类型推导，用于从初始化表达式中推断出变量的数据类型。从这个意义上讲，auto并非一种“类型”声明，而是一个类型声明时的“占位符”，**编译器**在编译时期会将auto替换为变量实际的类型

定义变量时必须初始化，否则无法推导

vs2013不支持auto是函数形参

auto不能作为自定义类型的成员变量

不能是auto数组

模板实例化不能是auto类型

```c++
auto x = 1;      // x的类型为int
auto y = foo();  // y的类型为double,foo是double foo() {}
struct m { int i; }str;
auto str1 = str;    // str1的类型是struct m
auto z;     // err, 无法推导，无法通过编译
auto b[3]={1,2,3};	//err
vector<auto>b={1};	//err
```

#### decltype

decltype实际上有点像auto的反函数， auto可以让你声明一个变量，而decltype则可以从一个变量或表达式中得到其类型

```c++
#include <typeinfo>
int i;
decltype(i) j = 0;		//通过推导i是int，将j声明为int
cout << typeid(j).name() << endl;   // 打印出int
float a;
double b;
decltype(a + b) c;
cout << typeid(c).name() << endl;   // 打印出double
vector<int> vec;
typedef decltype(vec.begin()) vectype; // 通过推导，将vectype定义为迭代器类型
vectype k;  							//iterator k;  
for (k = vec.begin(); k < vec.end(); k++)
{
    // 做一些事情
}
```

#### 追踪返回类型

返回类型后置：在函数名和参数列表后面指定返回类型

```c++
int func(int, int);
auto func2(int, int) -> int;	//指定返回值为int
auto func3(int a,double b)	->decltype(a+b);	//推导结果是double

template<typename T1, typename T2>
auto sum(const T1 &t1, const T2 &t2) -> decltype(t1 + t2)	//auto不能用于模板，但配合追踪返回类型可以实现自动推导
{    return t1 + t2;}
template <typename T1, typename T2>
auto mul(const T1 &t1, const T2 &t2) -> decltype(t1 * t2)
{    return t1 * t2;}
int main()
{
    auto a = 3;
    auto b = 4L;
    auto pi = 3.14;

    auto c = mul( sum(a, b), pi );
    cout << c << endl;  // 21.98
    return 0;
}
```

### 易用性改进

#### 变量初始化

##### 类内成员初始化

```c++
class Mem
{
public:
    Mem(int i): m(i){} //初始化列表给m初始化
    int m;
};
class Group
{
public:
    Group(){}
private:
    int data = 1;   	// 使用"="初始化非静态普通成员，也可以 int data{1};
    int data2{1};
    Mem mem{2};	// 对象成员，创建对象时，可以使用{}来调用构造函数
    string name{"mike"};
};
```

##### 列表初始化

```c++
int a[]{1, 3, 5};	//内置类型初始化
int i = {1};  
int j{3}; 
struct Person  
{  
  std::string name;  
  int age;  
};  
int main()  
{  
    Person p = {"Frank", 25};  	//列表初始化
    std::cout << p.name << " : " << p.age << std::endl;  
}
std::vector<int> ivec1(3, 5);  
std::vector<int> ivec2 = {5, 5, 5};  
std::vector<int> ivec3 = {1,2,3,4,5}; //不使用列表初始化用构造函数难以实现
```

##### 防止类型收窄

类型收窄指的是导致数据内容发生变化或者**精度丢失**的隐式类型转换。使用列表初始化可以防止类型收窄，编译阶段进行报错

部分编译器可能仍然允许通过

```c++
int main(void)
{
    const int x = 1024;
    const int y = 10;

    char a = x;                 // 收窄，但可以通过编译,但内容丢失
    char* b = new char(1024);   // 收窄，但可以通过编译

    char c = { x };             // err, 收窄，无法通过编译
    char d = { y };             // 可以通过编译
    unsigned char e{ -1 };      // err,收窄，无法通过编译

    float f{ 7 };               // 可以通过编译
    int g{ 2.0f };              // err,收窄，无法通过编译
    float * h = new float{ 1e48 };  // err,收窄，无法通过编译
    float i = 1.2l;                 // 可以通过编译

    return 0;
}
```

#### 基于范围的for循环

使用基于范围的for循环，其for循环迭代的范围必须是可确定的

```c++
int func(int a[])//形参中数组是指针变量，无法确定元素个数 int *a同理
{
    for(auto e: a) // err, 编译失败
    {
        cout << e;
    }
}
int main()
{
    int a[5] = { 1, 2, 3, 4, 5 };
    for (int & e: a)
    {
        e *= 2;
    }
    for (int & e: a)
    {
        cout << e << ", ";
    }
    cout << endl;
    int b[] = {1, 2, 3, 4, 5};
    func(b);
    return 0;
}

```

#### 静态断言

C/C++提供了调试工具assert，这是一个宏，用于在**运行阶段**对断言进行检查，如果条件为真，执行程序，否则调用abort()

静态断言则是在**编译阶段**就执行

```c++
int main(){
    bool flag=false;
    assert(flag==true);	//运行时检查
    //该static_assert用来确保编译仅在32位的平台上进行，不支持64位的平台
    static_assert( sizeof(void *)== 4, "64-bit code generation is not supported."); 
    cout << "Hello World!" << endl;
    return 0;
}
```

#### noexcept

```c++
void func3() throw(int, char) //只能够抛出 int 和char类型的异常
{//C++11已经弃用这个声明
     throw 0;
}
void BlockThrow() throw() //代表此函数不能抛出异常，如果抛出，就会异常
{
    throw 1;
}
//代表此函数不能抛出异常，如果抛出，就会异常
//C++11 使用noexcept替代throw()
void BlockThrowPro() noexcept
{
    throw 2;
}
```

#### nullptr

nullptr是为了解决原来C++中NULL的二义性问题而引进的一种新的类型，因为NULL实际上代表的是0

#### 强类型枚举

C++ 11引入了一种新的枚举类型，即“枚举类”，又称“强类型枚举”。声明请类型枚举非常简单，只需要**在enum后加上使用class或struct**。

“传统”的C++枚举类型有一些缺点：它会在一个代码区间中抛出枚举类型成员（如果在相同的代码域中的两个枚举类型具有相同名字的枚举成员，这会导致命名冲突），它们会被隐式转换为**整型**，并且不可以指定枚举的底层数据类型。

在C++11中，强类型枚举解决了这些问题：

```c++
int main()
{
    enum Status1{Ok, Error};
    //enum Status2{Ok, Error};//err, 导致命名冲突, Status已经有成员叫Ok, Error

    enum class Status {Ok, Error};
    enum struct Status2{Ok, Error};

    //Status flag2 = Ok; // err，必须使用强类型名称
    Status flag3 = Status::Ok;	//加上枚举作用域

    enum class C : char { C1 = 1, C2 = 2};//指定枚举的底层数据类型
    enum class D : unsigned int { D1 = 1, D2 = 2, Dbig = 0xFFFFFFF0U };
    return 0;
}
```

#### 常量表达式

常量表达式主要是允许一些计算发生在编译时，即发生在代码编译而不是运行的时候。这是很大的优化：假如有些事情可以在编译时做，它将只做一次，而不是每次程序运行时都计算

使用constexpr，你可以创建一个编译时的函数：

```c++
int GetNum(){
    return 1;
}
constexpr int GetConst(){
    return 3;
}
int main(){
    //enum {e1=GetNum(),e2};	//报错
    int arr[ GetConst() ] = {0};
    enum { e1 = GetConst(), e2 };

    constexpr int num = GetConst();
    return 0;
}
```

constexpr函数的限制：

l 函数中只能有一个return语句（有极少特例）

l 函数必须返回值（不能是void函数）

l 在使用前必须已有定义

l return返回语句表达式中不能使用非常量表达式的函数、全局数据，且必须是一个常量表达式

常量表达式的构造函数有以下限制：

l 函数体必须为空

l 初始化列表只能由常量表达式来赋值

#### 用户定义字面量/自定义后缀
