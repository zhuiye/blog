# type & interface 区别

主要区别在于不能重新打开类型来添加新属性，而接口总是可扩展的。

## 官方的说明

- https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases

- https://github.com/SunshowerC/blog/issues/7

其实从语义上来说，完全是不同的，type 类型别名，interface 是描述对象的结构，
他们两者在一定程度上可以实现相似的描述。

## 类型别名的使用场景

类型别名不能重复-类型创建后无法更改

任何类型的名称，

多次使用联合类型，那么我们 可以 用一个名称引用它。这就是类型别名，如下例子。另外还有 的元组，基础类型，这等情况下用 type
`type stringOrNumber=string|number`

## 接口的使用场景

接口声明是命名对象类型的另一种方式：

## 优先考虑 interface

这也是官方的推荐，除了 type 的使用场景外，描述数据就用 interface, -废话
