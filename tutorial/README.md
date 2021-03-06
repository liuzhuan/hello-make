# Make 命令教程

make 通过 Makefile 来构建软件，所有的依赖规则和构建步骤都在 Makefile 中指定。

除了默认的 Makefile 文件，也可以使用命令行指定其他的构建文件：

```sh
$ make -f rules.txt
# or
$ make --file=rules.txt
```

## Makefile 文件格式

Makefile 由一系列规则（rules）构成。每条规则的形式如下：

```
<target>: <prerequisite>
[tab]<commands>
```

其中有三大组件：目标（target）、前置条件（prerequisite）和命令（commands）。

## 目标

一个目标就构成一条规则。目标通常是文件名，可以是一个，也可以是多个。

除了文件名，目标还可以是某个操作的名字，称为“伪目标”（phony target）。

```
clean:
    rm *.o
```

```sh
$ make clean
```

### 伪目标

如果当前目录中，正好有一个文件叫做 `clean`，那么这个命令就不会执行。因为 make 发现目标文件已经存在，不用重新构建，从而也就不用执行命令了。

为了避免这种情况，可以声明 clean 为“伪目标”：

```
.PHONY: clean
clean:
    rm *.o
```

声明为“伪目标”后，make 就不会检查当前目录下是否存在一个叫做 clean 的文件，而是每次都执行对应的命令。

### 默认目标

如果 make 命令运行时没有指定目标，默认会执行 Makefile 的第一个目标。

## 前置条件

前置条件通常是一组文件，之间用空格分隔。

如果要生成多个文件，往往采用下面的写法：

```
source: file1 file2 file3
```

```sh
$ make source
```

上面的 source 是一个伪目标。

## 命令

命令表示如何更新目标文件。

每行命令之前必须有一个 tab 键。如果想用其他键，可以用内置变量 `.RECIPEPREFIX` 声明。

```
.RECIPEPREFIX = >
all:
> echo Hello, World
```

需要注意的是，每行命令在一个单独的 shell 中执行，这些 shell 之间没有继承关系：

```
var-lost:
    export foo=bar
    echo "foo=[$$foo]"
```

上面代码执行后（`make var-lost`），取不到 foo 的值。因为两个命令在不同的进程中执行。一个解决办法是将两行代码写在一行，中间用分号分隔。

```
var-kept:
    export foo=bar; echo "foo=[$$foo]"
```

另一个解决办法是在换行符前增加反斜线转义：

```
var-kept:
    export foo=bar; \
    echo "foo=[$$foo]"
```

## 自动变量

make 命令提供了一些自动变量（Automatic Variables），它们的值与当前规则有关。主要有以下几个：

### `$@`

`$@` 表示当前目标。比如 `make foo` 的 `$@` 就表示 `foo`。

```
a.txt b.txt:
    touch $@
```

等同于如下写法：

```
a.txt:
    touch a.txt
b.txt:
    touch b.txt
```

## REF

- [Make 命令教程 - 阮一峰](http://www.ruanyifeng.com/blog/2015/02/make.html)，2015/02/20
- [GNU make](https://www.gnu.org/software/make/manual/make.html)