---
title: jQuery.extend
date: 2019-07-21 13:16:38
tags: jQuery
---
## JQuery.extend()方法详解

### 基本的使用方法..

1.只传一个对象的时候，对象里面的属性，会扩充到jQuery函数的原型中或者jQuery函数的属性中

```js
$.extend({sayHello(){
    console.log('hello wolrd')
}})
$.sayHello()

jQuery.fn.extend({sayHello(){
    console.log('hello wolrd')
}})
$().sayHello()

```

2.传入两个两个object 对象的甚至多个，会合并后续对象的属性到第一个对象中，并返回合并之后的值

```js
let a={name:'aiden',age:18}
let b={age:24}
const res=$.extend(a,b)
console.log(a)  // {name:'aiden',age:24}
console.log(res)  //{name:'aiden',age:24}
```

3.如果第一个参数传true,则执行深复制

```js
let a={name:'aiden',age:18,disrtc:{provinc:'GD',city:'ZH',zone:'lz'}}
let b={age:24,disrtc:{provinc:'GD',city:'gz'}}
const res=$.extend(true,a,b)
console.log(a)  //  name:'aiden',age:24,disrtc:{provinc:'GD',city:'gz',zone:'lz'}
console.log(res)  // name:'aiden',age:24,disrtc:{provinc:'GD',city:'gz',zone:'lz'}
```

分析1.5版本的extend实现..

```js
jQuery.extend = jQuery.fn.extend = function() {
	 var options, name, src, copy, copyIsArray, clone,
		target = arguments[0] || {},
		i = 1,
		length = arguments.length,
		deep = false;

	// Handle a deep copy situation 如果传经来的第一个参数为boolean ,
	if ( typeof target === "boolean" ) {
		deep = target;
		target = arguments[1] || {};
		// skip the boolean and the target
		i = 2;
	}

	// Handle case when target is a string or something (possible in deep copy)
	if ( typeof target !== "object" && !jQuery.isFunction(target) ) {
		target = {};
	}
    // 如果只传如一个就把target设为jquery对象
	// extend jQuery itself if only one argument is passed---
	if ( length === i ) {
		target = this;
		--i; //重置i=0
	}

	for ( ; i < length; i++ ) {
		// Only deal with non-null/undefined values 不处理值为传经来null和undefined
		if ( (options = arguments[ i ]) != null ) {
			// Extend the base object
			for ( name in options ) {
				src = target[ name ];
				copy = options[ name ];

				// Prevent never-ending loop 如果 ???
				if ( target === copy ) {
					continue;
				}
                 // 合并对象，或者数组
				// Recurse if we're merging plain objects or arrays
				if ( deep && copy && ( jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)) ) ) {
                    // 如果要复制的属性的值是一个数组..
					if ( copyIsArray ) {
						copyIsArray = false;
						clone = src && jQuery.isArray(src) ? src : [];

					} else {
						clone = src && jQuery.isPlainObject(src) ? src : {};
					}

					// Never move original objects, clone them --递归...
					target[ name ] = jQuery.extend( deep, clone, copy );

				// Don't bring in undefined values --不处理带 undefined的值
				} else if ( copy !== undefined ) {
					target[ name ] = copy;
				}
			}
		}
	}

	// Return the modified object  返回一个改变的对象
	return target;
};
```

扩展 Object.assign()

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign