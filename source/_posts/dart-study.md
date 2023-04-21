---
title: dart-study
date: 2019-07-22 23:29:18
tags:
---
#dart Study
https://dart.dev/guides/language/language-tour#a-basic-dart-program

### dart 语言教程

### 一个基础的dart 程序
下面的代码用了dart基础功能
```dart
    // 定义一个函数
    printInteger(int aNumber) {
        print('The number is $aNumber.'); // Print to console.
    }

    // app的执行入口main函数.
    main() {
        // 声明并初始化一个变量
        var number = 42;
        printInteger(number); // Call a function.
    }
```
<b>//</b>  这是一个单行的注释

<b>int</b> 一个内置的类型(built in types),其它内置类型有String,bool...

<b>$variableName (or ${expression})</b> 字符串插值，类似字符串模板

### 重要概念
----

1. 一切皆对象
2. Dart是强类型语言，类型声明是可选的，因为有类型推断,number 被推断为int,
3. dart 支持常规类型，List\<int>或者 List\<dynamic>
4. dart 支持全局函数，如 main(), 可以嵌套函数，或者当作类实例的方法，对象的方法
5. 相似的，dart 也支持全局的变量,也可当作对象的属性，类实例的属性
6. 不像java,<b>dart</b> 没有关键字 <b>public, protected ,private</b> 如果一个标识符以"_"开头,它将在这个库是私有的.
7. Dart 的工具能报告两种类型问题，警告和错误,警告不会阻止你的代码执行，但错误会

### keyWords

略....

### 变量

创建变量并初始化
```dart
   var name="hengcheng"
   
   dymaic name="hengcheng"
   String name="hengcheng"
```
变量储存引用,name引用了一个带有值hengcheng得字符串对象
```dart
    int lineCount;
    assert(lineCount == null);
```
没被赋值得变量有一个默认值为<b>null</b>

#### Final and const
如果你没有意图去改变一个变量得值，用const 或者final

```dart
   final name = 'Bob'; // Without a type annotation
   final String nickname = 'Bobby';
```
### 内置类型
1. numbers
2. strings
3. booleans
4. lists(就像数组) 
5. sets 
6. maps
7. runes 
8. symbols

### 字符串转数字
```dart
    // String -> int
    var one = int.parse('1');
    assert(one == 1);

    // String -> double
    var onePointOne = double.parse('1.1');
    assert(onePointOne == 1.1);

    // int -> String
    String oneAsString = 1.toString();
    assert(oneAsString == '1');

    // double -> String
    String piAsString = 3.14159.toStringAsFixed(2);
    assert(piAsString == '3.14');
```
