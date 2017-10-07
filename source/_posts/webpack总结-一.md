---
title: webpack总结(一)
date: 2017-10-07 12:24:53
tags:
    -webpack
---
# 前言

作为一个前端开发者,现在已经不可避免的要和webpack打交道了,尤其使用vuejs以后,项目上不可避免要使用集成webpack的vue-cli.

vue-cli基本上已经配置好了相关的webpack配置,基本上就可以开发了,不用开发人员再配置webpack,省时省力.

但是webpack还是要弄清除基本的配置,尤其在面试的时候不可避免被问到,我在网上看到好多讲解的文章,受益匪浅,但是总是看时明白,过两天就忘记了,望而复始,所以想总结一下,以便能有个地方可以随时查阅,下面会引用别人的文章,以及自己的理解,肯定会有错误之处,请多对比.

# webpack特点

是一个模块打包工具,不同于gulp,gulp只是一个构建工具,只执行相应的任务,比如说压缩/合并/检查/自动刷新等等,替代了人工操作,提高了开发人员的工作效率而已,不会对项目结构有所影响.

webpack会把js/css/image/html等文件都视作模块,根据模块依赖关系进行静态分析,然后将这些模块按照指定的规则生成对应的静态资源.不仅仅可以执行压缩/合并等任务,还会深度参与项目结构.而且可以根据需求生成多个打包js,可以异步加载,实现按需加载.

两个特点:
1. 一切皆模块

2. 按需加载

# webpack基本配置

可以子命令行执行  webpack .........来打包

一般专门配置webpack.config.js来方便的只在命令行执行webpack来打包.

实际开发中一般又分成webpack.dev.config.js和webpack.build.config.js,还可能有webpack.test.config.js.具体可以参考vue-cli,后面在具体讲解.


    const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
    const webpack = require('webpack'); //to access built-in plugins
    const path = require('path');
    
    const config = {
      entry: './path/to/my/entry/file.js',
      output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
      },
      module: {
        rules: [
          {test: /\.(js|jsx)$/, loader: 'babel-loader'}
        ]
      },
      plugins: [
        new webpack.optimize.UglifyJsPlugin(),
        new HtmlWebpackPlugin({template: './src/index.html'})
      ]
    };
    
    module.exports = config;

这是一个简单的示例,还有其他配置选项,下面一个一个来介绍.(因为webpack2配置方面有点改变比如,loader->rules等等,可能下面的会有所冲突)

## 生成source Maps(调试用)
需要在配置中设置
devtool:'source-map'  (总共有七个选项,不同选项,打包速度也快,但也越不利于调试,调试也是各坑,有的选项打不上断点或者断点在下一行)
    
## 构建本地服务器
只从有了nodejs以后,前端就可以用node在本地起服务,而不用在配置java服务.
而且webpack也是基于nodejs的.

1.npm 安装webpack-dev-server,这是一个基于express的webpack服务.

2.在配置中
    
        devServer:{
            contentBase:'./dist',
            colors:true,
            historyApiFallback:true,
            inline:true
        }

## loaders 
   通过不同的loader,对各种文件进行处理
   1. 安装
   2. 在配置的modules属性下进行配置
           
            
      module: {
          rules: [
            {
              test: /\.(js|vue)$/,
              loader: 'eslint-loader',
              enforce: 'pre',
              include: [resolve('src'), resolve('test')],
              options: {
                formatter: require('eslint-friendly-formatter')
              }
            },
            {
              test: /\.vue$/,
              loader: 'vue-loader',
              options: vueLoaderConfig
            },
            {
              test: /\.js$/,
              loader: 'babel-loader',
              include: [resolve('src'), resolve('test')]
            },
            {
              test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
              loader: 'url-loader',
              options: {
                limit: 10000,
                name: utils.assetsPath('img/[name].[hash:7].[ext]')
              }
            },
            {
              test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
              loader: 'url-loader',
              options: {
                limit: 10000,
                name: utils.assetsPath('media/[name].[hash:7].[ext]')
              }
            },
            {
              test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
              loader: 'url-loader',
              options: {
                limit: 10000,
                name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
              }
            }
          ]
        }
            
   必选属性:
   
   test: 匹配要处理的文件扩展名(正则表达式)
   
   loader而: 加载器
   
   可选属性:
   
   include: 手动添加必须处理的文件(文件夹)
   exclude: 手动屏蔽不需要处理的文件(文件夹)
      
   query: 提供额外的处置选项,也可以直接写在loader里(webpack2应该是改成options,如上面的配置)
   
   
        {test:/\.png|jpe?g|ico$/,
         loader:'url-loader',
         exclude:'/node-modles/',(举例而已,实际没有)
         query:{
            limited:10000,
            name: '[name].[ext]?[hash]'
         }
        }
        
        
## bable 
将ES6转化为ES5的包,会有好几个包,核心功能在bable-core这个包中
用的最多的是解析ES6的babel-prsent-es2015和解析jsx的bable-present-react
配置如下:
  
  
      loaders:[{
        test:/\.js$/,
        exclude:'/node_modules',
        laoder:'babel',
        query:{
            presets:['es2015','react']
        }
      }]
      
bable还有非常的配置选项,实际一般把配置选项放到'bablerc'这个单独的文件中,webpack会自动调用.