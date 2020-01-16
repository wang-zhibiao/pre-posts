### webpack的安装

##### 全局安装

```js
npm i webpack webpack-cli -g
```

##### 项目安装

```js
npm i webpack webpack-cli -D
```

##### 项目初始化

```js
npm init -y
```

##### 项目中的webpack执行打包(npx)

```js
npx webpack
```

es6 导入导出规范

```js
let wang = require('./wang.js')
export default {}
```

commonJS（cmd）导入导出规范

```js
import wang from './wang.js'
module.exports = {} // 暴露对象
```

 四个**核心概念**：

- 入口(entry)：程序的入口 就是你想要打包的目录
- 输出(output)：打包后的文件位置
- loader：对于你想要打包的文件进行转换的配置
- 插件(plugins)：解决loader无法实现的事情

新建文件**webpack.config.js**

```js
const path = require('path');

module.exports = {
  entry: './src/index.js', //打包的入口
  output: {
    path: path.resolve(__dirname, 'dist'), //输出路径
    filename: 'bundle.js' //打包的文件
  }
     mode: 'production' // 打包模式为生产 或者development开发模式
};
```

__dirname  当前路径

path.resolve(__dirname, 'dist') 解析当前路径的绝对路径

path.join(__dirname, 'dist') 拼接当前路径的绝对路径

指定使用哪个文件进行打包  一个开发模式  一个生产模式

```js
npx webpack --config webpack.custom.config.js // 执行mode为development开发模式
```

快捷命令输出

```js
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
     "build":"webpack --config webpack.custom.config.js"
  }
//执行时就快捷输入   npm run build
```

##### 使用webpack-dev-server(推荐)

会开启服务器

安装 

```js
npm i webpack-dev-server -D
//package.json
 "dev": "webpack-dev-server"
```

自动编译完成时的地址是127.0.0.1：8080/

打包后的文件在内存中

配置dev

```js
const path = require('path');
module.exports = {
    devServer: {
        open: true, //自动打开
        port: 3000, //端口号
        // compress: true, //是否压缩
        // contentBase: './src', //指定路径
        hot: true, // 开启热更替，便于代码更新，快速补充，不需要再次打包生成
    }
};
```

html-webpack-plugin插件(把html打包---服务器使用)

```js
npm i html-webpack-plugin -D
```

html-webpack-plugin插件使用

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    plugins: [
        new HtmlWebpackPlugin({
            filename: "index.html",
            template: "./src/index.html"
        })
    ]
};
```

会自动引入js文件

### loader使用

#### 处理css文件

```js
//安装
npm i css-loader style-loader -D
//配置
module.exports = {
    //模块
  module: {
    rules: [
      {
         // webpack解析文件是从右到左
        test: /\.css$/i,//正则匹配.css文件
        use: ['style-loader', 'css-loader'], // csss-loader 解析css文件 style-loader 将解析的文件放入html文件中  
      },
    ],
  },
};
```

#### 处理less和sass

```js
//安装
npm i less less-loader sass-loader node-sass -D
//配置
 { test: /\.less$/, use: ['style-loader','css-loader', 'less-loader',], },
 {test: /\.s[ac]ss$/i,use: ['style-loader','css-loader', 'sass-loader',],},
```

处理img（图片）和 字体图标

```js
//安装
npm i file-loader -D
//配置
{test: /\.(png|jpeg|gif|bmp|jpg)$/,use: "file-loader"},
```

#### 处理字体图标

```js
{test: /\.(woff|woff2|eot|svg|ttf)$/,use: "file-loader"},
```

#### 处理图片的其他高级功能（url-loader）

```js
//基于file-loader安装
npm i url-loader -D
//配置
{test: /\.(png|jpeg|gif|bmp|jpg)$/,use: {loader:'url-loader',options:{
    limit:5*1024, //如果图片小于5kb大小就转换为base64格式
    outputPath:'images', //打包的路径
    name:'[name]-[hash:8].[ext]', //原图片的名字.尾缀名  [hash:数字]=>哈希名字  ：后保留几位
}}},
```

#### 处理babel（兼容js）

```js
//安装  @babel/core核心   @babel/preset-env 预设语法包
npm install babel-loader @babel/core @babel/preset-env webpack -D
//配置
{
      test: /\.js$/,
     // exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env'], //语法预设
          plugins: ['@babel/plugin-proposal-class-properties'] //语法的插件，想要哪个就下载哪个
        }
      	 exclude: /node_modules/   //匹配正则忽略这个文件
    }
}
```

处理generator函数

```js
npm install --save-dev @babel/plugin-transform-runtime
npm install --save @babel/runtime
//配置
{"plugins": ["@babel/plugin-transform-async-to-generator"]},
```

### babelrc  

分配好具体的配置在哪





