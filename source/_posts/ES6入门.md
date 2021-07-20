---
title: ES6入门
date: 2021-07-11 21:36:56
tags:
- ES6
category: 前端
---

<!-- more -->

> ECMAScript 是语言的标准 6是版本 在2015推出，也叫ES2015
>
> ECMA是个组织，这个标准规定了一些语法和API

# 与JS的关系

- JavaScript(浏览器端) = ECMAScript（语法+API）+DOM+BOM
- 主流浏览器几乎全部支持ES6
- 不支持可以用Babel转码

# let和const

- let 声明变量，const声明常量（不能重新赋值）

> Why const

- 可以将不想改变的量声明为常量，以避免意外的更改

- 对于引用类型的const可以修改内部的属性

- 不确定用什么可以先用const

## 与var的区别

1. 重复声明

```javascript
 //已经存在的变量又声明了一遍
        var a = 1;
        var a = 2;
```

let不允许**重复声明**（当然const也不行），杜绝了这种方式意外改变原来值的情况

> 注意，只要同名变量已经存在就不能再用let和const声明，无论之前是var声明的还是函数参数

```javascript
  function func(a){
            var b = 4;
//报错       let a = 1;
//          let b = 3;
       }
```

2. 变量提升

var会提升**变量的声明**到当前作用域的顶部

```javascript
console.log(a);
var a = 1;
//等价于下面,此时a会打印出undefined
var a;
console.log(a);
a = 1;
```

而let和const不存在这个特性，使用起来更符合直觉

3. 暂时性死区

只要作用域内存在let、const它们所声明的变量或常量就自动绑定这个区域，不受外部影响

```js
let a = 2;
let b = 1;
function func(){
  //b可以正常打印，a会报错，因为一旦声明let或const就不能再到外部作用域寻找
  console.log(b);
  console.log(a);
  let a = 1;
}
```

var没有这个特性

> 其实如果写过静态类型的语言，就明白这些「特性」就是为了弥补动态类型语言的缺陷，如果有良好的编码习惯，这些「特性」可能都不会注意到。

4. window对象的属性和方法

在全局作用域中，var声明的变量，通过function声明的函数，会自动变成window对象的属性或方法**let,const不会**。

5. 块级作用域

var没有块级作用域，因此下面的代码可以把i打印出来

```javascript
 for(var i = 0;i<e;i++){
           console.log(i)
       }
 console.log(i)
```

let和const有块级作用域

- 注意 ：函数的函数体（函数作用域）和对象的花括号并不构成块级作用域

**作用域链**：就是当前作用域找不到就到外层作用域找直到全局作用域

# 模板字符串和箭头函数

> 估计是最常用的俩了

## 模板字符串

普通的字符串以‘或者”包裹，模板字符串以``包裹（esc下面那个）

可以很方便的拼接字符串

```js
const person = {
  username: 'Alex',
  age: 18,
  sex: 'male'
}
  const info =  "name=" + person.name + 
        "age=" + person.age + 
        "sex=" + person.sex;
console.log(info);
//使用模板字符串
const info = `name=${person.name}age=${person.age}sex=${person.sex}`;


```

### 模板字符串注意事项

1. 如何换行

```js
//转义字符
const info = `1\n2`
//模板字符串会保留空格，换行，缩进等信息，所以直接敲个回车就行
const info = `1
     					2`
```

2. 输出特殊字符

反斜杠转义即可

3. 模板字符串注入

> 所有表达式都可以注入（最后能得出值的）

```js
 const username = 'alex';
        const person = {age:18,sex:'male'}
        const getSex = function (sex) {
            return sex === 'male' ? '男' : '女';
        };
        const info = `${username},${person.age},
						${person.sex + 2},${getSex(person.sex)}`;
```

## 箭头函数

> 我学过的其他静态语言里有个很类似的东西叫lamda表达式

直接来看一个箭头函数

```js
 const add = (x,y) =>{
            return x + y;
        }
```

结构就是(参数)=>{函数体}，如果只有一个参数可以省略圆括号，只有一个语句可以省略花括号和return（一起省略）

```js
const add = x =>  x;
```

如果要返回一个对象，需要在花括号外面包裹一层圆括号

```js
 const add = (x,y) =>({
            value: x + y
        })
```

## this指向

- 全局作用域中的this指向

```js
//指向window对象
console.log(this);
```

- 普通函数体中的this

```js
  function func(){
          console.log(this);
      }
```

此时this指向调用者（没被调用时认为谁也不指）

this指向和谁调用有关，但是和在哪调用无关

```js
//在严格模式下调用者为undifined，即为没有调用者
func();
//但是非严格模式下会被转换成window.func()
```

- 构造函数this指向实例化的对象

```js
function Person(username){
            this.username = username;
        }
        const p = new Person('a');
```

- 箭头函数中的this

箭头函数没有自己的this，会根据作用域链向外层查找this

```js
//指向window
const p = () => console.log(this);
```

### 不适合使用箭头函数的场景

1. 作为构造函数

显然构造函数需要this指向实例化的对象，而箭头函数没有自身的this

2. 需要this指向调用对象

比如在绑定事件时

3. 使用arguments时

箭头函数不适用不定参数arguments，需要使用剩余参数

# 解构赋值

> 解析某一数据的解构，将想要的东西提取出来，赋值给常量或变量

## 数组的解构赋值

1. 模式（结构）匹配

```const [] = [1,2,3];```

```const [a, [], b] = [1, [2, 3], 4];```

2. 索引值相同的完成赋值

``````js
//如果想赋给变量用let
const [a, b, c] = [1, 2, 3];
console.log(`${a}${b}${c}`);
``````

就是得是同种结构才能结构，对应位置进行赋值

- 解构赋值的默认值

```js
//此时会取出undifined
const [a, b] = [];
//设置默认值
const [a=1,b=2] = []
```

**注意：**默认值只有数组成员严格等于===undifined时才会生效

- 默认值表达式

默认值表达式中的值是**惰性求值**的，用到时才会执行

> 几种常见用法

```js
function func(){
  //arguments并不是数组，但是可以这么做
  [a,b] = arguments;
}

//解析文档树
const [p1, p2, p3] = document.querySelectorAll("p");
//求和
 const add = ([x, y]) => x + y;
        add([1, 2]);
//交换
[x,y] = [y,x];
```

## 对象的解构赋值

1. 模式(结构)匹配
2. 属性名相同完成赋值

```js
  const {age, username} = {username: 'aaa', age: 18};
        console.log(age, username);
//完整
const {age:age,username:username} = {username: 'aaa',age: 18};
//也可以起个别名
   const {age: hahaha, username: uname} = {username: 'aaa', age: 18};
        console.log(hahaha, uname);
```

> 实际上就是根据key来赋值给value

### 注意

- 默认值的生效条件

```js
//使用默认值，这里可以省略key
const {age: age=10, username: uname='aaa'} = {};
        console.log(age, uname);
```

同样是严格等于undefined时才会使用默认值

- 惰性求值
- 将一个已经声明的变量用于解构赋值

```js
let x = 2;
({x} = {x:3})
```

由于会被视为块所以需要加个括号

- 可以取到继承的属性

```js
const {toString} = {};
console.log(toString);
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712193714.png" alt="image-20210712193714110" style="zoom:50%;" />

> 常见应用

```js
const logPersonInfo = ({age,username}) => console.log(username, age);
logPersonInfo({username: 'alex', age: '18'});
```

> 一个复杂的嵌套，能看懂基本就没问题了

```js
const obj = {
    x: 1,
    y: [2,3,4],
    z:{
        a:5,
        b:6
    }
};

const {
   y
    y: [, yy]
} = obj;
//打印3和数组
console.log(yy,y);
```

## 其他类型的解构赋值

- 字符串

既可以用数组也可以用对象

```js
const [a,,l] = 'hello';
console.log(a, l);
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712201127.png" alt="image-20210712201127685" style="zoom:50%;" />

```js
const {0:a,4:o} = 'hello';
console.log(a,o);
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712202002.png" alt="image-20210712202001917" style="zoom:50%;" />

- 数值和布尔值

会将对应值转换为对象再赋值，不怎么常用

- undefined 和 null

这俩不能转换成对象所以不能解构赋值

# 对象字面量增强

> 对象字面量是什么

```js
//实例化构造函数生成对象
const person = new Object();
person.age = 18;
person.speak = function(){}
//对象字面量
const person = {
    age:18,
    speak:function(){}
}
```

### 简介表示法

- 属性的简洁表示法

```js
const age = 18;
const person = {
    // age:age,
    age
}
```

当键名和常量名一样的时候可以只写一个

```js
let age = 18;
const person = {
    // age:age,
    age
}
console.log(person.age);
age = 19;
console.log(person.age);
```

<img src="https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712210202.png" alt="image-20210712210201908" style="zoom:50%;" />

- 方法的简洁表示法

```js
const person = {
    // speak:function(){
    //    
    // }
    speak(){}
}   
```

 ### 方括号语法

> 当想要给对象添加属性时

```js
const prop = 'age';
const person = {};
//点语法
person.prop = 18;
//方括号语法
person[prop] = 18;
console.log(person)
```

![image-20210712210709818](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210712210709.png)

可以这么写，就是求一个值作为key,方括号里可以写**任何能求值的表达式**

```js
const prop = 'age';
const person = {
    [prop]:18
}
console.log(person);
```

方括号语法主要在点语法不能用的时候使用，比如属性名不怎么规范的时候

### 函数参数默认值

```js
const func = (x=2,y=1)=>{}
```

- 同样，只有参数严格等于undifined时会使用默认值（不传就是undifined）
- 同样惰性求值
- 最好从参数列表右侧开始设置默认值，要不然得显示传递undifined

> 实际应用

```js
//接受很多参数
//这样还得记住参数的顺序
const logUser = (a = 3, b = 2, c = 1) => {

}
//这样传一个对象,调用时解构赋值,默认值可以用来处理不传参的情况
const logUser = ({username =’f', age, sex}={}) => {

}
logUser({username: 'a', age: 'b', sex: 'c'})
```

# 剩余参数与展开运算符

## 剩余参数

```js
//args就是剩余参数，其实就是可变参数
const add = (x,y,z,...args)=>{
}
```

- 剩余参数是一个数组，没有值就是空数组

### 剩余参数注意事项

- 使用箭头函数时不能省略圆括号
- 使用剩余参数代替arguments，尤其是箭头函数没有arguments
- 只能是最后一个参数

### 实际应用

```js
//解构赋值
const [num, ...args] = [1,2,3,4];
//对象解构 打印z { a: 5, c: 4 }
const {x, ...z} = {x: 1, a: 5, c: 4};
console.log(z);
```

## 数组展开运算符

如果需要将数组展开成单个元素，比如将数组传给接受数字类型参数的方法时

```js
const arr = [1, 2, 3, 4, 5];
console.log(Math.max(arr));//会打印NaN
console.log(Math.max(...arr))// 打印5
```

**根本区别**

- 展开运算符[1,2,3]->1,2,3;剩余参数1,2,3->[1,2,3]

### 实际应用

```js
//数组深拷贝
const a = [1, 2];
const b = a;
const c = [...a];
console.log(a, b, c);
a[0] = 3;
console.log(a, b, c);
```

![image-20210714144812651](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210714144812.png)

```js
//合并数组
const a = [1];
const b = [2];
const c = [...a, ...b];
console.log(c);
```

对于字符串也很实用

```js
//得到字符串数组
const a = [...'abcdefg'];
console.log(a);
```

还可以把类数组转换一下

```js
const a = [...arguments];
```

## 对象展开运算符

对象只能在{}中展开，将原来的属性罗列出来赋给新的对象

```js
const apple = {
    color: 'red',
    taste: 'sweet'
}
const b = {...apple};
//false
console.log(apple===b)
```

也可以合并对象

```js
const apple = {
    color: 'red',
    taste: 'sweet'
}
const bug = {
    color: 'black',
    taste: '???'
}
//后面的会覆盖前面的相同属性
const c = {...apple, ...bug};
console.log(c)
```

![image-20210714150400768](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210714150400.png)

### 注意事项

1. 展开空对象什么也不做

2. 展开非对象，会自动转换成对象，罗列其属性
3. 字符串和数组在对象中展开会根据索引赋值

4. 对象中的对象不会被展开

### 应用

```js
//深拷贝
const a = {};
const c = {...a};
console.log(a === c);
//默认属性
const defaultPam = {
    username: 'aaa'
}
const func = userParm=>{
    const parm = {...defaultPam, ...userParm};
}
```

# Set和Map

## Set

无序，无重复值的集合，只能由new来创建

```js
//添加和创建
const s = new Set();
s.add(1);
```

- size属性获得元素个数

常用操作

```js
const s = new Set();
s.add(1);
s.has(1);
s.delete(1);
s.clear();
```

- 遍历

```js
const s = new Set();
s.add(1);
//set里key==value set就是调用forEach的那个set，只要value的话可以只传递value
//最后的document位置的参数是改变this指向
s.forEach(function(key,value,set){
    console.log(key, value, set);
},document)
```

- 几种构造方式

```js
 const s = new Set([1,2,1]);
 const s = new Set("gi");
 const s = new Set(document.querySelectorAll('p'));
```

### 注意事项

- Set判断基于===，但是NaN是一样的

应用：去重，存放元素

## Map

无序键值对集合，同样只能用new来创建

```js
const map = new Map();
//key可以是任意类型
map.put('a', 1);
```

- api差不太多，重复添加会覆盖

- 遍历

```js
m.forEach(function(value,key,map){
},document)
```

- 构造

```js
//传递一个二维数组
const map = new Map([['a', 1]]);
//set
const set = new Set(['a',1]);
const map = new Map(set);
//map
const map = new Map(map2);//也是深拷贝
```

### 注意事项

- 也是基于===

# iterator

就是迭代器，可以遍历可迭代对象

```js
const arr = [1, 2, 3];
const it = arr[Symbol.iterator]();
let next = it.next();
while(!next.done) {
    console.log(next.value);
    next = it.next();
}
```

### for...of

语法糖，基于iterator

```js
for(const item of arr){
    console.log(item);
}
//获取可迭代对象
arr.entries();//键值
arr.keys();//键
```

### 原生可遍历和非原生可遍历

- 有Symbol.iterator方法，并且这个方法可以生成可遍历对象，就是可遍历的
- 数组，字符串，Set，Map，arguments，NodeList都是原生可遍历的
- 一般的对象都是非原生可遍历

```js
//自定义迭代器
person[Symbol.iterator] = ()=>{
    let index = 0;
    return {
      //实现next方法
        next(){
            index++;
            if(index ===1){
                return {
                    value: person.age,
                    done: false
                }
            }else{
                return {
                    done: true
                }
            }
        }
    }
}
```

### 其他使用iterator场景

- 数组解构赋值
- 数组展开
- Set，Map构造

# ES6新增方法

- Includes()

字符串匹配，数组包含元素搜索

```js
//abc中是否有a
console.log('abc'.includes('a'));//true
//搜索开始位置 默认0
console.log('abc'.includes('a',1));//false
//数组 NaN和NaN认为相等
console.log([1, 2, 3].includes(1));
```

- padStart(),padEnd()

补全字符串

```js
//如果长度小于原长不会生效 补全字符串超出长度截取后面
console.log('x'.padEnd(10, 'ab'));//xababababa
console.log('x'.padStart(10, 'ab'));//ababababax
```

- trimStart(),trimEnd()（也叫left，right）

去除空格

- Array.from()

转换其他数据类型（所有可迭代类型，有length属性的对象）到数组

```js
const obj = {
    '0': 1,
 length:2,
    '1': 2
}
console.log(Array.from(obj));
```

第二个参数可以是过滤器，也可以是更改this指向

- find(),findIndex()

找到满足条件的第一个结果，分别返回值和索引

```js
//打印15
console.log([1, 5, 10, 15].find((value, index, arr) => {
    return value > 5 && index > 2;
}));
```

- Object.assign()

合并对象,属性被合并到传入的第一个对象中,同名属性后面的覆盖前面的

```js
const a = {key:'aaa'};
const b = {value:'bbb'};
Object.assign(a, b);
//{ key: 'aaa', value: 'bbb' }
console.log(a);
```

- Object.keys(),Object.values(),Object.entires()

获取对象的键，值，键值集合，返回数组，数组的同名方法是实例方法返回iiterator。

对于顺序有要求不要用这个遍历

# Promise

> 用来解决过多的回调函数之间的嵌套

 **什么是Promise**

异步操作的一种解决方案，异步的另一种形式是回调函数，不过层层嵌套的回调函数会被称为callback hell。Promise可以解决这个问题。

```js
const p = new Promise((resolve, reject) => {
    //一开始是Promise是pending（未完成），执行resolve，变成fulfulled（resolved）已成功
    resolve({alg:1});
    //调用reject变成rejected
    reject({alg:2});
});
//then方法，成功和失败两个回调
p.then((data)=>{
    console.log('success', data);
},(err)=>{
    console.log('err', err);
})
```

Promise的状态一旦变化就不会再变了

## then()

1. 什么时候执行

前面调用resolve或者reject时

pending->fulfilled时执行then的第一个回调函数（成功）

Pending->rejected时执行then的第二个回调函数 (失败)

2. 执行后的返回值

```js
//then方法，成功和失败两个回调
p.then((data)=>{
    console.log('success', data);
},(err)=>{
    console.log('err', err);
    return 'alg';
    //相当于
    // return new Promise((resolve) => {
    //     resolve('alg');
    // })
}).then((data)=>{
	
})

```

then方法会将返回值包装一层Promise，并且**默认调用成功回调resolved.**

如果想要执行失败的方法只需手动new一个Promise返回并调用rejected方法

```js
    return new Promise((resolve, reject) => {reject('err')});
```

## catch()

> 本质就是第一个参数为null的then()，专门用来处理失败的情况

```js
p.then(data=>{
    
}).catch(err=>{
    
})
```

catch可以捕获前面的错误 ，一般会在then后面加上一个。

## finally()

一定会执行的函数，和捕获异常里的finally差不多，不接收参数

```js
.finally(res=>{

})
//等价于
.then(result=>{
    return result;
},err=>{
    return new Promise((resolve,reject)=>{
        reject();
    })
})
```

## Promise.resolve()和Promise.reject()

### Promise.resolve()

对于成功状态的简写

```js
new Promise((resolve) => {
    resolve('foo')
});
//等价于
Promise.resolve('foo');
```

如果传入Promise参数则直接返回,也就是传入的Promise接管了then的控制权

```js
const a = new Promise((resolve) => {
    setTimeout(resolve, 1000, 'haah');
});
Promise.resolve(a).then(data=>{
    console.log(data);
});
//等价于
a.resolve(a).then(data=>{
    console.log(data);
}); 
```

具有then方法的对象,会直接调用then方法

```js
const a = {
    then(resolve,reject){
        console.log('aaaaaaaa');
        resolve();
        //reject()
    }
}

Promise.resolve(a).then(data =>{
    console.log(data)},err=>{
    console.log(err);
})
```

### Promise.reject()

失败状态的简写

```js
new Promise((resolve,reject)=>{
    reject('error');
})

Promise.reject('error');
```

差不多，就是直接向后原封不动的传递参数

## Promise.all()

- Promise.all()关注多个Promise对象的状态变化，传入多个Promise实例，包装成一个新的Promise实例返回

```js
const delay = ms=>{
    return new Promise((resolve)=>{
        setTimeout(resolve, ms);
    })
}

const p1 = delay(1000).then(()=>{
    console.log('p1');
    return 'p1';
})

const p2 = delay(2000).then(()=>{
    console.log('p2');
    return 'p2';
})
//所有状态成功才会成功，否则就会失败
Promise.all([p1, p2]).then(data=>{
    console.log(data);
},err=>{
    console.log(err);
})

```

## Promise.race()和Promise.allSettled()

### race()

和上面的一样传入多个Promise

- 第一个完成的决定状态

### allSettled()

- 永远成功

## 注意事项

1. resolve和reject之后的代码

能执行，但是不推荐，建议return后面调用这俩函数

2. 如果传入的数组包含不是Promise的对象会被转换成Promise，任何可遍历的都可以作为参数

3. 错误处理

可以统一处理

## 异步加载图片

```js
const loadImgAsync = url=>{
    return new Promise((resolve, reject)=>{
        const image = new Image();
        img.onload = ()=>{
            resolve(img);
        }
        img.onerror = ()=>{
            reject(new Error(`?${url}`))
        }
        img.src = url;
    })
}
```

# Class类

> 和其他静态类型的语言很像是不是

```js
class Person{
    //构造方法，不显式声明会有默认实现
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
    speak(){
        console.log(this.name,this.age);
    }
}

let person = new Person('ALG',8);
person.speak();
//和构造方法比较
function Person(name,age){
    this.name = name;
    this.age = age;
}

Person.prototype.speak = function () {
    console.log(this.name, this.age);
};
```

实际上Class的写法本质上就是构造方法，只是语法糖而已

```js
console.log(typeof Person)
```

![image-20210719151625677](https://raw.githubusercontent.com/C1EYE/figureBed/main/img/20210719151625.png)

## 两种定义形式

1. 声明形式

```js
class Person{
    //构造方法，不显式声明会有默认实现
    constructor(name,age) {
        this.name = name;
        this.age = age;
    }
    speak(){
        console.log(this.name,this.age);
    }
}
```

2. 表达式形式

```js
const Person = class{
  constructor(){}
}
```

> 和立即执行的匿名函数一样的匿名类

## 类的属性

1. 实例属性

```js
class Person {
//在这里声明无需任何修饰
    name = 'ALG'
    age = 1
//方法也一样
    func = function(){}
//可以在构造方法里声明，但是在方法中引用需要this
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    speak() {
        console.log(this.name, this.age);
    }
}
```

2. 类属性（类的非函数属性兼容性有问题，不要在开发中用）

```js
  static speak(){
    //这里this指向类本身
        console.log('AOOO')
    }
```

## 私有属性和方法

> ES6不支持，需要一些小技巧

- 变量名前加'_'规定是私有
- 将私有属性方法移出类

## 继承

- 使用extents关键字

```js
class Child extends Person {
    constructor(name, age) {
        super(name, age);
    }
}
```

>  所有属性都会被继承

- Override

写同名会覆盖

## super

和其他语言里的super功能差不多...

```js
super()
super.func()//指向父类的原型
```

- 在静态方法中指向父类本身

# Module模块系统

- 模块化
- 消除全局变量
- 管理加载顺序

```js
//导出
export default xxxx;
//导入
import xxx from 'xxx'
```

- html中引入

```html
<script src="..." type="module"></script>
```

## 导出和导入

> 如果不导出也可以导入，多次导入只是执行一次代码

- export default 对应 import

```js
export default age;//只能有一个
import 任意合法名字 from '....'//会把导出的对象赋值给变量
```

- export 对应 import

```js
export const age = 19;//必须是声明或语句
export {age};//这样也行
import {age} from '...'//必须严格对应
```

- 多个导出

```js
export {a,b,c};
import {a,b,c} from '...'
```

- 起别名

```js
export {a as aaa};
import {aaa as a};
```

- 整体导入

```js
import * as xxx from '...'//作为一个对象，会把两种导出方式都导入
```

## 注意事项

- 模块顶层this指向

按模块方式加载（type="module")this指向undefined，否则指向window

- import关键字和函数

import会提升整个模块到头部，率先执行，不能在代码块中执行

import()方法可以

- 复合写法

```
export {xxx} from '...';
//类似下面，但是不能在当前模块访问xxx
import {xxx} from '...';
export {xxx} from '...';
```

