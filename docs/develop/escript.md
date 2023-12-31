# es6+基础

## es6 ~ es13所有新特性

### ECMAScript简介

> ECMAScript是一个脚本语言规范，通常看作是js的标准规范，但是js其实是ES的扩展语言。

- 在浏览器中，js = ES + webApis(BOM,DOM)
- 在node中，js = ES + nodeApis(fs,net,etc…)

### ES 新规范

#### let和const声明变量

- var定义的变量，变量提升，没有块的概念，可以跨块访问。
- let定义的变量，只能在块作用域里访问，不能声明同名变量。
- const用来定义常量，使用时必须初始化(即必须赋值)，不能声明同名变量，只能在块作用域里访问，而且不能修改，但是在定义的对象时对象属性值可以改变。

#### symbol

> Symbol是ES6中引入的一种新的基本数据类型，用于表示一个独一无二的值，不能与其他数据类型进行运算。它是JavaScript中的第七种数据类型，与undefined、null、Number（数值）、String（字符串）、Boolean（布尔值）、Object（对象）并列。
```js
const a = Symbol();
console.log(a);  //Symbol()
```

#### 模板字符串

```js
let str="hello world"
// es6之前
let html="<div>"+
            " <a>"+url+"</a>"+
        "</div>";
//es6
let eshtml=`<div>
            <a>${url}</a>
        </div>`
```

+ 字符串新方法

```js
let str = 'blue,red,orange,white';

// 判断字符串是否包含参数字符串
str.includes('blue');//true
// 判断字符串是否以参数字符串开头或结尾
str.startsWith('blue');//true
str.endsWith('blue');//false

// 指定次数返回一个新的字符串。
console.log('hello'.repeat(2));   //'hellohello'

// 按给定长度从前面或后面补全字符串，返回新字符串。
let arr = 'hell';
console.log(arr.padEnd(5,'o'));  //'hello'
console.log(arr.padEnd(6,'o'));  //'helloo'
console.log(arr.padEnd(6));  //'hell  ',如果没有指定将用空格代替

console.log(arr.padStart(5,'o'));  //'ohell'
```

#### 解构表达式

- array

```js
let [a,b,c] = [1,2,3];
console.log(a,b,c);    //1,2,3
 
let [a,b,c] = [1,,3];
console.log(a,b,c);    //1,undefined,3
 
let [a,,b] = [1,2,3];
console.log(a,b);//1,3
 
let [a,..b] = [1,2,3];  //...是剩余运算符，表示赋值运算符右边除第一个值外剩余的都赋值给b
console.log(a,b);//1,[2,3]
```

- object

```js
let obj = { 
	name: "ren", 
	age: 12, 
	sex: "male" 
};

let { name, age, sex } = obj;
console.log(name, age, sex); //'ren' 12 'male'

let { name: myName, age: myAge, sex: mySex } = obj; //自定义变量名
console.log(myName, myAge, mySex); //'ren' 12 'male'

```

#### 对象

> Map和Set属于es6新增加的对象

- Map Map对象用于保存键值对

```js
let myMap = new Map([['name','ren'],['age',12]]);
console.log(myMap);  //{'name'=>'ren','age'=>12}

myMap.set('sex','male');
console.log(myMap);  //{'name'=>'ren','age'=>12,'sex'=>'male'}
console.log(myMap.size);  //3

myMap.get('name');  //'ren'
myMap.has('age');  //true
myMap.delete('age');  //true
myMap.has('age');  //false
myMap.get('age');  //undefined
```

- Set 可以理解为后端的Set集合对象 Set对象和Map对象类似，但它存储不是键值对。类似数组，但它的每个元素都是唯一的。

```js
let mySet = new Set([1,2,3]);//里面要传一个数组，否则会报错
console.log(mySet);  //{1,2,3}

mySet.add(4);
console.log(mySet);  //{1,2,3,4}

mySet.delete(1);  //true
mySet.has(1);  //false
console.log(mySet);  //{2,3,4}
```
> 利用Set对象唯一性的特点，可以轻松实现数组的去重

- 数组的新方法

```js
// Array.from()方法可以将可迭代对象转换为新的数组。
let arr = [1, 2, 3];
let obj = {
    double(n) {
        return n * 2;
    }
}
console.log(Array.from(arr, function (n){
    return this.double(n);
}, obj)); // [2, 4, 6]

//  includes()方法
let arr = [1,33,44,22,6,9]
let ary = arr.includes(22)

// map()、filter() 方法
let arr = [1, 33, 44, 2, 6, 9];

let newarr1 = arr.filter((v) => v > 10); //newarr1-------[33, 44]
let newarr2 = arr.filter((v) => v * 2);  //newarr2-------[1, 33, 44, 2, 6, 9]

let newarr3 = arr.map((v) => v > 10);    //newarr3-------[false, true, true, false, false, false]
let newarr4 = arr.map((v) => v * 2);     //newarr4-------  [2, 66, 88, 4, 12, 18]

// forEach()方法
let arr = [1,33,44,2,6,9]
let a1= []
arr.forEach((v, i)=>{
  if (v > 10) {
    a1.push(arr[i])
  }  
})
console.log(a1) [33,44]

//  find()方法 查找数组中符合条件的第一个元素，直接将这个元素返回出来
let arr = [1,33,44,2,6,9]
let a= arr.find(v => v > 10)
console.log(a) // 33

// some() 找到一个符合条件的就返回true,所有都不符合返回false 
// every() 数组所有值都符合条件才会返回true,有一个不符合返回false。
let arr = [1,2,3,4,6,11]
let newarr = arr.some(function(v){
  return v > 10
})
console.log(newarr) //true
let newarr2 = arr.every(function(v){
  return v > 10
})
console.log(newarr2) //false
```

- object的新方法

```js
// Object.is() 判断两个值是否为同一个值，返回一个布尔类型的值。
const obj1 = {};
const obj2 = {};
console.log(Object.is(obj1, obj2)); // false
const obj3 = {};
const value1 = obj3;
const value2 = obj4;
console.log(Object.is(value1, value2)); // true

// Object.assign()方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象，并返回目标对象
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const obj3 = { a:5 , c: 3 };
//对象合并，把后面对像合并到第一个对象，对象里相同的属性会覆盖
Object.assign(obj1, obj2, obj3);
console.log(obj1); // { a: 5, b: 2 , c:3}

// Object.keys() 返回对象所有属性
// Object.values() 返回对象所有属性值
// Object.entries() 返回多个数组，每个数组是 key--value 
```
- ...(对象扩展符)

```js
let person={
    name: "admin",
    age: 12,
    wife:{
        name:"迪丽热巴"
    }
}
let person2={...person}
console.log(person2===person);//false
console.log(person2);//{name: 'admin', age: 12, wife: {…}}

// 合并对象
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const obj3 = { a: 5, c: 3 };
let newObj ={...obj1,...obj2,...obj3}
console.log(newObj); // { a: 5, b: 2 , c:3}
```

#### 函数

- 参数默认值

```js
function add(a = 0, b = 0) {
    return a + b;
}
let x=add(); 
let y=add(2); 
let z=add(3, 4); 
```

- 箭头函数

```js
let add = (a,b) => {
    return a+b;
}
let print = () => {
    console.log('hi');
}
let fn = a => a * a;
```

- 箭头函数和普通函数最大的区别在于其内部this永远指向其父级对象的this

```js
 var age = 123;
 let obj = {
     age:456,
     say:() => {
         console.log(this.age);  //this指向window
     }
 };
obj.say();   //123
```

#### Class 类

```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    say() {
        console.log(this.name + ":" + this.age);
    }
}
class Student extends Person {
    constructor(name, age, sex) {
        super(name, age);
        this.sex = sex;
    }
}
var student = new Student("admin", 12, "male");
student.name;   //'admin'
student.sex;    //'male'
student.say(); //'ren:12'
```

#### promise

> Promise 是一个异步操作返回的对象，用来传递异步操作的消息

> Promise本身是同步，then( ) ， catch( )方法是异步

```js
 function test() {
    const pro = new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log("异步请求成功");
            // resolve("成功了")
            reject("失败")
        }, 3000)
    })

    return pro
}

test().then(res => {
    console.log(res);
}).catch(err => {
    console.log(err);
}).finally(() => { // 成功失败都会执行
    console.log("finally");
});
```

#### Module模块化

> ES6 Module是静态的，也就是说它是在编译阶段运行，和var以及function一样具有提升效果。

```js
//import导入
import menus from "@/router";
import { Loading, Redirect } from "@/components";
import { changeBlogTitle, getDecode } from "@/utils";

//export 导出
const x = 10
const y = 20
export {x}
export default y
```

> export {<变量>}导出的是一个变量的引用，export default导出的是一个值

#### 幂运算符

```js
3 ** 2  //9
// or
Math.pow(3, 2) //9
```

#### 异步迭代for await

```js
// 这段代码不会正常运行，循环本身依旧保持同步，并在在内部异步函数之前全部调用完成。
async function process(array) {
  for (let i of array) {
    await doSomething(i);
  }
}

async function process(array) {
  for await (let i of array) {
     doSomething(i);
  }
}
```

####  es10 Array Object新方法

- Array.prototype.flat() 通过参数控制扁平深度（默认深度为 1）

```js
const arr1 = [1, 2, [3, 4]]
arr1.flat() // [1, 2, 3, 4]

const arr2 = [1, 2, [3, 4, [5, 6]]]
arr2.flat() // [1, 2, 3, 4, [5, 6]]

const arr3 = [1, 2, [3, 4, [5, 6]]]
arr3.flat(2) // [1, 2, 3, 4, 5, 6]

const arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]]
arr4.flat(Infinity) // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

- Array.prototype.flatMap() 扁平顺便map一下

```js
const arr = [1, 2, 3, 4]
arr.flatMap(x => [x * 2]) // [2, 4, 6, 8]
// 只有一层是扁平的
arr.flatMap(x => [[x * 2]]) // [[2], [4], [6], [8]]
```

- Object.fromEntries() map转化为对象

```js
const entries = new Map([
  ["apple", "origin"],
  ["grapes", "peach"]
])
console.log(Object.fromEntries(entries)) // { apple: "origin", grapes: "peach" }

// 交换属性和值
function foo(obj) {
  return Object.fromEntries(Object.entries(obj).map(([key, value]) => [value, key])
  )
}

// 将 url上的参数提取为一个对象
const params = Object.fromEntries(new URLSearchParams("foo=bar&baz=qux"));
console.log(params); // {foo: "bar", baz: "qux"}
```

- String.prototype.trimStart() 和 String.prototype.trimEnd() 去除空格

```js
let message = "     Hello      ";
message = message.trimEnd().trimStart();
console.log(message); // "Hello"
```

#### BigInt

> BigInt是一种特殊的数字类型，它支持任意长度的整数。
```js
// Number类型只能安全地表示-9007199254740991 (-(2^53-1)) 和9007199254740991(2^53-1)之间的整数，任何超出此范围的整数值都可能失去精度。
29007199254740992 === 9007199254740993; //true

//要创建一个 bigint，可以在一个整数的末尾添加字符n，或者调用函数 BigInt()。BigInt 函数使用字符串、数字等来创建一个BigInt。
19007199254740992n === 9007199254740993n; //false
```

## 相关链接

- [最全的—— ES6有哪些新特性？](https://juejin.cn/post/7092965421740982303)