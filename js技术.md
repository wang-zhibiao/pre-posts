### vue中对watch的利用

```js
  watch: {
    "planForm.number": function(newVal, oldVal) {
      // 解决数字键盘可以输入输入多个小数点问题
      if (newVal == "" && oldVal.toString().indexOf(".") > 0) {
        this.planForm.number = oldVal;
        return;
      }
      // 保留两位小数
      if (newVal) {
        newVal = newVal.toString();
        var pointIndex = newVal.indexOf(".");
        //不出现两位小数点
        if (newVal.toString().lastIndexOf(".") > pointIndex) {
          this.planForm.number = oldVal;
          return;
        }
        if (pointIndex > 0 && newVal.length - pointIndex > 3) {
          this.planForm.number = oldVal;
          return;
        }
      }
    }
  },
```

### 一个map函数内使用里面对某个子项进行every判断是否为数字

```js
value.mpa(v=>{
    if(v.every(item=>isNaN(item))){}
})
```

### 什么是 IIFE (立即调用的函数表达式)

`IIFE`是一个立即调用的函数表达式，它在创建后立即执行

```js
(function IIFE(){
    console.log( "Hello!" );
})();
// "Hello!"
```

### 无loop生成指定长度的数组

```js
const List = len =>[...new Array(len).keys()]
const list = List(10)  //=>list=[0,1,2,3,4,5,6,7,8,9]
```

### 数组的去重

```js
const list = [1,2,45,1,5,4,245,2];
const uniqueList = [ ...new Set(list)] //=>uniqueList= [1,2,45,5,4,245]
```

### 点击回到顶部的js

```js
Gotop.onclick = function () {
        //判断是否有无定时器
        if (c) {
            clearInterval(c);
        }
        c = setInterval(function () {
            var target = 0; //顶部的距离
            var step = 10; //每次滚动的距离
            var curent = getScroll().scrollTop; //滚动出去的距离
            //判定有没有滚动
            if (curent > target) {
                step = -Math.abs(step);
            }

            //使页面开始一直往上滚
            curent += step;
            document.documentElement.scrollTop = curent;
            document.body.scrollTop = curent;
            //判断最后页面是不是到了最开始的位置
            if (Math.abs(curent) <= target) {
                clearInterval(c);
                document.documentElement.scrollTop = target;
                document.body.scrollTop = target;
                return;
            }
        }, 2)
    }
    // 获取页面滚动出去的距离，处理兼容性
    function getScroll() {
        return {
            scrollTop: document.documentElement.scrollTop || document.body.scrollTop,
            scrpllLeft: document.documentElement.scrpllLeft || document.body.scrollLeft
        }
    }
```

map返回就是一个新的数组，也就是说 可以使用new进行创建一个数组

### for in 使用

```js
let object = {a:1,b:2,c:3}
for(let i=0 in object){
    console.log(i)
}
// a,b,c
```

[`for...in`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in) 语句以原始插入顺序迭代对象的[可枚举属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)。

`for...of` 语句遍历[可迭代对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Iterators_and_Generators#Iterables)定义要迭代的数据。

### for in 去掉对象中为空的键值

```js
 for (const key in filter) {
        if (filter[key] === "") {
          delete filter[key];
       }
  }
```

### 数组对象的去重

```js
let TempObj = {};
let newArr = [...arr1,...arr2].reduce(
   (item, next) => {
     TempObj[next.id]? "": (TempObj[next.id] = true && item.push(next));
     return item;
     },[]
);
```

说明：其中item代表的是数组的每次遍历之后的项，next表示遍历的下一项。



## 12.生成随机字符串 (可指定长度)

```text
/**
 * 生成随机字符串(可指定长度)
 * @param len
 * @returns {string}
 */
randomString = function(len) {
  len = len || 8;
  var $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';
  /****默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1****/
  var maxPos = $chars.length;
  var pwd = '';
  for (var i = 0; i < len; i++) {
    pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
  }
  return pwd;
}
```

## .生成随机颜色值

```text
function getRandomColor () {
  const rgb = []
  for (let i = 0 ; i < 3; ++i){
    let color = Math.floor(Math.random() * 256).toString(16)
    color = color.length == 1 ? '0' + color : color
    rgb.push(color)
  }
  return '#' + rgb.join('')
}
```

#### 数组的方法可以进行二次筛选

```js
var hufflepuff = wizards.filter(function (wizard) {
  return wizard.house === 'Hufflepuff';
}).map(function (wizard) {
  return wizard.name;
});
```

在filter后进行map方法，由于filter返回的是一个数组 所有再次map的是一个数组相当于：

```js
wizards.filter(function (wizard) {
  return wizard.house === 'Hufflepuff';
})  //=>返回一个数组
let arr = wizards.filter(function (wizard) {return wizard.house === 'Hufflepuff';}) 
arr.map(function (wizard) {return wizard.name;});
```

## es6的引入进行别名设置

```js
import * as 别名 from '@/路径';
```

*代表所有，可以使用{变量名}部分引入，as  把什么作为别名

使用时。

```js
别名.定义的变量或者函数
```

单个变量设置别名

```js
import { export as 别名 } from "@/路径";
```

### 对象的键值不仅仅只是一个字符串

```js
let user = {
    name:'wang',
    age:20
}
let key = 'name';
user[key] //=>  输出wang

//key 可以是一个变量
let key = prompt("What do you want to know about the user?", "name");
//prompt  浏览器的输入弹窗  在输入后会返回输入的值给key（默认输入name）
user[key] //==> John（如果输入 "name"）
```

我们对于变量的键值应该是灵活的使用

key是一个变量可以通过变量的赋值进而改变对象的输出

### 对象内的计算属性

我们可以在对象字面量中使用方括号。这叫做 **计算属性**。

```js
let key = prompt("What do you want to know about the user?", "name");

let user = {
  [key]: "wang", // 属性名是从 key 变量中得到的
};

alert( user.name ); // 5 如果 key="name"
```

对象的属性判断是否存在

```
user.key === undefined  返回true表示不存在
“key” in user  返回true表示存在
```

