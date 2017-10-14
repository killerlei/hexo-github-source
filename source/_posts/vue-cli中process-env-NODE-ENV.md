---
title: vue-cli中process.env.NODE_ENV
date: 2017-10-08 17:25:32
tags: [ vue-cli, webpack,nodejs] 
categories: [front]
---
看前面vue-cli讲解的文章,对process.env.NODE_ENV,一直不理解是怎么设置的,怎么就变成production或者development
    
    // webpack.base.config.js
    output: {
        path: config.build.assetsRoot,
        filename: '[name].js',
        publicPath: process.env.NODE_ENV === 'production'
          ? config.build.assetsPublicPath
          : config.dev.assetsPublicPath
      },
      
      
通过看了不同的文章,大概有所了解.

请参考[process.env](http://cnodejs.org/topic/57a409657a922d6f358cd22d)

[改变运行脚本的环境变量](http://blog.404mzk.com/%E5%8C%BA%E5%88%86%E6%B5%8B%E8%AF%95%E5%92%8C%E6%AD%A3%E5%BC%8F%E7%8E%AF%E5%A2%83.html)

[业务代码如何判断生产/开发环境](http://array_huang.coding.me/webpack-book/chapter2/webpack-dev-production-environment.html)        

[DefinePlugin中的淫技巧](http://blog.csdn.net/sinat_17775997/article/details/70140322)

<!-- more -->        

### process.env
process对象用于处理与当前进程相关的事情，它是一个全局对象，可以在任何地方直接访问到它而无需引入额外模块。 它是 EventEmitter 的一个实例。

process.env 获取当前系统环境信息的对象，常规可以用来进一步获取环境变量、用户名等系统信息：

    console.log(process.env);
    console.log('username: ' + process.env.USERNAME);
    console.log('PATH: ' + process.env.PATH);

### webpack 开发和生产的区别
开发环境(development)和生产环境(production)的构建目标差异很大。

在开发环境中，我们需要具有强大的、具有实时重新加载(live reloading)或热模块替换(hot module replacement)能力的 source map 和 localhost server。

而在生产环境中，我们的目标则转向于关注更小的 bundle，更轻量的 source map，以及更优化的资源，以改善加载时间。

由于要遵循逻辑分离，我们通常建议为每个环境编写彼此独立的 webpack 配置。

分别为

webpack.config.js

webpack.dev.js

webpack.production.js

### 如何区分生产环境还是开发环境
引入process.env，这样就可以在业务代码中靠process.env.NODE_ENV来判断.

在webpack.base.config.js区分process.env.NODE_ENV来决定设置webpack配置为开发还是生产.
    
        // webpack.base.config.js
        output: {
            path: config.build.assetsRoot,
            filename: '[name].js',
            publicPath: process.env.NODE_ENV === 'production'
              ? config.build.assetsPublicPath
              : config.dev.assetsPublicPath
          },
 
      
###  process.env.NODE_ENV如何设置 

1. 因为process是nodejs全局变量,可以通过命令行设置
        
        
        export NODE_ENV = production && webpack
        
        export NODE_ENV = dev && webpack
        
2. 也可以通过package.json设置
        
        
            {
              "scripts": {
                "dev": "export NODE_ENV=dev&&webpack  --progress --colors",
                "production": "export NODE_ENV=production&&webpack  --progress --colors",
              },

3. 也可以借助webpack.DefinePlugin插件,在代码里面设置,如
          
             
                //webpack.dev.js
             plugins: [
                new webpack.DefinePlugin({
                  'process.env': config.dev.env
                }),
             
             //config/index.js
             dev: {
                 env: require('./dev.env'),
                 port: 8080,
    
            //config/dev.env.js
            var merge = require('webpack-merge')
            var prodEnv = require('./prod.env')
            
            module.exports = merge(prodEnv, {
              NODE_ENV: '"development"'
            })

但是在build.js中  process.env.NODE_ENV = 'production'  ,不知道这个和webpack.prod.js里面的有什么区别.
     