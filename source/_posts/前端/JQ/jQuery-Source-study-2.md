---
title: jQuery-Source-study-2
date: 2019-07-08 22:53:21
tags: jQuery
---
## jQuery(fn)的执行过程

```js
/* 对于这样的代码，我们应该是很熟悉，意思是什么呢？
  在DOM完成加载的时候，执行function(){}函数,
  平常可见window.onload window.addEventListener('load') 都是等
  页面资源（图片，样式）加载完成时，才进行触发，
  而DOMContentLoaded事件只要Dom完成装载就进行触发
*/
$(function(){

})
```

  假设有如下代码:

```js
$(function(){console.log(1)})
window.onload=function(){
    console.log(2)
}
window.addEventListener('load',()=>{console.log(3)})
/*
  执行的顺序为 1 ->3-> 2
*/
```

接下来，看看$(Fn)的具体实现过程,来看代码

```js
function jQuery(a,c) {
    // 目前我们什么都不传------$()
	// Shortcut for document ready (because $(document).each() is silly)
        /*
           从这个代码可知,当我们传入一个函数的时候,调用jQuery(document)->生成的是一个
           {0:document,length:1}的jQuery对象，然后调用对象上的ready方法
        */
	if ( a && a.constructor == Function && jQuery.fn.ready )
		return jQuery(document).ready(a);
    
    /** ....省略一些代码...**/
}
```

## jQuery.fn.ready()

```js
ready: function(f) {
		// If the DOM is already ready --如果已经加载了，直接调用传经来的函数
		if ( jQuery.isReady )
			// Execute the function immediately 
            // 这里面也可以看出$(function(){console.log(this)}) this===document
			f.apply( document ); 
			
		// Otherwise, remember the function for later
		else {
             // 如果就加入队列.---这个很重要，因为执行DOMContenLoaded的时候调用
            // jQuery.ready()里面执行readyList的遍历
			// Add the function to the wait list
			jQuery.readyList.push( f );
		}
		return this;
	}
```

立即执行函数监听 DOMContentLoaded

```js
new function(){
  	// If Mozilla is used  如果时mozilla 或者 opera浏览器就 ...
	if ( jQuery.browser.mozilla || jQuery.browser.opera ) {
		// Use the handy event callback
		document.addEventListener( "DOMContentLoaded", jQuery.ready, false );
	
	// If IE is used, use the excellent hack by Matthias Miller
	// http://www.outofhanwell.com/blog/index.php?title=the_window_onload_problem_revisited
	} else if ( jQuery.browser.msie ) {
	/*
	  下面简单解析下代码，是为了hack当时的ie,创建一个script元素，并利用其
	  onreadystatechange事件的时机来调用传进来的函数
	
	*/
		// Only works if you document.write() it
		document.write("<scr" + "ipt id=__ie_init defer=true " + 
			"src=//:><\/script>");
	
		// Use the defer script hack
		var script = document.getElementById("__ie_init");
		script.onreadystatechange = function() {
			if ( this.readyState != "complete" ) return;
			this.parentNode.removeChild( this );
            //调用函数对象商得jQuery.ready()方法
			jQuery.ready();
		};
	
		// Clear from memory
		script = null;
	
	// If Safari  is used
	} else if ( jQuery.browser.safari ) {
		// Continually check to see if the document.readyState is valid
        //如果是safari的化，只能是不断的调用SetInterval 判断document.readyState是否complete 
		jQuery.safariTimer = setInterval(function(){
			// loaded and complete are both valid states
			if ( document.readyState == "loaded" || 
				document.readyState == "complete" ) {
	
				// If either one are found, remove the timer
				clearInterval( jQuery.safariTimer );
				jQuery.safariTimer = null;
	
				// and execute any waiting functions
				jQuery.ready();
			}
		}, 10);
	} 
}
```

## jQuery.ready

```js
jQuery.extend({
	/*
	 * All the code that makes DOM Ready work nicely.
	 */
	isReady: false,
	readyList: [],
	
	// Handle when the DOM is ready
	ready: function() {
		// Make sure that the DOM is not already loaded
		if ( !jQuery.isReady ) {  //设置状态
			// Remember that the DOM is ready
			jQuery.isReady = true;
			
			// If there are functions bound, to execute
			if ( jQuery.readyList ) {
				// Execute all of them
				for ( var i = 0; i < jQuery.readyList.length; i++ )
                    //在document的作用域下调用传进来的函数
					jQuery.readyList[i].apply( document );
				
				// Reset the list of functions --重置readyList
				jQuery.readyList = null;
			}
		}
	}
});
```

