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

或者叫“自定义后缀”，主要作用是简化代码的读写。通过自定义字面量后缀，允许整数、浮点数、字符及字符串字面量产生用户定义类型的对象

如果使用这种写法，`_cm`, `_m`, `_mm` 等函数将在运行时被调用，如果希望在编译时就调用字面量后缀函数，则需要把函数定义为 `constexpr`

根据 C++ 11 标准，只有下面参数列表才是合法的：

```c++
char const *;
unsigned long long;
long double;
/*以下四种，第二个参数会自动推断为字符串的长度*/
char const *, size_t;
wchar_t const *, size_t;
char16_t const *, size_t;
char32_t const *, size_t;
```

```c++
size_t operator"" _len(char const * str, size_t size)
{
    return size;
}
int main()
{
    cout << "mike"_len <<endl; //结果为4
	return 0;
}
```

#### 原生字符串字面值

原生字符串字面值(raw string literal)使用户书写的字符串“所见即所得”。C++11中原生字符串的声明相当简单，只需在字符串前加入前缀，即**字母R**，并在引号中**使用括号左右标识**，就可以声明该字符串字面量为原生字符串了

```c++
cout<<R"(hello,\n)"<<endl;
string str=R"(hello \n
	abc,123
	world\r)";	//会将括号内所有内容文本输出，包括回车，空格等
```

### 类的改进

#### 继承构造

C++ 11允许派生类继承基类的构造函数（默认构造函数、复制构造函数、移动构造函数除外）

```c++
/*A的构造函数包含两个参数*/
class B : public A
{
public:
    //B(intx,int y):A(x,y){}	//原始写法    
    using A::A; 				// 继承构造函数
    virtual void ExtraInterface(){}
};
int main(){	
    B obj(1,2);
    return 0;
}
```

注意：

l 继承的构造函数只能初始化基类中的成员变量，不能初始化派生类的成员变量

l 如果基类的构造函数被声明为**私有**，或者派生类是从基类中**虚继承**，那么不能继承构造函数

l 一旦使用继承构造函数，编译器不会再为派生类生成默认构造函数

#### 委托构造

如果一个类包含多个构造函数，C++ 11允许在一个构造函数中的定义中**使用另一个**构造函数，但这必须通过**初始化列表**进行操作，如下：

```c++
class Test{
    public:
    	Test(int x):Test(x,'a'){};
    	int a;
    	char b;
    private:
    	Test(int x,char y):a(x),b(y){};
}
int main(){
    Test obj(10);//构造后a为10，b为'a'
}
```

#### 继承控制

##### final

组织类的进一步派生，虚函数的进一步重写

```c++
class A1 final{
    int a;
};
class A2:public A1{};	//无法通过编译
```

##### override

确保在派生类中声明的函数跟基类的虚函数有相同的签名

```c++
class A1{
    public:
    virtual void func(int a){};
}
class A2:public A1{
    public:
    virtual void func() override{};//由于参数和基类不一样，编译不通过，返回值同样限制
}
```

#### 类默认函数的控制

##### =default

如果程序员为类显式的自定义了非默认构造函数，编译器将不再会为它隐式地生成默认**无参**构造函数

程序员只需在函数声明后加上“=default;”，就可将该函数声明为 "=default"函数，编译器将为显式声明的 "=default"函数自动生成函数体。**效率比用户写的高**

```c++
class X{
    public:
    X()=default;	//以前若想用无参构造需要用户另外重写
    X(int i){a=i};
    int a;
}
X::X()=default;	//类外同样可用
```

"=default"函数特性仅适用于类的特殊成员函数，且该特殊成员函数没有默认参数，包含：**默认构造函数、析构函数、拷贝构造函数以及拷贝赋值运算符**

##### =delete

让程序员显式的禁用某个函数，程序员只需在函数声明后上“=delete;”，就可将该函数禁用。

```c++
class X
{
public:
    X();
    X(const X&) = delete;  // 声明拷贝构造函数为 deleted 函数
    X& operator = (const X &) = delete; // 声明拷贝赋值操作符为 deleted 函数
};
int main()
{
    X obj1;
    X obj2=obj1;   // 错误，拷贝构造函数被禁用
    X obj3;
    obj3=obj1;     // 错误，拷贝赋值操作符被禁用

    return 0;
}
```

#### 模板的改进

##### 右尖括号>改进

此前版本连续两个右尖括号(>>)会被编译解释成右移操作符，而不是模板参数表的形式，需要一个空格进行分割，以避免发生编译时的错误。

在C++11标准中，要求编译器对模板的右尖括号做单独处理，使编译器能够正确判断出">>"是一个右移操作符还是模板参数表的结束标记

```c++
/*此前标准*/
Y<X<1> > x1;    // ok, 编译成功
Y<X<2>> x2;     // err, 编译失败
```

##### 模板的别名

此前通过typedef给类型起别名，不能新建类型

```c++
typedef int int32
using sint = int;
cout<<is_same<int32,sint>::value;	//判断两个类型是否相等
```

##### 函数模板的默认模板参数

C++11之前，类模板是支持默认的模板参数，却不支持**函数模板**的默认模板参数

```c++
//1、普通函数带默认参数，c++98 编译通过，c++11 编译通过
void DefParm(int m = 3) {}
//2、类模板是支持默认的模板参数，c++98 编译通过，c++11 编译通过
template <typename T = int>
class DefClass {};
//3、函数模板的默认模板参数， c++98 - 编译失败，c++11 - 编译通过
template <typename T = int> void DefTempParm(T a) {}
```

**类模板**的默认模板参数必须**从右往左**定义，函数模板的默认模板参数则没这个限定

```c++
template<class T1, class T2 = int> class DefClass1;
template<class T1 = int, class T2> class DefClass2;   // 无法通过编译
```

### 可变参数的模板

在C++11之前，类模板和函数模板只能含有固定数量的模板参数。C++11增强了模板功能，允许模板定义中包含0到任意个模板参数，这就是可变参数模板。

可变参数模板和普通模板的语义是一样的，只是写法上稍有区别，声明可变参数模板时需要在typename或class后面带上省略号“...”

```c++
template<class ... T> 
void func(T ... args)//T叫模板参数包，args叫函数参数包
{
    cout<<sizeof...(args)<<endl;	//获取参数个数
    /*可变参数模板函数*/}
int main(){
    func<int>(10);
    func<int,char>(10,'a');	//都可通过
}
```

### 右值引用

在赋值表达式中，出现在等号左边的就是“左值”，而在等号右边的，则称为“右值”

左值引用和右值引用都是属于引用类型。无论是声明一个左值引用还是右值引用，都必须立即进行初始化。

左值引用是具名变量值的别名，而右值引用则是不具名（匿名）变量的别名

```c++
/*左值引用*/
int &a = 2;       // 左值引用绑定到右值，编译失败, err
int b = 2;        // 非常量左值
const int &c = b; // 常量左值引用绑定到非常量左值，编译通过, ok
const int d = 2;  // 常量左值
const int &e = c; // 常量左值引用绑定到常量左值，编译通过, ok
const int &b = 2; // 常量左值引用绑定到右值，编程通过, ok
/*右值引用*/
int && r1 = 22;
int x = 5;
int y = 8;
int && r2 = x + y;
T && a = ReturnRvalue();
int c;
int &&d=c;	//右值引用是不能够绑定到任何的左值的
```

**const 类型 &**为 “万能”的引用类型，它可以接受非常量左值、常量左值、右值对其进行初始化；

#### 返回值优化技术

在return一个对象时，先生成一个临时对象，由旧对象初始化新对象，释放旧对象并返回新对象，该过程会有额外的拷贝构造和析构产生，在返回取值的生命周期结束后该临时对象才会释放

```c++
Mystring func(){
    Mystring obj("mike");
    return obj;	//生成临时对象并由旧对象初始化，返回临时对象
}
int main(){
    Mystring tmp=func();	//将临时对象过渡给tmp，此处做了一次优化，否则还会有一次拷贝构造和析构
    return 0;
}
```

有的编译器，如qt会有额外优化，在return处直接返回原对象，不产生临时对象

#### 移动语义

右值引用是用来支持转移语义的。转移语义可以将资源 ( 堆，系统对象等 ) 从一个对象转移到另一个对象，这样能够减少不必要的临时对象的创建、拷贝以及销毁，能够大幅度提高 C++ 应用程序的性能。

要实现转移语义，需要定义转移构造函数，还可以定义转移赋值操作符。对于右值的拷贝和赋值会调用转移构造函数和转移赋值操作符。

普通的函数和操作符也可以利用右值引用操作符实现转移语义。

##### 移动构造函数/移动赋值函数

```c++
class MyString
{
public:
    MyString(const char *tmp = "abc")
    {//普通构造函数
        len = strlen(tmp);  //长度
        str = new char[len+1]; //堆区申请空间
        strcpy(str, tmp); //拷贝内容
        cout << "普通构造函数 str = " << str << endl;
    }
    MyString(const MyString &tmp)
    {//拷贝构造函数
        len = tmp.len;
        str = new char[len + 1];
        strcpy(str, tmp.str);
        cout << "拷贝构造函数 tmp.str = " << tmp.str << endl;
    }
    //移动构造函数,参数是非const的右值引用
    MyString(MyString && t)
    {   str = t.str; //拷贝地址，没有重新申请内存
        len = t.len;
     
        t.str = NULL;//原来指针置空,此步非常重要，否则会发生重复释放问题，在析构里也要有对应的判断
        cout << "移动构造函数" << endl;
    }
    MyString &operator= (const MyString &tmp)
    {//赋值运算符重载函数
        if(&tmp == this)
        {
            return *this;
        }
        //先释放原来的内存
        len = 0;
        delete []str;
        //重新申请内容
        len = tmp.len;
        str = new char[len + 1];
        strcpy(str, tmp.str);
        cout << "赋值运算符重载函数 tmp.str = " << tmp.str << endl;
        return *this;
    }
    //移动赋值函数，参数为非const的右值引用
    MyString &operator=(MyString &&tmp)
    {
        if(&tmp == this)
        {
            return *this;
        }
        //先释放原来的内存
        len = 0;
        delete []str;
        //无需重新申请堆区空间
        len = tmp.len;
        str = tmp.str; //地址赋值
        tmp.str = NULL;
        cout << "移动赋值函数\n";
        return *this;
    }
    ~MyString()
    {//析构函数
        cout << "析构函数: ";
        if(str != NULL)
        {
            cout << "已操作delete, str =  " << str;
            delete []str;
            str = NULL;
            len = 0;
        }
        cout << endl;
    }
private:
    char *str = NULL;
    int len = 0;
};
MyString func() //返回普通对象，不是引用
{
    MyString obj("mike");
    return obj;
}
int main(){
    MyString &&tmp = func(); //右值引用接收
    return 0;
}
```



和拷贝构造函数类似，有几点需要注意：

l 参数（右值）的符号必须是右值引用符号，即“&&”。

l 参数（右值）不可以是常量，因为我们需要修改右值。

l 参数（右值）的资源链接和标记必须修改，否则，右值的析构函数就会释放资源，转移到新对象的资源也就无效了。

有了右值引用和转移语义，我们在设计和实现类时，对于需要动态申请大量资源的类，应该设计转移构造函数和转移赋值函数，以提高应用程序的效率。

##### std::move

标准库提供了函数 std::move，这个函数以非常简单的方式将左值引用转换为右值引用。

```c++
int a;
int &&r1 = a;      		// 编译失败
int &&r2 = std::move(a);  	// 编译通过
```

##### 完美转发 std::forward

完美转发适用于这样的场景：需要将一组参数原封不动的传递给另一个函数。“原封不动”不仅仅是参数的值不变，在 C++ 中，除了参数值之外，还有一下两组属性：**左值／右值和 const/non-const**。完美转发就是在参数传递过程中，所有这些属性和参数值都不能改变，同时，而不产生额外的开销，就好像转发者不存在一样。

```c++
#include <iostream>
using namespace std;
template <typename T> void process_value(T & val)
{
    cout << "T &" << endl;
}
template <typename T> void process_value(const T & val)
{
    cout << "const T &" << endl;
}
//函数 forward_value 是一个泛型函数，它将一个参数传递给另一个函数 process_value
template <typename T> void forward_value(const T& val)
{
    process_value(val);
}
template <typename T> void forward_value(T& val)
{
    process_value(val);
}
int main()
{
    int a = 0;
    const int &b = 1;
    //函数 forward_value 为每一个参数必须重载两种类型，T& 和 const T&
    forward_value(a); // T&
    forward_value(b); // const T &
    forward_value(2); // const T &
    return 0;
}
```

对于一个参数就要重载两次，也就是函数重载的次数和参数的个数是一个正比的关系。这个函数的定义次数对于程序员来说，是非常低效的。

那C++11是如何解决完美转发的问题的呢？实际上，C++11是通过引入一条所谓“引用折叠”（reference collapsing）的新语言规则，并结合新的模板推导规则来完成完美转发

###### 引用折叠

| TR的类型定义 | 声明v的类型 | v的实际类型 |
| ------------ | ----------- | ----------- |
| T &          | TR          | T &         |
| T &          | TR &        | T &         |
| T &          | TR &&       | T &         |
| T  &&        | TR          | T  &&       |
| T &&         | TR &        | T &         |
| T  &&        | TR &&       | T  &&       |

**一旦定义中出现了左值引用，引用折叠总是优先将其折叠为左值引用**

C++11中，std::forward可以保存参数的左值或右值特性：

```c++
#include <iostream>
using namespace std;
template <typename T> void process_value(T & val)
{
    cout << "T &" << endl;
}
template <typename T> void process_value(T && val)
{
    cout << "T &&" << endl;
}
template <typename T> void process_value(const T & val)
{
    cout << "const T &" << endl;
}
template <typename T> void process_value(const T && val)
{
    cout << "const T &&" << endl;
}
//函数 forward_value 是一个泛型函数，它将一个参数传递给另一个函数 process_value
template <typename T> void forward_value(T && val) //参数为右值引用
{
    process_value( std::forward<T>(val) );//C++11中，std::forward可以保存参数的左值或右值特性
}
int main()
{
    int a = 0;
    const int &b = 1;
    forward_value(a); // T &
    forward_value(b); // const T &
    forward_value(2); // T &&
    forward_value( std::move(b) ); // const T &&
    return 0;
}
```

### 智能指针

#### unique_ptr

unique_ptr持有对对象的独有权，同一时刻**只能有一个**unique_ptr指向给定对象（通过禁止拷贝语义、只有移动语义来实现）。 

unique_ptr指针本身的生命周期从unique_ptr指针创建时开始，直到离开作用域。**离开作用域时，若其指向对象，则将其所指对象销毁**(默认使用delete操作符，用户可指定其他操作)

```c++
class Test{
    public:
    ~Test(){
        cout<<"析构";
    }
}
int main(){
    unique_ptr<Test> up1(new Test);	//结束后自动析构
    //up1=nullptr;或up=NULL手动析构
    //up1.reset();成员函数显式析构
    
    //unique_ptr<Test> up2=up1;	//不可用，禁用拷贝构造
    unique_ptr<Test> up2=std::move(up1);	//可以，将up1转义给up2，但up1不能再操作堆空间
    up1.reset(new int(22));		//如果有参数，先释放再绑定新内容
    int *p=up1.release();	//释放控制权但不释放内存;
    delete p;
    return 0;
}
```



#### shared_ptr

shared_ptr允许多个该智能指针共享第“拥有”同一堆分配对象的内存，这通过引用计数（reference counting）实现，会记录有多少个shared_ptr共同指向一个对象，一旦最后一个这样的指针被销毁，也就是一旦某个对象的引用计数变为0，这个对象会被自动删除

```c++
int main()
{
    shared_ptr<int> sp1(new int(22));
    shared_ptr<int> sp2 = sp1;
    cout << "count: " << sp2.use_count() << endl; //打印引用计数 结果为2
    sp1.reset();    //显式让引用计数减1
    cout << "count: " << sp2.use_count() << endl; //打印引用计数 结果为1
    cout << *sp2 << endl;   // 22
    return 0;
}
```

#### weak_ptr

配合shared_ptr使用，它可以指向shared_ptr指针指向的对象内存，却并不拥有该内存，没有重载*和->，它可以从一个shared_ptr或另一个weak_ptr对象构造，它的构造和析构**不会引起引用计数的增加或减少**。

可以使用lock获得一个可用的shared_ptr对象，在所指对象内存已经无效时，返回指针空值nullptr

```c++
shared_ptr<int> sp1(new int(22));
shared_ptr<int> sp2 = sp1;
weak_ptr<int> wp=sp1;
cout << "count: " << sp1.use_count() << endl; //打印引用计数 结果为2
cout << "count: " << wp.use_count() << endl; //打印引用计数 结果为2
shared_ptr<int> sp3=wp.lock();
cout << "count: " << sp1.use_count() << endl; //打印引用计数 结果为3
sp1.reset();sp2.reset();sp3.reset();
shared_ptr<int> tmp=wp.lock();	//此时tmp为nullptr
```

### 闭包

函数是代码，状态是一组变量，将代码和一组变量捆绑 (bind) ，就形成了闭包。

闭包的状态捆绑，必须发生在运行时

#### 仿函数：重载 operator()

```c++
class MyFunctor
{
public:
    MyFunctor(int tmp) : round(tmp) {}
    int operator()(int tmp) { return tmp + round; }
private:
    int round;
};
int main()
{
    int round = 2;
    MyFunctor f(round);//调用构造函数
    cout << "result = " << f(1) << endl; //operator()(int tmp)  结果为3
    return 0;
}
```



#### std::bind绑定器

##### stf::function

在C++中，可调用实体主要包括：函数、函数指针、函数引用、可以隐式转换为函数指定的对象，或者实现了opetator()的对象。

C++11中，新增加了一个std::function类模板，它是对C++中现有的可调用实体的一种类型安全的包裹。通过指定它的模板参数，它可以用**统一的方式处理**函数、函数对象、函数指针，并允许保存和延迟执行它们

```c++
#include <iostream>
#include <functional>   //std::cout
using namespace std;
void func(void)
{//普通全局函数
    cout << __func__ << endl;
}
class Foo
{
public:
    static int foo_func(int a)
    {//类中静态函数
        cout << __func__ << "(" << a << ") ->: ";
        return a;
    }
};
class Bar
{
public:
    int operator()(int a)
    {//仿函数
        cout << __func__ << "(" << a << ") ->: ";
        return a;
    }
};
int main()
{
    //绑定一个普通函数
    function< void(void) > f1 = func;
    f1();	//等价于func
    //绑定类中的静态函数
    function< int(int) > f2 = Foo::foo_func;
    cout << f2(111) << endl;
    //绑定一个仿函数
    Bar obj;
    f2 = obj;
    cout << f2(222) << endl;
    /*
     运行结果：
        func
        foo_func(111) ->: 111
        operator()(222) ->: 222
    */
    return 0;
}
```

std::function对象最大的用处就是在实现函数回调，使用者需要注意，它不能被用来检查相等或者不相等，但是可以与NULL或者nullptr进行比较

##### std::bind

在C++11中，提供了std::bind，它绑定的参数的个数不受限制，绑定的具体哪些参数也不受限制，由用户指定

```c++
#include <iostream>
#include <functional>   //std::bind
using namespace std;
void func(int x, int y)
{
    cout << x << " " << y << endl;
}
int main()
{
    bind(func, 1, 2)();                     //输出：1 2
    bind(func, std::placeholders::_1, 2)(1);//输出：1 2  std::placeholders::_1是占位符
    using namespace std::placeholders;    // adds visibility of _1, _2, _3,...
    bind(func, 2, _1)(1);       //输出：2 1
    bind(func, 2, _2)(1, 2);    //输出：2 2
    bind(func, _1, _2)(1, 2);   //输出：1 2
    bind(func,_2, _1)(1, 2);    //输出：2 1
    //bind(func, 2, _2)(1);	//err, 调用时没有第二个参数
    return 0;
}
```

#####  std::bind和std::function配合使用

```c++
#include <iostream>
#include <functional>   //std::cout
using namespace std;
using namespace std::placeholders;    // adds visibility of _1, _2, _3,...
class Test
{
public:
    int i = 0;
    void func(int x, int y)
    {
        cout << x << " " << y << endl;
    }
};
int main()
{
    Test obj; //创建对象
    function<void(int, int)> f1 = bind(&Test::func, &obj, _1, _2);	//非静态成员函数依赖于实例化对象
    f1(1, 2);   //输出：1 2	等价于obj.func(1,2)
    function< int &()> f2 = bind(&Test::i, &obj);
    f2() = 123;	//等价于obj.i=123;
    cout << obj.i << endl;//结果为 123
    return 0;
}
```

#### lambda表达式

lambda 表达式(lambda expression)是一个匿名函数，lambda表达式基于数学中的 λ 演算得名。

C++11中的lambda表达式用于定义并创建**匿名的函数对象**，以简化编程工作。

[函数对象参数] (操作符重载函数参数)mutable->返回值{函数体}

```c++
[capture](parameters)mutable->returnType
{
    statement
}
```

**[]标识一个lambda的开始，不能省略，内部可以放不同内容实现不同功能**

空：没有使用任何函数对象参数

=：函数体内可以便甲Lambda所在作用范围内所有可见的局部变量（包括Lambda所在类的this)，并且是**值传递**方式（相当于编译器自动为我们按值传递了所有局部变量)

&：函数体内可以使用Lambda所在作用范围内所可见的局部变量（包括lambda所在类的this)，并且是**引用传递**方式（相当于编译器自动为我们按引用传递了所有局部变量

this：函数体内可以使用Lambda**所在类**中的成员变量

a：	将a按值传递，只能访问a；函数体不能修改传递进来的a的拷贝，因为默认函数为const，要想修改，添加mutable修饰符

&a：按引用传递

多个参数：a,&b：a值传递，b引用传递；	=,&a,&b：除了a和b按引用传递，其它按值传递			

```c++
[=]()//此处若不加等号则不能修改btnL
{
    btnL->setText("change");
}();//截止到小括号以前只是声明（函数地址），加上小括号后是函数调用
```

**()标识重载的()操作符的参数，没有时可省略**

**mutable:可修改声明，加上后可以修改按值传进来的拷贝（不是本身）**

**返回值：->后接类型**

一般建议使用[=]

```c++
connect(btn3,&QPushbutton::clicked,[&](){btn3->setText("ccc");});//该写法会报错
```

当进行信号与槽连接时，前信号的对象（此处为btn3）会进入一个只读状态，而setText进行了修改，引起冲突

```c++
connect(btn3,&QPushbutton::clicked,[=](){btn3->setText("ccc");});//正常运行
```

而使用=值传递时，会产生一个拷贝构造指针，间接修改

lambda表达式内可再调用函数，且能调用多个

```c++
connect(btn3,&QPushbutton::clicked,[=](){this->st->treat("文本")});
//原先clicked不能连接string为参的treat函数（clicked内默认bool类型参数），通过这种方式可以间接连接
```

```c++
class Test
{
public:
    int i = 0;

    void func(int x, int y)
    {
        auto x1 = []{ return i; };          //err, 没有捕获外部变量
        auto x2 = [=]{ return i+x+y; };     //ok, 值传递方式捕获所有外部变量
        auto x3 = [=]{ return i+x+y; };     //ok, 引用传递方式捕获所有外部变量
        auto x4 = [this]{ return i; };      //ok, 捕获this指针
        auto x5 = [this]{ return i+x+y; };  //err, 没有捕获x, y
        auto x6 = [this, x, y]{ return i+x+y; };//ok, 捕获this指针, x, y
        auto x9 = [this]{ return i++; };        //ok, 捕获this指针, 并修改成员的值
    }
};
int main()
{
    int a = 0, b = 1;
    auto f1 = []{ return a; };      //err, 没有捕获外部变量
    auto f2 = [=]{ return a; };     //ok, 值传递方式捕获所有外部变量
    auto f3 = [=]{ return a++; };   //err, a是以赋值方式捕获的，无法修改
    auto f4 = [=]() mutable { return a++; };   //ok, 加上mutable修饰符后，可以修改按值传递进来的拷贝
    auto f5 = [&]{ return a++; };               //ok, 引用传递方式捕获所有外部变量, 并对a执行自加运算
    auto f6 = [a]{ return a+b; };               //err, 没有捕获变量b
    auto f9 = [a,&b]{ return a+(b++); };        //ok, 捕获a, &b
    auto f8 = [=,&b]{ return a+(b++); };        //ok, 捕获所有外部变量，&b

    return 0;
}
```

事实上，仿函数是编译器实现lambda的一种方式，通过编译器都是把lambda表达式转化为一个仿函数对象。因此，在C++11中，lambda可以视为仿函数的一种等价形式

```c++
class MyFunctor
{
public:
    MyFunctor(int tmp) : round(tmp) {}
    int operator()(int tmp) { return tmp + round; }
private:
    int round;
};
int main()
{
    //仿函数
    int round = 2;
    MyFunctor f1(round);//调用构造函数
    cout << "result1 = " << f1(1) << endl; //operator()(int tmp)
    //lambda表达式
    auto f2 = [=](int tmp) -> int { return tmp + round; } ;
    cout << "result2 = " << f2(1) << endl;
    return 0;
}
```

通过上面的例子，我们看到，仿函数以round初始化类，而lambda函数也捕获了round变量，其它的，如果在参数传递上，两者保持一致。

##### 优势

lambda表达式的价值在于，就地封装短小的功能闭包，可以及其方便地表达出我们希望执行的具体操作，并让上下文结合更加紧密

```c++
#include <vector>
#include <algorithm> //std::for_each
#include <iostream>
using namespace std;
vector<int> nums;
vector<int> largeNums;
class LNums
{
public:
    LNums(int u): ubound(u){} //构造函数

    void operator () (int i) const
    {//仿函数
        if (i > ubound)
        {
            largeNums.push_back(i);
        }
    }
private:
    int ubound;
};
int main()
{
    //初始化数据
    for(auto i = 0; i < 10; ++i)
    {
        nums.push_back(i);
    }
    int ubound = 5;

    //1、传统的for循环
    for (auto itr = nums.begin(); itr != nums.end(); ++itr)
    {
        if (*itr > ubound)
        {
            largeNums.push_back(*itr);
        }
    }
    //2、使用仿函数
    for_each(nums.begin(), nums.end(), LNums(ubound));

    //3、使用lambda函数和算法for_each
    for_each(nums.begin(), nums.end(), [=](int i)
        {
            if (i > ubound)
            {
                largeNums.push_back(i);
            }
        }
        );
    //4、遍历元素
    for_each(largeNums.begin(), largeNums.end(), [=](int i)
        {
            cout << i << ", ";
        }
        );
    cout << endl;
    return 0;
}
```

