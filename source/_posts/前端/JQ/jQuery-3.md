---
title: jQuery-3
date: 2019-07-11 23:56:35
tags: jQuery
---
## $.ajax

早期的ajax封装比较简单，后期越来越复杂,

先来看一个立即执行函数,这个的目的是绑定一些Ajax事件

```js

new function(){
	var e = "ajaxStart,ajaxStop,ajaxComplete,ajaxError,ajaxSuccess".split(',');
	for ( var i = 0; i < e.length; i++ ) new function(){
		var o = e[i];
		jQuery.fn[o] = function(f){
			return this.bind(o, f);
		};
	};
};
```

```js
ajax: function( type, url, data, ret, ifModified ) {
		// If only a single argument was passed in,
		// assume that it is a object of key/value pairs
    // 如果没穿url ,相当 ajax({complete:()=>{}})
		if ( !url ) {   
			ret = type.complete;
			var success = type.success;
			var error = type.error;
			data = type.data;
			url = type.url;
			type = type.type;
		}
		
         // 触发 ajaxStart 事件  init active =0
		// Watch for a new set of requests
		if ( ! jQuery.active++ )
			jQuery.event.trigger( "ajaxStart" );
        //未完成请求
		var requestDone = false;
	   // 创建xml对象
		// Create the request object
		var xml = new XMLHttpRequest();
	   // 构建请求
		// Open the socket
		xml.open(type || "GET", url, true);
		//设置正确的请求头,如果数据被设置--其实这里的逻辑有问题，如果是get请求，data 存在，也没必要设置了
		// Set the correct header, if data is being sent
		if ( data )
			xml.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
		// 设置是否缓存有，一般针对get 请求
		// Set the If-Modified-Since header, if ifModified mode.
		if ( ifModified )
			xml.setRequestHeader("If-Modified-Since",
				jQuery.lastModified[url] || "Thu, 01 Jan 1970 00:00:00 GMT" );
		
		// Set header so calling script knows that it's an XMLHttpRequest
		xml.setRequestHeader("X-Requested-With", "XMLHttpRequest");
	
		// Make sure the browser sends the right content length
		if ( xml.overrideMimeType )
			xml.setRequestHeader("Connection", "close");
		
    //一个监听器
		// Wait for a response to come back
		var onreadystatechange = function(istimeout){
            //  如果请求完成或者 超时
			// The transfer is complete and the data is available, or the request timed out
			if ( xml && (xml.readyState == 4 || istimeout == "timeout") ) {
                // 设置请求完成
				requestDone = true;
               //   判断状态 时超时，还是未改变，还是成功, 还是error
				var status = jQuery.httpSuccess( xml ) && istimeout != "timeout" ?
					ifModified && jQuery.httpNotModified( xml, url ) ? "notmodified" : "success" : "error";
				
				// Make sure that the request was successful or notmodified
				if ( status != "error" ) {
                     
					// Cache Last-Modified header, if ifModified mode.
					var modRes = xml.getResponseHeader("Last-Modified");
                    // 如果设置缓存和响应有相关的头，则设置起来
					if ( ifModified && modRes ) jQuery.lastModified[url] = modRes;
					
					// If a local callback was specified, fire it
					if ( success ) success( xml, status );
					
					// Fire the global callback --触发ajax成功事件
					jQuery.event.trigger( "ajaxSuccess" );
				
				// Otherwise, the request was not successful
				} else {
					// If a local callback was specified, fire it
					if ( error ) error( xml, status );
					
					// Fire the global callback
					jQuery.event.trigger( "ajaxError" );
				}
				// 不管成功还是失败，都触发 AjaxComplete
				// The request was completed
				jQuery.event.trigger( "ajaxComplete" );
				
				// Handle the global AJAX counter
                // 对这次请求重置一些状态.....
				if ( ! --jQuery.active )
                    // 触发ajax 停止事件
					jQuery.event.trigger( "ajaxStop" );
	
				// Process result ---ret:compelete 监听函数
				if ( ret ) ret(xml, status);
				
				// Stop memory leaks
				xml.onreadystatechange = function(){};
				xml = null;
				
			}
		};
		xml.onreadystatechange = onreadystatechange;
		
		// Timeout checker --检查是否超时
		if(jQuery.timeout > 0)  // ajaxTimeout() 设置超时时间，默认timeout为0
			setTimeout(function(){
				// Check to see if the request is still happening
				if (xml) {
					// Cancel the request --取消请求
					xml.abort();

					if ( !requestDone ) onreadystatechange( "timeout" );

					// Clear from memory
					xml = null;
				}
			}, jQuery.timeout);
		
		// Send the data
		xml.send(data);
	},
```

## parse --序列化参数

```js
param: function(a) {
		var s = [];
		//如果传入这样的格式[{name:'xxx',value:'hengcheng'},{name:'age',value:'16'}]
		// If an array was passed in, assume that it is an array
		// of form elements
		if ( a.constructor == Array ) {
			// Serialize the form elements
			for ( var i = 0; i < a.length; i++ )
				s.push( a[i].name + "=" + encodeURIComponent( a[i].value ) );
			
		// Otherwise, assume that it's an object of key/value pairs {age:16,xxx:'hengcheng'}
		} else {
			// Serialize the key/values
			for ( var j in a )
				s.push( j + "=" + encodeURIComponent( a[j] ) );
		}
		
		// Return the resulting serialization ---['xxx=hengcheng']
		return s.join("&");'xxx=hengcheng&age=16'
	}
```

## $.get() 

```js
get: function( url, data, callback, type, ifModified ) {
   //  type==='json'| script|'xml'
		if ( data.constructor == Function ) {
			type = callback;
			callback = data;
			data = null;
		}
		
		if ( data ) url += "?" + jQuery.param(data);
		
		// Build and start the HTTP Request
		jQuery.ajax( "GET", url, null, function(r, status) {
			if ( callback ) callback( jQuery.httpData(r,type), status );
		}, ifModified);
	},
```

