# 

# 1.js变量和类型

### 1.基本数据类型

 number，string，null，undefined，boolean，Symbol,bigInt(es7，安装存储,操作大整数)

引用数据类型 Objecr 包含function Array  Date

### 2.Symbol类型在实际开发中的应用、可手动实现一个简单的Symbol

ES6引入了一种新的原始数据类型Symbol，表示独一无二的值，由Symbol函数生成

var symbol = new Symbol()

```
var s1 = Symbol('foo');
var s2 = Symbol('bar');

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```

用来定义一个常量 <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529142558070.png" alt="image-20200529142558070" style="zoom:150%;" />

实现symbol

```
(function() {
    var root = this;

    var generateName = (function(){
        var postfix = 0;
        return function(descString){
            postfix++;
            return '@@' + descString + '_' + postfix
        }
    })()

    var SymbolPolyfill = function Symbol(description) {

        if (this instanceof SymbolPolyfill) throw new TypeError('Symbol is not a constructor');

        var descString = description === undefined ? undefined : String(description)

        var symbol = Object.create({
            toString: function() {
                return this.__Name__;
            },
            valueOf: function() {
                return this;
            }
        })

        Object.defineProperties(symbol, {
            '__Description__': {
                value: descString,
                writable: false,
                enumerable: false,
                configurable: false
            },
            '__Name__': {
                value: generateName(descString),
                writable: false,
                enumerable: false,
                configurable: false
            }
        });

        return symbol;
    }

    var forMap = {};

    Object.defineProperties(SymbolPolyfill, {
        'for': {
            value: function(description) {
                var descString = description === undefined ? undefined : String(description)
                return forMap[descString] ? forMap[descString] : forMap[descString] = SymbolPolyfill(descString);
            },
            writable: true,
            enumerable: false,
            configurable: true
        },
        'keyFor': {
            value: function(symbol) {
                for (var key in forMap) {
                    if (forMap[key] === symbol) return key;
                }
            },
            writable: true,
            enumerable: false,
            configurable: true
        }
    });

    root.SymbolPolyfill = SymbolPolyfill;

})()
```

### 3.javaScript中的变量在内存中的具体存储形式

JavaScript中的变量分为基本类型和引用类型 基本类型是保存在栈内存中的简单数据段，它们的值都有固定的大小，保存在栈空间，通过按值访问

引用类型是保存在堆内存中的对象，值大小不固定，栈内存中存放的该对象的访问地址指向堆内存中的对象，JavaScript不允许直接访问堆内存中的位置，因此操作对象时，实际操作对象的引用

![img](https://upload-images.jianshu.io/upload_images/12665637-aa862638a1e26ae5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/648/format/webp)

### 4.null和undefined的区别

**`null`表示没有对象，即该处不应该有值**

1） 作为函数的参数，表示该函数的参数不是对象

2） 作为对象原型链的终点

**`undefined`表示缺少值，即此处应该有值，但没有定义**

1）定义了形参，没有传实参，显示undefined

2）对象属性名不存在时，显示undefined

3）函数没有写返回值，即没有写return，拿到的是undefined

4）写了return，但没有赋值，拿到的是undefined

null和undefined转换成number数据类型



```
null == undefined //true
null === undefined //false
tyepeof null 'object'
typeof undefined 'undefined'
null 默认转成 0
undefined 默认转成 NaN
Number(null) // 0
Number(unfined) // NaN
```

5.可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529145649703.png" alt="image-20200529145649703" style="zoom:150%;" />

### 5.js精度问题

计算机的二进制实现和位数限制有些数无法有限表示。就像一些无理数不能有限表示，如 圆周率 3.1415926...，1.3333... 等。JS 遵循 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_floating_point) 规范，采用双精度存储（double precision），占用 64 bit

```
0.1 >> 0.0001 1001 1001 1001…（1001无限循环）
0.2 >> 0.0011 0011 0011 0011…（0011无限循环）
```

此时只能模仿十进制进行四舍五入了，但是二进制只有 0 和 1 两个，于是变为 0 舍 1 入。这即是计算机中部分浮点数运算时出现误差，丢失精度的根本原因

解决方法 乘以倍数到整数，再还原

​	0.1 + 0.2 

（0.1* 10 + 0.2 * 10）/10   //0.3

### 6.检查js数据类型的方法

1. ​	typeof  只能检测基本数据类型、不能检测引用类型

   Var a = function() {}

   Typeof(a) function 

   Typeof({}) object 

   Typeof(null) object 

   Typeof(undefined) undefined

   2.检查数组or对象

   instance of  A是否是B的实例

   ![image-20200529151902800](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529151902800.png)

   原理：查看对象B的prototype指向的对象是否在对象A的[[prototype]]链上。如果在，则返回true,如果不在则返回false。不过有一个特殊的情况，当对象B的prototype为null将会报错(类似于空指针异常)。

   实现：

   ![image-20200529152031099](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529152031099.png)

![image-20200529152240945](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529152240945.png)

不是很准确的判断数据类型

最好的是方法 Object.prototype.toString().call()

**[注意]Object.prototype.toString()可以识别标准类型及内置对象类型，但不能识别自定义类型**

判断数组的方法几种

1.instanceof 2.Array.isArray() 3.Object.prototype.toString().call() 4.Array.prototype.isPrototypeOf(obj1), 5.arr.construtor == Array

判断对象的方法

Object.prototype.toString.call(obj) === '[object Object]'

obj.constructor === Object

obj instanceof Object 不推荐，数组也是true

7.

# .深浅拷贝

浅拷贝就是指拷贝一个对象，里面嵌套的数组或者对象，修改一个对象的属性，会影响另一个对象或者数组

**仅仅复制对象的引用，而不是对象本身**

深拷贝就是指完全的拷贝一个对象，即使嵌套了对象，两者也相互分离，修改一个对象的属性，也不会影响另一个，都复制一遍

JS 中有个重要的类型叫做引用类型。这种类型在使用的过程中，因为传递的值是引用，所以很容易发生一些副作用，比如

```
let a = {age: 1}
let b = a;
b.age = 2;
//console.log(a.age) //2 
```

一般一层的结构直接用浅拷贝就能解决

```
let b ={...a}
b.age = 2
```

### 1.浅拷贝的几种方法

1.对象

```
let a = {
	age: 1
}
var b = Object.assign({},a)
b.age = 2;
console.log(b)
```

实际项目中Object.assign(target,...source)：可以把多个源对象自身的可枚举的属性拷贝给目标对象，然后返回的是目标对象

如果是一层结构，这种方法就是深拷贝

拓展运算符的方法来实现浅拷贝

```
let b ={...a}
b.age = 2
```

还有Object.create() 方法

```
let sourceObj = {
    str: 'hh1',
    number: 10
}
let targetObj = Object.create(sourceObj)
targetObj.str = 'hh2'
console.log(sourceObj);
```

2.数组的几种浅拷贝方法

运算符

```
 var arr = [1,2,3]
  let arr1 = [...arr]
  arr1.push(4)
  console.log(arr,arr1)
```

concat方法

```
	var arr = [1,2,3]
   let arr1 = arr.concat()
   arr1.push(4)
   console.log(arr,arr1)
```

slice方法

```
const arr1 = [1,{username:'ming',},];
let arr2 = arr1.slice();
arr2[0] = 2;
arr2[1].username = 'meng'
console.log(arr1);
console.log(arr2); 

```

循环方法

```
const arr1 = [1, 2, ['ming', 'abc'], 5];

const shallowClone = (arr) => {
  const dst = [];
  for (let prop in arr) {
    if (arr.hasOwnProperty(prop)) {
        dst[prop] = arr[prop];
    }
  }
  return dst;
}

const arr2 = shallowClone(arr1);
arr2[2].push('wuhan');
arr2[3] = 5;

console.log(arr1);
console.log(arr2);
```

实际项目一般用的是深拷贝，防止数据的混乱

### 2.深拷贝

1.本人项目，数据中没有null和undefined 的情况下 一般使用JSON里面的方法

```
let a = {age：1，obj：{name：‘我是对象’}}
let b = JSON.parse(JSON.stringify(a))
```

缺点：

1）它会抛弃对象的 constructor，深拷贝之后，不管这个对象原来的构造函数是什么，在深拷贝之后都会变成 Object；
2）这种方法能正确处理的对象只有 Number, String, Boolean, Array, 扁平对象，即那些能够被 json 直接表示的数据结构。RegExp 对象是无法通过这种方式深拷贝。
3）只有可以转成 JSON 格式的对象才可以这样用，像 function 没办法转成 JSON。

2.递归遍历数组or对象到基本数据

```
var deepCopy = function(obj) {
    if (typeof obj !== 'object') return;
    var newObj = obj instanceof Array ? [] : {};
    for (var key in obj) {
        if (obj.hasOwnProperty(key)) {
            newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key];
        }
    }
    return newObj;
}
```



# 数组去重

还是分两种情况，都是基本数据类型的数组和带有对象的数组

普通基本类型

## 1.filter方法

​	利用Array.prototype.filter()过滤功能

- Array.prototype.indexOf()返回的是第一个索引值
- 只将数组中元素第一次出现的返回
- 之后出现的将被过滤掉

```
 let arr1= [1,2,6,2,3,7,5,5]
  Array.prototype.unique = function () {
  return this.filter((item, index) => {
    return this.indexOf(item) === index;
  })
}
console.log(arr1.unique())
```

2.indexOf+foreach方法

```
let arr = [1, 2, 3, 22, 233, 22, 2, 233, 'a', 3, 'b', 'a'];
Array.prototype.unique = function () {
  const newArray = [];
  this.forEach(item => {
    if (newArray.indexOf(item) === -1) {
      newArray.push(item);
    }
  });
  return newArray;
}

```

## 3.es6方法 set结构

```
Array.prototype.unique = function () {
  return [...new Set(this)];
} //性能比较好
或者Array.from(new Set(this))
```

## 4.双重for循环

- [ ] ```
  function unique(arr){            
          for(var i=0; i<arr.length; i++){
              for(var j=i+1; j<arr.length; j++){
                  if(arr[i]==arr[j]){         //第一个等同于第二个，splice方法删除第二个
                      arr.splice(j,1);
                      j--;
                  }
              }
          }
  return arr;
  }
  var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
      console.log(unique(arr))
  ```

## 5.利用sort排序方法再遍历比较相玲元素

```js
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return;
    }
    arr = arr.sort()
    var arrry= [arr[0]];
    for (var i = 1; i < arr.length; i++) {
        if (arr[i] !== arr[i-1]) {
            arrry.push(arr[i]);
        }
    }
    return arrry;
}
     var arr = [1,1,'true','true',true,true,15,15,false,false, undefined];
     console.log(unique(arr))
```

## 6.利用es6 数组的includes方法 查找数组是否有某个值

```
function unique(arr) {
    if (!Array.isArray(arr)) {
        console.log('type error!')
        return
    }
    var array =[];
    for(var i = 0; i < arr.length; i++) {
            if( !array.includes( arr[i]) ) {//includes 检测数组是否有某个值
                    array.push(arr[i]);
              }
    }
    return array
}
var arr = [1,1,'true','true',true,true,15,15,false,false];
 console.log(unique(arr))
```



## 7.利用对象的hasOwnProperty方法

```
function unique(arr) {
    var obj = {};
    return arr.filter(function(item, index, arr){
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}
    var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
        console.log(unique(arr))
```



## 8.利用递归的方法

```
function unique(arr) {
        var array= arr;
        var len = array.length;

    array.sort(function(a,b){   //排序后更加方便去重
        return a - b;
    })

    function loop(index){
        if(index >= 1){
            if(array[index] === array[index-1]){
                array.splice(index,1);
            }
            loop(index - 1);    //递归loop，然后数组去重
        }
    }
    loop(len-1);
    return array;
}
 var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
console.log(unique(arr))
```



## 9.利用map结构 不会出现相同的key值



```
function arrayNonRepeatfy(arr) {
  let map = new Map();
  let array = new Array();  // 数组用于返回结果
  for (let i = 0; i < arr.length; i++) {
    if(map .has(arr[i])) {  // 如果有该key值
      map .set(arr[i], true); 
    } else { 
      map .set(arr[i], false);   // 如果没有该key值
      array .push(arr[i]);
    }
  } 
  return array ;
}
 var arr = [1,1,'true','true',true,true,15,15,false,false, undefined,undefined, null,null, NaN, NaN,'NaN', 0, 0, 'a', 'a',{},{}];
    console.log(unique(arr))
```

# 3.闭包

概念： 内部函数使用外部的变量的一种函数

闭包可用于实现隐藏变量和创建函数工厂。

滥用闭包

```

for（var i= 0;i<4i++）{
    setTimeOut(function() {
        console.log(i)
    },1000)
}
```

原因是因为 setTimeout 函数创建了一个可以访问其外部作用域的函数(闭包)，该作用域是包含索引 i 的循环。经过 1 秒后，执行该函数并打印出 i 的值，该值在循环结束时为 4，因为它循环经过0,1,2,3,4并且循环最终停止在 4

修改方法

1.var 改为let let形成块级作用域（**块级作用域声明的变量只会在当前作用域及其以下的作用域可以访问的到**）

2.IIFE

```
 for(var i =0;i<5;i++) {

          setTimeout((function() {

             console.log(i)

         })(),1000)

     }
```

经典面试题

```
function fun(n,o){
    console.log(o);
    return {
        fun:function(m){
            return fun(m,n);
        }
    }
}
var a=fun(0);// undefined，因为第二个参数未定义
a.fun(1); //0
a.fun(2); //0
a.fun(3); //0
var b=fun(0).fun(1).fun(2).fun(3);//undefined 0 1 2,参数[0,] [1,0] [2,1] [3,2]
b.fun(1);//3
var c=fun(0).fun(1);// undefined 0，并且放置[1,0]
c.fun(2);//1,c创建时候传入[1,0],使n=1,（m=2，n=1）,闭包调用o输出1
c.fun(3);//1，解释同上（m=3，n=1）
```

重新调用fun就会生成一个新的闭包

缺点： 变量无法回收，造成内存泄漏

# 4.原型、原型链和继承

每一个函数上都有一个属性prototype 叫做原型，一般函数这个属性没啥作用，但是在构造函数中，能建立和对象之间的关系

了解构造函数

```
function Fun（age） {
    this.age = age
}
var a = new Fun(20) 

```

上面是一个Fun构造，并生成实例化对象 a 

**new 的作用，new 一个构造函数做了哪些东西**

```
function New(Func) {
    var obj = Object.create(Func.protyotype) //创建一个对象，并且将构造函数原型指向该对象
    //2.执行该构造函数并改变this指向传递参数
    var rest =  Func.call(obj)
    retun rest //最后返回对象
}
var Func = function（age） {
    this.age = age 
}//创建一个构造函数

//实例化

var b = new(Func)
```

```

```

**原型相当于一个函数的公共区域，函数可以共享原型的属性和方法**

JS在创建对象的时候，都有一个叫做**proto**的内置属性，用于指向创建它的函数对象的原型对象prototype

```
function Fun(name) {
	this.name = name 
}
var b= new Fun("hh")

```

![image-20200529090457320](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200529090457320.png)

```tsx
 console.log(b.constructor); //function Fun(){}    
```

b是Fun构造出来的，b的construtor就指向创造它的构造函数Fun

在 Javascript 语言中，constructor 属性是专门为 function 而设计的，它存在于每一个 function 的prototype 属性中。这个 constructor 保存了指向 function 的一个引用。

例子

> ```
> function Person() {
>        
>     }
>     var p = new Person()
>     console.log(Person.prototype); // Object{} 
>     console.log(p.prototype); // undifined
>     console.log(p.constructor); //function Person(){}    
>     此处的p是通过 Person函数构造出来的，所以p的constructor属性指向Person
>     console.log(Person.constructor); //function Function(){}
>     之前提过，每个函数其实是通过new Function（）构造的
>     console.log({}.constructor); // function Object(){}
> 	每个对象都是通过new Object（）构造的
>     console.log(Object.constructor); // function Function() {}
>     Object也是一个函数，它是Function（）构造的
>     console.log([].constructor);  //function Array(){}
> ```

总结一下： 函数都是new Function() 创造出来的，对象都是new Object() 的 所有函数或者对象的constructor

就分别指向对应的构造函数

```
console.log(Function instanceof Object); // true
    console.log(Object instanceof Function); // true
```

再来看一个属性 __proto__

一般情况下，指向的是该对象的构造函数的prototype

规则

```
Object.prototype.__proto__ === null   顶层对象是Object
Object.__proto__ === Function.prototype
```

![img](https://user-gold-cdn.xitu.io/2019/2/14/168e9d9b940c4c6f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

继承

1.原型继承

```
Son.prototype = new Father()
```

子类的原型指向父类的实例

优点： 子类可以继承父类原型的属性和方法

缺点：如果父类更改原型的属性和方法，则子类的原型也会发生变化

​			子类实例无法向父类传参数

​				继承单一

2.构造函数继承

# 5.节流和防抖

函数防抖和节流是优化高频率执行js代码的一种手段，js中的一些事件如浏览器的resize、scroll，鼠标的mousemove、mouseover，input输入框的keypress等事件在触发时，会不断地调用绑定在事件上的回调函数，极大地浪费资源，降低前端性能。为了优化体验，需要对这类事件进行调用次数的限制。

### 防抖

在事件被触发n秒后再执行回调，如果在这n秒内又被触发，则重新计时。

```
		function debounce(fn, delay) {
            let timer; // 维护一个 timer
            return function () {
                // let _this = this; // 取debounce执行作用域的this
                let args = arguments;
                if (timer) {
                    clearTimeout(timer);
                }
                timer = setTimeout(()=> {
                    fn.apply(this, args); // 用apply指向调用debounce的对象，相当于this.fn(args);
                }, delay);
            };
        }
        function testDebounce(e, content) {
            console.log(e, content);
        }
        var testDebounceFn = debounce(testDebounce, 1000); // 防抖函数
        document.onmousemove = function (e) {
            testDebounceFn(e, 'debounce'); // 给防抖函数传参
        }
```

### 节流

每隔一段时间，只执行一次函数。

```
function throttle(fn, delay) {
    let timer;
    return function () {
        let _this = this;
        let args = arguments;
        if (timer) {
            return;
        }
        timer = setTimeout(function () {
            fn.apply(_this, args);
            timer = null; // 在delay后执行完fn之后清空timer，此时timer为假，throttle触发可以进入计时器
        }, delay)
    }
}
function testThrottle(e, content) {
    console.log(e, content);
}
let testThrottleFn = throttle(testThrottle, 1000); // 节流函数
document.onmousemove = function (e) {
    testThrottleFn(e, 'throttle'); // 给节流函数传参
}

```

异同点：

```
相同：
	都可以通过使用 setTimeout 实现。
	目的都是，降低回调执行频率。节省计算资源。
不同：
	函数防抖，在一段连续操作结束后，处理回调，利用clearTimeout 和 setTimeout实现。
	函数节流，在一段连续操	作中，每一段时间只执行一次，频率较高的事件中使用来提高性能。
	函数防抖关注一定时间连续触发的事件只在最后执行一次，而函数节流侧重于一段时间内只执行一次。
```

# 6.call，apply，bind的作用和如何实现的

相同点：

- ​			改变this的指向
- 

不同：

- ​	call 从第二个参数开始为传入的参数
- apply 传入的是一个数组，数组的每个成员即为传入的参数
- bind 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

call实现原理

```
Function.prototype.ca_ll = function(context,..agrs) {
    context = context || window
    context.fn = this;
    const result = context.fn(this,...agrs)
    delelt context.fn
    return result
}

// 测试用例
var foo = {
    name: 'zl'
}
function func(job, age) {
    console.log(this.name);
    console.log(job, age);
}

func.ca_ll(foo, 'coder', 45);  //zl coder 45

```

```
apply

Function.prototype.myApply = function(context,agrs = []) {
    context = context || window
    context.fn = this;
    const result = context.fn(this,...agrs)
    delelt context.fn
    return result
}
```

```
bind

Function.prototype.myBind = function(context) {
    context = context || window
    context.fn = this;
    const agrgs = [].slice.call(arguments,1)
    return function() {
    	fn.apply(this,args.concat([].slice.call(arguments)))
    }
    delelt context.fn
    return result
}
```

7.统计一个页面出现最多的标签

```
let tagLists = document.querySelectorAll("*") ;
       let nodeNameList = [...tagLists].map(v=>v.nodeName);
       let sumList = nodeNameList.reduce((obj,key)=>{
           obj[key] = (obj[key] || 0) + 1;
           return obj;
       },{});//sumList 是一个对象
	   console.log(sumList)
       arrayList =  Object.entries(sumList);//对象转换key 和 value 二维数组，
	   console.log(arrayList)
       arrayList.sort((prev,next)=>{
           return  next[1] - prev[1] ;
       });
       console.log(arrayList)
        top3List = arrayList.slice(0,3); //数量前3的标签
```

8.解析url参数

```
//拆分字符串形式
    function queryToObj() {
        const res = {}
        const search = location.search.substr(1);//去掉前面的“?”
        search.split('&').forEach(paramStr => {
            const arr = paramStr.split('=')
            const key = arr[0]
            const val = arr[1]
            res[key] = val
        })
        return res
    }

    //正则形式
    function queryRegExp(name){
        const search = location.search.substr(1);
        const reg = new RegExp(`(^|&)${name}=([^&]*)(&|$)`,'i')
        const res = search.match(reg)
        if(res == null){
            return null
        }
        return res[2]
    }

    // URLSearchParams
    function queryURL(name){
        const search = location.search
        const p = new URLSearchParams(search)
        return p.get(name)
    }
    
     let str = 'http://www.baidu.com/user/?k=1&a=2'
       const str1 = str.split('?')[1].split('&')
	   for (let i=0;i<str1.length;i++) {
		   let a = str1[i].split("=")
		   obj[a[0]] = a[1]
	   }
		console.log(obj)
```

