# Linux随记
## 常用指令

```ctrl+alt+T```打开终端
```ctrl+alt+F1~F6```切换tty1~tty6操作接口环境，一般图形桌面是tty1
```locate```显示当前支持语言，在纯命令行时无法使用中文，可以修改语言 
```LANG=en_US.utf8``` ``` export LC_ALL=en_US.utf8```  前者为修改输出信息，后者为所有信息 
```data```显示日期与时间 ```cal```打开日历 ```bc```打开计算器 
```ls ls -a ls -al```为列出文件，列出含隐藏文件，列出所有文件及信息 
```cd```切换目录，```pwd```查看当前目录（不加-p为链接目录），```mkdir```建立新目录（加-p可单次建立多层目录），```rmdir```删除空目录  

```touch```创建文件，如```touch test.txt```;```cat```查看文件内容;```more```使用翻页状态查看，按q退出

```echo```打印命令，如```echo $PATH```打印环境变量，$代表后接的是变量 
```cp ~/.bashrc /tmp/bashrc```将~下的.bashrc复制到tmp并改名为bashrc,若不带路径，则为原地复制并改名，当使用mv时则为重命名效果

```ps```监控后台进程工作情况，默认只显示当前可以和用户交互的进程

## 知识记录
  + .代表当前目录  
  + 对于root和普通用户，终端~代表的路径分别是/root和/home/"用户名"，终端中的提示符也不同，分别是#和$  
  + 为命令添加选项时，-后加缩略名，--后加完整名，如帮助：-h  --help 命令太长可以用\后接回车进行换行  
  + ```ctrl+c```为停止当前程序```ctrl+d```为键盘输入结束，可当做exit ```shift+Page Up/Page Down```终端向前向后翻页  
  + 在指令前加man，如```man date```可查看有关指令的详细说明，```--help```则是更简洁的提示 有时候在```/usr/share/doc/```还会有相关文档   
  + 查看系统使用状态：```who```查看在线用户 ```netstat -a```查看网络联机状态 ```ps -aux```查看后台程序  
  + 不同用户的PATH不同，如root和普通用户，对应的命令也可能不同，将某个路径添加到PATH： ```PATH="${PATH}:/root"```将/root添加到PATH 有时因为缓存关系不会立刻生效，注销再登录即可  
#### 文件权限
  在使用```ls -al```时可看到详细信息，如```-rw-r--r--. 1 root root 1864 May 4 18:00 setup.xml```前十个字符为文件权限，后面为：<u>链接数，文件拥有者，所属用户组，文件大小，最后修改时间，文件名</u>。  
  ```-rw-r--r--```第一个为类型，-代表文件，d代表目录，l为链接文件等；后面每三个为一组，分别是：拥有者权限，用户组权限，其他人权限。完整权限为```rwx```可读，可写，可执行，无权限则显示-,复制文件时权限不变  
  修改文件用户组```chgrp [-R] 文件名```，组必须存在；修改拥有者```chown [-R] 文件名```;修改权限```chmod [-R] xyz 文件名```其中-R代表递归到子目录和文件，xyz为权限数字，r:4,w:2,x:1,选择相加为权限，如设置rwxr-x---为```chmod 750 文件名```  
  xyz也可使用符号格式代替，如```chmod u=rwx 文件名```其中u为user，可换为g(group),o(others),a(all);=为设置，可更换为+（加入），-（移除），rwx则与上同理，不过无权限为省略，不是-替换

###### 系统目录（部分必要）
|  目录   | 内容  |
|  ----  | ----  |
| /bin  | 单人维护模式可使用的命令，如cp,mkdir |
| /boot  | 内核和启动文件 |
| /dev  | 所有的接口和设备以文件格式保存于此 |
| /etc  | 配置文件 |
| /lib  | 库文件 |
| /media | 可移除设备，如光盘 |
| /mnt | 暂时挂载额外设备 |
| /opt | 第三方软件目录 |  

###### umask

用于定义新建文件或目录的默认权限，每次新建一个文件时，文件的默认权限是由`umask`的值决定的。当我们输入`umask`命令时，它会输出一个4为的八进制数值，如0002。如果umask值的**某位被设置**，在新建文件或目录时将禁用对应的权限。

例如`umask`的值为0245意味着新建的目录具有(777-002)=0532八进制权限

如果`umask`的值为000，该用户新建的文件具有(666-000)=666八进制权限

-------------------------

#### 文件操作

```bash
cp [-r] 目标文件[夹] 复制方向 #将文件复制并命名，文件夹必须带递归参数-r
cp ~/.bashrc /tmp/bashrc	#将~下的.bashrc复制到tmp并改名为bashrc,若不带路径，则为原地复制并改名
mv 目标文件[夹] 移动方向	#将文件[夹]移动并命名，原地使用则为重命名效果
rm [-r -f] 文件1 文件2 文件3 ...	#删除文件或文件夹，-r用于文件夹，-f用于强制删除	#rm支持通配符*模糊匹配
```

##### find查找文件

```bash
which 命令名	#查找命令对应的程序文件存放位置，如which cd
find 起始路径 -name "被查找文件名"	#查找文件存放位置，支持通配符，如 find / -name "test*"
find 起始路径 -size +|-数字[单位]	#按文件大小查找文件，如：find / -size +100M 查找大于100MB的文件
```

##### grep命令

grep命令可从文件中通过关键字过滤文件行

```bash
grep [-n] 关键字 路径
grep "test" test.txt #在test.txt中查找包含test的行
```

•选项-n，可选，表示在结果中显示匹配的行的行号。

•参数，关键字，必填，表示过滤的关键字，带有空格或其它特殊符号，建议使用””将关键字包围起来

•参数，文件路径，必填，表示要过滤内容的文件路径，<u>可作为内容输入端口</u>

##### wc统计命令

统计文件的行数，单词数量等

```bash
wc [-c -m -l -w] 文件路径
```

•选项，-c，统计bytes数量

•选项，-m，统计字符数量

•选项，-l，统计行数

•选项，-w，统计单词数量

•参数，文件路径，被统计的文件，<u>可作为内容输入端口</u>

##### ln链接命令

软链接

```bash
ln -s file file.s	#创建file的软连接，命名为file.s
```

软连接存储的是目标文件路径，其大小取决于路径长度；相对路径起始于当前软链接所在目录

软链接的权限默认全开放，但不影响指向文件的属性

硬链接

```bash
ln file file.h		#创建file的硬链接
```

硬链接继承源文件所有属性，对硬链接进行修改同时会修改源文件及相关的其它硬链接

硬链接与源文件及其它相同硬链接有着同样的I结点(Inode)，本质为操作系统为每个文件赋予唯一的Inode，当有相同Inode的文件存在时彼此同步

删除硬链接会将硬链接计数减1（原文件也算一个），当减为0后文件删除

##### stat查看文件信息

```bash
stat [option] file
```

添加-f选项后，则输出文件所在文件系统的信息

stat默认不遵循符号链接，添加-L,查看符号链接指向的文件信息

##### | 管道符

管道符```|```，将管道符左边命令的输出内容作为管道符右边命令的输入内容

```bash
ls ~ | grep test	#将~目录下的文件列出来，并在其中筛选出包含test的部分
```

管道符可以嵌套使用

##### echo

将内容输出在终端

```bash
echo $PATH	#将PATH文件输出在终端上
```

输出带有空格等特殊符号时建议使用" "包围，否则会被识别为新命令部分

###### 反引号

```bash
echo pwd	#输出的是pwd三个字符
echo `pwd`	#输出的是pwd命令的执行结果
```

##### 重定向符

```bash
echo "test 1" > test.txt	#将“test 1”覆盖写入test.txt
echo "test 2" >> test.txt	#将“test 2”追加写入test.txt
```

##### tail命令

tail命令可以追踪文件尾部内容

```bash
tail [-f -num] 路径	#-f为持续跟踪，num为跟踪行数，默认为10
tail -f -3 test.txt	 #持续追踪test.txt的最后三行
```

##### tar压缩

```bash
tar zcvf 文件名 压缩包名	#文件名可以有多个，最后一个是压缩包名
```

tar本质是打包工具，z代表gzip，c为创建，v为显示过程，f为文件 

将z换为j表示使用bzip2进行压缩

将c换为x，表示进行解压，如

```bash
tar zxvf 压缩包名
```

#### vi/vim编辑器

###### 命令模式（Command mode）

命令模式下，所敲的按键编辑器都理解为命令，以命令驱动执行不同的功能。此状态下，不能自由进行文本编辑。

###### 输入模式（Insert mode）

也就是所谓的编辑模式、插入模式。此模式下，可以对文件内容进行自由编辑。

###### 底线命令模式（Last line mode）

以：开始，通常用于文件的保存、退出。

```bash
vim 文件路径	#打开文件，如果不存在则新建
```

打开文件后进入命令模式，按下i/a/o进入输入模式，输入模式按esc回到命令模式

命令模式按下:进入底线命令模式，输入wq并回车保存文件并退出编辑器

## 

#### 用户与用户组

查看当前用户：```whoami```

###### 组管理（需要root权限）

```bash
groupadd 组名	#创建用户组
groupdel 组名 #删除用户组
```

###### 用户管理（需要root权限）

```bash
useradd [-g -d] 用户名		#创建用户 -g指定组，不指定则会创建用户同名组并加入；-d指定home路径，不指定则为/hmoe/用户名
useradd test -g testgroup -d /home/test3
passwd 用户名				#为用户添加密码
userdel [-r] 用户名		#删除用户 -r为把home目录一起删除，不带r则保留
id [用户名]				#查看用户所属组，不填则为自查
usermod -aG 用户组 用户名	  #修改用户组

getnet passwd	#查看当前系统有哪些用户
getnet group	#查看系统有哪些组
```

#### 终端进程

##### 挂载后台

在命令后加```&```，将其切换成后台程序，但此时该程序仍属于该shell，关闭终端后就会停止

##### jobs查看后台

查看当前系统运行的用户级的程序

##### fg，bg前后台切换

##### env查看所有环境变量

#### Linux编程工具

##### gcc

```shell
gcc -E  #预处理，得到.i文件，展开头文件，删除注释，空行等
gcc -S	#编译，得到.s文件，检查语法规范
gcc -c	#汇编，得到.o文件，将汇编指令翻译为机器指令
gcc		#链接，，得到可执行程序，数据段合并，地址回填

-Wall	#显示所有警告信息
-D		#向程序动态注册宏定义
```

编译阶段消耗系统资源最多

##### 静态库与动态库

静态库：编译时将库包含到程序文件中，使用 .o文件

动态库：将库加载到内存中，程序运行时从内存调用

###### 创建静态库：

```shell
gcc -c div1.c -o div1.o		#生成.o文件
ar rcs libmymath.a div1.o	#使用ar工具，lib[名称].a 只有中间部分可修改，两端为固定写法,.o文件可有多个
```

###### 使用静态库

```shell
gcc -c test1.c libmymath.a -o test1		#一同添加到编译文件即可，源码在前库在后
gcc test.c ./lib/libmymath.a -o a.out -I ./inc	#若头文件与源码不在同一路径，在命令末尾-I指定
```

###### 创建动态库

```shell
gcc -c add.c -o add.o -fPIC		#生成与位置无关.o代码
gcc -shared -o lib库名.so add.o sub.o div.o	#制作动态库 gcc -shared整体是制作动态库工具
```

动态库函数地址只有偏移地址，基址要在动态库加载到内存后才会填充 @plt（实际地址=基址+偏移）

###### 使用动态库

```shell
gcc test.c -o a.out -l mymath -L ./lib -I ./inc	#-l指定库名（不包含lib和.so），-L指定路径,-I指定头文件路径
```

但此时运行a.out会提提示找不到库，原因：

链接器：工作于链接阶段，需要提供-l和-L

动态链接器：工作于程序运行阶段，需要提供动态库所在位置

**此时需要通过更新环境变量来设定动态库路径**

```shell
export LD_LIBRARY_PATH=./lib
```

但此时环境变量只有效于当前终端，若想长期有效，有以下方法

**写入环境变量配置文件.bashrc，将以下内容添加到bashrc文件**

```bash
export LD_LIBRARY_PATH=./lib
```

**除了设置环境配置文件，还可以将库拷贝到标准库所在位置，但该方法需要root权限进行操作**

**可以通过修改配置文件```/etc/ld.so.conf```进行路径设置，在末尾添加库的绝对路径，然后执行生效**

```shell
sudo ldconfig -v	#-v作用是显示过程
```

动态库是否加载到内存，取决于程序是否运行

动态库每次加载位置不固定

动态库和静态库同时存在，编译器默认用动态库

##### ldd工具

ldd可以查看可执行程序运行时需要加载哪些动态库及其路径

```shell
ldd a.out
```

##### GDB调试

```shell
gcc gdbtest.c -o a.out -g	#添加-g参数启用调试,得到调试表
gdb a.out	#进入调试模式
```

gdb调试命令

```shell
list 1	#列出源码，输入l或list翻页，list数字指定位置
break 52	#设置断点，break 行号 或 b 行号
run		#运行程序，run或r
step	#单步，s
next	#下一行，n
run		#查找段错误出现位置
finish	#结束当前函数调用
set args	#设置main函数命令行参数
```

##### makefile

项目代码编译管理

目标：依赖条件

（tab缩进）命令

```shell
hello:hello.c
	gcc hello.c -o hello #前面有一个tab缩进
```

###### 基本规则
若想生成目标，检查规则中的依赖条件是否存在，如不存在，则寻找是否有规则用来生成该依赖文件

```shell
hello:hello.o
	gcc hello.o -o hello
hello.o:hello.c
	gcc -c hello.c -o hello.o
```

目标的修改时间应晚于依赖条件，若不满足说明发生了更新，make会重新生成该目标

makefile默认会将第一个目标作为最终目标，开头添加ALL可修改

```shell
ALL:a.out
```

###### makefile函数

```shell
src=$(wildcard *.c)
#找到当前目录下所有后缀为.c的文件，赋值给src
obj=$(patsubst %.c,%.o, $(src))
#把src变量里所有后缀为.c的文件替换为.o，赋值给obj
```

$(wildcard *.c) 匹配当前工作目录下所有.c文件，将文件名组成列表，赋值给src

$(patsubst 参数1, 参数2, 参数3) 将参数3中包含参数1的部分替换为参数2  $(src)表示取值

```shell
src=$(wildcard *.c)	#add.c sub.c div1.c hello.c
obj=$(patsubst %.c,%.o, $(src))	#add.o sub.o div1.o hello.o

ALL:a.out
a.out: $(obj)
	gcc $(obj) -o a.out
add.o:add.c
	gcc -c add.c -o add.o
sub.o:sub.c
	gcc -c sub.c -o sub.o
hello.o:hello.c
	gcc -c hello.c -o hello.o
div1.o:div1.c
	gcc -c div1.c -o div1.o
```

默认makefile名称固定，如果为其它名称则使用-f指定

```shell
make -f m1	#自定义名称是m1的makefile
```

clean 清除指定目标，命令行```make clean```可以执行预设指令，添加```-n```参数进行命令预览

```shell
clean:
	-rm -rf $(obj) a.out	#添加-时删除不存在文件不报错
```

###### 自动变量

只能出现在规则命令位置

```$@```：表示规则中的目标

```$^```：表示所有依赖条件

```$<```：表示第一个依赖条件，如果应用在模式规则，可将依赖条件列表中的依赖依次取出，套用模式规则

```shell
a.out: $(obj)
	gcc $^ -o $@	# $^表示obj所有内容，$@表示a.out
```

###### 模式规则

```shell
%.o:%.c
	gcc -c $< -o $@
#可以替换以下类型的指令
#add.o:add.c
#	gcc -c add.c -o add.o
```

如果目录中存在名为clean的文件，执行make clean时可能会识别为生成clean，可通过添加伪目标的方式解决

```shell
.PHONY: clean ALL
```

 make 命令后面跟的参数如果出现在.PHONY 定义的伪目标中，那就直接在Makefile中就执行伪目标的依赖和命令，不管Makefile同级目录下是否有该伪目标同名的文件

```shell
src=$(wildcard ./src/*.c)
obj=$(patsubst ./src/%.c ./obj/%.o $(src))	#此处src是带着./src/这一部分路径的，若.o路径与.c不同则需要分开 写出（事先要求.o放在obj
```

### 系统函数

##### open/close函数

函数在头文件unistd.h里，宏在fcntl.h里

**int open(const char \*pathname, int flags);**

**int open(const char \*pathname, int flags, mode_t mode);**

pathname:路径	flags：打开方式		mode：权限（一般是新建时用）

返回值为整型

**int close(int fd);**

fd：打开的文件描述符，一般为open的返回值

```c
int fd;
fd=open("./dir/test.txt",O_RDONLY|O_CREAT,0644);//打开目标文件，如果没有则新建，权限为644，权限与umask取与 
close(fd);
```

```c
printf(fd,errno,strerror(errno));//errno是一个全局变量，记录错误号，而strerror则是输出错误号对应的错误原因
```

##### read/write函数

**ssize_t read(int fd, void \*buf, size_t count);** 

**ssize_t write(int fd, const void \*buf, size_t count);**

buf：缓冲区	count：缓冲区大小

read返回值为读到的字节数，为0表示读到文件末尾，-1失败

read返回值为写入的字节数，-1失败

```c
//使用read和write实现cp
int main(int argc,char *argv[])
{
    int fd1=open(argv[1],O_RDONLY);
    if(fd1==-1){
        perror("open error");//
        exit(1);
    }
    int fd2=open(argv[2],O_RDWR|O_CREAT|O_TRUNC,0664);
    while((n=read(fd1,buf,1024))!=0){
        write(fd2,buf,n);
    }
    close(fd1);
    close(fd2);
}
```

```perror(char*)```：传出错误信息与error号对应的错误类型，类似于```printf(char*,strerror(errno))```

```strace```后接可执行程序可以跟踪系统调用

read和write函数无用户级缓冲，每次执行都会访问一次磁盘IO，因此效率低

而库函数fputc有一个4kb的用户缓冲，缓冲满或结束时才会访问磁盘IO，高于read和write

在用户和内核层(kernel)之间有一步预读入缓输出，系统会根据自身判断进行操作

![预读入缓输出](..\note\图\预读入缓输出.jpg)

阻塞/非阻塞：文件属性，当读设备文件或网络文件会出现，读常规文件不会出现该情况

```c
#include<unistd.h>
#include<stdlib.h>
#include<stdio.h>
int main(void)
{
    char buf[10];
    int n;
    n=read(STDIN_FILENO,buf,10);//STDIN_FILENO是标准输入设备（一般是键盘）的文件描述符
    if(n<0){
        perror("read STDIN_FILENO");
        exit(1);
    }
    write(STDOUT_FILENO,buf,n);
    return 0;
}
```

当read返回值为-1，且errno=EAGAIN或EWOULDBLOCK，说明不是失败，而是在以非阻塞方式读取设备或网络文件，且文件无数据



##### 文件描述符

 结构体PCB 的成员变量file_struct *file 指向文件描述符表。

 从应用程序使用角度，该指针可理解记忆成一个字符指针数组，下标0/1/2/3/4...找到文件结构体。

本质是一个键值对0、1、2...都分别对应具体地址。但键值对使用的特性是自动映射，我们只操作键不直接使用值。

![文件描述符](..\note\图\文件描述符.png)

文件描述符默认用表中未占用的最小值

##### fcntl修改文件属性

改变一个**已经打开的**文件属性

```c
flags=fcntl(STDIN_FILENO,F_GETFL);//获取stdin属性信息
if(flags==-1){
    perror("fcntl error");
    exit(1);
}
flags|=O_NONBLOCK;//位或，添加非阻塞状态
int ret=fcntl(STDIN_FILENO,F_SETFL,flags);
if(ret==-1){
    perror("fcntl error");
    exit(1);
}
```

一般使用dup而不使用fcntl

##### 重定向dup/dup2

dup使用一个未使用的最小文件描述符fd指向目标文件

成功：返回一个新文件描述符；失败：-1设置errno为相应值

```c
int main(int argc,char *argv[])
{
    int fd=open(argv[1],O_RDONLY);//fd理论为3
    int newfd=dup(fd);//newfd应为4
    printf("newfd=%d\n",newfd);
    return 0;
}
```

dup2将一个文件描述符复制给新的文件描述符

```c
int fd1=open(argv[1],O_RDWR);
int fd2=opne(argv[2],O_RDWR);
int fdret=dup2(fd1,fd2);//将fd1的 指向复制给fd2，fd2此时指向fd1的文件		fd1和fd2指向的都是fd1
printf("fdret=%d\n",fdret);//此处输出应为4
```

```c
dup2(fd1,STDOUT_FILENO);	//将屏幕输出重定向到文件
printf("----1234----");		//此行本应输出到屏幕，现在则输出到fd1文件内
```

区别：dup是自动选择，dup2是人为指定

```c
int fd1=open(argv[1],O_RDWR);
int newfd=fcntl(fd1,F_DUPFD,0);//0被占用，则fcntl使用可用的最小描述符返回，实现dup功能
int newfd2=fcntl(fd1,F_DUPFD,7);//返回不小于7的文件描述符
```



##### lseek文件读写偏移函数

off_t lseek(int fd,off_t offset,int whence)

（文件描述符，偏移量，偏移起始位置）

返回值：成功：较起始位置偏移量	失败：-1和errno

**文件的读和写使用的是相同偏移**

```c
int main(void)
{
    int fs,n;
    char msg[]="test of lseek\n";
    char ch;
    fd=open("lseek.txt",O_RDWR|O_CREAT,0644);
    if(fd<0){
        perror("open lseek.txt error");
        exit(1);
    }
    write(fd,msg,strlen(msg));//将字符写入文件
    lseek(fd,0,SEEK_SET);//修改文件读写指针位置，若不修改则指针仍在末尾，读不到内容
    while((n=read(fd,&ch,1))){
        if(n<0){
            perror("read error");
            exit(1)；
        }
        write(STDOUT_FILENO,&ch,n);
    }
    closr(fd);
    return 0;
}
```

```c
int lenth=lseek(fd,0,SEEK_END);//通过偏移获取文件大小
```

```c
int lenth=lseek(fd,111,SEEK_END);//在末尾偏移达到扩展文件大小的效果
write(fd,"\0 ",1);//lseek只是指针操作，若想真实影响到文件必须引起IO操作
```

实际使用更建议使用stat 

 ##### 目录项dentry和Inode

inode本质为结构体，存储文件的属性信息。如：权限、类型、大小、时间、用户、盘块位置，也叫作文件属性管理结构，大多数的inode都存储在磁盘上。
少量常用、近期使用的inode会被缓存到内存中。

dentry为目录项，本质也为结构体，重要成员变量有两个{文件名，inode,...}，文件内容保存在磁盘盘块中

![inode](..\note\图\inode.jpg)

##### stat函数

获取文件属性（从Inode结构图获得）

int stat(const char *path, struct stat *buf)	（文件路径，文件属性(传出参数))

返回0成功，-1失败

```c
int main(int argc,char *argv[])
{
    struct stat sbuf;
    int ret=stat(argv[1],&sbuf);//错误检查简化忽略，实际用时加上
    printf("file size:%ld\n",sbuf.st_size);//更换成员便可查看其它属性
    return 0;
}
```

stat可以使用宏函数来进行文件判断

```c
if(S_ISREG(sb.st_mode)){
    printf("is regular");
}
```

**stat默认穿透符号链接去访问指向的文件，而lstat不会**，其它用法一致

 

##### 传出参数当返回值

1.指针作为函数参数

2.在函数调用之前，指针指向的空间有意义

3.在函数内部，先读操作，再写操作

4.函数调用结束后充当函数返回值

##### link/unlink函数

目录项游离于inode之外，将文件名单独存储是为了实现文件共享。Linux允许多个目录项共享一个inode，即共享盘块(data)。不同文件名，在内核眼里是同一个文件。link函数，可以为已经存在的文件创建目录项(硬链接)。

int link(const char *oldpath, const char *newpath);

**mv命令既是修改了目录项，而并不修改文件本身**

unlink删除一个文件的目录项

int unlink(const char *pathname); 

**unlink只是让文件具备被释放条件，实际要等待占用文件的程序结束后才会释放，在程序结束前该文件仍可读写**

**readlink**：读链接文件

##### 目录函数

```DIR *opendir(const char* name);```	根据传入的目录名打开一个目录，成功返回目录结构体指针，失败返回NULL

```int closedir(DIR *dirp)```关闭打开的目录，成功返回0，失败1并设置errno

```struct dirent *readdir(DIR *dirp);```成功返回**目录项**结构体指针；失败返回NULL并设置errno为相应值

```dirent```包含```inode```和```char dname[256]```

```c
int main(int argc,char *argv[])
{
    DIR *dp;
    struct dirent *sdp;
    dp=opnedir(argv[1]);
    if(dp==null){
        perror("opendir error");
        exit(1);
    }
    while((sdp=readdir(dp))!=NULL)//readdir运行一次获取一个目录项，到结尾时返回null
    {
        printf("%s\t",sdp->d_name);
    }
    printf("\n");
    closedir(dp);
    return 0;
}
```

##### 递归实现遍历目录

1.判断命令行参数argv[1]

2.判断用户指定的是否目录	stat S_ISDIR()或isFile 

3.读目录：

opendir(dir)

如果是普通文件则打印，目录则递归调用自己，需要拼接目录访问绝对路径

closedir()

```c


#define PATH_LEN 256
void fetchdir(const char *dir,void(*fcn)(char*))
{
    char name[PATH_LEN];
    struct dirent *sdp;
    DIR *dp;
    if((dp=opendir(dir))==NULL)//打开目录失败
    {
        //perror("fecth can't open");
        fprint(stderr,"fetchdir:can't open %s\n",dir);
        return;
    }
    while((sdp=readdir(dp))!=NULL)
    {
        if(strcmp(sdp->d_name,".")==0||strcmp(sdp->d_name,"..")==0)//目录里包含.和..两项，防止无限递归
        {
            continue;
        }
        if(strlen(dir)+strlen(sdp->d_name)+2>sizeof(name))//dentry长度256上限,+2是\分隔符和\0结束标记
        {
            fprintf(stderr,"fecthdir:name %s %s too long\n",dir,sdp->d_name);
        }
        else{
            sprintf(name,"%s/%s",dir,sdp->d_name);//拼接
            (*fcn)(name);
        }
    }
    closedir(dp);
}
void isfile(char *name)
{
    struct stat sbuf;
    if(stat(name,&sbuf)==-1)//文件名无效
    {
        fprintf(stderr,"isfile:can't access %s\n",name);
        exit(1);
    }
    if((sbuf.st_mode&S_IFMT)==S_IFDIR)//判断是否为目录
    {
        fetchdir(name,isfile);		//回调函数  
    }
    printf("%8ld %s\n",sbuf.st_size,name);	//不是目录则打印文件名
}
int main(int argc,char *argv[])
{
    if(argc==1)
        isfile(".");
    else
        while(--argc>0)//多个目录
            isfile(*++argv);//逐个调用命令行传入的目录
    return 0;
}
```

函数 F1 调用函数 F2 的时候，函数 F1 通过参数给 函数 F2 传递了另外一个函数 F3 的指针，在函数 F2 执行的过程中，函数F2 调用了函数 F3，这个动作就叫做回调（Callback）



```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/stat.h>
#include <pthread.h>
void isFile(char *name);
void read_dir(char *dir)//处理目录
{
    char path[256];
    DIR *dp;
    struct dirent *sdp*;
    dp=opendir(dir);
    if(dp==NULL){
        perror("opendir error");
        return;
    }
    while((sdp=readdir(dp))!=NULL){//读取目录项
        if(strcmp(sdp->d_name,".")==0||strcmp(sdp->d_name,"..")==0){
            continue;
        }
        sprintf(path,"%s/%s",dir,sdp->d_name);//目录项本身不可访问，需要拼接路径 
        isFile(path);
    }
    closedir(dp);
    return ;
}
void isFile(char *name)
{
    int ret=0;
    struct stat sb;
    ret=stat(name.&sb);//获取文件属性
    if(ret==-1){
        perror("stat error");
        return;
    }
    if(S_ISDIR(sb.st_mode)){
        read_dir(name);
    }
    printf("%10s\t\t%ld\n",name,sb.st_size);
    return 0;
}
int main(int argc,char* argv[])
{
    if(argc==1){
        isFile(".");//如果输入为空，则读当前目录
    }else{
        isFile(argv[1]);
    }
    return 0;
}
```



### 进程相关

程序：未运行的编译好的二进制文件，只占用磁盘

进程：是活跃的程序，占用系统资源。在内存中执行

并发：一个时间段有多个进程在运行

MMU：虚拟内存与物理内存映射

PCB：进程控制块，本质是结构体



##### 进程状态

初始态，就绪态，运行态，挂起态与终止态

#####  环境变量env

① 字符串(本质) ② 有统一的格式：名=值[:值] ③ 值用来描述进程环境信息

使用形式：与命令行参数类似。

加载位置：与命令行参数类似。位于用户区，高于stack的起始位置。

引入环境变量表：须声明环境变量。extern char ** environ

一些比较重要的环境变量的含义如下：

PATH：可执行文件搜索路径

SHELL：当前shell，通常为/bin/bash

TERM：当前终端类型

LANG：语言和locate

HOME：当前主目录路径

##### 进程控制

###### fork函数

创建一个子进程，成功时返回 PID，失败返回-1

fork系统调用进入内核，内核copy进程，修改pid，并把pid通过eax寄存器返回给父进程

```c
pid_t pid=fork(); 
```

创建子进程后，父进程继续执行，子进程异步接着fork()之后的部分运行

此时子进程和父进程都会返回一个pid，子进程内返回0，父进程内返回子进程的pid

```c
getpid();//返回当前进程pid
getppid();//返回父进程pid
```

如果进程内有多个fork()，子进程创建后也会执行后续的fork()，此时可以提高判断fork()返回值是否为0来判断当前是父进程还是子进程

##### PCB进程控制块

进程控制块（PCB）来维护进程相关的信息，Linux内核的进程控制块是task_struct结构体。

- 进程id。系统中每个进程有唯一的id，在C语言中用pid_t类型表示，其实就是一个非负整数。

- 进程的状态，有就绪、运行、挂起、停止等状态。
- 进程切换时需要保存和恢复的一些CPU寄存器。
- 描述虚拟地址空间的信息。
- 描述控制终端的信息。
- 当前工作目录（Current Working Directory）。
- umask掩码。
- 文件描述符表，包含很多指向file结构体的指针。
- 和信号相关的信息。
- 用户id和组id。
- 会话（Session）和进程组。
- 进程可以使用的资源上限（Resource Limit）。

##### 进程共享

父子相同处: 全局变量、.data、.text、栈、堆、环境变量、用户ID、宿主目录、进程工作目录、信号处理方式...等（几乎是用户空间0-3G所有内容）

父子不同处: 1.进程ID  2.fork返回值  3.父进程ID  4.进程运行时间  5.闹钟(定时器)  6.未决信号集

父子进程间资源遵循**读时共享写时复制**

父子进程共享：1. 文件描述符(打开文件的结构体) 2. mmap建立的映射区 (进程间通信详解)

##### 父子进程GDB

gdb只能跟踪一个进程，可以在**fork()函数调用之前**设置跟踪进程，默认父进程

```set follow-fork-mode child``` 命令设置gdb在fork之后跟踪子进程。

```set follow-fork-mode parent``` 设置跟踪父进程。

##### exec函数族

#include<unistd.h>

fork创建子进程后执行的是和**父进程相同**的程序（但有可能执行不同的代码分支），子进程要调用一种exec函数以执行另一个程序。当进程调用一种exec函数时，该进程的用户空间代码和数据完全**被新程序替换**，从新程序的启动例程开始执行。调用exec并不创建新进程，所以调用exec前后该进程的**id并未改变**

 **不再执行父进程程序内容，而是执行指定的新程序**

exec函数只有出错时返回 

###### execlp函数

加载一个进程，借助PATH环境变量    

```int execlp(const char *file, const char *arg, ...);```       成功：无返回；失败：-1

file:要加载的程序的名字。该函数需要配合PATH环境变量来使用，当PATH中所有目录搜索后没有参数1则出错返回。

arg:提供给程序的参数

```c
execlp("ls","ls","-l","-d","-h",NULL);//NULL的作用为结尾哨兵
//exec函数的arg是从argv[0]开始算的，而argv[0]是程序本身，后续参数是从1开始计数,如果不多加ls，此时argv[0]是-l
//终端中ls -l里的-l位置是argv[1]
```

也可以指定一个字符数组用来传参

```c
char *argv[]={"ls","-l","-h",NULL};
```



###### execl函数

根据路径加载程序

 int execl(const char *path, const char *arg, ...)

除了程序使用路径传递，其余用法相同

###### execvp函数

加载一个进程，使用自定义环境变量env

##### 回收子进程

###### 孤儿进程：

父进程先于子进程结束，则子进程成为孤儿进程，子进程的父进程改为init进程，称为init进程领养孤儿进程

###### 僵尸进程:

子进程终止，父进程尚未回收，子进程残留资源（PCB）存放于内核中，变成僵尸（Zombie）进程

僵尸进程不能用kill清除，通过kill父进程，由init进行接管，再进行回收

###### wait函数回收子进程

一个进程在终止时会关闭所有文件描述符，释放在用户空间分配的内存，但它的PCB还保留着，内核在其中保存了一些信息：如果是正常终止则保存着退出状态，如果是异常终止则保存着导致该进程终止的信号是哪个。这个进程的父进程可以调用wait或waitpid获取这些信息，然后彻底清除掉这个进程

父进程调用wait函数可以回收子进程终止信息。该函数有三个功能：

① 阻塞等待子进程退出 

② 回收子进程残留资源 

③ 获取子进程结束状态(退出原因)。

wait执行后会一直等待，直到子进程结束

**pid_t wait(int \*status);**  成功：清理掉的子进程ID；失败：-1 (没有子进程)

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/wait.h>
int main(){
    oid_t pid,wpid;
    int status;
    pid=fork();
    if(pid==0){//子进程
        printf("---child,parent is %d \n",getppid());
        sleep(10);
        printf("-----child die-----\n");
        return 70;
    }else if(pid>0){
        wpid=wait(%status);//子进程未终止时会一直阻塞在这
        if(wpid==-1)
        {
            perror("wair error");
            exit(1);
        }
        if(WIFEXITED(status)){//为真，说明正常终止
            printf("child exit with %d/n",WEXITSTATUS(status));
        }
        if(WIFSIGNALED(status))//为真，说明被信号终止
        {
            printf("child killed by signal %d/n",WTERMSIG(status));
        }
        printf("----parent wait finish\n");
    }else{
        perror("fork");
        return 1;
    }
    return 0;
}
```

###### waitpid回收子进程

作用同wait，但可指定pid进程清理，**可以**不阻塞（实际取决于选项）

注意：**一次wait或waitpid调用只能清理一个子进程**，清理多个子进程应使用循环

**pid_t waitpid(pid_t pid, int \*status, in options);**

参数pid： 

**> 0** **回收指定ID**的子进程  

**-1** **回收任意子进程（相当于wait**）

0 回收和当前调用waitpid一个组的所 有子进程

 < -1 回收指定进程组内的任意子进程

waitpid回收**多个**子进程：循环执行waitpid，直到返回值为-1

非阻塞：

```c
while((wpid=waitpid(-1,NULL,WNOHANG))!=-1){
    if(wpid>0){
        printf("wait child %D \n",wpid);
    }
    else if(wpid==0){
        sleep(1);
        continue;
    }
}
```

阻塞：

```c
while((wpid=waitpid(-1,NULL,0))){
    printf("wait child %D \n",wpid);
}
```

 ### 进程间通信

进程间通信本质是一块缓冲区，目前常用方法有：

① 管道 (使用最简单)

② 信号 (开销最小)

③ mmap共享映射区 (无血缘关系)

④ socket本地套接字 (最稳定)

#### 管道

1. 其本质是一个伪文件(实为内核缓冲区) 

2. 由两个文件描述符引用，一个表示读端，一个表示写端。

3. 规定数据从管道的写端流入管道，从读端流出。

管道的原理: 管道实为内核使用环形队列机制，借助内核缓冲区(4k)实现。

管道的局限性：

① 自己写，不能自己读。

② 管道中数据不可反复读取。一旦读走，管道中不再存在。 

③ 采用半双工通信方式，数据只能在单方向上流动。

④ 只能在有公共祖先的进程间使用管道。

文件：普通文件，目录，软链接

伪文件（不占磁盘空间）：字符，块，管道，套接字

创建并打开管道：**int pipe(int pipefd[2])**

参数：fd[0]:读端	fd[1]:写端	返回值：成功返回0，失败-1	errno

```c
//通过管道，使父进程向子进程传数据
int fd[2];
ret=pipe(fd);
pid_t pid;
pid=fork();
char* str="hello pipe\n";
char buf[1024];
if(pid>0){
    close(fd[0]);//关闭读取占用
	write(fd[1],str,strlen(str));
	close(fd[1]);
    sleep(1);
}
else if(pid==0){
    close(fd[1]);//关闭写入占用
    ret=read(fd[0],buf,sizeof(buf));
    write(STDOUT_FILENO,buf,ret);
    close(fd[0]);
}

```

① 读管道： 1. 管道中有数据，read返回实际读到的字节数。

​                    2. 管道中无数据：

​						(1) 管道写端被全部关闭，read返回0 (类似读到文件结尾)

​                        (2) 写端没有全部被关闭，read阻塞等待(不久的将来可能有数据递达，此时会让出cpu)

  ② 写管道： 1. 管道读端全部被关闭， 进程异常终止(也可使用捕捉SIGPIPE信号，使进程不终止)

​                       2. 管道读端没有全部关闭： 

​							(1) 管道已满，write阻塞。（实际上系统会自动扩容）

​                       	 (2) 管道未满，write将数据写入，并返回实际写入的字节数。

```c
//父子进程实现ls | wc -l
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
int main(int argc,char *argv[])
{
    int fd[2];
    int ret;
    pid_t pid;
    ret=pipe(fd);
    if(ret=-1){sys_err("pipe error");}
    pid=fork();
    if(pid==-1){sys_err("fork_error");}
    else if(pid>0){//父进程
        close(fd[0]);
        dup2(fd[0],STDIN_FILENO);//输入重定向到键盘输入
        execlp("wc","wc","-l",NULL);}
    else if(pid==0){
        close(fd[1]);//子进程
        dup2(fd[1],STDOUT_FILENO);//输出重定向到屏幕
        execlp("ls","ls",NULL);}
    return 0;
}
//由于父进程先执行( exexlp将程序转走所以此处sleep无效)，若让子进程读则会一直阻塞
//兄弟进程类似，但要注意关闭父进程占用
```

#### 命名管道FIFO

管道(pipe)只能用于“有血缘关系”的进程间。但通过FIFO，不相关的进程也能交换数据。

***int mkfifo(const char *pathname,mode_t mode) ***

pathname：路径名/管道名		mode：umask掩码

失败返回-1

```c
int ret=mkfifo("mytestfifo",0664);
if (ret==-1){printf("error");}
```

运行后会创建一个管道文件在指定位置，其它进程可以通过文件操作方式进行访问

一旦使用mkfifo创建了一个FIFO，就可以使用open打开它，常见的文件I/O函数都可用于fifo。如：close、read、write、unlink等。不支持lseek

已读取的数据在管道里消失，不能再读

fifo支持多读端多写端

#### 存储映射

把磁盘文件映射到内存 。当从缓冲区中取数据，就相当于读文件中的相应字节。于此类似，将数据存入缓冲区，则相应的字节就自动写入文件。这样，就可在不适用read和write函数的情况下，使用地址（指针）完成I/O操作。

##### mmap函数

void *mmap(void *adrr, size_t length, int prot, int flags, int fd, off_t offset); 

addr：指定映射区首地址，通常传NULL（自动）	length：共享内存映射区大小	prot：共享内存映射区读写属性	PROT_READ ，PROT_WRITE等

flags：标注共享属性 MAP_SHARED，MAP_PRIVATE		fd:用于创建内存映射区的文件描述符		offset：偏移位置，4K整数倍 

返回值 ：

成功：返回创建的映射区首地址；	失败：**MAP_FAILED**宏

```c
//mmap建立映射区
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<sys/mman.h>
#include<fcntl.h>
#include<unistd.h>
#include<pthread.h>
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
int main(int argc,char *argv[]){
	char *p=NULL;
	int fd;
	fd=open("testmap",O_RDWR|O_CREAT|O_TRUNC,0644);
	if(fd==-1)
    sys_err("open error");
//lseek(fd,10,SEEK_END);
//write(fd,"\0",1);
	ftruncate(fd,10);
	int len=lseek(fd,0,SEEK_END);
	p=mmap(NULL,len,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
	if(p==MAP_FAILED){
	    sys_err("mmap error");
	}
	strcpy(p,"hello mmap");//使用p对文件进行读写操作
	printf("-----%s\n",p);
	int ret=munmap(p,len);
	if(ret==-1)
	{
	    sys_err("mummap error");
	}
	return 0;
}
```

##### munmap函数

类似free，删除特定地址区域的对象映射。

同malloc函数申请内存空间类似的，mmap建立的映射区在使用结束后也应调用类似free的函数来释放。

**int munmap(void *addr, size_t length);** 				成功：0； 失败：-1

##### 注意事项

1.当用于创建映射区的文件的大小为0，然后mmap指定非0大小创建映射区，发生总线错误（用于映射的文件必须要有实际大小）；指定0大小创建映射区，参数无效

2.当用于创建映射区的文件属性为只读，映射区属性为读写，发生参数错误

3.mmap建立映射区会读一次文件，需要READ权限

4.文件描述符fd在mmap创建映射区完成后就可关闭，后续靠地址访问

5.偏移量offset必须是4096整数倍（MMU最小单位是4K）

6.对申请的内存不能越界访问

7.munmap用于释放的地址，必须是mmap申请返回的地址（若执行p++过则不是原来的地址）

8.若映射区权限为私有“MAP_PRIVATE”，则修改只在内存有效，不会修改磁盘

###### MMAP函数的安全调用方式

1.fd=open("文件名",O_RDWR)

2.mmap(NULL,有效文件大小，PROT_READ|PROT_WRITE,MAP_SHARED,fd,0)

###### 父子进程使用mmap进程间通信

父进程先创建映射区	open(O_RDWR) 

指定共享权限	mmap(MAP_SHARED)

fork()创建子进程

###### 无血源性进程mmap通信

写进程先打开文件，权限共享，再用mmap创建映射区并写入

读进程打开同名文件，创建映射区并读取

mmap：数据可反复读取

fifo：数据仅能读取一次

##### 匿名映射

通常为了建立映射区要open一个temp文件，创建好了再unlink、close掉，比较麻烦。 可以直接使用匿名映射来代替。

匿名映射只能用于血缘关系进程通信

使用MAP_ANONYMOUS (或MAP_ANON)， 如: 

​     **int *p = mmap(NULL, 4, PROT_READ|PROT_WRITE, MAP_SHARED|MAP_ANONYMOUS, -1, 0);**

 "4"为随意举例，该位置表大小，可依实际需要填写。



值得注意的是：MAP_ANON和 /dev/zero 都不能应用于非血缘关系进程间通信。只能用于亲子进程间

/dev/null（空设备文件或黑洞文件）是一个特殊的设备文件，所有写入其中的数据，都会被丢弃的无影无踪，/dev/null通常被用于丢弃不需要的数据输出，或作为用于输入流的空文件。这些操作通常由重定向完成。

/dev/zero是一个特殊的字符设备文件，当我们使用或读取它的时候，它会提供无限连续不断的空的数据流（特殊的数据格式流）。

### 信号

简单，不能携带大量信息，满足条件才发送

A给B发送信号，B收到信号之前执行自己的代码，收到信号后，不管执行到程序的什么位置，都要暂停运行，去处理信号，处理完毕再继续执行。与硬件中断类似——异步模式。但信号是软件层面上实现的中断，早期常被称为“软中断”。

**每个进程收到的所有信号，都是由内核负责发送的，内核处理。**

**产生信号**: 

1. 按键产生，如：Ctrl+c、Ctrl+z、Ctrl+\
2. 系统调用产生，如：kill、raise、abort
3. 软件条件产生，如：定时器alarm
4. 硬件异常产生，如：非法访问内存(段错误)、除0(浮点数例外)、内存对齐出错(总线错误)
5. 命令产生，如：kill命令

**递达**：递送并且到达进程。

**未决**：产生和递达之间的状态。主要由于阻塞(屏蔽)导致该状态。

**信号的处理方式:** 

1. 执行默认动作 
2. 忽略(丢弃) 
3. 捕捉(调用户处理函数)

进程控制块PCB还包含了信号相关的信息，主要指阻塞信号集和未决信号集。一般以01位表示是否触发

**阻塞信号集**(**信号屏蔽字**)： 将某些信号加入集合，对他们设置屏蔽，当屏蔽x信号后，再收到该信号，该信号的处理将推后(解除屏蔽后)

**未决信号集**: 

1. 信号产生，未决信号集中描述该信号的位立刻翻转为1，表信号处于未决状态。当信号被处理对应位翻转回为0。这一时刻往往非常短暂。 
2. 信号产生后由于某些原因(主要是阻塞)不能抵达。这类信号的集合称之为未决信号集。在屏蔽解除前，信号一直处于未决状态。

**信号4要素：**

1. 编号 2. 名称 3. 事件 4. 默认处理动作 

### Linux常规信号一览表

1) SIGHUP: 当用户退出shell时，由该shell启动的所有进程将收到这个信号，默认动作为终止进程

2) SIGINT：当用户按下了<Ctrl+C>组合键时，用户终端向正在运行中的由该终端启动的程序发出此信号。默认动作为终止进程。

3) SIGQUIT：当用户按下<ctrl+\>组合键时产生该信号，用户终端向正在运行中的由该终端启动的程序发出些信号。默认动作为终止进程。

4) SIGILL：CPU检测到某进程执行了非法指令。默认动作为终止进程并产生core文件
5) SIGTRAP：该信号由断点指令或其他 trap指令产生。默认动作为终止里程 并产生core文件。
6) SIGABRT: 调用abort函数时产生该信号。默认动作为终止进程并产生core文件。
7) SIGBUS：非法访问内存地址，包括内存对齐出错，默认动作为终止进程并产生core文件。
8) SIGFPE：在发生致命的运算错误时发出。不仅包括浮点运算错误，还包括溢出及除数为0等所有的算法错误。默认动作为终止进程并产生core文件。
9) **SIGKILL**：无条件终止进程。本信号不能被忽略，处理和阻塞。默认动作为终止进程。它向系统管理员提供了可以杀死任何进程的方法。
10) SIGUSE1：用户定义 的信号。即程序员可以在程序中定义并使用该信号。默认动作为终止进程。
11) SIGSEGV：指示进程进行了无效内存访问。默认动作为终止进程并产生core文件。
12) SIGUSR2：另外一个用户自定义信号，程序员可以在程序中定义并使用该信号。默认动作为终止进程。
13) SIGPIPE：Broken pipe向一个没有读端的管道写数据。默认动作为终止进程。
14) SIGALRM: 定时器超时，超时的时间 由系统调用alarm设置。默认动作为终止进程。
15) SIGTERM：程序结束信号，与SIGKILL不同的是，该信号可以被阻塞和终止。通常用来要示程序正常退出。执行shell命令Kill时，缺省产生这个信号。默认动作为终止进程。
16) SIGSTKFLT：Linux早期版本出现的信号，现仍保留向后兼容。默认动作为终止进程。
17) SIGCHLD：子进程状态发生变化时，父进程会收到这个信号。默认动作为**忽略**这个信号。
18) SIGCONT：如果进程已停止，则使其继续运行。默认动作为继续/忽略。
19) **SIGSTOP**：停止进程的执行。信号不能被忽略，处理和阻塞。默认动作为暂停进程。
20) SIGTSTP：停止终端交互进程的运行。按下<ctrl+z>组合键时发出这个信号。默认动作为暂停进程。
21) SIGTTIN：后台进程读终端控制台。默认动作为暂停进程。
22) SIGTTOU: 该信号类似于SIGTTIN，在后台进程要向终端输出数据时发生。默认动作为暂停进程。
23) SIGURG：套接字上有紧急数据时，向当前正在运行的进程发出些信号，报告有紧急数据到达。如网络带外数据到达，默认动作为忽略该信号。
24) SIGXCPU：进程执行时间超过了分配给该进程的CPU时间 ，系统产生该信号并发送给该进程。默认动作为终止进程。
25) SIGXFSZ：超过文件的最大长度设置。默认动作为终止进程。
26) SIGVTALRM：虚拟时钟超时时产生该信号。类似于SIGALRM，但是该信号只计算该进程占用CPU的使用时间。默认动作为终止进程。
27) SGIPROF：类似于SIGVTALRM，它不公包括该进程占用CPU时间还包括执行系统调用时间。默认动作为终止进程。
28) SIGWINCH：窗口变化大小时发出。默认动作为忽略该信号。
29) SIGIO：此信号向进程指示发出了一个异步IO事件。默认动作为忽略。
30) SIGPWR：关机。默认动作为终止进程。
31) SIGSYS：无效的系统调用。默认动作为终止进程并产生core文件。
32) SIGRTMIN ～ (64) SIGRTMAX：LINUX的实时信号，它们没有固定的含义（可以由用户自定义）。所有的实时信号的默认动作都为终止进程。

##### kill信号和函数

**int kill(pid_t pid,int sig)**

用于向任何进程组或进程发送信号，本身与kill命令不同

 成功：0；失败：-1

pid > 0: 发送信号给指定的进程。

pid = 0: 发送信号给 "与调用kill函数进程属于同一进程组的所有进程"。

pid < 0: 取|pid|发给对应进程组。

pid = -1：发送给进程有权限发送的系统中所有进程。

sig：不推荐直接使用数字，应使用宏名，因为不同操作系统信号编号可能不同，但名称一致

```c
kill(getppid(),SIGKILL);//SIGKILL是发送的信号
```

使用kill命令时，在id号前加-号可以杀死进程组

```shell
kill -9 12489//杀死12489进程
kill -9 -12489//杀死1249进程组
```

##### alarm函数

设置定时器(闹钟)。在指定seconds后，内核会给当前进程发送14）SIGALRM信号。进程收到该信号，默认动作终止。

**每个进程都有且只有唯一个定时器。**

**unsigned int alarm(unsigned int seconds)**; 		返回0或剩余的秒数，无失败。

alarm(0)则为取消定时，返回剩余秒数

使用time命令可查看程序运行时间

##### setitimer函数

设置定时器(闹钟)。 可代替alarm函数。**精度微秒us**，可以实现周期定时。

**int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value)**;   成功：0；失败：-1，设置errno

参数：which：指定定时方式

​		  ① 自然定时：ITIMER_REAL → 14）SIGLARM                       以系统真实的时间来计算，它送出SIGALRM信号

​          ② 虚拟空间计时(用户空间)：ITIMER_VIRTUAL → 26）SIGVTALRM   以该进程在用户态下花费的时间来计算，它送出SIGVTALRM信号。

​          ③ 运行时计时(用户+内核)：ITIMER_PROF → 27）SIGPROF       以该进程在用户态下和内核态下所费的时间来计算。它送出SIGPROF信号

与之对应的还有getitimer函数

itimerval 类型：

```c
struct itimerval {
    struct timeval it_interval; /* next value */
    struct timeval it_value;    /* current value */
};
//itimeval又是由两个timeval结构体组成，timeval包括tv_sec和tv_usec两部分。当中tv_se为秒。tv_usec为微秒
struct timeval {
    time_t      tv_sec;         /* seconds */
    suseconds_t tv_usec;        /* microseconds */
};
```

**settimer工作机制是，先对it_value倒计时，当it_value为零时触发信号。然后重置为it_interval。继续对it_value倒计时。一直这样循环下去。**

old_value參数，通常使用不上设置为NULL，它是用来存储上一次setitimer调用时设置的new_value值

```c
void myfunc(int signo)
{
    printf("hello world\n");
}
int main(void)
{
    struct itimerval it,oldit;
    signal(SIGALRM,myfunc);
    it.it_value.tv_sec=2;
    it.it_value.tv_usec=0;
    it.it_interval.tv_sec=5;
    it.it_interval.tv_usec=0;
    if(setitimer(ITIMER_REAL,&it,&oldit)==-1){
        perror("setitimer error");
        return -1;
    }
    while(-1);
    return 0;
}
```

### 信号集操作函数

内核通过读取**未决信号集**来判断信号是否应被处理。信号屏蔽字mask可以影响未决信号集。而我们可以在应用程序中自定义阻塞信号集set来改变mask

可通过改变阻塞信号集来改变未决信号集，阻塞和未决信号集初始都是0

#### 信号集设定

sigset_t set;           // typedef unsigned long sigset_t; 自定义信号集

int sigemptyset(sigset_t *set);           信号集清0                成功：0；失败：-1

int sigfillset(sigset_t *set);                  信号集置1               成功：0；失败：-1

int sigaddset(sigset_t *set, int signum);      将某个信号加入信号集         成功：0；失败：-1

int sigdelset(sigset_t *set, int signum);       将某个信号清出信号集        成功：0；失败：-1

int sigismember(const sigset_t *set, int signum);  判断某个信号是否在信号集中    返回值：在集合：1；不在：0；出错：-1

 sigset_t类型的本质是位图。但不应该直接使用位操作，而应该使用上述函数，保证跨系统操作有效

#### sigprocmask函数

用来屏蔽信号、解除屏蔽也使用该函数。其本质，读取或修改进程的信号屏蔽字(PCB中)

  **严格注意，屏蔽信号：只是将信号处理延后执行(延至解除屏蔽)**；而忽略表示将信号丢处理。

int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);   成功：0；失败：-1，设置errno

参数：

 set：传入参数，是一个位图，set中哪位置1，就表示当前进程屏蔽哪个信号。

 oldset：传出参数，保存旧的信号屏蔽集。

 how参数取值：   假设当前的信号屏蔽字为mask

1. SIG_BLOCK: 当how设置为此值，set表示需要屏蔽的信号。相当于 mask = mask|set
2. SIG_UNBLOCK: 当how设置为此，set表示需要解除屏蔽的信号。相当于 mask = mask & ~set
3. SIG_SETMASK: 当how设置为此，set表示用于替代原始屏蔽及的新屏蔽集。相当于 mask = set若，调用sigprocmask解除了对当前若干个信号的阻塞，则在sigprocmask返回前，至少将其中一个信号递达。

![信号屏蔽字](..\note\图\信号屏蔽字.jpg)

set是用户自定义的，mask是PCB内置的

#### sigpending函数

读取当前进程的**未决**信号集

int sigpending(sigset_t *set); set传出参数。  返回值：成功：0；失败：-1，设置errno

 ```c
 #include<stdio.h>
 #include<stdlib.h>
 #include<string.h>
 #include<signal.h>
 #include<unistd.h>
 #include<errno.h>
 #include<pthread.h>
 void sys_err(const char *str)
 {
     perror(1);
     exit(1);
 }
 void print_set(sigset_t *set)
 {
     int i;
     for(i=1;i<32;i++){
         if(sigismember(&set,i))
             putchar('1');
         else
             putchar('0');
     }
     printf("\n");
 }
 int main(int argc,char *argv[])
 {
     sigset_t set,oldset,pedset;
     int ret=0;
     sigemptyset(&set);
     sigaddset(&set,SIGINT);//阻塞信号ctrl+c
     ret=sigprocmask(SIG_BLOCK,&set,&oldset);
     if(ret==-1)
         sys_err("sigprocmask error");
     while(1){
         ret=sigpending(&pedset);
     	if(ret==-1)
         	sys_err("sigpending error");
     	print_set(&pedset);
         sleep(1);
     }    
     return 0;
 }
 //运行后ctrl+c操作被阻塞
 ```

### 信号捕捉

#### signal函数

**注册**一个信号捕捉函数

```c
void sig_catch(int signo){
    printf("catch sig: %d\n",signo);
    return;
}
signal(SIGINT,sig_catch);//只需注册，当ctrl+c触发时自动执行
while(1);
```

#### sigaction函数

修改信号处理动作（通常在Linux用其来注册一个信号的捕捉函数）

**int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);** 成功：0；失败：-1，设置errno

参数：

​		  act：传入参数，新的处理方式。

​          oldact：传出参数，旧的处理方式。

###### sigaction结构体

```c
struct sigaction {
   void   (*sa_handler)(int);
   void   (*sa_sigaction)(int, siginfo_t *, void *);
   sigset_t  sa_mask; 
   int    sa_flags; 
   void   (*sa_restorer)(void);
  };
```



​     sa_restorer：该元素是过时的，不应该使用，POSIX.1标准将不指定该元素。(弃用)

​     sa_sigaction：当sa_flags被指定为SA_SIGINFO标志时，使用该信号处理程序。(很少使用)

① sa_handler：指定信号捕捉后的处理函数名(即注册函数)。也可赋值为SIG_IGN表忽略 或 SIG_DFL表执行默认动作

② sa_mask: 调用信号处理函数时，所要屏蔽的信号集合(信号屏蔽字)。注意：仅在处理函数被调用期间屏蔽生效，是临时性设置。

③ sa_flags：通常设置为0，表使用默认属性。

```c
void sig_catch(int signo)//回调函数
{
    printf("catch sig: %d\n",signo);//signo为SIGINT
    return;
}
int main(int argc,char *argv[]){
    struct sigaction act,oldact;
    act.sa_handler=sig_catch;//设置回调函数
    sigemptyset(&(act.sa_mask));//清空屏蔽字，当sig_catch工作有效
    act.sa_flags=0;
    
    int ret=sigaction(SIGINT,&act,&oldact);//注册信号捕捉函数
    if(ret==-1)
        sys_err("sigaction error");
    while(1);
    return 0;
}
```

#### 信号捕捉特性

1. 进程正常运行时，默认PCB中有一个信号屏蔽字，假定为☆，它决定了进程自动屏蔽哪些信号。当注册了某个信号捕捉函数，捕捉到该信号以后，要调用该函数。而该函数有可能执行很长时间，在这期间所屏蔽的信号不由☆来指定。而是用sa_mask来指定。调用完信号处理函数，再恢复为☆。
2. XXX信号捕捉函数执行期间，XXX信号自动被屏蔽。
3. 阻塞的常规信号不支持排队，产生多次只记录一次。（后32个实时信号支持排队

#### 内核实现信号捕捉过程

![信号捕捉内核](..\note\图\信号捕捉内核.jpg)

#### SIGCHLD信号

产生条件：

子进程终止时

子进程接收到SIGSTOP信号停止时

子进程处在停止态，接受到SIGCONT后唤醒时

```c
//借助信号回收子进程
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/wait.h>
#include <signal.h>
void sys_err(char *str)
{
    perror(str);
    exit(1);
}
void catch_child(int signo)
{
    pid_t wpid;
    wpid=wait(NULL);
    
    if(wpid==-1)
        sys_err("wait error");
    printf("catch child id %d\n",wpid);
    return ;
}
int main(int argc,char*argv[])
{
    pid_t pid;int i;
    for (i = 0; i < 10; i++) {
    if ((pid = fork()) == 0)
        break;
    }
	if(10==i){
        struct sigaction act;
        act.sa_handler=catch_child;//设置回调函数
        sigemptyset(&act.sa_mask);//设置捕捉函数执行期间屏蔽字
        act.sa_flags=0;
        sigaction(SIGCHLD,&act,NULL);//注册信号捕捉函数
        printf("i'm parent,pid=%d\n",getpid());
        while(1); 
    }else{
        printf("i'm child,pid=%d\n",getpid());
    }
    return 0;
}
//由于信号回调函数执行期间不处理新信号，此方法会产生僵尸进程
```

```c
void catch_child(int signo)
{
    pid_t wpid;int status;
    while((wpid=waitpid(-1，&status,0))!=-1)//使用循环阻塞回收所有结束子进程	用wait会阻塞，waitpid非阻塞情况下会直接返回-1，不会阻塞
    {
        if(WIFEXITED(status))//获取退出状态
	        printf("catch child id %d,ret=%d\n",wpid,WEXITSTATUS(status));
    }
    return ;
}
```

如果父进程完成注册之前子进程全部结束，则会出现不触发信号的情况

```c
sigset_t set;
sigemptyset(&set);
sigaddset(&set,SIGCHLD);
sigprocmask(SIG_BLOCK,&set,NULL);//阻塞

/*pid_t pid;int i;
    for (i = 0; i < 10; i++) {
    if ((pid = fork()) == 0)
        break;
    }
	if(10==i){
        struct sigaction act;
        act.sa_handler=catch_child;//设置回调函数
        sigemptyset(&act.sa_mask);//设置捕捉函数执行期间屏蔽字
        act.sa_flags=0;
        sigaction(SIGCHLD,&act,NULL);//注册信号捕捉函数*/

		sigprocmask(SIG_UNBLOCK,&set,NULL);//解除阻塞
        
        /*printf("i'm parent,pid=%d\n",getpid());
        while(1);*/
//在原main函数中添加,防止父进程注册成功前发生子进程结束而收不到信号的情况
```



#### 中断系统调用

系统调用可分为两类：慢速系统调用和其他系统调用。

1. 慢速系统调用：可能会使进程永远阻塞的一类。如果在阻塞期间收到一个信号，该系统调用就被中断,不再继续执行(早期)；也可以设定系统调用是否重启。如，read、write、pause、wait...
2. 其他系统调用：getpid、getppid、fork...

慢速系统调用被中断的相关行为，实际上就是pause的行为： 如，read

​          ① 想中断pause，信号不能被屏蔽。

​          ② 信号的处理方式必须是捕捉 (默认、忽略都不可以)

​          ③ 中断后返回-1， 设置errno为EINTR(表“被信号中断”)

可修改sa_flags参数来设置被信号中断后系统调用是否重启。SA_INTERRURT不重启。 SA_RESTART重启

### 进程与线程

#### 进程组和会话

进程组，也称之为作业，代表一个或多个进程的集合。每个进程都属于一个进程组。

当父进程，创建子进程的时候，默认子进程与父进程属于同一进程组。进程组ID==第一个进程ID(组长进程)。可以使用kill -SIGKILL -进程组ID(负的)来将整个进程组内的进程全部杀死。

组长进程可以创建一个进程组，创建该进程组中的进程，然后终止。只要进程组中有一个进程存在，进程组就存在，与组长进程是否终止无关。

进程组生存期：进程组创建到最后一个进程离开(终止或转移到另一个进程组)。

一个进程可以为自己或子进程设置进程组ID

会话是多个进程组的集合

##### 创建会话

创建一个会话需要注意以下6点注意事项：

1. 调用进程不能是进程组组长，该进程变成新会话首进程(session header)
2. 该进程成为一个新进程组的组长进程。
3. 需有root权限 (ubuntu不需要)
4. 新会话丢弃原有的控制终端，该会话没有控制终端
5. 该调用进程是组长进程，则出错返回
6. 建立新会话时，先调用fork, 父进程终止，子进程调用setsid()

##### getsid函数

获取进程所属的会话ID

​	**pid_t getsid(pid_t pid);** 成功：返回调用进程的会话ID；失败：-1，设置errno

​	pid为0表示察看当前进程session ID

组长进程不能成为新会话首进程，新会话首进程必定会成为组长进程。
类似：getpgid() 获取组id

##### setsid函数

创建一个会话，并以自己的ID设置进程组ID，同时也是新会话的ID。

​     **pid_t setsid(void);** 成功：返回调用进程的会话ID；失败：-1，设置errno

​     调用了setsid函数的进程，既是新的会长，也是新的组长。

#### 守护进程

Daemon(精灵)进程，是Linux中的后台服务进程，通常**独立于控制终端**并且周期性地执行某种任务或等待处理某些发生的事件。一般采用以d结尾的名字。

创建守护进程，最关键的一步是调用setsid函数创建一个新的Session，并成为Session Leader。

##### 创建守护进程模型

1. 创建子进程，父进程退出

​		所有工作在子进程中进行，形式上脱离了控制终端

2. 在子进程中创建新会话

　　setsid()函数

　　使子进程完全独立出来，脱离控制

3. 根据需要改变当前目录，如根目录

　　 chdir()函数

　　 防止占用可卸载的文件系统，也可以换成其它路径

4. 重设文件权限掩码

　　  umask()函数

　　  防止继承的文件创建屏蔽字拒绝某些权限， 增加守护进程灵活性

5. 关闭文件描述符

　　   继承的打开文件不会用到，浪费系统资源，无法卸载

6. 开始执行守护进程核心工作守护进程退出处理程序模型                                                                                                                 

```c
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
int main(int argc,char *argv[])
{
    pid_t pid;
    int ret;
    pid=fork();
    if(pid>0)	//父进程终止
        exit(0);
    pid=setsid();//创建新会话
    if(pid==-1)
        sys_err("setsid error");
    //ret=chdir("/home/yufan");//改变工作目录，根据实际修改
    if(ret==-1)
        sys_err("chdir err");
    umask(0022);	//改变文件访问权限掩码
    close(STDIN_FILENO);//关闭文件描述符 0
    fd=open("/dev/null",O_RDWR);//fd-->0
    if(fd==-1)
        sys_err("open error");
    dup2(fd,STDOUT_FILENO);//重定向stdout和stderr
    dup2(fd,STDERR_FILENO);
    
    return 0;
}
```

#### 线程

LWP：light weight process 轻量级的进程，本质仍是进程(在Linux环境下)

进程：独立地址空间，拥有PCB 

线程：有独立的PCB，但没有独立的地址空间(共享)

区别：在于是否共享地址空间。     独居(进程)；合租(线程)。

Linux下：    线程：最小的执行单位

​                     进程：最小分配资源单位，可看成是只有一个线程的进程。

当线程创建后，原始的进程也变为线程之一

三级映射：进程PCB --> 页目录(可看成数组，首地址位于PCB中) --> 页表 --> 物理页面 --> 内存单元



1. 轻量级进程(light-weight process)，也有PCB，创建线程使用的底层函数和进程一样，都是clone
2. 从内核里看进程和线程是一样的，都有各自不同的PCB，但是PCB中指向内存资源的三级页表是相同的
3. 进程可以蜕变成线程
4. 线程可看做寄存器和栈的集合
5. 在linux下，线程最是小的执行单位；进程是最小的分配资源单位

##### 线程共享资源

​     1.文件描述符表 

​     2.每种信号的处理方式 ，但mask不共享（不建议线程和信号混用）

​     3.当前工作目录

​     4.用户ID和组ID

​     5.内存地址空间 (.text/.data/.bss/heap/共享库)

##### 线程非共享资源

​     1.线程id

​     2.处理器现场和栈指针(内核栈)

​     3.独立的栈空间(用户空间栈)

​     4.errno变量

​     5.信号屏蔽字mask

​     6.调度优先级 

##### 线程优、缺点

​     优点：   1. 提高程序并发性    2. 开销小    3. 数据通信、共享数据方便

​     缺点：   1. 库函数，不稳定    2. 调试、编写困难、gdb不支持     3. 对信号支持不好

​     优点相对突出，缺点均不是硬伤。Linux下由于实现方法导致进程、线程差别不是很大。

##### 创建线程相关源语

###### pthread_self函数
获取线程ID。其作用对应进程中 getpid() 函数。
**pthread_t pthread_self(void);**	返回值：成功：0；	失败：无

pthread_t是无符号型变量

###### pthread_create函数

创建一个新线程。对应进程中fork() 函数。

**int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);**

返回值：成功：0；    失败：错误号     -----Linux环境下，所有线程特点，失败均直接返回错误号

参数1：传出参数，保存系统为我们分配好的线程ID

参数2：通常传NULL，表示使用线程默认属性。若想使用具体属性也可以修改该参数。

参数3：函数指针，指向线程主函数(线程体)，该函数运行结束，则线程结束。

参数4：**线程主函数**执行期间所使用的参数。

```c
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
void *tfn(void *arg)
{
    printf("thread:pid =%d,tid=%lu\n",getpid(),pthread_self());
    return NULL;
}
int main(int argc,char *argv[]){
    pthread_t tid;
    int ret=pthread_create(&tid,NULL,tfn,NULL);
    if(ret!=0){
        perror("pthread_create error");
    }
    printf("main:pid=%d,tid =%lu\n",getpid(),pthread_self());
    sleep(1);//防止线程执行前主程序提前结束
    return 0;
}
```

如果需要向函数传参，则用(void*)类型进行传递

```int ret=pthread_create(&tid,NULL,tfn,(void*)i);```

之后在函数内 按需进行类型转换，如```int i=(int *)arg```

注意：这里不用引用传递的原因是子线程执行期间，主线程依然在运行，此时若用引用取值则会取到已在主线程里变化过的值

###### pthread_exit函数

将单个线程退出

**void pthread_exit(void *retval);**   参数：retval表示线程退出状态，通常传NULL

###### pthread_join函数

阻塞等待线程退出，获取线程退出状态       其作用，对应进程中 waitpid() 函数。

```int pthread_join(pthread_t thread, void **retval);```成功：0；失败：错误号

参数：thread：线程ID ；retval：存储线程结束状态。（【注意】：不是指针）

这里子线程返回值要是void*类型

```c
void *tfn(void *arg)
{
    return (void*)70;
}
int main(){
    int *retval;
    //此处创建子线程部分省略,tid是线程号
    int ret=pthread_join(tid,(void **)&retval);
    printf("thread return:%d\n",(void *)retval);
}
```

###### pthread_cancel函数

杀死(取消)线程             其作用，对应进程中 kill() 函数。

```int pthread_cancel(pthread_t thread);```  成功：0；失败：错误号

【注意】：线程的取消并不是实时的，而有一定的延时。需要等待线程到达某个取消点(检查点)。

被杀死的线程必须有机会进入内核态，cancel类似信号，若是线程没机会进入内核，那就杀不死（比如空循环而没有执行内容）

```c
void *tfn3(void *arg)
{
    while(1){
        //pthread_testcancel();//解决方法是让线程有执行内容
    }
}
pthread_create(&tid,NULL,tfn3,NULL);//此时该语句无法杀死线程，因为线程没有执行内容未进入内核

```

###### pthread_detach函数

实现线程分离

```int pthread_detach(pthread_t thread);``` 成功：0；失败：错误号

线程分离状态：指定该状态，线程主动与主控线程断开关系。线程结束后，其退出状态不由其他线程获取，而直接自己自动释放。网络、多线程服务器常用。

在线程中检查错误不再能使用perror()，而是应该查看返回的错误号，如strerror(错误号);

##### 线程属性

linux下线程的属性是可以根据实际项目需要，进行设置，之前我们讨论的线程都是采用线程的默认属性，默认属性已经可以解决绝大多数开发时遇到的问题。如我们对程序的性能提出更高的要求那么需要设置线程属性，比如可以通过设置线程栈的大小来降低内存的使用，增加最大线程个数。

```c
typedef struct {
int    etachstate;   //线程的分离状态
int    schedpolicy;  //线程调度策略
struct sched_param     schedparam; //线程的调度参数
int    inheritsched; //线程的继承性
int    scope;       //线程的作用域
size_t guardsize;    //线程栈末尾的警戒缓冲区大小
int    stackaddr_set; //线程的栈设置
void*  stackaddr;    //线程栈的位置
size_t stacksize;     //线程栈的大小
} pthread_attr_t;//此仅为示例，新版本linux内容不同
```

线程属性无法直接修改，需通过函数接口设置

###### 线程属性初始化

注意：应先初始化线程属性，再pthread_create创建线程

创建线程属性结构体

```pthread_attr_t attr;```

初始化线程属性

``` int pthread_attr_init(pthread_attr_t *attr);``` 成功：0；失败：错误号 

销毁线程属性所占用的资源

```int pthread_attr_destroy(pthread_attr_t *attr);```成功：0；失败：错误号

###### 线程分离状态

线程的分离状态决定一个线程以什么样的方式来终止自己。

非分离状态：线程的默认属性是非分离状态，这种情况下，原有的线程等待创建的线程结束。只有当pthread_join()函数返回时，创建的线程才算终止，才能释放自己占用的系统资源。

分离状态：分离线程没有被其他的线程所等待，自己运行结束了，线程也就终止了，马上释放系统资源。应该根据自己的需要，选择适当的分离状态。

线程分离状态的函数：

设置线程属性，分离or非分离

```int pthread_attr_setdetachstate(pthread_attr_t *attr, int detachstate); ```

获取线程属性，分离or非分离

```int pthread_attr_getdetachstate(pthread_attr_t *attr, int *detachstate); ```

参数：   attr：已初始化的线程属性

detachstate： PTHREAD_CREATE_DETACHED（分离线程）PTHREAD _CREATE_JOINABLE（非分离线程）

```c
pthread_attr_t attr;//创建线程属性结构体
int ret=pthread_attr_init(&attr);//初始化线程属性
ret=pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);//设置线程属性为分离
ret=pthread_create(&tid,%attr,tfn,NULL);//创建线程
ret=pthread_attr_destroy(&attr);//销毁线程属性资源
```

##### 线程使用注意事项

1. 主线程退出其他线程不退出，主线程应调用pthread_exit
2. 避免僵尸线程

pthread_join回收

pthread_detach设置分离

pthread_create指定分离属性

被join线程可能在join函数返回前就释放完自己的所有内存资源，所以不应当返回被回收线程栈中的值;

3. malloc和mmap申请的内存可以被其他线程释放 
4. 应避免在多线程模型中调用fork，除非马上exec，子进程中只有调用fork的线程存在，其他线程在子进程中均pthread_exit
5. 信号的复杂语义很难和多线程共存，应避免在多线程引入信号机制

##### 线程同步

同步即协同步调，按预定的先后次序运行。

**线程同步，指一个线程发出某一功能调用时，在没有得到结果之前，该调用不返回。同时其它线程为保证数据一致性，不能调用该功能。**

**所有“多个控制流，共同操作一个共享资源”的情况，都需要同步。**

##### 互斥锁mutex

Linux中提供一把互斥锁mutex（也称之为互斥量）。

每个线程在对资源操作前都尝试先加锁，成功加锁才能操作，操作结束解锁。互斥锁实质上是操作系统提供的一把“建议锁”（又称“协同锁”），建议程序中有多线程访问共享资源的时候使用该机制。但并没有强制限定。     

因此，即使有了mutex，如果有线程不按规则来访问数据，依然会造成数据混乱。

相关函数：

```c
pthread_mutex_t lock;//创建锁

pthread_mutex_init();//初始化
pthread_mutex_destroy();//销毁
pthread_mutex_lock();//加锁
pthread_mutex_trylock();
pthread_mutex_unlock();//解锁
//成功返回0，失败返回errno
```

pthread_mutex_t 类型，其本质是一个结构体。为简化理解，应用时可忽略其实现细节，**简单当成整数看待。**

一般步骤为：创建锁->初始化->加锁->访问数据->解锁->销毁锁

```c
#include<stdio.h>
#include<string.h>
#include<pthread.h>
#include<stdlib.h>
#include<unistd.h>
pthread_mutex_t mutex;
void *tfn(void *arg)
{
    srand(time(NULL));
    while(1)
    {
        pthread_mutex_lock(&mutex);	//加锁
        printf("hello");
        sleep(rand()%3);	//模拟长时间操作共享资源
        printf("world\n");
        pthread_mutex_unlock(&mutex);	//解锁
        
        sleep(rand()%3);
    }
    return NULL;
}
int main(void)
{
    pthread_t tid;
    srand(time(NULL));
    int ret=pthread_mutex_init(&mutex,NULL);//初始化互斥锁
    if(ret!=0){
        fprintf(stderr,"mutex init error:%s\n",strerror(ret));
    }
    pthread_create(&tid,NULL,tfn,NULL);
    while(1){
        pthread_mutex_lock(&mutex);	//加锁
        printf("HELLO");
        sleep(rand()%3);
        printf("WORLD\n");
        pthread_mutex_unlock(&mutex);	//解锁
        
        sleep(rand()%3);//将sleep放在锁外的原因是给其它线程竞争机会
    }
    pthread_join(tid,NULL);
    pthread_mutex_destroy(&mutex);
    return 0;
}
```

动态初始化：pthread_mutex_init(&mutex,NULL);

静态初始化：pthread_mutex_t mutex=PTHREAD_MUTEX_INITIALIZER//使用宏初始化

注意事项：

访问资源前加锁，访问完成后立即解锁，保证锁的粒度尽量小

可以把互斥锁结构体看作整数，初值为1

加锁：--操作，阻塞线程			解锁：++操作，唤醒阻塞的线程		try锁：尝试加锁，成功++，失败返回并设置错误号

##### 读写锁rwlock

与互斥量类似，但读写锁允许更高的并行性。其特性为：**写独占，读共享。**

锁只有一把，读方式加锁为读锁，写方式加锁为写锁，写锁优先级更高

```c
pthread_rwlock_init();
pthread_rwlock_destroy();
pthread_rwlock_rdlock();
pthread_rwlock_wrlock();
pthread_rwlock_tryrdlock();
pthread_rwlock_trywrlock();
pthread_rwlock_unlock();
//以上7 个函数的返回值都是：成功返回0， 失败直接返回错误号。	
//pthread_rwlock_t类型	用于定义一个读写锁变量。
pthread_rwlock_t rwlock;
```

##### 死锁现象

1. 线程试图对同一个互斥量A加锁两次。

2. 线程1拥有A锁，请求获得B锁；线程2拥有B锁，请求获得A锁，两个线程互相等待  

##### 条件变量

本身不是锁，但它也可以造成线程阻塞。通常与互斥锁配合使用。给多线程提供一个会合的场所。

```c
pthread_cond_init;		//初始化函数
pthread_cond_destroy	//销毁函数
pthread_cond_wait		//阻塞等待函数
pthread_cond_timedwait	//阻塞等待函数，包含超时设定
pthread_cond_signal		//唤醒至少一个阻塞在条件变量上的线程
pthread_cond_broadcast	//唤醒全部阻塞在条件变量上的线程
//以上6 个函数的返回值都是：成功返回0， 失败直接返回错误号。
pthread_cond_t			//条件变量类型	用于定义条件变量
pthread_cond_t cond;
```

动态初始化：pthread_cond_init(&cond,NULL);

静态初始化：pthread_cond_t mutex=PTHREAD_COND_INITIALIZER//使用宏初始化

###### pthread_cond_wait

阻塞等待一个条件变量满足

1. 阻塞等待条件变量cond（参1）满足 
2. 释放已掌握的互斥锁（解锁互斥量）相当于pthread_mutex_unlock(&mutex);

 			**1. 2.**两步为一个原子操作。

3. 当被唤醒，pthread_cond_wait函数返回时，解除阻塞并重新申请获取互斥锁pthread_mutex_lock(&mutex);

![条件变量阻塞](..\note\图\条件变量阻塞.jpg)

###### 生产者消费者模型

**消费者端：**

1.创建锁 pthread_mutex_t mutex

2.初始化pthread_mutex_init(&mutex,NULL)

3.加锁pthread_mutex_lock(&lock)

4.等待条件满足pthread_cond_wait(&cond,&mutex)

​			1)阻塞等条件变量

​			2)解锁unlock

​			操作......

​			3)加锁lock

5.访问共享数据

6.解锁，释放条件变量，销毁锁

**生产者端：**

1.生产数据

2.尝试加锁pthread_mutex_lock(&mutex)

3.将数据放入公共区域

4.解锁pthread_mutex_unlock(&mutex)

5.通知阻塞在条件变量的线程

​	pthread_cond_signal()

​	pthread_cond_broadcast()

回到1

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
void err_thread(int ret,char *str){
    if(ret!=0){
        fprintf(stderr,"%s:%s\n",str,strerror(ret));
        pthread_exit(NULL);
    }
}
struct msg{
    int num;
    struct msg *next;
};
pthread_mutex_t mutex=PTHREAD_MUTEX_INITIALIZER;//定义并初始化互斥量
pthread_cond_t has_data=PTHREAD_COND_INITIALIZER;//定义并初始化条件变量
//此处省略返回值检查
void *producer(void *arg)
{
    while(1){
    struct msg *mp=malloc(sizeof(struct msg));
    mp->num=rand()%1000+1;				//模拟生产数据
    printf("produce data\n");
    pthread_mutex_lock(&mutex);//加锁互斥量
    mp->next=head;				//写公共区域
    head=mp;
    pthread_mutex_unlock(&mutex);//解锁互斥量
    pthread_cond_signal(&had_data);//唤醒阻塞线程
    sleep(rand()%3);
    }
    return NULL;
}
void *consumer(void *arg)
{
    while(1){
    struct msg *mp;
    pthread_mutex_lock(&mutex);//加锁互斥量
    while(head==NULL){		/*当有多个消费者，被唤醒时都会跳出wait，但只有一份数据，所以要再次判断是否有数据*/
        pthread_cond_wait(&had_data,&mutex);//阻塞等待条件变量,解锁
    }										//pthread_cond_wait返回时重新加锁
    mp=head;
    head=mp->next;
    pthread_mutex_unlock(&mutex);//解锁互斥量
    printf("consumer:%d\n",mp->num);
    free(mp);
    sleep(rand()%3);
    }
    return NULL;
}
int main(int argc,char *argv[])
{
    int ret;
    pthread_t pid,cid;
    srand(time(NULL));
    ret=pthread_create(&pid,NULL,producer,NULL);//生产者
    
    ret=pthread_create(&cid,NULL,consumer,NULL);//消费者
    
    pthread_join(pid,NULL);
    pthread_join(cid,NULL);
}
```

若有多个消费者，则应该先判断条件变量再唤醒，否则当前一个消费者结束后，下一个阻塞的消费者会立刻拿锁，造成空读阻塞

##### 信号量

相当于初始化值为N的互斥量，N表示可以同时访问共享数据区的线程数

由于互斥锁的粒度比较大，如果我们希望在多个线程间对某一对象的部分数据进行共享，使用互斥锁是没有办法实现的，只能将整个数据对象锁住。这样虽然达到了多线程操作共享数据时保证数据正确性的目的，却无形中导致线程的并发性下降。线程从并行执行，变成了串行执行。与直接使用单进程无异。

信号量，是相对折中的一种处理方式，既能保证同步，数据不混乱，又能提高线程并发。

信号量与信号无关，即可用于线程，也能用于进程

```c
sem_init//初始化
sem_destroy//销毁
sem_wait//每调用一次做一次--操作，当信号量值为0时阻塞
sem_trywait//尝试对信号量加锁
sem_timedwait//限时尝试对信号量加锁
sem_post//每调用一次做一次++操作，当信号量值为N时阻塞
//以上6 个函数的返回值都是：成功返回0， 失败返回-1，同时设置errno。(注意，它们没有pthread前缀)
//sem_t类型，本质仍是结构体。但应用期间可简单看作为整数，忽略实现细节（类似于使用文件描述符）。 
sem_t sem;// 规定信号量sem不能 < 0。头文件 <semaphore.h
```

int sem_init(sem_t *sem, int pshared, unsigned int value);

参1：sem信号量 

参2：pshared取0用于线程间；取非0（一般为1）用于进程间 

参3：value指定信号量初值

###### 信号量实现生产者消费者模型

在生产和消费时，资源数量会发生变化，可以用信号量来表示

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
#define NUM 5
int quene[NUM];
sem_t blank_number,product_number;
void *producer(void *arg)
{
    int i=0;
    while(1){
        sem_wait(&blank_number);	//生产者将空格数--，为0则阻塞等待
        quene[i]=rand()%1000+1;		//生产一个产品
        printf("produce %d\n",quene[i]);
        sem_post(&product_number);	//产品数++
        i=(i+1)%NUM;				//借助下标实现环形
        sleep(rand()%1);
    }
}
void *consumer(void *arg)
{
    int i=0;
    while(1){
        sem_wait(&product_number);	//消费者将产品数--
        printf("consume %d\n",quene[i]);
        quene[i]=0;					//消费一个产品
        sem_post(&blank_number);	//空格数++
        i=(i+1)%NUM;
        sleep(rand()%3);
    }
}
int main(int argc,char *argv[]){
    pthread_t pid,cid;
    sem_init(&blank_number,0,NUM);
    sem_init(&product_number,0,0);
    
    pthread_create(&pid,NULL,producer,NULL);
    pthread_create(&cid,NULL,consumer,NULL);
    
    pthread_join(pid,NULL);
    pthread_join(cid,NULL);
    
    sem_destroy(&blank_number);
    sem_destroy(&product_number);
    
    return 0;
}
```

## 网络编程

### 网络基础知识

协议：从应用的角度出发，协议可理解为“规则”，是数据传输和数据的解释的规则。



TCP[传输控制协议](http://baike.baidu.com/view/544903.htm)（Transmission Control Protocol）是一种面向连接的、可靠的、基于字节流的[传输层](http://baike.baidu.com/view/239605.htm)通信协议。

UDP用户数据报协议（User Datagram Protocol）是[OSI](http://baike.baidu.com/view/113948.htm)参考模型中一种无连接的[传输层](http://baike.baidu.com/view/239605.htm)协议，提供面向事务的简单不可靠信息传送服务。

HTTP[超文本传输协议](http://baike.baidu.com/view/468465.htm)（Hyper Text Transfer Protocol）是[互联网](http://baike.baidu.com/view/6825.htm)上应用最为广泛的一种[网络协议](http://baike.baidu.com/view/16603.htm)。

FTP文件传输协议（File Transfer Protocol）

IP协议是[因特网](http://baike.baidu.com/view/1706.htm)互联协议（Internet Protocol）

ICMP协议是Internet控制[报文](http://baike.baidu.com/view/175122.htm)协议（Internet Control Message Protocol）它是[TCP/IP协议族](http://baike.baidu.com/view/2221037.htm)的一个子协议，用于在IP[主机](http://baike.baidu.com/view/23880.htm)、[路由](http://baike.baidu.com/view/18655.htm)器之间传递控制消息。

IGMP协议是 Internet 组管理协议（Internet Group Management Protocol），是因特网协议家族中的一个组播协议。该协议运行在主机和组播路由器之间。

[ARP](http://baike.baidu.com/view/32698.htm)协议是正向[地址解析协议](http://baike.baidu.com/view/149421.htm)（Address Resolution Protocol），通过已知的IP，寻找对应主机的[MAC地址](http://baike.baidu.com/view/69334.htm)。

[RARP](http://baike.baidu.com/view/32772.htm)是反向地址转换协议，通过MAC地址确定IP地址。



TCP协议注重数据的传输。http协议着重于数据的解释。

##### OSI7层与TCP4层

![7层4层](..\note\图\7层4层.png)

###### TCP/IP

传输层 常见协议有TCP/UDP协议。

应用层 常见的协议有HTTP协议，FTP协议，NFS，SSH，TELNET

网络层 常见协议有IP协议、ICMP协议、IGMP协议。

网络接口层 常见协议有ARP协议、RARP协议。

![TCP封装](..\note\图\TCP封装.png)

###### OSI七层

1. **物理层**：主要定义物理设备标准，如网线的接口类型、光纤的接口类型、各种传输介质的传输速率等。它的主要作用是传输比特流（就是由1、0转化为电流强弱来进行传输，到达目的地后再转化为1、0，也就是我们常说的数模转换与模数转换）。这一层的数据叫做比特。
2. **数据链路层**：定义了如何让格式化数据以帧为单位进行传输，以及如何让控制对物理介质的访问。这一层通常还提供错误检测和纠正，以确保数据的可靠传输。如：串口通信中使用到的115200、8、N、1
3. **网络层**：在位于不同地理位置的网络中的两个主机系统之间提供连接和路径选择。Internet的发展使得从世界各站点访问信息的用户数大大增加，而网络层正是管理这种连接的层。
4. **传输层**：定义了一些传输数据的协议和端口号（WWW端口80等），如：TCP（传输控制协议，传输效率低，可靠性强，用于传输可靠性要求高，数据量大的数据），UDP（用户数据报协议，与TCP特性恰恰相反，用于传输可靠性要求不高，数据量小的数据，如QQ聊天数据就是通过这种方式传输的）。 主要是将从下层接收的数据进行分段和传输，到达目的地址后再进行重组。常常把这一层数据叫做段。
5. **会话层**：通过传输层(端口号：传输端口与接收端口)建立数据传输的通路。主要在你的系统之间发起会话或者接受会话请求（设备之间需要互相认识可以是IP也可以是MAC或者是主机名）。
6. **表示层**：可确保一个系统的应用层所发送的信息可以被另一个系统的应用层读取。例如，PC程序与另一台计算机进行通信，其中一台计算机使用扩展二一十进制交换码(EBCDIC)，而另一台则使用美国信息交换标准码（ASCII）来表示相同的字符。如有必要，表示层会通过使用一种通格式来实现多种数据格式之间的转换。
7. **应用层**：是最靠近用户的OSI层。这一层为用户的应用程序（例如电子邮件、文件传输和终端仿真）提供网络服务。

 

##### C/S与B/S模型

客户机(client)/服务器(server)模式。需要在通讯两端各自部署客户机和服务器来完成数据通信。

浏览器(Browser)/服务器(Server)模式。只需在一端部署服务器，而另外一端使用每台PC都默认配置的浏览器即可完成数据的传输。

|      | C/S                                                    | B/S                                |
| ---- | ------------------------------------------------------ | ---------------------------------- |
| 优点 | 缓存大量数据、协议，速度快                             | 安全性，跨平台，开发工作量小       |
| 缺点 | 客户端安插至用户主机上，对用户主机的**安全性构成威胁** | 不能缓存大量数据，必须严格遵守http |



##### 以太网帧格式

根据MAC地址完成数据包传输

![以太网帧](..\note\图\以太网帧.png)

其中的源地址和目的地址是指网卡的硬件地址（也叫MAC地址），长度是48位。以太网帧中的数据长度规定最小46字节，最大1500字节，ARP和RARP数据包的长度不够46字节，要在后面补填充位。**最大值1500**称为以太网的最大传输单元（MTU）

##### ARP数据报格式

![以太网帧](..\note\图\arp.png)

根据IP地址获取MAC地址

##### IP协议

![以太网帧](..\note\图\ip段.png)

IP数据报的首部长度和数据长度都是可变长的，但总是**4字节的整数倍**。

版本：IPv4或IPv6

TTL：生存时间，限制跳转上限

源/目的IP：32位

##### TCP/UDP数据报

TCP稳定性好但开销大

###### TCP

![tcp数据报](..\note\图\tcp数据报.png)

16位源端口号，16位目的端口号，32位序号，32位确认序号，6个标志位，16位窗口大小

###### UDP

16位源端口号，16位目的端口号

##### 网络传输流程

数据->应用层->传输层->网络层->链路层->网络环境

### SOCKET编程

套接字：Socket本身有“插座”的意思，在Linux环境下，用于表示进程间网络通信的特殊文件类型。**本质为内核借助缓冲区形成的伪文件**。

一个文件描述符指向一个套接字，该套接字内部由内核借助两个缓冲区实现

**在网络通信中，套接字一定是成对出现的**。一端的发送缓冲区对应对端的接收缓冲区

网络字节序：

​						小端法：高位存高地址（PC本地）

​						大端法：高位存低地址（网络存储）

TCP/IP规定，网络数据流应采用**大端字节序**

由于主机字节序为小端法，需要做**网络字节序**和**主机字节序**的转换

```c
#include <arpa/inet.h>

uint32_t htonl(uint32_t hostlong);	//本地->网络(IP)
uint16_t htons(uint16_t hostshort);	//本地->网络(port)
uint32_t ntohl(uint32_t netlong);	//网络->本地(IP)
uint16_t ntohs(uint16_t netshort);	//网络->本地(port)
//ip地址转换函数
#include <arpa/inet.h>
int inet_pton(int af, const char *src, void *dst);//本地转网络
const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);//网络转本地
```

**int inet_pton(int af, const char *src, void *dst)**	成功：1	异常：0

| af                          | src                      | dst                    |
| --------------------------- | ------------------------ | ---------------------- |
| (网络协议)AF_INET，AF_INET6 | 传入IP地址（点分十进制） | 传出转换后的网络字节序 |

**const char *inet_ntop(int af, const void *src, char *dst, socklen_t size);**	成功：dst	失败：NULL

| af                          | src                | dst                      | size    |
| --------------------------- | ------------------ | ------------------------ | ------- |
| (网络协议)AF_INET，AF_INET6 | 传入的网络字节序IP | 传出IP地址（点分十进制） | dst大小 |

##### sockaddr地址模型创建

IP+端口

```c
struct sockaddr_in addr;		//创建sockaddr结构体
addr.sin_family=AF_INET/AF_INET6;
addr.sin_port=htons(端口号);
inet_pton(af,"ip地址",(void*)&dst);//将ip转为网络字节序，dst要事先声明int

addr.sin_addr.s_addr=dst;//设定ip地址
addr.sin_addr.s_addr=htonl(INADDR_ANY);//或者使用宏，取出系统中有效的任意ip地址

bind(fd,(struct sockaddr *)&addr,size);//根据函数要求进行格式转换
```

##### socket模型创建流程

![socket模型创建](..\note\图\socket模型创建.png)

一个服务器与一个客户端通信，共有**三个套接字**

```c
/*服务端*/
socket();//创建套接字
bind();//绑定ip和端口
listen();//设置同时建立连接的上限数，最大128
accept();//阻塞监听客户端连接
```

当accept与客户端成功建立连接，会返回一个**新的**socket文件描述符用来通信，原有的socket重复监听

```c
/*客户端*/
socket();//创建套接字
connect();//绑定ip和端口,与服务器建立连接
```

**int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);**

sockdf:  	socket文件描述符

addr:  		传出参数，返回链接客户端地址信息，含IP地址和端口号

addrlen: 	传入传出参数（值-结果）,传入sizeof(addr)大小，函数返回时返回真正接收到地址结构体的大小

返回值：  	成功返回一个新的socket文件描述符，用于和客户端通信，失败返回-1，设置errno



当客户端没有使用bind()函数绑定地址结构，则会自动采用隐式绑定

#### C/S模型-TCP

以下目标：客户端发数据-->服务端处理数据-->客户端获取处理过的数据

**server：**

1.socket(int domain, int type, int protocol)	创建socket

2.bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen)		绑定服务器地址结构

(socket文件描述符，sockaddr结构体,sizeof(addr)长度)

3.listen(int sockfd, int backlog)		设置监听上限	backlog：监听上限

4.accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen)		阻塞监听客户端连接

addrlen：传入sizeof(addr)大小，函数返回时返回真正接收到地址结构体的大小

5.read(fd)		读socket获取客户端数据

6.数据操作

7.write(fd)

8.close()

**client():**

1.socket()	创建socke

2.connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen)	与服务器建立连接

sockfd:  socket函数返回值

struct sockaddr *addr:  服务器地址结构

```c
serv_addr.sin_family=AF_INET;
serv_addr.sin_port=htons(SERV_PORT);    
inet_pton(AF_INET,"服务器IP地址",&serv_addr.sin_addr.s_addr);//转换为网络字节序并设置，此处ip与端口必须和服务端设置一致
```

addrlen:  服务器地址结构的大小：sizeof(addr)

3.wirte()		写数据到socket

4.read()		读取返回数据

5.数据操作

6.close()

###### 获取客户端地址结构

```inet_ntop(AF_INET,&clit_addr.sin_addr.s_addr,client_IP,sizeof(client_IP))```获取IP

```ntohs(clit_addr.sin_port))```获取端口

```c
/*创建服务端进程，接收客户端文字并用大写返回*/
#include<stdio.h>
#include<stdlib.h>
#include<ctype.h>
#include<sys/socket.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
#include<arpa/inet.h>
#define SERV_PORT 8088
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
int main(int argc,char *argv[])
{
    int lfd=0,cfd=0;
    int ret,i;
    char buf[BUFSIZ],client_IP[1024];
    
    struct sockaddr_in serv_addr,clit_addr;    
    socklen_t clit_addr_len;
    serv_addr.sin_family=AF_INET;
    serv_addr.sin_port=htons(SERV_PORT);
    serv_addr.sin_addr.s_addr=htonl(INADDR_ANY);//构建地址结构
    
    lfd=socket(AF_INET,SOCK_STREAM,0);//创建套接字
    if(lfd==-1){
        sys_err("socket err");
    }
    bind(lfd,(struct sockaddr *)&serv_addr,sizeof(serv_addr));//绑定服务器地址结构
    listen(lfd,128);
    clit_addr_len=sizeof(clit_addr);
    cfd=accept(lfd,(struct sockaddr *)&clit_addr,&clit_addr_len);
    if(cfd==-1){
        sys_err("accept err");
    }
    printf("client ip:%s port:%d\n",
           inet_ntop(AF_INET,&clit_addr.sin_addr.s_addr,client_IP,sizeof(client_IP)),
          ntohs(clit_addr.sin_port));//获取客户端IP
    while(1){
    	ret=read(cfd,buf,sizeof(buf));
    	write(STDOUT_FILENO,buf,ret);
    	for(i=0;i<ret;i++){
    	    buf[i]=toupper(buf[i]);//此处小写转大写为目标转换，实际换为自己需要的计算
    	}
    	write(cfd,buf,ret);
    }
    
    return 0;
}
```
1创建

```c
/*创建客户端进程*/
#include<stdio.h>
#include<stdlib.h>
#include<ctype.h>
#include<sys/socket.h>
#include<arpa/inet.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#include<pthread.h>
#define SERV_PORT 8088
void sys_err(const char *str)
{
    perror(str);
    exit(1);
}
int main(int argc,char *argv[])
{
    int cfd;
    int conter=10;//设置写入次数
    char buf[BUFSIZ];
    struct sockaddr_in,*serv_addr;//服务器地址结构
    serv_addr.sin_family=AF_INET;
    serv_addr.sin_port=htons(SERV_PORT);    
    inet_pton(AF_INET,"127.0.0.1",serv_addr.sin_addr.s_addr);//转换为网络字节序并设置，此处ip与端口必须和服务端设置一致
    
    cfd=socket(AF_INET,SOCK_STREAM,0);
    if(cfd==-1)
        sys_err("socket error");
    
    int ret=connect(cfd,(struct sockaddr *)&serv_addr,sizeof(serv_addr));
    if(ret!=0)
        sys_err("connect error");
    while(--conter)
    {
        write(cfd,"hello",5);//向客户端写数据
        ret=read(cfd,buf,sizeof(buf));//接收客户端数据
        write(STDOUT_FILENO,buf,ret);
        sleep(1);
    }
    close(cfd);
    return 0;
}
```

###### TCP通信时序——三次握手四次挥手

![三次握手](..\note\图\三次握手.jpg)

SYN：请求建立连接标志位，SYN后会携带一个头包号，对方回复时会在ACK后加接收尾包号

ACK：同意连接

FIN：关闭连接

二者各自拥有一个包号，全称沿用，根据包号回执来判断包是否完整接收，回执号与ACK一起，如发8001(10)，回复ACK 8011

断开为4次原因是半关闭，本质是关闭缓冲区

三次握手发生在内核空间，accept()和connect()函数会体现该过程

流量控制：滑动窗口，根据缓冲区大小制定，防止传输速度大于处理速度

![tcp通讯流程](..\note\图\tcp通讯流程.jpg)

CLOSED->SYN_SENT->SYN_RCVD->ESTABLISHED	建立连接	FIN_WAIT_1->CLOSE_WAIT->FIN_WAIT_2	半关闭	LAST_ACK->TIME_WAIT->CLOSED

![tcp状态转换](..\note\图\tcp状态转换.jpg)



主动发起端：**CLOSE**——发送SYN——**SYN_SEND**——接收ACK，SYN——**SYN_SEND**——发送ACK——**ESTABLISHED**

主动关闭端：**ESTABLISHED**——发送FIN——**FIN_WAIT_1**——接收ACK——**FIN_WAIT_2(半关闭)**——接收对方FIN——**FIN_WAIT_2**——回发ACK——**TIME_WAIT**

被动连接端：**CLOSE**——**LISTEN**——接收SYN——**LISTEN**——发送ACK，SYN——**SYN_RCVD**——接收ACK——**ESTABLISHED**

被动关闭端：**ESTABLISHED**——接收FIN——**ESTABLISHED**——发送ACK——**CLOSE_WAIT**——发送FIN——**LAST_ACK**——接收ACK——**CLOSE**

###### 2MSL时长

主动关闭的一方在TIME_WAIT后会经历TIME_WAIT后再进入close状态，而被动端则没有，每次接收FIN会重新计时

保证最后一个ACK被对方接收，如果对方没收到会继续发FIN，**让4次握手关闭流程更加可靠**

###### 端口复用函数

在server的TCP连接没有完全断开之前不允许重新监听是不合理的。因为，TCP连接没有完全断开指的是connfd（127.0.0.1:6666）没有完全断开，而我们重新监听的是listenfd（0.0.0.0:6666），虽然是占用同一个端口，但IP地址不同，connfd对应的是与某个客户端通讯的一个具体的IP地址，而listenfd对应的是wildcard address。解决这个问题的方法是使用setsockopt()设置socket描述符的选项SO_REUSEADDR为1，表示允许创建端口号相同但IP地址不同的多个socket描述符。

在server代码的socket()和bind()调用之间插入如下代码：

```c
int opt = 1;//1为生效
setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt));
```

###### 半关闭

通信双方中只有一端关闭通信【FIN_WAIT_2】，但一般看不到这个状态，可以使用shutdown()函数实现

使用close中止一个连接，但它只是减少描述符的引用计数，并不直接关闭连接，只有当描述符的**引用计数为0**时才关闭连接。

**shutdown**不考虑描述符的引用计数，**直接关闭描述符**。也可选择中止一个方向的连接，只中止读或只中止写。

#### 出错处理封装函数

系统调用不能保证每次都成功，必须进行出错处理，这样一方面可以保证程序逻辑正常，另一方面可以迅速得到故障信息。

为使错误处理的代码不影响主程序的可读性，我们把与socket相关的一些系统函数加上错误处理代码包装成新的函数，做成一个模块wrap.c：

```c
//wrap.c
#include <stdlib.h>
#include <errno.h>
#include <sys/socket.h>
void perr_exit(const char *s)
{
	perror(s);
	exit(1);
}
int Accept(int fd, struct sockaddr *sa, socklen_t *salenptr)
{
	int n;
	again:
	if ( (n = accept(fd, sa, salenptr)) < 0) {
		if ((errno == ECONNABORTED) || (errno == EINTR))
			goto again;
		else
			perr_exit("accept error");
	}
	return n;
}
int Bind(int fd, const struct sockaddr *sa, socklen_t salen)
{
	int n;
	if ((n = bind(fd, sa, salen)) < 0)
		perr_exit("bind error");
	return n;
}
int Connect(int fd, const struct sockaddr *sa, socklen_t salen)
{
	int n;
	if ((n = connect(fd, sa, salen)) < 0)
		perr_exit("connect error");
	return n;
}
int Listen(int fd, int backlog)
{
	int n;
	if ((n = listen(fd, backlog)) < 0)
		perr_exit("listen error");
	return n;
}
int Socket(int family, int type, int protocol)
{
	int n;
	if ( (n = socket(family, type, protocol)) < 0)
		perr_exit("socket error");
	return n;
}
ssize_t Read(int fd, void *ptr, size_t nbytes)
{
	ssize_t n;
again:
	if ( (n = read(fd, ptr, nbytes)) == -1) {
		if (errno == EINTR)
			goto again;
		else
			return -1;
	}
	return n;
}
ssize_t Write(int fd, const void *ptr, size_t nbytes)
{
	ssize_t n;
again:
	if ( (n = write(fd, ptr, nbytes)) == -1) {
		if (errno == EINTR)
			goto again;
		else
			return -1;
	}
	return n;
}
int Close(int fd)
{
	int n;
	if ((n = close(fd)) == -1)
		perr_exit("close error");
	return n;
}
ssize_t Readn(int fd, void *vptr, size_t n)
{
	size_t nleft;
	ssize_t nread;
	char *ptr;

	ptr = vptr;
	nleft = n;

	while (nleft > 0) {
		if ( (nread = read(fd, ptr, nleft)) < 0) {
			if (errno == EINTR)
				nread = 0;
			else
				return -1;
		} else if (nread == 0)
			break;
		nleft -= nread;
		ptr += nread;
	}
	return n - nleft;
}

ssize_t Writen(int fd, const void *vptr, size_t n)
{
	size_t nleft;
	ssize_t nwritten;
	const char *ptr;

	ptr = vptr;
	nleft = n;

	while (nleft > 0) {
		if ( (nwritten = write(fd, ptr, nleft)) <= 0) {
			if (nwritten < 0 && errno == EINTR)
				nwritten = 0;
			else
				return -1;
		}
		nleft -= nwritten;
		ptr += nwritten;
	}
	return n;
}

static ssize_t my_read(int fd, char *ptr)
{
	static int read_cnt;
	static char *read_ptr;
	static char read_buf[100];

	if (read_cnt <= 0) {
again:
		if ((read_cnt = read(fd, read_buf, sizeof(read_buf))) < 0) {
			if (errno == EINTR)
				goto again;
			return -1;	
		} else if (read_cnt == 0)
			return 0;
		read_ptr = read_buf;
	}
	read_cnt--;
	*ptr = *read_ptr++;
	return 1;
}

ssize_t Readline(int fd, void *vptr, size_t maxlen)
{
	ssize_t n, rc;
	char c, *ptr;
	ptr = vptr;

	for (n = 1; n < maxlen; n++) {
		if ( (rc = my_read(fd, &c)) == 1) {
			*ptr++ = c;
			if (c == '\n')
				break;
		} else if (rc == 0) {
			*ptr = 0;
			return n - 1;
		} else
			return -1;
	}
	*ptr = 0;
	return n;
}
```

```c
//wrap.h
#ifndef __WRAP_H_
#define __WRAP_H_
void perr_exit(const char *s);
int Accept(int fd, struct sockaddr *sa, socklen_t *salenptr);
int Bind(int fd, const struct sockaddr *sa, socklen_t salen);
int Connect(int fd, const struct sockaddr *sa, socklen_t salen);
int Listen(int fd, int backlog);
int Socket(int family, int type, int protocol);
ssize_t Read(int fd, void *ptr, size_t nbytes);
ssize_t Write(int fd, const void *ptr, size_t nbytes);
int Close(int fd);
ssize_t Readn(int fd, void *vptr, size_t n);
ssize_t Writen(int fd, const void *vptr, size_t n);
ssize_t my_read(int fd, char *ptr);
ssize_t Readline(int fd, void *vptr, size_t maxlen);
#endif
```

### 高并发服务器

#### 多进程并发服务器

使用多进程并发服务器时要考虑以下几点：

1. 父进程最大文件描述个数(父进程中需要close关闭accept返回的新文件描述符)
2. 系统内创建进程个数(与内存大小相关)
3. 进程创建过多是否降低整体服务性能(进程调度)

**创建流程：**

```c
1.socket()	创建监听socket
2.bind()		绑定服务器地址结构
3.listen()
4.while(1){
	cfd=Accept(lfd);		//接收客户端连接请求
	pid=fork();
	if (pid==0){
        close(lfd);	//关闭用于连接的套接字lfd
		read();
		数据操作
		write();}
		}else if(pid>0){
		close(cfd);	//关闭用于与客户端通信的套接字cfd
		continue;}
}
```

5.子进程：
```c
close(lfd);
	read();
	操作;
	write();
父进程：
	close(fd);
	注册信号捕捉函数		SIGCHLD
	在回调函数中完成子进程回收
	while(waitpid())
```

```c
#
```



#### 多线程并发服务器

在使用线程模型开发服务器时需考虑以下问题：

1. 调整进程内最大文件描述符上限
2. 线程如有共享数据，考虑线程同步
3. 服务于客户端线程退出时，退出处理。（退出值，分离态）
4. 系统负载，随着链接客户端增加，导致其它线程不能及时得到CPU

**创建流程：**
```c
1.socket()	创建监听socket
2.bind()		绑定服务器地址结构
3.listen()
4.while(1){
	cfd=Accept(lfd);		//接收客户端连接请求
	pthread_create(&tid,NULL,tfn,NULL);
	ptjread_datech(tid);//如果想回收子线程返回值，使用pthread_join(tid,void**)	另外为了防止信号与线程冲突，创建一个新线程用于子线程回收（兄弟进程之间不能用这个方法）
}
5.子线程
void *tfn(void *arg)	//回调函数
{
	close(lfd);	//关闭用于连接的套接字lfd
	read(cfd);
	数据操作
	write(cfd);
}
```

**read返回0代表已经读到末尾，对端已经关闭**

### 多路I/O转接服务器

由于原程序每次需要程序阻塞等待与接收，当连接数很多时会影响效率

该类服务器实现的主旨思想是，不再由应用程序自己监视客户端连接，取而代之由内核替应用程序监视文件。

主要使用的方法有三种：select，poll，epoll

#### select

select代理accept进行监听，当发生连接时由select接收并转至accept，accept生成接收文件描述符后转交select，待有数据传入时再由select转入用户程序

select采用的是轮询模型，解决1024以下客户端时使用select是很合适的，但如果链接客户端过多，会大大降低服务器响应效率

```c
int select(int nfds, fd_set *readfds, fd_set *writefds,	fd_set *exceptfds, struct timeval *timeout);
```

nfds: 			监控的所有文件描述符集里**最大文件描述符加1**，因为此参数会告诉内核检测前多少个文件描述符的状态（循环上限）
readfds：	监控有读数据到达文件描述符集合，传入传出参数
writefds：	监控写数据到达文件描述符集合，传入传出参数
exceptfds：	监控异常发生达文件描述符集合,如带外数据到达异常，传入传出参数

**描述符放入以上三个集合表示进行对应的监听，传入的是需要监听的，传出时是实际发生事件的**，数据模型为位图

timeout：	定时阻塞监控时间，3种情况
	1.NULL，永远等下去
	2.设置timeval，等待固定时间
	3.设置timeval里时间均为0，检查描述字后立即返回，轮询

```c
struct timeval {
	long tv_sec; /* seconds */
	long tv_usec; /* microseconds */
};
/*文件描述符集合操作函数*/
void FD_CLR(int fd, fd_set *set); 	//把文件描述符集合里fd位清0
int FD_ISSET(int fd, fd_set *set); 	//测试文件描述符集合里fd是否置1
void FD_SET(int fd, fd_set *set); 	//把文件描述符集合里fd位置1
void FD_ZERO(fd_set *set); 			//把文件描述符集合里所有位清0
```

socket创建

```c
lfd=socket();				//创建套接字
bind();						//绑定地址结构
listen();					//设置监听上限
fd_set rset,allset;				//创建监听集合
FD_ZERO(&allset);				//将监听集合清空
FD_SET(lfd,&allset);			//将lfd添加到读集合
while(1){
    rset=allset;			//保存监听对象
	ret=select(lfd+1,&rset,NULL,NULL,timeval类型(可以是NULL));		//监听文件描述符集合对应事件
	if (ret >0){				/*有监听的描述符满足对应事件*/
    	if (FD_ISSET(lfd,&rset)){			//1在，0不在
        	cfd=accept();			//建立连接，返回用于通信的文件描述符
        	FD_SET(cfd,&allset);	//添加到监听通信描述符集合中
    	}
        for(i=lfd+1;i<=最大文件描述符;i++){
            FD_ISSET(lfd,&rset);			//有read,write事件发生
        	read();
            /*数据操作*/
            write();
        }
	}
}
```

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
#include<arpa/inet.h>
#include<ctype.h>

#include "wrap.h"

#define SERV_PORT 8088

int main(int argc,char *argv[])
{
    int i,j,n,nready;
    int maxfd=0;
    int listenfd,connfd;//监听和连接fd
    char buf[BUFSIZ];

    struct sockaddr_in clie_addr,serv_addr;
    socklen_t clie_addr_len;

    listenfd =Socket(AF_INET,SOCK_STREAM,0);
    int opt=1;
    setsockopt(listenfd,SOL_SOCKET,SO_REUSEADDR,&opt,sizeof(opt));
    bzero(&serv_addr,sizeof(serv_addr));
    serv_addr.sin_family=AF_INET;
    serv_addr.sin_addr.s_addr=htonl(INADDR_ANY);
    serv_addr.sin_port=htons(SERV_PORT);

    Bind(listenfd,(struct sockaddr *)&serv_addr,sizeof(serv_addr));
    Listen(listenfd,128);
    fd_set rset,allset;         //rset用来读事件文件描述符集合，allset用来暂存
    maxfd=listenfd;

    FD_ZERO(&allset);
    FD_SET(listenfd,&allset);   //构造select监控文件描述符集

    while (1)
    {
        rset=allset;            //每次循环重新设置监控信号集
        nready=select(maxfd+1,&rset,NULL,NULL,NULL);
        if(nready<0)
            perr_exit("select err");
        if(FD_ISSET(listenfd,&rset)){       /*listenfd有事件，有新的客户端链接请求*/
            clie_addr_len=sizeof(clie_addr);
            connfd=Accept(listenfd,(struct sockaddr *)&clie_addr,&clie_addr_len);       //accept不会阻塞
            FD_SET(connfd,&allset);             //向监控文件描述符集合allset添加新的文件描述符connfd

            if(maxfd<connfd)    //新产生的文件描述符比max大，更新max
                maxfd=connfd;
            if(0==--nready)                 // 说明select只返回了一个listenfd，后续的for不需要执行
                continue;            
        }

        for(i=listenfd+1;i<=maxfd;i++){     /*除了listenfd还有别的位，检查有哪个fd有通信*/
            if(FD_ISSET(i,&rset)){          /*当前i位置有写入*/
                if((n=Read(i,buf,sizeof(buf)))==0){ /*读取完毕，此时对端client关闭链接，则服务端也关闭对应链接*/
                    Close(i);
                    FD_CLR(i,&allset);
                }
                else if(n>0){               /*还有数据没读完*/
                    for(j=0;j<n;j++)
                        buf[j]=toupper(buf[j]);
                    write(STDOUT_FILENO,buf,n);    
                    Write(i,buf,n);
                }
            }
        }
    }

    Close(listenfd);
    return 0;
    
}
```

核心为循环遍历部分，每次从select中取出描述符，如果不为零说明发生连接或者数据传递，listen的fd为最低位，而新建立的connect的fd为最高位，在此范围遍历即可

优点：是多路IO转接中唯一一种可以跨平台

缺点：如果fd跨度大且分布散乱，则会有很长的遍历范围，且文件描述符最大为1024

改进：可以将for循环改成一个自定义数组，将使用的fd添加到数组中，使用时只需查询数组头部不为-1的值即可

```c
int client[FD_SETSIZE];
for(i=0;i<FD_SETSIZE;i++){
    client[i]=-1;//初始化
}
for(i=0;i<FD_SETSIZE;i++)
{
    if(client[i]=-1)
    {
        client[i]=fd;//添加fd
        break;
    }
}
for(i=0;i<=maxi;i++){
    if((sockfd=client[i])<0)
        continue;//没在数组里就不用判断
    if(FD_ISSET(sockfd,&rset)){
        if((n=Read(sockfd,buf,sizeof(buf)))==0){
            Close(sockfd);
            FD_CLR(sockfd,&allset);
            client[i]=-1;
        }else if(n>0){
            for(j=0;j<n;j++)
                buf[j]=toupper(buf[j]);
            write(sockfd,buf,n);
        }
        if(--nready==0)
            break;
    }
}
```

#### poll

```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

fds：pollfd结构体数组，包含了fd和读写事件

nfds：监控数组中有多少文件描述符需要被监控

timeout：超时时长，单位是毫秒

返回值：返回满足对应监听事件的文件描述符总个数



如果不再监控某个文件描述符时，可以把pollfd中，fd设置为-1，poll不再监控此pollfd，下次返回时，把revents设置为0。

相较于select而言，poll的优势：

​     1. 传入、传出事件分离。无需每次调用时，重新设定监听事件。

​     2. 文件描述符上限，可突破1024限制。能监控的最大上限数可使用配置文件调整
