



## 函数防抖（debounce）

> 所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间。

```js
let timeout = null
export const debounce = (func, delay) => {
    //如果定时器不等于null就清除定时器
    if (timeout !== null) clearTimeout(timeout)
    //设置定时器延时执行函数
    timeout = setTimeout(func, delay)//如果在这个delay时间内执行了setTimeout，否者就会被下一个执行所清除，这样就是一个接一个进行执行，但是只执行最后一个
}
```

## 函数节流(Throttle)

>所谓节流，就是指连续触发事件但是在 n 秒中只执行一次函数。节流会稀释函数的执行频。就相当于，一个水龙头在滴水，可能一次性会滴很多滴，但是我们只希望它每隔 500ms 滴一滴水，保持这个频率。即我们希望函数在以一个可以接受的频率重复调用。

```js
//初始化函数
let timer = null
let start = 0
export const Throttle = (func, wait, trailing) => {
    //在函数调用时，获取时间戳
    let now = new Date()
    //判断剩下的等待时间
    let remaining = wait - (now - start)
    console.log(remaining)
    // 拦截：延迟时间>多次执行间隔时间，执行：多次执行间隔时间>延迟时间
    if ((now - start) >= wait) {
        // 由于setTimeout存在最小时间精度问题，因此会存在到达wait的时间间隔
        // 但之前设置的setTimeout操作还没被执行，因此为保险起见，这里先清理setTimeout操作
        timer && clearTimeout(timer)
        timer = null
        start = now
        console.log('非频繁操作，点击间隔为：', remaining)
        //返回回调的函数-》不管this指向
        return func()
    } else if (!timer && trailing) {
        // trailing有值时，延时执行func函数
        // 频繁操作，第一次设置定时器之后，之后的不会再走到这里创建定时器
        // 清除问题，只能在第二有效点击的时候才会清除
        timer = setTimeout(() => {
            timer = null
            console.log('频繁操作，定时器延时执行')
            return func
        }, wait)
    }
}
```

