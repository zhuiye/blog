---
title: jQuery Source study (1)
date: 2019-07-07 23:10:19
tags: jQuery
---

## 基本的构造

```js
// 一个jQuery 函数
function jQuery(a,c) {
    
}
var $ = jQuery;  //---起别名

//原型放在jQuery.fn属性中--为什么这么写呢?为了方便引用吧，至少我现在是这样看的
jQuery.fn = jQuery.prototype = {}

/* 
 extends 方法放在jQuery函数对象的extend 以及原型对象的extends属性中
 为什么这么写呢？通过jQuery.extend()这样扩展方法，会在jQuery属性上加，相当于JQUERY类的静态方法
 原型调用的话，就是jQuery实例化的对象的方法
 目前1.0.1版本的比较简单，后续复杂很多
 简单分析一个extend 方法，其实也是把传进来的obj 通过遍历其中的属性，添加到"this"中
 eq:jQuery.extend({name:'xxx'})
 jQuery.extend({name:'xxx'},{a:'bbb'})=>return {name:'xxx',a:'bbb'}
*/
jQuery.extend = jQuery.fn.extend = function(obj,prop) {
	if ( !prop ) { prop = obj; obj = this; }
	for ( var i in prop ) obj[i] = prop[i];
	return obj;
};
jQuery.extend({})
```

## $()的执行过程

$(),返回的是一个jQuery对象(类数组) 

 如下:

```
jQuery
{
  0:document(Dom)
  length:1
}
```

接下来，我们来看一下$()的构造函数

```js
function jQuery(a,c) {
    // 目前我们什么都不传------$()
	// Shortcut for document ready (because $(document).each() is silly)
	if ( a && a.constructor == Function && jQuery.fn.ready )
		return jQuery(document).ready(a);
    
	// Make sure that a selection was provided
        //  因为我们什么都不传，jQuery.context也无值 所以a=document
	a = a || jQuery.context || document;
	// Watch for when a jQuery object is passed as the selector
	if ( a.jquery )
		return $( jQuery.merge( a, [] ) );

	// Watch for when a jQuery object is passed at the context
	if ( c && c.jquery )
		return $( c ).find(a);
	
	// If the context is global, return a new object
    // 因为上下文是在全局作用域下调用，所以返回一个新的jQuery对象
	if ( window == this )
		return new jQuery(a,c);

	// Handle HTML strings --处理html字符串
    /*  分析一下这个正则，意思是匹配 除了“<”开头0至多个的一个字符，接下来捕获< 任意多个字符 >
     最后以0个或者多个不是">"的字符结尾
    */
	var m = /^[^<]*(<.+>)[^>]*$/.exec(a);
    // 如果匹配到---
	if ( m ) a = jQuery.clean( [ m[1] ] );
    
    
	// Watch for when an array is passed in
    // $()走的是jQuery.find()-- 返回[document]
    // get 方法是进行生成jquery类数组对象的操作 --此方法在原型上..
   //  如果我们传入一个div  标签选择器 --走find()--返回一个[dom]
	this.get( a.constructor == Array || a.length && !a.nodeType && a[0] != undefined && a[0].nodeType ?
		// Assume that it is an array of DOM Elements
		jQuery.merge( a, [] ) :

		// Find the matching elements and save them for later
		jQuery.find( a, c ) );

  // See if an extra function was provided
	var fn = arguments[ arguments.length - 1 ];
	
	// If so, execute it in context
	if ( fn && fn.constructor == Function )
		this.each(fn);
}
```

## jQuery.prototype.get()方法

```js

get: function( num ) {
       //如果传入一个数组 ...
		// Watch for when an array (of elements) is passed in
		if ( num && num.constructor == Array ) {

			// Use a tricky hack to make the jQuery object
			// look and feel like an array
			this.length = 0;
            // 此方法调用会给当前的对象，生成类数组对象
            //跟Array.prototype.push.apply(this,num)--一样
			[].push.apply( this, num );
			// 返回jquery对象，链式调用
			return this;
		} else
            //如果什么都没传,返回jquery对象中保存的 [dom] 数组
            //传入数字，返回this[num] --也就是dom对象
			return num == undefined ?

				// Return a 'clean' array
				jQuery.map( this, function(a){ return a } ) :

				// Return just the object
				this[num];
	},
/*
   从jquery对象调用get()方法，也就转成了dom对象或者对象数组...
*/
```



## jQuery.find()方法

此方法实现的是通过解析css类字符串，返回DOM数组,

如果$() 走的是find() 返回的是 [document]

```js
	find: function( t, context ) {
		// Make sure that the context is a DOM Element
		if ( context && context.nodeType == undefined )
			context = null;
	
		// Set the correct context (if none is provided)
		context = context || jQuery.context || document;
	
		if ( t.constructor != String ) return [t];
	    // 这个是jQuery 自定义的选择符
		if ( !t.indexOf("//") ) {
			context = context.documentElement;
			t = t.substr(2,t.length);
		} else if ( !t.indexOf("/") ) {
			context = context.documentElement;
			t = t.substr(1,t.length);
			// FIX Assume the root element is right :(
			if ( t.indexOf("/") >= 1 )
				t = t.substr(t.indexOf("/"),t.length);
		}
	  // 没有传上下文的化，默认就是document
		var ret = [context];
		var done = [];
		var last = null;
	
		while ( t.length > 0 && last != t ) {
			var r = []; // 
			last = t; // div
	
			t = jQuery.trim(t).replace( /^\/\//i, "" ); //去除空格 替换以// 开头
			
			var foundToken = false; //是否找到的标识
			/*
			//正则的匹配模式字符串数组 
			token: [
                        "\\.\\.|/\\.\\.", 
                        "a.parentNode",
                        ">|/", 
                        "jQuery.sibling(a.firstChild)",
                        "\\+", 
                        "jQuery.sibling(a).next",
                        "~",
                        function(a){
                            var r = [];
                            var s = jQuery.sibling(a);
                            if ( s.n > 0 )
                                for ( var i = s.n; i < s.length; i++ )
                                    r.push( s[i] );
                            return r;
				        }
				],
			*/
			for ( var i = 0; i < jQuery.token.length; i += 2 ) {
				if ( foundToken ) continue;

				var re = new RegExp("^(" + jQuery.token[i] + ")");
				var m = re.exec(t);
				
				if ( m ) {
					r = ret = jQuery.map( ret, jQuery.token[i+1] );
					t = jQuery.trim( t.replace( re, "" ) );
					foundToken = true;
				}
			}
			// 如果没有找到
			if ( !foundToken ) {
				if ( !t.indexOf(",") || !t.indexOf("|") ) {
					if ( ret[0] == context ) ret.shift();
					done = jQuery.merge( done, ret );
					r = ret = [context];
					t = " " + t.substr(1,t.length);
				} else {
                    // 分析正则 捕获以# . 0 个或者开头的字符到 捕获接下人的任意字符
					var re2 = /^([#.]?)([a-z0-9\\*_-]*)/i;
					var m = re2.exec(t);
		            //如果传进来的是一个 #id 选择器
					if ( m[1] == "#" ) {
						// Ummm, should make this work in all XML docs
						var oid = document.getElementById(m[2]);
                        // 如果没有[]
						r = ret = oid ? [oid] : [];
                        // qingchu???
						t = t.replace( re2, "" );
					} else {
                        
						if ( !m[2] || m[1] == "." ) m[2] = "*";
		                // console.log(m[2],r) div []

						for ( var i = 0; i < ret.length; i++ )
							r = jQuery.merge( r,
								m[2] == "*" ?
									jQuery.getAll(ret[i]) :
									ret[i].getElementsByTagName(m[2])
							);
                        // ----r=[dom]
					}
				}
	
			}

			if ( t ) {
				var val = jQuery.filter(t,r); // return { r: r, t: t };
				ret = r = val.r;
				t = jQuery.trim(val.t); // 除去空格
			}
		}
	
		if ( ret && ret[0] == context ) ret.shift();
		done = jQuery.merge( done, ret );
	
		return done;
	},
```

## jQuery.filter()

```js
filter: function(t,r,not) {
		// Figure out if we're doing regular, or inverse, filtering
		var g = not !== false ? jQuery.grep :
			function(a,f) {return jQuery.grep(a,f,true);};
		
		while ( t && /^[a-z[({<*:.#]/i.test(t) ) {

			var p = jQuery.parse;

			for ( var i = 0; i < p.length; i++ ) {
				var re = new RegExp( "^" + p[i][0]

					// Look for a string-like sequence
					.replace( 'S', "([a-z*_-][a-z0-9_-]*)" )

					// Look for something (optionally) enclosed with quotes
					.replace( 'Q', " *'?\"?([^'\"]*?)'?\"? *" ), "i" );

				var m = re.exec( t );

				if ( m ) {
					// Re-organize the match
					if ( p[i][1] )
						m = ["", m[1], m[3], m[2], m[4]];

					// Remove what we just matched
					t = t.replace( re, "" );

					break;
				}
			}
	
			// :not() is a special case that can be optomized by
			// keeping it out of the expression list
			if ( m[1] == ":" && m[2] == "not" )
				r = jQuery.filter(m[3],r,false).r;
			
			// Otherwise, find the expression to execute
			else {
				var f = jQuery.expr[m[1]];
				if ( f.constructor != String )
					f = jQuery.expr[m[1]][m[2]];
					
				// Build a custom macro to enclose it
				eval("f = function(a,i){" + 
					( m[1] == "@" ? "z=jQuery.attr(a,m[3]);" : "" ) + 
					"return " + f + "}");
				
				// Execute it against the current filter
				r = g( r, f );
			}
		}
	
		// Return an array of filtered elements (r)
		// and the modified expression string (t)
		return { r: r, t: t };
	},
```

