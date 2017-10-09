# Promise概述

> 先看代码

	(new Promise(step1))
    .then(step2)
	.then(step3)
	.then(step4);
	这就是promise的写法，不是写多个回调函数的方法。

> promise对象的三种状态

- 异步操作“未完成”（pending）
- 异步操作“已完成”（resolved，又称fulfilled）
- 异步操作“失败”（rejected）

> 这三种的状态的变化途径只有两种。

- 异步操作从“未完成”到“已完成”
- 异步操作从“未完成”到“失败”。

> 这种变化只能发生一次，一旦当前状态变为“已完成”或“失败”，就意味着不会再有新的状态变化了。因此，Promise对象的最终结果只有两种。

- 异步操作成功，Promise对象传回一个值，状态变为resolved。
- 异步操作失败，Promise对象抛出一个错误，状态变为rejected。

> Promise对象使用then方法添加回调函数。then方法可以接受两个回调函数，第一个是异步操作成功时（变为resolved状态）时的回调函数，第二个是异步操作失败（变为rejected）时的回调函数（可以省略）。一旦状态改变，就调用相应的回调函数。

	po.then(
	  console.log,
	  console.error
	);

> 图片加载的案例

  	var preloadImage = function (path) {
  	return new Promise(function(resolve, reject) {
    var image = newImage();
    image.onload  = resolve;
    image.onerror= reject;
    image.src = path;
  	});
	};

> resolve 和 reject

	function helloWorld (ready) {
    return new Promise(function (resolve, reject) {
        if (ready) {
            resolve("Hello World!");
        } else {
            reject("Good bye!");
        }
    });
    }

	helloWorld(true).then(function (message) {
	    alert(message);
	}, function (error) {
	    alert(error);
	});

> hello world案例

	function printHello (ready) {
	    return new Promise(function (resolve, reject) {
	        if (ready) {
	            resolve("Hello");
	        } else {
	            reject("Good bye!");
	        }
	    });
	}
	
	function printWorld () {
	    alert("World");
	}
	
	function printExclamation () {
	    alert("!");
	}
	
	printHello(true)
	    .then(function(message){
	        alert(message);
	    })
	    .then(printWorld)
	    .then(printExclamation);

	可以给then下面的函数传参数

	function printHello (ready) {
	    return new Promise(function (resolve, reject) {
	        if (ready) {
	            resolve("Hello");
	        } else {
	            reject("Good bye!");
	        }
	    });
	}
	
	printHello(true).then(function (message) {
	    return message;
	}).then(function (message) {
	    return message  + ' World';
	}).then(function (message) {
	    return message + '!';
	}).then(function (message) {
	    alert(message);
	});
	//结果就是hello world !

### Promise.all 和 Promise.race

> Promise.all 可以接收一个元素为 Promise 对象的数组作为参数，当这个数组里面所有的 Promise 对象都变为 resolve 时，该方法才会返回。

	var p1 = new Promise(function (resolve) {
    setTimeout(function () {
        resolve("Hello");
    }, 3000);
	});
	
	var p2 = new Promise(function (resolve) {
	    setTimeout(function () {
	        resolve("World");
	    }, 1000);
	});
	
	Promise.all([p1, p2]).then(function (result) {
	    console.log(result); 
		// ["Hello", "World"]
	});
	
> 上面的例子模拟了传输两个数据需要不同的时长，虽然 p2 的速度比 p1 要快，但是 Promise.all 方法会按照数组里面的顺序将结果返回。

> 日常开发中经常会遇到这样的需求，在不同的接口请求数据然后拼合成自己所需的数据，通常这些接口之间没有关联（例如不需要前一个接口的数据作为后一个接口的参数），这个时候 Promise.all 方法就可以派上用场了。
	
> 还有一个和 Promise.all 相类似的方法 Promise.race，它同样接收一个数组，不同的是只要该数组中的 Promise 对象的状态发生变化（无论是 resolve 还是 reject）该方法都会返回。