





## vue3.0版本的开发

### 项目结构

![项目结构](https://github.com/wang-zhibiao/imageLib/blob/master/vue3.0项目结构.png?raw=true)

解析：项目存在public（公共） favicon.ico（网站图标） index.html(入口文件)

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width,initial-scale=1.0">
  <link rel="icon" href="<%= BASE_URL %>favicon.ico">
  <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
  <title>网站名</title>
</head>

<body>
  <noscript>
    <strong>We're sorry but advertisement doesn't work properly without JavaScript enabled. Please enable it to
      continue.</strong>
  </noscript>
  <div id="app"></div>
  <!-- built files will be auto injected -->
  <script src="https://cdn.bootcss.com/vue/2.5.17-beta.0/vue.runtime.min.js"></script>
  <script src="https://cdn.bootcss.com/vue-router/3.0.1/vue-router.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/moment.js/2.22.2/moment.min.js"></script>
  <script src="http://jira.finway.com.cn/vue-source/axios.min.js"></script>
  <script src="https://unpkg.com/element-ui/lib/index.js"></script>
</body>

</html>
```

此入口采用cdn引入外部的element-ui  axios  vue  vue-router moment 

### src

src作为项目的资源文件存放路径-包含以下的子文件夹

#### assets

存放images，JSON文件

调用时可以使用 路径 "@/assets/images/"+"图片名"

#### components（自定义公共组件）

如果你创建了一些公共组件 在整个项目中多次调用时，可以拆分为单独的公共组件

#### request（请求）/api

##### 对于axios的二次封装

```js
// 引入模块
import axios from "axios"
import router from '../router'
import {Message} from 'element-ui'
// 是否允许跨域
axios.defaults.withCredentials = true;
// axios初始化：延迟时间，主路由地址
let instance = axios.create({
    baseURL: process.env.VUE_APP_BASE_API,
    timeout: 10000,
});

// 设置拦截器
instance.interceptors.request.use(
    config => {
        config.headers = {      'Content-Type':'application/x-www-form-urlencoded'
    } 
        //在发送请求之前做某事
        return config;
    },
    error => {
        //请求错误时做些事
        Promise.reject(error)
    });
//响应拦截器
instance.interceptors.response.use(
    response => {
        //请求结束成功
        if (response.status === 200 || response.status === 304 || response.status === 201) {
            return response.data.data
        }
        return response;
    },
    error => {
        //请求错误时做些事
        if (error && error.response) {
            if (error.response.status === 400 || String(error).indexOf('code 400') !== -1) {
                Message.error(error.response.data.message)
                return Promise.reject(error.response.data)
            }
            if (error.response.status === 401 || String(error).indexOf('code 401') !== -1) {
                Message.error(error.response.data.message)
                // 函数进入then后面的第二个err函数，如果没有就进入catch函数, 表单提交就可以根据这个重置参数以及重置按钮状态，防止按钮卡滞
                // return Promise.reject(error)
            }
            if (String(error).indexOf('code 500') !== -1) {
                if (error.response.data.message) {
                    Message.error(error.response.data.message)
                } else {
                    Message.error('服务器出现问题，请刷新重试')
                }
                return Promise.reject(error)
            }
            // 终止Promise调用链
            return error
            // return new Promise(() => {})
        }
    });

// 是否销毁拦截器
// 1.给拦截器起个名称 var myInterceptors = instance.interceptors.requesst.use();
// 2.instance.interceptors.request.eject(myInterceptor);


//模块化导出
export default {
    get(url, params = {}) {
        if (!url) return;
        return instance({
            method: 'get',
            url: url,
            params,
            timeout: 30000
        })
    },
    post(url, data = {}) {
        if (!url) return;
        return instance({
            method: 'post',
            url: url,
            data,
            timeout: 3000000
        })
    },
    postFile(url, data) {
        if (!url) return;
        return instance({
            method: 'post',
            url: url,
            data
        })
    },
    put(url, data) {
        if (!url) return;
        return instance({
            method: 'put',
            url: url,
            data
        })
    },
    patch(url, data) {
        if (!url) return;
        return instance({
            method: 'patch',
            url: url,
            data
        })
    },
    delete(url, data) {
        if (!url) return;
        return instance({
            method: 'delete',
            url: url,
            data,
            timeout: 30000
        })
    },
}
```

在自定义的分类api中把请求路径进行分类

```js
import axios from "../axios";

function login(params) {
   return axios.post("/api/login", params);
}

function logout() {
    return axios.get("/api/logout");
}
export {
    login,
    logout
};
```

#### store

##### store-modules

定义 wang.js

```js
import {getRegionData,} from "@/request/api/addPlan";

const state = {
    City: [],
};

const getters = {};

const mutations = {
    setCity(state, payload) {
        state.City = payload;
    },
};

const actions = {
    async getCIty({
        commit
    }, payload) {
        const res = await getRegionData({
            access_token: payload.access_token
        });
        commit("setCity", res.data);
    },
};

export default {
    namespaced: true,
    state,
    getters,
    actions,
    mutations
};
```

注意必须在store-index.js(入口)引入

```js
import Vue from 'vue'
import Vuex from 'vuex'
import wang from './modules/wang'
Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    wang,
  },
  getters
})
```

##### 对于getters的使用

定义getters.js

```js
const getters = {
    City: state => state.wang.City,
}
export default getters
```

在页面.vue使用

```js
import { mapGetters } from "vuex";

export default {
computed: {
    ...mapGetters(["City"])
  },
 methods:{
	wang(){
		console.log(this.City)
    }
 }
}
```

#### styles(css样式)

分成element.css(修改UI的样式),index.css（主要的样式）,reset.css（样式初始化）

index.js

```css
@import './reset.css';
@import './icon.css';
@import './element.css';
```

在main.js引入

```js
//初始化css样式
import '@/styles/index.css'
```

#### utils(工具)

每个项目特有的工具函数，封装了对应项目需要的公共函数

#### views(视图)

包含Home.vue (主体视图窗口)  布局容器的使用及插槽

```js
<template>
  <div class="home">
    <!-- 主体框架 -->
    <el-container>
      <el-aside :style="isCollapse ? {width:0+'px'} : {width:200+'px'}">
        <Aside />
      </el-aside>
      <el-container>
        <el-header>
          <Header />
        </el-header>
        <!-- router-view插槽 -->
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template> 

<script>
import { mapGetters } from "vuex";
import Header from "../components/header";
import Aside from "../components/aside";
export default {
  computed: {
    ...mapGetters(["isCollapse"])
  },
  name: "home",
  data() {
    return {
      width: 200
    };
  },
  components: {
    Header,
    Aside
  },
};
</script>
<style lang="less" scoped>
.home {
  min-width: 1263px;
  height: 100%;
}
</style>
```

在views定义的模板.vue都需要在路由进行引入

### App.vue

只有一个路由插槽

```js
<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style>
</style>

```



### main.js(主要文件)

main用于外部资源的引入以及vue的全局绑定

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import axios from './request/axios'
import store from './store/index'
import ElementUI from 'element-ui' //element-ui的全部组件
import 'element-ui/lib/theme-chalk/index.css' //element-ui的css
Vue.use(ElementUI, {
  size: 'small',
  zIndex: 3000
})
//全局绑定axios
Vue.prototype.$http = axios;
//初始化css样式
import '@/styles/index.css'
Vue.config.productionTip = false
new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```



### router.js(路由)

```js
import Vue from 'vue'
import Router from 'vue-router'
import Home from './views/Home.vue'

Vue.use(Router)

//解决导航栏重复点击跳转报错报错
const originalPush = Router.prototype.push
Router.prototype.push = function push(location) {
  return originalPush.call(this, location).catch(err => err)
}
export default new Router({
  routes: [{
      path: '/',
      name: 'home',
      component: Home,
      redirect: '/popularize',
      children: [{
          path: '/popularize',
          name: 'popularize',
          component: resolve => require(['@/views/popularize/popularize.vue'], resolve)
        }
      ]
    },
  ]
})
```

在最新的vue-router中已经解决这个问题 可删除

```js
//解决导航栏重复点击跳转报错报错
const originalPush = Router.prototype.push
Router.prototype.push = function push(location) {
  return originalPush.call(this, location).catch(err => err)
}
```



路由的懒加载方式

```js
component: resolve => require(['@/views/popularize/popularize.vue'], resolve)
component: () => import('@/views/popularize/popularize.vue')

```

### .env.development（开发模式的环境变量）

在开发中有一些需要单独抽离的变量

api请求的baseUrl(注意命名必须前缀带VUE_APP__)

```js
VUE_APP_BASE_API ='http://dev.dsp.api.kuafugame.com'
```

在axios.js中使用

```js'
process.env.VUE_APP_BASE_API
```

### .env.production(上线后的环境变量，为打包所使用)

.gitignore(npm忽略清单)

```js
.DS_Store
node_modules
/dist

# local env files
.env.local
.env.*.local

# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Editor directories and files
.idea
.vscode
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?
```

在项目准备上线后，请配置好vue.config.js

### [脚手架配置](https://cli.vuejs.org/zh/config/)

#### 最基本的配置

```js
module.exports = {
    publicPath: process.env.NODE_ENV === "production" ? "./" : "/",
    // outputDir: 在npm run build时 生成文件的目录 type:string, default:'dist'
    // outputDir: 'dist',
    // pages:{ type:Object,Default:undfind }
    assetsDir: "./",
    productionSourceMap: false,
    devServer: {
        port: 80, // 端口号
        host: "127.0.0.1",
        https: false, // https:{type:Boolean}
        disableHostCheck: true,
        // open: true, //配置自动启动浏览器
        // proxy: 'http://dev.dsp.kuafugame.com/' // 配置跨域处理,只有一个代理
        // proxy: {
        //   "/api": {
        //     target: "<url>",
        //     ws: true,
        //     changeOrigin: true
        //   },
        //   "/foo": {
        //     target: "<other_url>"
        //   }
        // } // 配置多个代理
    },
};
```

#### 增加额外的配置

```js
//压缩代码的npm包
const CompressionPlugin = require("compression-webpack-plugin")
module.exports = {
    // 判断是在什么环境下运行
    publicPath: process.env.NODE_ENV === "production" ? "./" : "/",
    // outputDir: 在npm run build时 生成文件的目录 type:string, default:'dist'
    // outputDir: 'dist',
    // pages:{ type:Object,Default:undfind }
    assetsDir: "./",
    productionSourceMap: false,
    //修改端口号
    devServer: {
        port: 80, // 端口号
        host: "127.0.0.1",
        https: false, // https:{type:Boolean}
        //内网打通
        disableHostCheck: true,
        // open: true, //配置自动启动浏览器
        // proxy: 'http://dev.dsp.kuafugame.com/' // 配置跨域处理,只有一个代理
        // proxy: {
        //   "/api": {
        //     target: "<url>",
        //     ws: true,
        //     changeOrigin: true
        //   },
        //   "/foo": {
        //     target: "<other_url>"
        //   }
        // } // 配置多个代理
    },
    //合并一下webpack的配置
    configureWebpack: config => {
        //外部引入第三方库，不想打包进vender时
        config.externals = {
            'vue': 'Vue',
            'vue-router': 'VueRouter',
            'moment': 'moment',
            'axios': 'axios',
            'element-ui': 'ELEMENT',
        }
        //第三方插件--压缩代码
        config.plugins.push(new CompressionPlugin({
            test: /\.js$|\.html$|.\css/, //匹配文件名
            threshold: 10240, //对超过10k的数据压缩
            deleteOriginalAssets: false //不删除源文件
        }))
    },
    //使打包时路由不会预先加载
    chainWebpack: config => {
        config.plugins.delete("prefetch")
    },
};
```

