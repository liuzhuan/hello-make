# Element 的 Makefile

Element，是由饿了么前端团队开发的基于 v2.0 的桌面端组件库。它的构建系统结合了 make 和 npm，以下是 [v2.4.1][permalink] 的 Makefile 文件：

```
.PHONY: dist test
default: help

# build all theme
build-theme:
	npm run build:theme

install:
	npm install

install-cn:
	npm install --registry=http://registry.npm.taobao.org

dev:
	npm run dev

play:
	npm run dev:play

new:
	node build/bin/new.js $(filter-out $@,$(MAKECMDGOALS))

new-lang:
	node build/bin/new-lang.js $(filter-out $@,$(MAKECMDGOALS))

dist: install
	npm run dist

deploy:
	@npm run deploy

pub:
	npm run pub

test:
	npm run test:watch

help:
	@echo "   \033[35mmake\033[0m \033[1m命令使用说明\033[0m"
	@echo "   \033[35mmake install\033[0m\t\033[0m\t\033[0m\t\033[0m\t---  安装依赖"
	@echo "   \033[35mmake new <component-name> [中文名]\033[0m\t---  创建新组件 package. 例如 'make new button 按钮'"
	@echo "   \033[35mmake dev\033[0m\t\033[0m\t\033[0m\t\033[0m\t---  开发模式"
	@echo "   \033[35mmake dist\033[0m\t\033[0m\t\033[0m\t\033[0m\t---  编译项目，生成目标文件"
	@echo "   \033[35mmake deploy\033[0m\t\033[0m\t\033[0m\t\033[0m\t---  部署 demo"
	@echo "   \033[35mmake pub\033[0m\t\033[0m\t\033[0m\t\033[0m\t---  发布到 npm 上"
	@echo "   \033[35mmake new-lang <lang>\033[0m\t\033[0m\t\033[0m\t---  为网站添加新语言. 例如 'make new-lang fr'"
```

整体上来讲，npm scripts 负责实际工作，此处的 make 充当 npm scripts 的快捷方式。

比较难以理解的部分是 `new` 和 `new-lang`。其中有几个高级用法 `filter-out`, `$@`, `MAKECMDGOALS`。其具体代码如下：

```
new:
	node build/bin/new.js $(filter-out $@,$(MAKECMDGOALS))

new-lang:
	node build/bin/new-lang.js $(filter-out $@,$(MAKECMDGOALS))
```

我们对其中的关键语法逐一拆解。

## filter-out 函数

其中的 [`filter-out`][text-func] 是 GNU make 的一个文本函数。它的签名是：

```
$(filter-out pattern...,text)
```

`text` 是空格分隔的字符串，`pattern` 是目标字符串模版。这个函数将 `text` 中所有匹配 `pattern` 的单词删除，返回剩余的单词。

比如，如果有如下变量定义：

```
objects=main1.o foo.o main2.o bar.o
mains=main1.o main2.o
```

以下的函数会返回所有除 `mains` 变量之外的其余变量：

```
$(filter-out $(mains),$(objects))
```

## $@

`$@` 表示当前目标。

## MAKECMDGOALS

`MAKECMDGOALS` 是一个特殊的 make 变量，表示在命令行中指定的构建目标名列表。

## REF

- [Makefile of element][permalink]
- [GNU make: Text Functions][text-func]
- [GNU make: Goals][goals]

[permalink]: https://github.com/ElemeFE/element/blob/c93851abd127412b1adce20de901b826d1b7fe19/Makefile#L1-L46
[text-func]: https://www.gnu.org/software/make/manual/html_node/Text-Functions.html
[goals]: https://www.gnu.org/software/make/manual/html_node/Goals.html