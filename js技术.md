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