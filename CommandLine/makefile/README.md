# Makefile

阮一峰的[Make命令教程](http://www.ruanyifeng.com/blog/2015/02/make.html)写的太好了。
我摘录以下要点

make只是一个根据指定的Shell命令进行构建的工具。它的规则很简单，
1. 要构建的文件是`<target>`
1. `<target>`依赖的那些文件是`<reprequisites>`
1. `<reprequisites>`比`<target>`新时，才使用`<command>`重新构建想要的那个`<target>`文件。
1. 没有`<reprequisites>`时，总是使用`<commands>`重新构建`<target>`。

## 最简单示例

```makefile
# 构建c.txt文件，需要准备好a.txt和b.txt文件
# 如果a.txt或者b.txt的修改时间，晚于c.txt，则按照后面的命令重新创建c.txt
# 如果c.txt不存在，则直接按照后面的命令，创建c.txt
c.txt: a.txt b.txt 
	# 第一个命令
	cat a.txt b.txt > c.txt
	# 第二个命令
	date >> c.txt
```
可以在simplest文件夹中，修改a.txt，b.txt，再用make命令观察c.txt的修改结果。

## Makefile文件格式
每一个Makefile文件，都由若干条规则构成。每个规则的结构如下：
```makefile
<target> : <prerequisites> 
[tab]  <commands>
```
`<prerequisites>` 和 `<commands>` 至少存在一个。
### 目标`<target>`
目标是规则的名称，目标通常是文件名。

```makefile
make
```
`make`命令不指定规则时，会执行Makefile文件的第一条规则。

```makefile
make c.txt
```
`make`命令会执行指定的`c.txt`规则。

有时，目标也可以是操作名称，比如，clean。当目标是操作名称`clean`时，如果目录中存在`clean`文件，`make clean`命令会不执行。需要申明`clean`为`伪目标`。如下
```makefile
.PHONY: clean
clean:
    rm *.o temp
```

### 前置条件`<prerequisites>`
```makefile
result.txt: source.txt
    cp source.txt result.txt
```
`result.txt`规则的前置条件为`source.txt`，意味着，

|                         | **存在**source.txt文件     | **不存在**source.txt文件    |
| ------------            | -------------------     | ------------------------- |
| __有`source.txt`规则__   | 依次执行`source.txt`,`result.txt`规则|依次执行`source.txt`,`result.txt`规则|
| __没有`source.txt`规则__ | 执行`result.txt`规则       | make命令报错                |


```makefile
.PHONY: source
source: file1 file2 file3
```
使用伪目标`source`可以让
```makefile
make source
```
相当于
```makefile
make file1
make file2
make file3
```

### 命令`<commands>`
命令就是普通的shell命令，但是一条命令对应一个shell，因此，多条命令之间，不能共享环境变量。如果有这个需求，需要添加`.ONESHELL:`命令
```makefile
.ONESHELL:
var-kept:
    export foo=bar; 
    echo "foo=[$$foo]"
```

## Makefile语法

### 注释
使用`#`来添加注释
```Makefile
test:
    #这是注释
```

### 取消回声
在命令前添加`@`来取消，命令执行过程中打印命令
```makefile
test:
    @# 这是测试
    @echo TODO
```
`@`通常加在，注释和`echo`命令前。

### 通配符
通配符（wildcard）用来指定一组符合条件的文件名。Makefile 的通配符与 Bash 一致，主要有星号（*）、问号（？）和 [...] 。比如， *.o 表示所有后缀名为o的文件。
```makefile
clean:
        rm -f *.o
```
### 模式匹配
Make命令允许对文件名，进行类似正则运算的匹配，主要用到的匹配符是%。比如，假定当前目录下有 f1.c 和 f2.c 两个源码文件，需要将它们编译为对应的对象文件。
```makefile
%.o: %.c
````
等同于下面的写法。
```makefile
f1.o: f1.c
f2.o: f2.c
```
使用匹配符%，可以将大量同类型的文件，只用一条规则就完成构建。

### 变量和赋值符
Makefile 允许使用等号自定义变量。
```makefile
txt = Hello World
test:
    @echo $(txt)
```
上面代码中，变量 txt 等于 Hello World。调用时，变量需要放在 $() 之中。
调用Shell变量，需要在美元符号前，再加一个美元符号，这是因为Make命令会对美元符号转义。
```makefile
test:
    @echo $$HOME
```
有时，变量的值可能指向另一个变量。
```makefile
v1 = $(v2)
```
上面代码中，变量 v1 的值是另一个变量 v2。这时会产生一个问题，v1 的值到底在定义时扩展（静态扩展），还是在运行时扩展（动态扩展）？如果 v2 的值是动态的，这两种扩展方式的结果可能会差异很大。
为了解决类似问题，Makefile一共提供了四个赋值运算符 （=、:=、？=、+=），它们的区别请看[StackOverflow](http://stackoverflow.com/questions/448910/makefile-variable-assignment)。
```makefile
VARIABLE = value
# 在执行时扩展，允许递归扩展。

VARIABLE := value
# 在定义时扩展。

VARIABLE ?= value
# 只有在该变量为空时才设置值。

VARIABLE += value
# 将值追加到变量的尾端。
```

### 函数

Makefile 还可以使用函数，格式如下。
```makefile
$(function arguments)
# 或者
${function arguments}
```
Makefile提供了许多内置函数，可供调用。下面是几个常用的内置函数。
（1）shell 函数
shell 函数用来执行 shell 命令
```makefile
srcfiles := $(shell echo src/{00..99}.txt)
```
（2）wildcard 函数
wildcard 函数用来在 Makefile 中，替换 Bash 的通配符。
```makefile
srcfiles := $(wildcard src/*.txt)
```
（3）subst 函数
subst 函数用来文本替换，格式如下。

```makefile
$(subst from,to,text)
```

下面的例子将字符串"feet on the street"替换成"fEEt on the strEEt"。

```makefile
$(subst ee,EE,feet on the street)
```
下面是一个稍微复杂的例子。
```makefile
comma:= ,
empty:=
# space变量用两个空变量作为标识符，当中是一个空格
space:= $(empty) $(empty)
foo:= a b c
bar:= $(subst $(space),$(comma),$(foo))
# bar is now `a,b,c'.
```

（4）patsubst函数
patsubst 函数用于模式匹配的替换，格式如下。
```makefile
$(patsubst pattern,replacement,text)
```
下面的例子将文件名"x.c.c bar.c"，替换成"x.c.o bar.o"。
```makefile
$(patsubst %.c,%.o,x.c.c bar.c)
```
（5）替换后缀名
替换后缀名函数的写法是：变量名 + 冒号 + 后缀名替换规则。它实际上patsubst函数的一种简写形式。
```makefile
min: $(OUTPUT:.js=.min.js)
```
上面代码的意思是，将变量OUTPUT中的后缀名 .js 全部替换成 .min.js 。

