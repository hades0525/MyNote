[TOC]
# linux 
## 权限
`d rwx r-x r-x 3 root root`
第 1 位为文件类型字段，后 9 位为文件权限字段

1. 文件类型:  
* d：目录  
* -：文件    
* l：链接文件

2. 权限字段
  9 位的文件权限字段中，每 3 个为一组，共 3 组，每一组分别代表对`文件拥有者u`、`所属群组g`以及`其它人o`的文件权限。
3. 数字代表文件的数量
4. 第一个root: 文件拥有者   
5. 第二个root:所属群组
6. rwx：代表可读4，可写2，可执行1

修改文件/目录的权限的命令：

```sh
chmod
chmod u=rwx,g=rw,o=r aaa.txt
chmod 764 aaa.txt
chmod +x aaa.txt

# 改变文件所属用户 ：
chown 用户名 文件名
```



##  命令
### 基本命令

>ctrl-a   可以将光标移至行首
>ctrl-e   可以将光标移至行尾
>ctrl-k   可以删除光标至行尾的所有内容
>ctrl-u   可以删除行内光标所在位置之前的内容
>ctrl-w   删除你键入的最后一个单词
>ctrl-l :   清屏



>Tab：命令和文件名补全；
>man:指令的基本用法与选项介绍。
>-- help:指令的具体信息显示
>pwd：显示当前所在位置



>ls: 列出文件或者目录的信息 ` ls [-atl]  file|dir`

>cd：切换目录  
>cd ..（或cd../）： 切换到上一层目录
>cd /： 切换到系统根目录
>cd ~： 切换到用户主目录
>cd -： 切换到上一个操作所在目录

>mkdir ：增加目录

>mv [dir] [newdir]：修改目录/文件的名称，移动位置

>cp [-r] ：复制文件/目录  -r 表示递归

>rm [-rf] ：删除文件/目录

#### find
>find ：查找目录下的文件及文件夹
>`find .`  ：列出当前目录及子目录下所有文件和文件夹
>`find /home -name “*.txt”` ：在/home目录下查找以.txt结尾的文件名
>`find /home -iname “*.txt”` ：同上，但忽略大小写:
>`find . \( -name "*.txt" -o -name "*.pdf" \)` 或` find . -name "*.txt" -o -name "*.pdf"`  ： 当前目录及子目录下查找所有以.txt和.pdf结尾的文件

### 系统信息

#### ps和grep
>ps -ef : 查看当前系统正在运行进程
>gerp：搜索 
>ps -ef | grep xx：查看特定的进程
>grep -v xx: 不包含
>grep -w xx:只显示全字符符合的列
>kill -9 [uid] : 杀死进程


>查看当前系统的网卡信息：ifconfig
>查看与某台机器的连接情况：ping
>查看当前系统的端口使用：netstat -an

>查看内存信息  cat /proc/meminfo
>查看CPU信息  cat /proc/cpuinfo




### 用户管理
* 用户添加 ：
  useradd : 
  -d 目录 指定用户主目录
  -g 用户组 指定用户所属的用户组。
  useradd -d usr/tlz -g root tlz
  口令管理：passwd  
  用户修改: usermod   用户删除: userdel -r


### 链接

Linux具有为一个文件起多个名字的功能，称为链接。被链接的文件可以存放在相同的目录下，但是必须有不同的文件名，而不用在硬盘上为同样的数据重复备份。另外，被链接的文件也可以有相同的文件名，但是存放在不同的目录下，这样只要对一个目录下的该文件进行修改，就可以完成对所有目录下同名链接文件的修改。

`ln -s /usr/mengqc/mub1 /usr/liu/abc`

执行该命令后，/usr/mengqc/mub1代表的路径将存放在名为/usr/liu/abc的文件中。

### 文件操作
>touch filename : 新建文件

>tail [-f] 查看文件的尾部内容
>tail -f : 显示文件最新追加的内容 查看日志更新
>tail -10 : 显示后10行
>tail +20 : 显示20行至文件末尾

>cat :
>more [-cd] : 全屏方式显示文本  -c 先清屏 -d 显示百分比
>回车：下一行
>空格：下一屏
>q : 退出

>less : 分页显示文本 
>上下键翻页
>q : 退出

#### 打包
>tar -zcvf 打包后的名.tar.gz 要打包的文件名 ：打包并压缩文件
>z：调用gzip压缩命令进行压缩
>c：打包文件
>v：显示运行过程
>f：指定文件名

>tar [-xvf] 压缩文件
>x : 解压

>unzip ：解压zip包和war包

#### awk
awk '{print $2}' : 取出第二个字段输出

#### sed

## vim
#### **正常模式**
复制一行：yy      y$(当前到行尾)
粘贴：p       
删除：s
上一行末尾：k
下一行末尾：j

#### **编辑模式**
正常模式进入编辑模式： i  o  a 

#### **命令模式**
进入命令模式： : 
查找：/    查找后用 n 移动光标
查找：？  从末尾开始查找
替换：
> s/x/y/g  把当前行的所有的x替换成y
>  %s/x/y/g 把所有的x替换成y
>  10,15s/1/2  把10-15行所有1替换成2

## shell

Shell 是一个程序，一般都是放在/bin或者/user/bin目录下，当前 Linux 系统可用的 Shell 都记录在`/etc/shells`文件中。
`/etc/shells`是一个纯文本文件，可以使用 cat 命令查看它。

查看当前 Linux 的默认 Shell，那么可以输出 SHELL 环境变量：` echo $SHELL`

>对于普通用户，Base shell 默认的提示符是美元符号$；
>对于超级用户（root 用户），Bash Shell 默认的提示符是井号#。该符号表示 Shell 等待输入命令。

>Shell 通过`PS1`和`PS2`两个环境变量来控制提示符格式：

>新建一个文件，扩展名为sh（sh代表shell），扩展名并不影响脚本执行
>第一行    `#!/bin/bash`     
>
>`#!/usr/bin/env bash`是优先选择的，因为它提供了灵活性，特别是你想在不同的版本下运行这个脚本；
>“#!” 是一个约定的标记，告诉系统脚本需要什么解释器来执行，使用哪一种Shell。
>`chmod +x` 使脚本可执行

### bash 命令

-c string 如果有 -c 选项，那么命令将从 string 中读取。如果 string 后 面
         有参数 (argument)，它们将用于给位置参数 (positional parameter
         ，以 $0 起始) 赋值。


### 特殊命令

| 变量 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| $$   | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |
| $?   | 上个命令的退出状态，或函数的返回值。                         |
| $0   | 当前脚本的文件名                                             |
| $n   | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。 |
| $#   | 传递给脚本或函数的参数个数。                                 |
| $*   | 传递给脚本或函数的所有参数。                                 |
| $@   | 传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同 |

### **命令替换**
是指Shell可以先执行命令，将输出结果暂时保存，在适当的地方输出。
```shell

在 bash shell 中，$( ) 与` ` (反引号) 都是用来做命令替换用(commandsubstitution)的。
`command`

$(command)
```

### **变量替换**

|形式|说明|
|---|---|
|${var}|变量本来的值|
|${var:-word}|如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。|
|${var:=word}|如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。|
|${var:?message}|变量 var 是否可以被正常赋值。若此替换出现在Shell脚本中，那么脚本将停止运行。|
|${var:+word}|如果变量 var 被定义，那么返回 word，但不改变 var 的值。|
### **运算符**

Bash 支持很多运算符，包括算数运算符、关系运算符、布尔运算符、字符串运算符和文件测试运算符。
原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

**条件表达式要放在方括号[]之间，并且要和括号有空格；
表达式和运算符之间也要有空格**

| 运算符 | 说明                                                         | 举例                                                         |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| +      | 加法                                                         | `expr $a + $b`                                               |
| *      | 乘法                                                         | `expr $a \* $b`  乘号前边必须加反斜杠才能实现乘法运算；      |
| ==     | 相等                                                         | [ $a == $b ] 条件表达式要放在方括号之间，并且要和括号有空格；表达式和运算符之间也要有空格 |
| -eq    | 检测两个数是否相等，相等返回 true                            | [ $a -eq $b ]                                                |
| -o     | 或运算，有一个表达式为 true 则返回 true。                    | [ $a -lt 20 -o $b -gt 100 ]                                  |
| -a     | 与运算，两个表达式都为 true 才返回 true。                    | [ $a -lt 20 -a $b -gt 100 ]                                  |
| -z     | 检测字符串长度是否为0，为0返回 true。                        | [ -z $a ]                                                    |
| -n     | 检测字符串长度是否为0，不为0返回 true。                      | [ -n $a ]                                                    |
| -d     | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ]                                                 |
| -f     | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ]                                                 |
| -s     | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ]                                                 |
| -e     | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ]                                                 |

```shell
if [ $a -lt 5 -o $b -gt 100 ]
then
    echo "$a -lt 100 -o $b -gt 100 : returns true"
else
    echo "$a -lt 100 -o $b -gt 100 : returns false"
fi
```

```shell

basename:
basename /usr/bin/sort          -> "sort"

dirname:
dirname /usr/bin/          -> "/usr"

-----------------------------------------------------------------

| 运算符：
command1 | command2
他的功能是把第一个命令command1 执行的结果 作为command2的 输入 传给command2
ls -s|sort -nr
该命令列出当前目录中的文档(含size)，并把输出送给sort命令作为输入，sort命令按数字递减的顺序把ls的输出排序。

---------------------------------------------------------------------
&& 运算符：
command1  && command2
&&左边的命令（命令1）返回真(即返回0，成功被执行）后，&&右边的命令（命令2）才能够被执行


|| 运算符：
command1 || command2
||左边的命令（command1）未执行成功，那么就执行||右边的命令（command2）


() 运算符：
(command1;command2;command3....)               多个命令之间用;分隔
把几个命令合在一起执行


(()) 运算符：
只要括号中的运算符、表达式符合C语言运算规则，都可用在$((exp))中
双括号中的变量可以不使用$符号前缀。括号内支持多个表达式用逗号分开。只要括号中的表达式符合C语言运算规则，比如可以直接使用for((i=0;i<5;i++))， 如果不使用双括号， 则为for i in `seq 0 4`或者for i in {0..4}。再如可以直接使用if (($i<5))， 如果不使用双括号， 则为if [ $i -lt 5 ]。


[[]] 运算符：
[[ ]] 结构比[ ]结构更加通用
比如，&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中
可以直接使用if [[ $a != 1 && $a != 2 ]], 如果不适用双括号, 则为if [ $a -ne 1] && [ $a != 2 ]或者if [ $a -ne 1 -a $a != 2 ]。
```

```shell

>/dev/null 2>&1

1：> 代表重定向到哪里，例如：echo "123" > /home/123.txt
2：/dev/null 代表空设备文件
3：2> 表示stderr标准错误
4：& 表示等同于的意思，2>&1，表示2的输出重定向等同于1
5：1 表示stdout标准输出，系统默认值是1，所以">/dev/null"等同于 "1>/dev/null"


```



### 字符串
```shell
# 获取字符串长度
string="abcd"
echo ${#string} #输出 4

# 截取字符串
string="alibaba is a great company"
echo ${string:1:4} #输出liba

```

### 数组
```shell

array_name=(value0 value1 value2 value3)

# 使用@ 或 * 可以获取数组中的所有元素
${array_name[*]}
${array_name[@]}


# 取得数组元素的个数
length=${#array_name[@]}

# 取得数组单个元素的长度
lengthn=${#array_name[n]}

```


### case esac语句
```shell
    case ${ACTION} in
    war)
        deploy_war  # 函数的调用
        ;;
    min)
        deploy_min
        ;;
    web)
        deploy_web
        ;;
    restart)
        stop_tomcat
        start_tomcat
        ;;
    *)
        usage
        ;;
    esac
```


### for
```shell
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

### function
```shell
function test() {
    local pids = #命名变量
    
    sh startup.sh >/dev/null 2>&1  # 运行脚本
    
    log "stop tomcat success."  # 输出日志
}

test  # 调用函数

```

### 重定向

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：
* 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
* 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
* 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

| 命令            | 说明                              |
| --------------- | --------------------------------- |
| command > file  | 将输出重定向到 file。             |
| command < file  | 将输入重定向到 file。             |
| command >> file | 将输出以追加的方式重定向到 file。 |

```python







```