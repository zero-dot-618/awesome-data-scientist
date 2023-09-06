# Linux基础

## 各发行版本

从技术上来说，李纳斯•托瓦兹开发的 Linux 只是一个内核。内核指的是一个提供设备驱动、文件系统、进程管理、网络通信等功能的系统软件，内核并不是一套完整的操作系统，它只是操作系统的核心。一些组织或厂商将 Linux 内核与各种软件和文档包装起来，并提供系统安装界面和系统配置、设定与管理工具，就构成了 Linux 的发行版本。Linux 的发行版说简单点就是将 Linux 内核与应用软件做一个打包。

目前常用的发行版本有Ubuntu、CentOS，此外还有RedHat、Debian、Fedora等。查看方式：

- `lsb_release` 命令
- `/etc/*-release` 文件
- `uname` 命令
- `/proc/version` 文件
- `dmesg` 命令

## 用户&用户组&权限

Linux是一个多用户系统，且不同的用户/组的权限是分开管理的，在安装软件、运行脚本的时候可能会碰到一些权限的问题，所以数据和算法也需要稍微了解一些相关知识。至少需要知道

- root 账号
- `su` 和 `sudo` 
- 读、写、可执行权限，`chmod` 命令

## 命令行

一个命令可能是可执行文件、shell 内置命令，还有可能是别名，可以通过 `type` 来判断。大部分情况下可以通过 `具体命令 --help`、`man` 来获取帮助文档，而不需要去借助搜索引擎，`apropos` 可以用来查找文档。

### 输入、输出、重定向

对于所有的 Linux 命令，我们首先要关注的就是它的输入、输出，即：

- 标准输出 `stdout`，即程序的结果，默认输出到终端窗口上
- 标准错误 `stderr`，运行过程中的状态以及错误信息，默认输出到终端窗口上
- 标准输入 `stdin`，默认情况下标准输入与键盘相连接

示例脚本文件 `error.sh` 的内容如下：

```sh
#!/bin/bash

echo "About to try to access a file that doesn't exist"
cat bad-filename.txt
```

echo 的内容（About to try to access a file that doesn't exist）会走到stdout，而 cat 后面这个文件不存在，会报错，相应的报错内容（cat: bad-filename.txt: No such file or directory）会走到stderr。

使用 `>` 重定向操作符可以将标准输出内容输出到文件中，而不是终端窗口上。另外可以用 `1>` 特指 `stdout`，用 `2>` 特指 `stderr`，还可以用 `>&` 将stderr、stdout输出到同一个目标。

```sh
# 执行文件前记得先给文件加上可执行权限：chmod +x error.sh

./error.sh # echo内容和cat报错内容都会输出到终端窗口
./error.sh > capture.txt # echo内容保存到文件capture.txt中，cat报错内容输出到终端窗口
./error.sh 2> capture.txt # echo内容输出到终端窗口，cat报错内容保存到文件capture.txt中
./error.sh 1> capture.txt 2> error.txt # echo内容保存到文件capture.txt中，cat报错内容保存到文件error.txt中
./error.sh > capture.txt 2>&1 #echo内容和cat报错内容都保存到文件capture.txt中
./error.sh &> capture.txt #echo内容和cat报错内容都保存到文件capture.txt中
```

`>` 会覆盖文件已有内容，如果要在文件后追加，需要使用 `>>`。此外，还可以使用管道符 `|` 将输出内容作为下一个命令的输入，例如 `ls | head`。如果不想要任何的输出内容，可以重定向到 `/dev/null`。

类似地，`<` 重定向操作符则可以将 `stdin` 的来源指定为某个文件。

### 日常使用

- 通过Tab键可以自动补全参数，使用上、下方向键可以快速定位到最近执行的命令。使用 `ctrl-r` 搜索命令行历史记录（按下按键之后，输入关键字便可以搜索，重复按下 `ctrl-r` 会向后查找匹配项，按下 Enter 键会执行当前匹配的命令，而按下右方向键会将匹配项放入当前行中，不会直接执行，以便做出修改）
- `ctrl-w` 删除光标前的一个单词，`ctrl-u` 删除行内光标前的所有内容，`ctrl-k` 删除光标至行尾的所有内容
- `alt-b`、`alt-f` 以单词为单位移动光标
- `ctrl-a` 将光标移动至行首，`ctrl-e` 将光标移动至行尾
- `ctrl-l` 可以清屏
- `alt-#` 可以快捷的在行首添加 `#`，这会把当前行的内容作为注释，再按下回车时不会执行任何命令。这样的好处是可以借助历史记录快速恢复写到一半的命令
- `man readline` 可以查看 Bash 中的默认快捷键
- `history` 查看命令行历史记录，再用 `!n`（n 是命令编号）就可以再次执行。其中有许多缩写，最有用的大概就是 `!$`， 它用于指代上次键入的参数，而 `!!` 可以指代上次键入的命令了
- `cd` 可以切换工作路径。`cd`、`cd ~` 切换到 home 目录；`cd -` 切换到前一个工作路径；`cd ..` 切换到上一级目录
- 把别名、shell 选项和常用函数保存在 ~/.bashrc；把环境变量的设定以及登陆时要执行的命令保存在 ~/.bash_profile。修改后不能立即生效，可以使用 `source` 命令
- 脚本中使用 `set -e` 设置为严格模式，在脚本发生错误时退出而不是继续运行
- `alias` 创建命令的快捷形式。例如：`alias ll='ls -latr'` 创建了一个新的命令别名 `ll`

### 软件管理

- `apt：`Debian 和 Ubuntu
- `yum`：CentOS、Fedora 和 RedHat

### 任务管理

- `kill`
- `nohup`、`disown`
- pstree -p 以一种优雅的方式展示进程树。
- top
- 使用 `nohup` 或 `disown` 使一个后台进程持续运行
- Bash 中的任务管理工具：&，ctrl-z，ctrl-c，jobs，fg，bg 等

### 文件管理

- `touch` 新建文件
- `mv` 移动
- `cp` 复制
- `rm` 删除
- `ln -s` 建立软连接

### 数据处理

- `cat` 连接和打印文件。`cat filename` 打印 filename 的文件内容到标准输出；`cat file1 file2 > file3` 将 file1 和 file2 的内容输出到 file3。
- `head`、`tail` 显示文件的前面 / 后面的行，默认 10 行。`head -n 500 file1` ：显示文件的前 500 行；`head -n 500 foo | tail -n 1` ：显示文件的第 500 行；`tail -f file1` ：显示文件 file1 的末尾，如果 file1 有新增内容，会同步更新显示
- `wc` 计算行数（ `-l` ）、字符数（ `-m` ）、单词数（ `-w` ）和字节数（ `-c` ）
- `split` 对文件进行切分。`-l` 按行数，`-b` 按字节数
- `shuf` 将输入按行随机打散，默认到标准输出，可以使用 `-o` 参数输出到指定文件
- `sort` 排序，`-u` 排序且去重，`-r` 逆序，`-o` 结果输出到文件（可以是输入的文本），`-n` 按数值排序，`-t` 指定分隔符 `-k` 指定按第几列
- `uniq` 去除**有序**输入中的重复行，经常和 `sort` 一起使用，如 `sort file1 | uniq`。`-c` 去除重复行的同时在每行行首输出改行重复的次数，`-d` 仅保留重复行，`-u` 仅保留不重复行，`-i` 忽略大小写
- `grep` 对输入内容进行匹配，支持正则。`-v` 只显示不能被匹配上的行，`-o` 只输出匹配的内容，`-i` 忽略大小写，`-n` 同时输出行号，`-c` 只统计匹配行数
- `sed` 按行处理输入内容，可以进行替换、删除、新增、选取等操作。`-n` 只输出处理过的行（否则默认输出所有行）；`-i` 原地修改文件。例如，删除内容为“NULL”的行：`sed -i '/^NULL$/d' filename`；替换文件中每行开头和末尾的空格和tab：`sed 's/^[ \t]*//;s/[ \t]*$//' < filename`（ `\t` 实测Mac上不行，Linux上可以）；在文件行首加入字符`__label__0`：`sed -i 's/^/__label__0 /'（`Mac 上需要写成 `sed -i '' -e 's/^/__label__0 /' filename`）
- `awk` 把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理
- `xargs` 
- 学会使用通配符 * （或许再算上 ? 和 [...]） 和引用以及引用中 ' 和 " 的区别

### 常用命令示例

```shell
# 去重且保存到新文件（会排序）
sort -u 文件名 > 新文件名
sort 文件名 | uniq > 新文件名

# 去重且保存到新文件（不会排序）
awk '!a[$0]++' 文件名

# 原地去重（不会排序）
gawk -i inplace '!a[$0]++' 文件名
```

### 参考资料

- [the-art-of-command-line](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)
- [Data Science at the Command Line](https://jeroenjanssens.com/dsatcl/)
- [Linux系列之重定向操作](https://www.cnblogs.com/chuckQu/p/16558771.html)
- [What Are stdin, stdout, and stderr on Linux?](https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/)
- [Linux系统简介和各发行版介绍](https://developer.aliyun.com/article/1213678)
- [查看 Linux 发行版名称和版本号的 8 种方法](https://linux.cn/article-9586-1.html)

