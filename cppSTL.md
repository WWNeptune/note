# STL

## STL常用容器

### string

**本质：**

* string是C++风格的字符串，而string本质上是一个类



**string和char * 区别：**

* char * 是一个指针
* string是一个类，类内部封装了char\*，管理这个字符串，是一个char*型的容器。



**特点：**

string 类内部封装了很多成员方法

例如：查找find，拷贝copy，删除delete 替换replace，插入insert

string管理char*所分配的内存，不用担心复制越界和取值越界等，由类内部进行负责

##### 构造函数

```c++
string(); //创建一个空的字符串 例如: string str;
string(const char* s); //使用字符串s初始化
string(const string& str); //使用一个string对象初始化另一个string对象
string(int n, char c); //使用n个字符c初始化
```

##### 赋值assign

```c++
string& operator=(const char* s); //char*类型字符串 赋值给当前的字符串
string& operator=(const string &s); //把字符串s赋给当前的字符串
string& operator=(char c); //字符赋值给当前的字符串
string& assign(const char *s); //把字符串s赋给当前的字符串
string& assign(const char *s, int n); //把字符串s的前n个字符赋给当前的字符串
string& assign(const string &s); //把字符串s赋给当前字符串
string& assign(int n, char c); //用n个字符c赋给当前字符串
```

##### 字符串拼接append

```c++
string& operator+=(const char* str); //重载+=操作符
string& operator+=(const char c); //重载+=操作符
string& operator+=(const string& str); //重载+=操作符
string& append(const char *s); //把字符串s连接到当前字符串结尾
string& append(const char *s, int n); //把字符串s的前n个字符连接到当前字符串结尾
string& append(const string &s); //同operator+=(const string& str)
string& append(const string &s, int pos, int n); //字符串s中从pos开始的n个字符连接到字符串结尾
```

##### 查找find/rfind和替换replace

```c++
int find(const string& str, int pos = 0) const; //查找str第一次出现位置,从pos开始查找
int find(const char* s, int pos = 0) const; //查找s第一次出现位置,从pos开始查找
int find(const char* s, int pos, int n) const; //从pos位置查找s的前n个字符第一次位置
int find(const char c, int pos = 0) const; //查找字符c第一次出现位置
//find从左起，rfind从右起，此时0分别代表第一次和最后一次
int rfind(const string& str, int pos = npos) const; //查找str最后一次位置,从pos开始查找
int rfind(const char* s, int pos = npos) const; //查找s最后一次出现位置,从pos开始查找
int rfind(const char* s, int pos, int n) const; //从pos查找s的前n个字符最后一次位置
int rfind(const char c, int pos = 0) const; //查找字符c最后一次出现位置

string& replace(int pos, int n, const string& str); //替换从pos开始n个字符为字符串str
string& replace(int pos, int n,const char* s); //替换从pos开始的n个字符为字符串s
```

##### 字符串比较compare

通过ASCII码比较，等于返回0，大于返回1，小于返回-1

```c++
int compare(const string &s) const; //与字符串s比较
int compare(const char *s) const; //与字符串s比较
```

##### 字符存取

```c++
char& operator[](int n); //通过[]方式取字符
char& at(int n); //通过at方法获取字符
```

##### 字符插入insert和删除erase

```c++
string& insert(int pos, const char* s); //插入字符串
string& insert(int pos, const string& str); //插入字符串
string& insert(int pos, int n, char c); //在指定位置插入n个字符c
string& erase(int pos, int n = npos); //删除从Pos开始的n个字符
```

##### 获取子串substr

```c++
string substr(int pos = 0, int n = npos) const; //返回由pos开始的n个字符组成的字符串
```

### VECTOR

迭代器： ```vector<T>::iterator```,```vector<T>::const_iterator```

起始迭代器```v.begin()```,指向起始地址;结束迭代器```v.end()```，指向末尾的**下一个**地址

##### 构造函数

```c++
vector<T> v; //采用模板实现类实现，默认构造函数
vector(v.begin(), v.end()); //将v[begin(), end())区间中的元素拷贝给本身。
vector(n, elem); //构造函数将n个elem拷贝给本身。
vector(const vector &vec); //拷贝构造函数。
```

##### 赋值assign

```c++
vector& operator=(const vector &vec); //重载等号操作符
assign(v1.begin(), v1.end()); //将[begin, end)区间中的数据拷贝赋值给本身。不包含end
assign(n, elem); //将n个elem拷贝赋值给本身。
```

##### 容量和大小

```c++
empty(); //判断容器是否为空
capacity(); //容器的容量
size(); //返回容器中元素的个数
resize(int num); //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删除。
resize(int num, elem); //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删除
```

容量不等于元素个数，resize改变的是元素数量，vector容器的容量没有变（系统自动扩充）

##### 插入insert和删除erase

```c++
push_back(ele); //尾部插入元素ele
pop_back(); //删除最后一个元素
insert(const_iterator pos, ele); //迭代器指向位置pos插入元素ele
insert(const_iterator pos, int count,ele); //迭代器指向位置pos插入count个元素ele
erase(const_iterator pos); //删除迭代器指向的元素
erase(const_iterator start, const_iterator end); //删除迭代器从start到end之间的元素
clear(); //删除容器中所有元素
```

插入insert和删除erase需要通过位置迭代器访问

##### 数据存取

```c++
at(int idx); //返回索引idx所指的数据v1.at(i)
operator[]; //返回索引idx所指的数据v1[i]
front(); //返回容器中第一个数据元素
back(); //返回容器中最后一个数据元素
```

##### 互换容器swap

```c++
swap(vec); // 将vec与本身的元素互换
```

可以通过swap收缩内存，节省未被使用的空间

```c++
vector<int>(v).swap(v)
```

左边vector<int>(v)是一个匿名对象，复制了v的内容（但空间不一样），然后通过swap(v)与原有的v交换，执行完后原有的v被释放，保留的是新创建的v

##### 预留空间

减少动态扩展的扩展次数

```c++
reserve(int len); //容器预留len个元素长度，预留位置不初始化，元素不可访问
```

### deque

**功能：**

* 双端数组，可以对头端进行插入删除操作

**deque与vector区别：**

* vector对于头部的插入删除效率低，数据量越大，效率越低
* deque相对而言，对头部的插入删除速度回比vector快
* vector访问元素时的速度会比deque快,这和两者内部实现有关

deque内部工作原理:

deque内部有个**中控器**，维护每段缓冲区中的内容，缓冲区中存放真实数据

中控器维护的是每个缓冲区的地址，使得使用deque时像一片连续的内存空间

##### 构造函数

```c++
deque<T> deqT; //默认构造形式
deque(beg, end); //构造函数将一个deque容器[beg, end)区间中的元素拷贝给本身。
deque(n, elem); //构造函数将n个elem拷贝给本身。
deque(const deque &deq); //拷贝构造函数
```

##### 赋值

```c++
deque& operator=(const deque &deq); //重载等号操作符
assign(beg, end); //将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem); //将n个elem拷贝赋值给本身。
```

##### 容量和大小

```c++
empty(); //判断容器是否为空
size(); //返回容器中元素的个数
resize(int num); //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删除。
resize(int num, elem); //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删
```

deque没有容量的概念，因为deque是由一段一段的定量的连续空间构成,vector是连续空间

##### 插入和删除

###### 两端插入

```c++
push_back(elem); //在容器尾部添加一个数据
push_front(elem); //在容器头部插入一个数据
pop_back(); //删除容器最后一个数据
pop_front(); //删除容器第一个数据
```

###### 指定位置

```c++
insert(pos,elem); //在pos位置插入一个elem元素的拷贝，返回新数据的位置。
insert(pos,n,elem); //在pos位置插入n个elem数据，无返回值。
insert(pos,beg,end); //在pos位置插入[beg,end)区间的数据，无返回值。
clear(); //清空容器的所有数据
erase(beg,end); //删除[beg,end)区间的数据，返回下一个数据的位置。
erase(pos); //删除pos位置的数据，返回下一个数据的位置。
```

##### 数据存取

```c++
at(int idx); //返回索引idx所指的数据
operator[]; //返回索引idx所指的数据
front(); //返回容器中第一个数据元素
back(); //返回容器中最后一个数据元素
```

##### 排序

```c++
sort(iterator beg, iterator end) //对beg和end区间内元素进行排序
```

该方法是STL的算法，使用要包含头文件```algorithm```

### stack栈

##### 常用接口

```c++
//构造函数
stack<T> stk; //stack采用模板类实现， stack对象的默认构造形式
stack(const stack &stk); //拷贝构造函数
//赋值
stack& operator=(const stack &stk); //重载等号操作符
//存取
push(elem); //向栈顶添加元素
pop(); //从栈顶移除第一个元素
top(); //返回栈顶元素

empty(); //判断堆栈是否为空
size(); //返回栈的大小
```

### quene队列

队列容器允许从一端新增元素，从另一端移除元素
队列中只有队头和队尾才可以被外界使用，因此队列不允许有遍历行为

```c++
//构造函数
queue<T> que; //queue采用模板类实现，queue对象的默认构造形式
queue(const queue &que); //拷贝构造函数
//赋值
queue& operator=(const queue &que); //重载等号操作符
//存取
push(elem); //往队尾添加元素
pop(); //从队头移除第一个元素
back(); //返回最后一个元素
front(); //返回第一个元素

empty(); //判断堆栈是否为空
size(); //返回栈的大小
```

### list链表

##### 构造

```c++
list<T> lst; //list采用采用模板类实现,对象的默认构造形式：
list(beg,end); //构造函数将[beg, end)区间中的元素拷贝给本身。
list(n,elem); //构造函数将n个elem拷贝给本身。
list(const list &lst); //拷贝构造函数。  
```

##### 赋值和交换

```c++
assign(beg, end); //将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem); //将n个elem拷贝赋值给本身。
list& operator=(const list &lst); //重载等号操作符
swap(lst); //将lst与本身的元素互换。
```

##### 大小操作

```c++
size(); //返回容器中元素的个数
empty(); //判断容器是否为空
resize(num); //重新指定容器的长度为num，若容器变长，则以默认值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删除。
resize(num, elem); //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。
//如果容器变短，则末尾超出容器长度的元素被删除
```

##### 插入和删除

```c++
push_back(elem);//在容器尾部加入一个元素
pop_back();//删除容器中最后一个元素
push_front(elem);//在容器开头插入一个元素
pop_front();//从容器开头移除第一个元素

insert(pos,elem);//在pos位置插elem元素的拷贝，返回新数据的位置。
insert(pos,n,elem);//在pos位置插入n个elem数据，无返回值。
insert(pos,beg,end);//在pos位置插入[beg,end)区间的数据，无返回值。

clear();//移除容器的所有数据
erase(beg,end);//删除[beg,end)区间的数据，返回下一个数据的位置。
erase(pos);//删除pos位置的数据，返回下一个数据的位置。
remove(elem);//删除容器中所有与elem值匹配的元素。
```

此处提供的位置pos为迭代器，并非顺序数字

##### 数据存取

```c++
front(); //返回第一个元素。
back(); //返回最后一个元素。
```

list容器中不可以通过[]或者at方式访问数据

##### 反转reverse和排序sort

```c++
reverse(); //反转链表
L.sort(); //链表排序,此处sort()不是标准算法，而是list内部的算法
```

所有不支持随机访问的容器，不支持标准算法，其内部会提供专用的算法（通过容器调用）

list的sort默认升序，如果想实现其它类型排序需要为sort提供仿函数

```c++
L.sort(compare);//此处假设list内部存的为person自定义类型，要按照内部的age变量进行降序排列
bool compare(person &p1,person &p2)
{
    return p1.age<p2.age;
}
```

### set/multiset容器

所有元素都会在插入时自动被排序

**本质：**

* set/multiset属于**关联式容器**，底层结构是用**二叉树**实现。

##### 构造

```c++
set<T> st;   //默认构造函数：
set(const set &st);//拷贝构造函数

set &operator=(const set &st);//等号赋值
```

##### 大小和交换

```c++
st.size();
st.empty();
st.swap(st2);
```

##### 插入insert和删除erase

```c++
st.insert(num);//使用insert，而不是push
st.erase(st.begin());
st.erase(30);
st.erase(st.begin(),st.end());//清空
st.clear();

pair<set<int>::iterator,bool> ret=st.insert(10);//set在插入时会返回插入是否成功
ret.second//使用pair类型接收返回结果
```

插入时自动排序，但不允许插重复值

```c++
pair<set<int>::iterator, bool> ret;
ret = myset.insert(1);
```

set插入时会返回一个pair，第二位bool为是否成功

##### 查找find和统计count

```c++
st.find(num);//返回的是一个迭代器，若为end()说明未找到
st.count(num);//返回的是数量int，set不允许重复内容，一般用于multiset
```

##### multiset

**set和multiset区别**：

* set不允许容器中有重复的元素
* multiset允许容器中有重复的元素

##### 自定义排序

需要再插入之前就定义排序规则

```c++
class MyCompare 
{
public:
	bool operator()(int v1, int v2)//重载括号 
    {
		return v1 > v2;
	}
};
set<int,Mycompare>s2;//此时s2便为降序排列
```

对于自定义数据类型，必需要提供排序规则

```c++
class MyCompare 
{
public:
	bool operator()(const Person& p1,const Person& p2)//重载括号 
    {
		return p1.age > p2.age;
	}
};
set<Person,Mycompare>s;
```

### unordered_set

与set容器类似，但不会排序

```c++
pair<unordered_set<int>::iterator, bool> ret;
ret = myset.insert(1);
```



### pair对组

##### 构造

```c++
pair<type, type> p ( value1, value2 );
pair<type, type> p = make_pair( value1, value2 );
```

取值

```c++
p.first;
p.second;
```

### map/multimap容器

* map中所有元素都是pair
* pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）
* 所有元素都会根据元素的键值自动排序


* map/multimap属于**关联式容器**，底层结构是用二叉树实现。
* 可以根据key值快速找到value值

##### 构造和赋值

```c++
map<T1,T2>m; //要提供两个数据类型
map(const map &mp);//拷贝构造
map& operator=(const map &mp);//等号赋值
```

##### 大小和交换

```c++
size();
empty();
swap(st);//和set基本相同
```

##### 插入insert和删除erase

map需要以pair的类型进行插入

```c++
m.insert(pair<int,int>(1,10));
m.insert(make_pair(2,20));
m.insert(map<int,int>::value_type(3,30));
m[5]=40;//不建议使用，可能会造成数据不连续，但可以用于访问

m.erase(m.begin());//删除
m.erase(3);//按照key进行删除
m.erase(m.begin(),m.end());//区间删除
m.clear();//清空
```

##### 查找和统计

```c++
map<int,int>::iterator pos=find(3);//返回迭代器，end()说明未找到
int num=count(3);//返回数量
```

##### 排序

按照key值默认升序，可使用仿函数改变规则

需要在插入数据前提供

```c++
class MyCompare {
public:
	bool operator()(int v1, int v2) {
		return v1 > v2;
	}
};
map<int, int, MyCompare> m;
```

## 函数对象(仿函数)

### 函数对象

* 重载**函数调用操作符**的类，其对象常称为**函数对象**
* **函数对象**使用重载的()时，行为类似函数调用，也叫**仿函数**

<u>函数对象(仿函数)是一个**类**，不是一个函数</u>

* 函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
* 函数对象超出普通函数的概念，函数对象可以有自己的状态
* 函数对象可以作为参数传递

```c++
#include <string>

//1、函数对象在使用时，可以像普通函数那样调用, 可以有参数，可以有返回值
class MyAdd
{
public :
	int operator()(int v1,int v2)
	{
		return v1 + v2;
	}
};

void test01()
{
	MyAdd myAdd;
	cout << myAdd(10, 10) << endl;
}

//2、函数对象可以有自己的状态
class MyPrint
{
public:
	MyPrint()
	{
		count = 0;
	}
	void operator()(string test)
	{
		cout << test << endl;
		count++; //统计使用次数
	}

	int count; //内部自己的状态
};
void test02()
{
	MyPrint myPrint;
	myPrint("hello world");
	myPrint("hello world");
	myPrint("hello world");
	cout << "myPrint调用次数为： " << myPrint.count << endl;
}

//3、函数对象可以作为参数传递
void doPrint(MyPrint &mp , string test)
{
	mp(test);
}

void test03()
{
	MyPrint myPrint;
	doPrint(myPrint, "Hello C++");
}

int main() {

	//test01();
	//test02();
	test03();

	system("pause");

	return 0;
}
```

### 谓词Pred

* 返回bool类型的仿函数称为**谓词**
* 如果operator()接受一个参数，那么叫做一元谓词
* 如果operator()接受两个参数，那么叫做二元谓词

#### 一元谓词

```c++
struct GreaterFive{
	bool operator()(int val) {
		return val > 5;//寻找大于5的元素
	}
};
vector<int>::iterator it = find_if(v.begin(), v.end(), GreaterFive());//find_if为STL算法
```

#### 二元谓词

```c++
class MyCompare
{
public:
	bool operator()(int num1, int num2)
	{
		return num1 > num2;
	}
};
sort(v.begin(), v.end(), MyCompare());
```

### 内建函数对象

STL提供的一些仿函数

#### 算术仿函数

* `template<class T> T plus<T>`                //加法仿函数
* `template<class T> T minus<T>`              //减法仿函数
* `template<class T> T multiplies<T>`    //乘法仿函数
* `template<class T> T divides<T>`         //除法仿函数
* `template<class T> T modulus<T>`         //取模仿函数
* `template<class T> T negate<T>`           //取反仿函数

除了negate，其它都是二元运算

```c++
negate<int> n;
cout << n(50) << endl;//取反

plus<int> p;
cout << p(10, 20) << endl;//相加
```

#### 关系仿函数

* `template<class T> bool equal_to<T>`                    //等于
* `template<class T> bool not_equal_to<T>`            //不等于
* `template<class T> bool greater<T>`                      //大于
* `template<class T> bool greater_equal<T>`          //大于等于
* `template<class T> bool less<T>`                           //小于
* `template<class T> bool less_equal<T>`               //小于等于

```c++
sort(v.begin(), v.end(), greater<int>());//对v进行降序排序
```

#### 逻辑仿函数

* `template<class T> bool logical_and<T>`              //逻辑与
* `template<class T> bool logical_or<T>`                //逻辑或
* `template<class T> bool logical_not<T>`              //逻辑非

```c++
transform(v.begin(), v.end(),  v2.begin(), logical_not<bool>());//transform为STL搬运函数，目标位置必须事先开辟空间
//逻辑非  将v容器搬运到v2中，并执行逻辑非运算
```

## STL算法

由STL提供的算法，使用时需包含头文件

* `<algorithm>`是所有STL头文件中最大的一个，范围涉及到比较、 交换、查找、遍历操作、复制、修改等等
* `<numeric>`体积很小，只包括几个在序列上面进行简单数学运算的模板函数
* `<functional>`定义了一些模板类,用以声明函数对象。

### 遍历算法

##### for_each

`for_each(iterator beg, iterator end, _func);  `起始迭代器，结束迭代器，函数或函数对象()

```c++
//普通函数
void print01(int val) 
{
	cout << val << " ";
}
//函数对象
class print02 
{
 public:
	void operator()(int val) 
	{
		cout << val << " ";
	}
};
for_each(v.begin(), v.end(), print01);
for_each(v.begin(), v.end(), print02());
```

##### transform

根据定义的仿函数规则，搬运容器到另一个容器中

搬运的目标容器必须要提前开辟空间，否则无法正常搬运

* `transform(iterator beg1, iterator end1, iterator beg2, _func);`

beg1 源容器开始迭代器   end1 源容器结束迭代器	beg2 目标容器开始迭代器	_func 函数或者函数对象

```c++
class TransForm//仿函数
{
public:
	int operator()(int val)
	{
		return val;
	}

};
transform(v.begin(), v.end(), vTarget.begin(), TransForm());//vTarget是事先开辟好空间的容器
```

### 查找算法

##### find

`find(iterator beg, iterator end, value);  `按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置

如果是自定义数据类型，则需要重载==来进行对比

```c++
class Person {
public:
	Person(string name, int age) 
	{
		this->m_Name = name;
		this->m_Age = age;
	}
	//重载==
	bool operator==(const Person& p) 
	{
		if (this->m_Name == p.m_Name && this->m_Age == p.m_Age) 
		{
			return true;
		}
		return false;
	}

public:
	string m_Name;
	int m_Age;
};
vector<Person>::iterator it = find(v.begin(), v.end(), p2);//p2是目标查找对象
```



##### find_if

`find_if(iterator beg, iterator end, _Pred);  `按值查找元素，找到返回指定位置迭代器，找不到返回结束迭代器位置

 _Pred 为函数或者谓词（返回bool类型的仿函数）

```c++
//内置数据类型
class Greater20
{
public:
	bool operator()(Person &p)
	{
		return p.m_Age > 20;
	}

};
vector<Person>::iterator it = find_if(v.begin(), v.end(), Greater20());
```

与find区别为find_if需要提供谓词，谓词内容根据数据类型定义

##### adjacent_find

查找**相邻**重复元素，返回相邻元素的第一个位置的迭代器

`adjacent_find(iterator beg, iterator end);  `

##### binary_search

查找指定元素是否存在（返回bool类型）

使用的二分查找法

**容器必须是有序的序列**

`bool binary_search(iterator beg, iterator end, value);  `

##### count

统计数据数量

`count(iterator beg, iterator end, value);  `

与find类似，对于自定义数据类型需要在类中提供==重载

##### count_if

统计符合谓词规则的数据数量

`count_if(iterator beg, iterator end, _Pred);  `

与find_if类似，需要根据数据类型提供谓词

### 排序算法

##### sort

`sort(iterator beg, iterator end, _Pred);  `

谓词默认为升序，其它规则或自定义数据类型需要提供谓词，或者内建函数对象如greater<T>()

##### random_shuffle

将指定范围内的数据进行随机打乱

`random_shuffle(iterator beg, iterator end);  `

可以通过设置随机种子改变随机状态

##### merge

将两个容器元素合并，并存放到另一容器中

**两个容器必须是有序的，目标容器必须事先开辟空间**

`merge(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `dest为目标容器的起始迭代器

##### reverse

将容器内元素进行反转

`reverse(iterator beg, iterator end);  `

### 拷贝和替换算法

##### copy

将容器内指定范围的元素拷贝到另一容器，**目标容器记得提前开辟空间**

`copy(iterator beg, iterator end, iterator dest);  `

##### replace

将容器内指定范围的旧元素修改为新元素，替换区间内满足条件的元素

`replace(iterator beg, iterator end, oldvalue, newvalue);  `

自定义数据类型需要重载==

##### replace_if

提供谓词，根据设定条件进行替换

`replace_if(iterator beg, iterator end, _pred, newvalue);  `

##### swap

互换两个容器内容，容器类型必须相同

`swap(container c1, container c2);  `

### 算术生成算法

##### accumulate

计算区间内容器元素累计总和，头文件注意是 numeric

`accumulate(iterator beg, iterator end, value);  `value为计数起始值

##### fill

向容器指定区间填充元素

`fill(iterator beg, iterator end, value);  `

### 集合算法

##### set_intersection

取两个容器的交集

`set_intersection(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `

* 求交集的两个集合必须是有序序列
* 目标容器开辟空间需要从**两个容器中取小值**
* set_intersection返回值是交集中最后一个元素的位置（便于输出等）

##### set_union

取两个容器的并集

特性与交集类似，但目标容器大小为**两容器之和**

`set_union(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `

##### set_difference

取两个容器的差集

特性与交集类似，但目标容器大小为**两容器较大值**

`set_difference(iterator beg1, iterator end1, iterator beg2, iterator end2, iterator dest);  `

差集：在前一个容器，不在后一个容器，因此容器不同的前后顺序会有不同的结果
