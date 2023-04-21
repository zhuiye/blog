---
title: jQuery.deferred
date: 2019-07-21 20:57:13
tags: jQuery
---
## jQuery.deferred()

### 由来

由于javascript中的存在着异步特性的调用，写异步代码的时候，容易造成回调函数地狱，使代码不雅观，于是便出现了一写解决方案，去解决这个嵌套地狱，如deferred,Promise方案...

### 常见的回调地狱

```js
$.ajax({url:'test.php',success:function(res){
    $.ajax({
        url:'test.php',
        data:res.data,
        success:function(res1){
            /*
                ......
                ...
            */
        }
    })
},error:function(){
    
}})
```

### deferred()

```js
$.ajax({url:'test.php'}).done(function(res){
    return $.ajax({url:'test1.php',data:res.data}) 
}).done(function(res1){
    
})
```

### 实现简单的deferred

1.Deferred 有done方法，有resovle 决定函数异步是否成功了的状态

```js
        const Deferred = function () {
            let callbackList = [];
            let failcallbackList = [];

            let deferred = {
                done: function (fn) {
                    // 简单的假设传进来的是fn--参数是函数
                    callbackList.push(fn)
                    return this;

                },
                resolve: function (data) {
                    for (let fn of callbackList) {
                        fn.call(this, data)
                    }
                    callbackList=[]
                    return this;
                },
                fail: function (fn) {
                    failcallbackList.push(fn)
                    return this;
                },
                reject: function (data) {
                    for (let fn of failcallbackList) {
                        fn.call(this, data)
                        
                    }
                    failcallbackList=[]
                    return this;
                }
            }
            return deferred;
        }

        let testFn = function () {
            const  deferred=Deferred();
            setTimeout(function () {
               // deferred.resolve('test data')
               deferred.reject(' occur error ')
            }, 2000)
            return deferred
        }
        testFn().done(function(data){
           console.log(data)
        }).fail(function(error){
           console.log(error)
        })
```

简单的实现大致如上，下面我们来看看jQuery.deferred的具体实现，源码参照1.5版本

```js
	// Create a simple deferred (one callbacks list)
   // 创建一个简单的deferred对象
	_Deferred: function() {
		var // callbacks list ---回调函数列表
			callbacks = [],
              // 储存上下文和参数
			// stored [ context , args ] 
			fired,
            // .
			// to avoid firing when already doing so
			firing,
			// flag to know if the deferred has been cancelled
			cancelled,
			// the deferred itself
			deferred  = {

				// done( f1, f2, ...)
				done: function() {
					if ( !cancelled ) {
						var args = arguments,
							i,
							length,
							elem,
							type,
							_fired;
						if ( fired ) {
							_fired = fired; //如果已经有了上下文信息 ，存储，
							fired = 0;// 重置
						}
						for ( i = 0, length = args.length; i < length; i++ ) {
							elem = args[ i ];
							type = jQuery.type( elem );
                            // 把函数加入私有的变量的列表中..
							if ( type === "array" ) {
                                  // 如果是传经来的是数组，如{[fn1,fn2]}--递归调用
								deferred.done.apply( deferred, elem );
							} else if ( type === "function" ) {
								callbacks.push( elem );
							}
						}
                        // 如果_fired有..触发
						if ( _fired ) {
							deferred.resolveWith( _fired[ 0 ], _fired[ 1 ] );
						}
					}
                    // 返回this ,回调用的
					return this;
				},

				// resolve with given context and args
				resolveWith: function( context, args ) {
					if ( !cancelled && !fired && !firing ) {
						firing = 1;
						try {
                            // 利用队列，顺序调用每一个函数
							while( callbacks[ 0 ] ) {
								callbacks.shift().apply( context, args );
							}
						}
						finally {
							fired = [ context, args ];
							firing = 0;
						}
					}
					return this;
				},

				// resolve with this as context and given arguments
				resolve: function() {
					deferred.resolveWith( jQuery.isFunction( this.promise ) ? this.promise() : this, arguments );
					return this;
				},

				// Has this deferred been resolved? 判断一个deffer 对象的状态
				isResolved: function() {
					return !!( firing || fired );
				},

				// Cancel
				cancel: function() {
					cancelled = 1;
					callbacks = [];
					return this;
				}
			};

		return deferred;
	},
        
    // Full fledged deferred (two callbacks list)
	Deferred: function( func ) {
		var deferred = jQuery._Deferred(),
			failDeferred = jQuery._Deferred(),
			promise;
		// Add errorDeferred methods, then and promise
        // 往deferred对象中添加errorDeferred方法和promise
		jQuery.extend( deferred, {
			then: function( doneCallbacks, failCallbacks ) {
				deferred.done( doneCallbacks ).fail( failCallbacks );
				return this;
			},
			fail: failDeferred.done,
			rejectWith: failDeferred.resolveWith,
			reject: failDeferred.resolve,
			isRejected: failDeferred.isResolved,
			// Get a promise for this deferred
			// If obj is provided, the promise aspect is added to the object
            // 返回一个deferred对象
            // 如果传入一个对象，会把deferred对象的所有属性赋予给这个对象
			promise: function( obj , i /* internal */ ) {
				if ( obj == null ) {
					if ( promise ) {
						return promise;
					}
					promise = obj = {};
				}
				i = promiseMethods.length;
				while( i-- ) {
					obj[ promiseMethods[ i ] ] = deferred[ promiseMethods[ i ] ];
				}
				return obj;
			}
		} );
		// Make sure only one callback list will be used
		deferred.then( failDeferred.cancel, deferred.cancel );
		// Unexpose cancel
		delete deferred.cancel;
		// Call given func if any
		if ( func ) {
			func.call( deferred, deferred );
		}
		return deferred;
	},

	// Deferred helper 这个方法相当于Promise.all([])
	when: function( object ) {
		var args = arguments,
			length = args.length,
			deferred = length <= 1 && object && jQuery.isFunction( object.promise ) ?
				object :
				jQuery.Deferred(),
			promise = deferred.promise(),
			resolveArray;

		if ( length > 1 ) {
			resolveArray = new Array( length );
			jQuery.each( args, function( index, element ) {
				jQuery.when( element ).then( function( value ) {
					resolveArray[ index ] = arguments.length > 1 ? slice.call( arguments, 0 ) : value;
					if( ! --length ) {
						deferred.resolveWith( promise, resolveArray );
					}
				}, deferred.reject );
			} );
		} else if ( deferred !== object ) {
			deferred.resolve( object );
		}
		return promise;
	},
     
        
```

跟promise 比起来，功能还没那么强大  To be continue.....