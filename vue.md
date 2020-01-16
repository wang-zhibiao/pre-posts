### 子组件传出单个参数时：

```js
// 子组件
this.$emit('test',this.param)
// 父组件
@test='test($event,userDefined)'
```



### 子组件传出多个参数时：

```js
// 子组件
this.$emit('test',this.param1，this.param2, this.param3)
// 父组件 arguments 是以数组的形式传入
@test='test(arguments,userDefined)'
```

### 对于异步请求的异常处理

```js
methods: {
    async getUserPageData() {
        try {
            //请求
           const res = await Promise()/api请求
        } catch(error) {
            // 失败的情况写在catch中
        }
    }
}

```

## handler方法和immediate属性

这里 watch 的一个特点是，最初绑定的时候是不会执行的，要等到 `firstName` 改变时才执行监听计算。那我们想要一开始就让他最初绑定的时候就执行改怎么办呢？我们需要修改一下我们的 watch 写法，修改过后的 watch 代码如下：

```js
watch: {
  firstName: {
    handler(newName, oldName) {
      this.fullName = newName + ' ' + this.lastName;
    },
    // 代表在wacth里声明了firstName这个方法之后立即先去执行handler方法
    immediate: true
  }
}
复制代码
```

注意到`handler`了吗，我们给 firstName 绑定了一个`handler`方法，之前我们写的 watch 方法其实默认写的就是这个`handler`，Vue.js会去处理这个逻辑，最终编译出来其实就是这个`handler`。

而`immediate:true`代表如果在 wacth 里声明了 firstName 之后，就会立即先去执行里面的handler方法，如果为 `false`就跟我们以前的效果一样，不会在绑定的时候就执行。

### vue中对于页面刷新时事件的监听

#### **使用原生的属性**`onbeforeunload`

```js
window.onbeforeunload = function (e) {
 e = e || window.event;
  
 // 兼容IE8和Firefox 4之前的版本
 if (e) {
 e.returnValue = '关闭提示';
 }
  
 // Chrome, Safari, Firefox 4+, Opera 12+ , IE 9+
 return '关闭提示';
};
```

#### 在methods中定义事件

```js
methods: {
 beforeunloadHandler (e) {
 // ...
 }
}
```

#### 在 `mounted`,`created` 钩子中注册事件

```js
window.addEventListener('beforeunload', e => this.beforeunloadHandler(e))
```

#### 在页面销毁`destroyed `时清除监听

```js
destroyed() {
 window.removeEventListener('beforeunload', e => this.beforeunloadHandler(e))
}
```

