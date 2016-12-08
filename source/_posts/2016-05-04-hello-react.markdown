---
title: 'Hello React'
layout: post
tags:
    - react
    - npm
    - webpack
---

## 需求
我在react还是0.x版本的时候，走过一遍react的Getting Started，目前虽然已经写了一些代码，但是完完整整的Hello World倒是没写过，所以决定补下这篇Hello World教程，并做一些相关知识的入门介绍。


## 参考内容
如果你能看得懂react官网的介绍，其实也没必要看我这篇转述的教程。

[React官方 Getting Started](https://facebook.github.io/react/docs/getting-started.html)  

[CDN静态文件（为什么不用Facebook的CDN大家都懂的）](https://cdnjs.com/libraries/react/)  

[Package Management](https://facebook.github.io/react/docs/package-management.html#using-react-from-npm)  

## 最简单的Hello World
单个静态文件，并使用CDN直接引用react.js、react-dom.js以及browser.min.js

```javascript
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.0.2/react.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/15.0.2/react-dom.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world! @eden</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

## React + npm + webpack

#### 1. 创建一个目录hello-react
之后的命令行操作都是在这个目录下进行的。

#### 2. npm init一个package.json文件，全部使用默认即可
```shell
npm init
```

#### 3. 配置npm依赖
这里的webpack我并没有使用全局安装，下面的步骤会用到

```shell
npm install --save react react-dom babel-preset-react babel-loader babel-core webpack
```

#### 4. 在hello.html和hello.js中写代码

##### hello.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <!-- No need for Babel! -->
  </head>
  <body>
    <div id="example"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

##### hello.js
```javascript
// hello.js
var React = require('react');
var ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world! I'm eden</h1>,
  document.getElementById('example')
);
```

#### 5. 用webpack生成bundle.js

##### 创建并配置babel：.babelrc

```
 { "presets": ["react"] }
```

##### webpack打包，使用本地目录下的webpack，而不是全局的
```
./node_modules/webpack/bin/webpack.js hello.js bundle.js --module-bind 'js=babel-loader'
```

这样我们就在hello-react的根目录下得到了下面这几个文件和目录：

```
./.babelrc
./bundle.js
./hello.html
./main.js
./node_modules
./package.json
```

#### 6. OK了
直接用浏览器打开./hello.html就是我们想要的用npm和webpack打包出来的，最简单的react hello worl

## webpack配置
上面我们使用了webpack的命令行进行打包，其实用npm配合webpack会更方便，同时我们也调整下我们的目录结构，把hello.js放到src目录下，并把打包生成的bundle.js,以及hello.html放到build目录下。


### 添加一个webpack.config.js
```
var path = require('path');

var config = {
    entry: [
        path.resolve(__dirname, 'src/hello.js')
    ],
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js',
    },
    module: {
        loaders: [{
            test: /\.jsx?$/,
            exclude: /node_modules/, 
            loader: 'babel',
            query: {}
        }]
    }
};

module.exports = config;
```

### 修改下package.json添加一个build的script

```
{
  "name": "hello-react",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "babel-core": "^6.8.0",
    "babel-loader": "^6.2.4",
    "babel-preset-react": "^6.5.0",
    "react": "^15.0.2",
    "react-dom": "^15.0.2",
    "webpack": "^1.13.0"
  }
}
```

### 通过npm来执行webpack打包
```
npm run-script build
```
这样我们就在hello-react的根目录下得到了下面这几个文件和目录：

```
./.babelrc
./build/bundle.js
./build/hello.html
./package.json
./src/hello.js
./webpack.config.js
```

### 好了，可以通过打开hello.html来查看下我们的成果了

### webpack 小结
再往后，你可能就需要用webpack-dev-server来自动刷新页面，这个本文就不具体介绍了，因为webpack可以做的事情非常多，大家慢慢去发现吧 :)

这里贴下在上面的步骤之后配置webpack-dev-server的方法：

```
npm install --save webpack-dev-server
```

#### webpack.config.js
```
var path = require('path');

var config = {
    entry: [
        'webpack/hot/dev-server',
        'webpack-dev-server/client?http://localhost:8080',
        path.resolve(__dirname, 'src/hello.js')
    ],
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'bundle.js',
    },
    module: {
        loaders: [{
            test: /\.jsx?$/,
            exclude: /node_modules/, 
            loader: 'babel',
            query: {}
        }]
    }
};

module.exports = config;
```

#### package.json
```
{
  "name": "hello-react",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "dev": "webpack-dev-server --devtool eval --progress --colors --hot --content-base build"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "babel-core": "^6.8.0",
    "babel-loader": "^6.2.4",
    "babel-preset-react": "^6.5.0",
    "react": "^15.0.2",
    "react-dom": "^15.0.2",
    "webpack": "^1.13.0",
    "webpack-dev-server": "^1.14.1"
  }
}
```

#### 执行脚本：
```
npm run-script dev
```

然后你就可以在浏览器上用http://127.0.0.1:8080/hello.html自动刷新你的修改了


## 关键字说明
[babel](https://babeljs.io/)

[webpack](http://webpack.github.io/)

[babel 6 教程](https://www.zfanw.com/blog/babel-6.html)

