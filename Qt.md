# Qt学习


### 基类

QWidget：基础空窗口

QMainWindow：主菜单窗口，包含基础的菜单栏等

QDialog：对话框相关

QWidget是另外两个的父类

### project文件

TARGET：生成的exe文件名称

TEMPLATE：模板

SOURCES：源文件

HEADERS：头文件

### main程序

```c++
    QApplication a(argc, argv);//a:应用程序对象
    Widget w;//w:窗口对象
    w.show();//调用show进行显示
    return a.exec();//进入消息循环 起到阻塞作用
```

### 命名规范与快捷键

类名首字母大写，单词之间首字母大写；

函数名与变量名首字母小写，单词之间首字母大写

运行 ctrl+r；编译ctrl+b；注释ctrl+/；字体缩放ctrl+滚轮；整行移动ctrl+shift+↑或↓；自动对其ctrl+i；同名h和cpp切换 F4

### 添加按键并绑定父窗口

在项目右键添加新文件，选择合适的父类（QPushbutton的父类是QWidget），完成后系统生成cpp和h文件（cmake项目需要向cmake文件内添加）,将头文件和继承的父类由QWidget改为QPushButton

一个新控件如果想绑定窗口，需要设置父窗口

```c++
QPushButton *btn=new QPushButton;
btn->setParent(this);//this表示绑定当前类的窗口
```

### 对象树

当一个对象绑定到父窗口（对象）时，父对象释放（窗口关闭）时该对象会一起被释放，无需手动delete

### 信号与槽

connect(信号发送者，发送的信号，信号接收者，处理信号（槽函数）)

```c++
connect(mybtn,&Mybutton::clicked,this,&Widget::close);//(指针，函数地址，指针，函数地址)
```

自定义信号，写到类的signal:下，返回值是void，只需要声明不需要实现；可以有参数，可以发生重载

自定义槽函数，写到public slots下，高版本可以是public，或者全局函数，或者lambda

返回值void，需要声明和实现，可以有参数，可以重载

触发自定义信号emit,如：

```c++
emit this->zt->hungry();
```

#### 信号与槽函数重载

如果信号与槽函数发生重载，则先创建函数指针，指定传参再连接，避免二义性（参数不明确）

以下是重载的例子，hungry和treat传入参数为QString	```void treat(QString name);	void hungry(QString name);```

```c++
void(teacher::* teachersignal)(QString)=&teacher::hungry;
void(student::* studentSlot)(QString)=&student::treat;
connect(this->zt,teachersignal,this->st,studentSlot);
```

此时若想调用时传参，则在触发信号处传入

```c++
emit this->zt->hungry("名字");
```

注：QString类型在打印室会带有双引号，若想去掉转为char*

```c++
qDebug()<<"student"<<name.toUtf8().data();
```

##### 信号连接信号

```c++
void(teacher::* teachersignal)()=&teacher::hungry;
void(student::* studentSlot)()=&student::treat;
connect(this->zt,teachersignal,this->st,studentSlot);
```

信号可以连接信号

一个信号可以连接多个槽函数，多个信号可以连接同一个槽函数

信号与槽的类型必须一一对应

信号的参数个数可以多于槽函数，反之不行，相同部分的参数类型也要一一对应

##### 断开信号

```c++
disconnect(this->zt,teachersignal,this->st,studentSlot);//与connect一致
```

#### Qt4版本的信号和槽

```c++
connect(this->zt,SIGNAL(hungry(QString)),this->st,SLOT(treat(QString)));
```

不用函数指针，直接传函数

优点：参数直观，写法简单；缺点：编译器不检查参数类型

底层：SIGNAL和SLOT将里面内容转为字符串处理

### lambda表达式

c++11中用于定义并创建匿名的函数对象，以简化编程工作

5.5.0以下Qt版本使用lambda需要在pro文件 CONFIG+=c++11

[函数对象参数] (操作符重载函数参数)mutable->返回值{函数体}

```c++
[capture](parameters)mutable->returnType
{
    statement
}
```

**[]标识一个lambda的开始，不能省略，内部可以放不同内容实现不同功能**

空：没有使用任何函数对象参数

=：函数体内可以便甲Lambda所在作用范内所有可见的局部变量（包括Lambda所在类的this)，并且是值传递方式（相当于编译器自动为我们按值传递了所有局部变量)

&：函数体内可以使用Lambda所在作用范围内所可见的局变量（包括lambda所在类的this)，并且是引用传递方式（相当于编译器自动为我们按引用传递了所有局部变量

this：函数体内可以使用Lambda所在类中的成员变量

a：将a按值传递，只能访问a；函数体不能修改传递进来的a的拷贝，因为默认函数为const，要想修改，添加mutable修饰符

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

### QMainWindow

主窗口类，包含一个菜单栏，多个工具栏，锚接部件，状态栏，中心部件

```c++
QMenuBar *bar= menuBar();//新建菜单栏
this->setMenuBar(bar);//设置到当前菜单
QMenu* menu1= bar->addMenu("Menu");//添加菜单选项
QAction *act1=menu1->addAction("act1");//为菜单选项添加子菜单选项
menu1->addSeparator();//分割线
/*新建一个子菜单并挂到某个菜单项下*/
QMenu * subMenu=new QMenu;
subMenu->addAction("Menu1");
subMenu->addAction("Menu2");
act1->setMenu(subMenu);
/*添加工具栏*/
QToolBar * toolbar=new QToolBar(this);
addToolBar(Qt::LeftToolBarArea, toolbar);
toolbar->setAllowedAreas(Qt::LeftToolBarArea|Qt::RightToolBarArea);
toolbar->setFloatable(false);//设置允许浮动
toolbar->addAction(act1);//添加子菜单项
```

