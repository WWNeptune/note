# Git使用学习
## 本地操作部分

### 初次使用

设置自己的用户名和邮箱（只是标记用）

```bash
git config --global user.name "NAME"
git config --global user.email name@email.com
```

### 创建仓库

在目标文件夹里输入```git init```

或者下载git仓库``` git clone 地址```

### 跟踪文件

在新建仓库后，仓库里的所有文件还是处于未跟踪状态，这时如果想将仓库中的文件加入跟踪，则执行

```bash
git add <name>
git add . #当前路径所有文件
```

如果想取消跟踪，则

```bash
git rm <name> #从目录移除
git rm --cache <name> #保留在目录但不跟踪
```

### 更新文件

在文件被修改后，通过缓存来记录修改

```bash
git add <file-name>
```

也可以撤销缓存

```bash
git reset HEAD <name>
```

缓存完成后便可commit提交本次修改

```bash
git commit				#输入后还需在编辑器内输入说明
git commit -m '提交说明' #该写法可以省略编辑器输入说明步骤
```

如果需要撤销提交，可以执行，但不能撤销首次提交

```bash
git reset head~ --soft
```

如果是第一次跟踪的文件```git add```，可以直接commit提交（毕竟还没改）

### 查看状态

输入命令```git status```可以查看当前路径下的文件情况，红色表示修改未暂存，绿色表示修改并已暂存，无则表示没有修改变动文件

输入命令```git diff```可以查看文件修改详情，命令```git log```可以查看提交历史记录

#### 小总结

新文件/修改文件 → git add → 暂存文件 → git commit →更新修改

## 远程仓库部分
### 新建远程仓库
在目标仓库创建一个空项目，获取到远程仓库链接后在本地进行添加
```bash
git remote add <name> <link>
git remote rename <old name> <new name> #名称只是本地识别用，与远程仓库无关
```

添加远程仓库后，便可将本地项目上传至仓库

```bash
git push <仓库名> <分支名>
```

一般推送至远程仓库时，需要登录账号，以github为例，需要仓库创建者创建一个令牌，给予权限后在git里进行登录上传