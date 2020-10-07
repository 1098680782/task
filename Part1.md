### 一、谈谈你是如何理解JS异步编程的，EventLoop、消息队列都是做什么的，什么是宏任 务，什么是微任务?

答：js异步编程是提高js单线程执行机制下的处理效率，针对一些耗时操作。

EventLoop事件循环不断监听调用栈和消息队列，当调用栈为空时，事件循环会从消息队列中取出第一个回调函数压入调用栈。

消息队列先进先出队列，里面存放各种消息。消息队列里存放的东西可以理解为我们注册的回调函数。

宏任务就是消息队列的任务，常见的接口请求  定时器等异步任务都是宏任务。

微任务基于当前任务而随当前任务结束后立即执行的任务

### 一、将下面6异步代码使用Promise的方式改进 

```
  setTimeout(function () {
          var a = 'hello'
          setTimeout(() => {
              var b = 'lagou'
              setTimeout(() => {
                  var c = 'I ♥ U'
                  console.log(a + b + c);
              }, 10)
          }, 10)
      }, 10)
 答： 
  		function p1() {
            return new Promise(function (resolve,reject) {
                setTimeout(() => {
                    resolve('hello')
                }, 10);
            })
        }
        function p2() {
            return new Promise(function (resolve,reject) {
                setTimeout(() => {
                    resolve('lagou')
                }, 10);
            })
        }
        function p3() {
            return new Promise(function (resolve,reject) {
                setTimeout(() => {
                    resolve('I ♥ U')
                }, 10);
            })
        }
        Promise.all([p1(),p2(),p3()]).then(function(res){
            console.log(res)
        })
```

### 二、基于以下代码完成下面的四个练习 

```
            const fp = require('lodash/fp')
            // 数据  // horsepower 马力，dollar_value 价格，in_stock 库存  
            const cars = [
                {
                    name: 'Ferrari FF',
                    horsepower: 660,
                    dollar_value: 700000,
                    in_stock: true
                }, {
                    name: 'Spyker C12 Zagato',
                    horsepower: 650,
                    dollar_value: 648000,
                    in_stock: false
                },
                {
                    name: 'Jaguar XKR‐S',
                    horsepower: 550, dollar_value: 132000,
                    in_stock: false
                },
                {
                    name: 'Audi R8',
                    horsepower: 525,
                    dollar_value: 114200,
                    in_stock: false
                }, {
                    name: 'Aston Martin One77',
                    horsepower: 750,
                    dollar_value: 1850000,
                    in_stock: true
                }, {
                    name: 'Pagani Huayra',
                    horsepower: 700,
                    dollar_value: 1300000,
                    in_stock: false
                }]
                
                
     
// 练习1：使用函数组合fp.flowRight()重新实现下面这个函数 
                let isLastInStock = function (cars) {
                    //获取最后一条数据
                    let last_car = fp.last(cars)
                    //获取最后一条数据的 in_stock 属性值
                    return fp.prop('in_stock', last_car)
                };
            // console.log(isLastInStock(cars))
   答：
		let  func = fp.flowRight(fp.prop('in_stock'),fp.last);
		console.log(func(cars));
		
		
// 练习2：使用fp.flowRight()、fp.prop()和fp.first()获取第一个car的name
	答：
	let  func2 = fp.flowRight( fp.prop("name"),fp.first);
	console.log(func2(cars));
	
	
	
// 练习3：使用帮助函数_average重构averageDollarValue，使用函数组合的方式实现 
	let _average = function (xs) {
    	return fp.reduce(fp.add, 0, xs) / xs.length
	}
	// <‐ 无须改动 
	let averageDollarValue = function (cars) {
   	 	let dollar_values = fp.map(function (car) {
        	return car.dollar_value
    	}, cars)
    	return _average(dollar_values)
	}
	// console.log(averageDollarValue(cars))
答：
	let func3 = fp.flowRight(_average,fp.map("dollar_value"));
	console.log(func3(cars));
	
	
	
//练习4：使用flowRight写一个sanitizeNames()函数，返回一个下划线连接的小写字符串， 把数组中name转换成这种 	//形式：例如： sanitizeNames(["Hello World"]) => ["hello_world"]
答：  （这道题有点懵 ， 我下面为什么用的fp.map("name",cars)  这是个数组为啥传进去和预期结果不一样，第一个为啥可以）

	let sanitizeNames = fp.flowRight(fp.join("_"),fp.split(" "))；
console.log(sanitizeNames(['Ferrari FF',"Spyker C12 Zagato","Jaguar XKR‐S","Audi R8"])) //这个可以
console.log(sanitizeNames(fp.map("name",cars))) //这个不行？不懂，我看到打印的是一个数组的形式啊....

```

### 三、基于以下提供的代码，完成后续的四个练习

```
support.js文件
class Container {
    static of(value) {
        return new Container(value)
    }
    constructor(value) {
        this._value = value;
    }
    map(fn) {
        return Container.of(fn(this._value))
    }
}
class Maybe {
    static of(x) {
        return new Maybe(x)
    }
    isNothing() {
        return this._value === null || this._value === undefined
    }
    constructor(x) {
        this._value = x;
    }
    map(fn) {
        return this.isNothing() ? this : Maybe.of(fn(this._value))
    }
}
module.exports = { Maybe, Container }



app.js文件
	const fp = require('lodash/fp')
	const { Maybe, Container } = require('./support')
// 练习1：使用fp.add(x,y) 和 fp.map(f,x) 创建一个能让functor里的值增加的函数 ex1
        let maybe = Maybe.of([5, 6, 1]);
        let ex1 = () => {
              //你需要实现的函数... 
            let re1 = fp.map(x => x + 1 , maybe._value);
            console.log(re1)
            //fp.add(x,y) 这个看了下文档是求两个值之和，在这道题中不知道咋用。
        }
        ex1();
        
 //练习2：实现一个函数ex2，能够使用 fp.first 获取列表的第一个元素
		let xs = Container.of(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
		let ex2 = () => {
             //你需要实现的函数... 
            let re2 = fp.first(xs._value)
            console.log(re2)
        }
        ex2();
        
  // 练习3：实现一个函数ex3，使用 safeProp 和 fp.first 找到user的名字的首字母
        let safeProp = fp.curry(function (x, o) {
            return Maybe.of(o[x])
        })
		let user = { id: 2, name: 'Albert' }	
		let ex3 = () => {
            //你需要实现的函数... 
            let re3 = fp.first(safeProp("name",user)._value)
            console.log(re3)
    	}
    	ex3();
 // 练习4：使用Maybe重写ex4，不要有 if 语句
      let ex4 = function (n) {
           if (n) {
               return parseInt(n)
           }
       }
	
	let ex4 = function (n) {
   		 return Maybe.of(parseInt(n));
	}


```



### 四、手写实现 MyPromise 源码 要求：尽可能还原Promise中的每一个API，并通过注释的方式描述思路和原 理。

```
MyPromis.js文件	
        // Promise 是一个类，执行这个类的时候，需要传递一个执行器，执行器会立即执行
        const PENDING = "pending";       //等待
        const FULFILLED = "fulfilled";   //成功
        const REJECTED = "rejected";     //失败
        class MyPromise {
            constructor(executor) {
                executor(this.resolve, this.reject)
            }
            // Promise的状态
            status = PENDING;

            //resolve，reject 用来更改状态
            resolve = (value) =>{
                //判断状态 是否是等待 不是阻止程序执行
                if(this.status !== PENDING){
                    return;
                }
                this.status = FULFILLED; // 将状态改为成功
                this.value = value; // 成功之后的值
                this.successCllback && this.successCllback(this.value)
            }
            reject = (reason) =>{
                 //判断状态 是否是等待 不是阻止程序执行
                 if(this.status !== PENDING){
                    return;
                }
                this.status = REJECTED;// 将状态改为失败
                this.reason = reason;// 成功之后的值
            }
            then(successCllback , failCallback){
                if(this.status == FULFILLED){ 
                    successCllback(this.value)
                }else if(this.status == REJECTED){
                    failCallback(this.reason);
                }else{

                    //将成功回调和失败回调存储起来
                    this.successCllback = successCllback;
                    this.failCallback = failCallback;
                }
            }
        }
        module.exports = MyPromise;


promise.js文件
	const MyPromise = require("./myPromise");
	let promise = new MyPromise((resolve,reject)=>{
    	// resolve("200");
    	reject("400")
	});
	promise.then(value => {
    	// console.log(value)
	},reason =>{
    	console.log(reason)
    })	
```

