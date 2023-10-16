# Linux学习

## Chapter 1

## Chapter 2

### 工作目录展示

1. cd (change directory) 更改工作目录

2. pwd (print work directory) 查看当前工作目录

### 相对路径和绝对路径

1. 绝对路径 : 以根目录作为起点,描述路径的一种写法,路径描述以`/`开头

2. 相对路径 : 以当前目录为起点,描述路径的一种写法,路径描述无需以`/`开头

3. 特殊路径符 : 

```Linux
.    表示当前目录
..   表示上一级目录
~    表示HOME目录
```

### 创建目录命令

mkdir : make directory 创建新的目录

语法 : `mkidr [-p] Linux路径`

-p : 表示自动创建不存在的父目录,适用于创建连续多层级的目录

### 文件操作命令

- `touch 创建文件`

- `cat 查看文件内容`

- `more 查看文件内容`-支持翻页,可以一页页展示  (使用q退出查看,空格翻页)

- `cp [-r] 参数1(被复制文件) 参数2(复制去的地方)` 用于复制文件\文件夹,-r表示文件夹的移动

- `mv 参数1 参数2` 用于移动文件\文件夹 

- `rm [-r -f] 参数1 参数2` 用于删除文件\文件夹 (-r表示删除文件夹,-f表示force强制删除)

>  使用`su - root`然后输入密码进入到root账户
> 
>  使用`exit` 退出root账户

- `grep [-n] 关键字 文件路径(可做内容端口)` 用于从文件中通过关键字过滤文件行 (-n表示结果中显示匹配的行的行号)

- `wc [-c -m -l -w] 文件路径` 用于统计文件的行数,单词数量

- `|`  将管道符左边命令的结果,作为右边命令的输入

- `echo 输出的内容` 用于在命令行输出指定内容 

- `` ` 作为命令执行

- 重定向符号`> 和 >>` 单箭头指覆盖写入到符号右侧指定的文件中,双箭头表示追加

- `tail [-f -num] Linux路径` 用于查看文件尾部内容,跟踪文件的最新更改,-f表示持续跟踪,-num表示查看尾部多少行,默认为10行

### 查找命令

- `which` 要查找的命令

- `find 起始路径 -name "被查找文件名"`  用于搜索指定的文件

- `find 起始路径 -size +!-n[kMG]`  (+/-表示大于和小于,n表示大小数字)

### vim编辑器

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-11-21-30-21-image.png)

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-11-21-42-07-image.png)

## Chapter 3

`sudo 其他命令` 为该条命令临时赋予root权限执行

> 需要进入root用户后使用`visudo`在里面添加账户,并且使用`exit`退出到普通账户,然后再使用带有root权限的指令

### 用户组管理

- 创建用户组  `groupadd 用户组名`

- 删除用户组  `groupdel 用户组名`

- 创建用户  `useradd [-g -d] 用户名`

- 删除用户  `userdel [-r] 用户名`

- 查看用户所属组  `id [用户名]`

- 修改用户所属组  `usermod -aG 用户组 用户名`

- 查看当前系统中有哪些用户  `getent passwd`

> 需要切换到root用户才能使用命令

#### Linux文件权限

<img title="" src="file:///C:/Users/Lenovo%20PC/AppData/Roaming/marktext/images/2023-07-12-22-31-24-image.png" alt="" data-align="left">

修改权限命令 `chmod [-R] 权限 文件夹或文件` (-R对文件夹内的全部内容应用同样的操作)

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-12-22-49-41-image.png)

修改文件\文件夹的所属用户  `chown [-R] [用户][:][用户组] 文件或文件夹`

### Chapter 4

#### 软件安装

`yum [-y] [install | remove | search] 软件名称`

-y : 自动确认,无需手动确认安装或者卸载过程

> yum命令需要root权限,且需要联网

#### 软件管理

`systemctl start | stop | status | enable | disable 服务名`

用于启动\停止\开机自启等服务

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-13-14-45-58-image.png)

#### 软连接

`ln -s 参数1 参数2`

用于将文件\文件夹链接到其他位置

#### 系统时间

`date [-d] [+格式化字符串]`

-d : 按照给定的字符串显示日期,一般用于日期计算

格式化字符串 : 通过特定的字符串标记,来控制显示的日期格式

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-13-16-47-33-image.png)

##### ntp时间自动校准

`systemctl start ntpd`    启动ntp程序

`systemctl enable ntpd`   设置开机自启动

> ntpdate -u npt.aliyun.com    手动校准时间

#### 网络请求

`ping [-c num] ip或主机名`    检查指定服务器是否可以连接

`wget [-b] url`     非交互式的文件下载器,-b表示后台下载,将日志写入到当前工作目录的wget-log文件

`curl [-O] url`       发送http请求

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-13-17-43-00-image.png)

`nmap ip地址`    查看暴露端口

`netstat -anp|grep 端口号`        查看指定端口的占用情况

#### 进程

`ps [-e -f]`        用于查看Linux系统中的进程信息

-e : 显示出全部的进程

-f : 以完全格式化的形式展示信息(展示全部信息)

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-13-21-03-42-image.png)

`kill [-9] pid`

-9 : 表示强制关闭进程

#### 主机状态监控

`top`    查看CPU/内存使用情况

![](C:\Users\Lenovo%20PC\AppData\Roaming\marktext\images\2023-07-13-21-19-54-image.png)

`df [-h]`    查看硬盘的使用情况

`iostat [-x] [num1] [num2]`        查看CPU\硬盘的相关信息

-x : 显示更多信息

`sar -n DEV num1 num2`                查看网络统计

-n : 查看网络,DEV表示查看网络接口port

#### 环境变量

`export 变量名=$变量值`

#### 压缩与解压

<img title="" src="file:///C:/Users/Lenovo%20PC/AppData/Roaming/marktext/images/2023-07-13-22-18-33-image.png" alt="" data-align="center">

`tar [-c -v -x -f -z -C] 参数1 参数2 ... 参数N`    解压压缩

<img src="file:///C:/Users/Lenovo%20PC/AppData/Roaming/marktext/images/2023-07-13-22-20-12-image.png" title="" alt="" data-align="center">

# 
