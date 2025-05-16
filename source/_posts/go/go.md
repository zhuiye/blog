# go 之学习

brew install go

go version

当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 protected ）。

这个导出从命名上来，有点意思

大写开头为导出属性
小写则为私有属性

并行赋值也被用于当一个函数返回多个返回值时，比如这里的 val 和错误 err 是通过调用 Func1 函数同时得到：val, err = Func1(var1)。

iota 在 const 关键字出现时将被重置为 0(const 内部的第一行之前)，const 中每新增一行常量声明将使 iota 计数一次(iota 可理解为 const 语句块中的行索引)。

导入包的机制，主要看 go.mod 文件

```mod

 module learnGo  // 这个相当于  整个项目的唯一标识，可以这么认为，对应 package中的 name ,
 // 此外，还需要 引入本地的保中加入其前缀

go 1.24.1

require (
	github.com/apcera/termtables v0.0.0-20170405184538-bcbc5dc54055 // indirect
	github.com/araddon/dateparse v0.0.0-20210429162001-6b43995a97de // indirect
	github.com/mattn/go-runewidth v0.0.10 // indirect
	github.com/rivo/uniseg v0.1.0 // indirect
	github.com/scylladb/termtables v0.0.0-20191203121021-c4c0b6d42ff4 // indirect

)
```

引入一个包 相当于 yarn install
`go get github.com/araddon/dateparse`

go context
https://zhuanlan.zhihu.com/p/68792989
