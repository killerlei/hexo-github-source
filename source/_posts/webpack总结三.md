---
title: webpack总结三
date: 2017-10-07 21:05:18
tags: [webpack] 
categories: [front]
---

## 插件
webpack有很多内置插件,也有第三方插件,用来拓展webpack相关功能
如果是第三方插件,需要先安装

### HtmlWebpackPlugin
自动生成html插件,自动在dist目录下自动生成一个index.html
<!-- more -->
    
     //webpack.config.js
      var HtmlWebpackPlugin = require('html-webpack-plugin');
      module.exports={
        entry:'./index.js',
        output:{
          path:__dirname+'/dist',
          filename:'bundle.js'
        }
        plugins:[
          new HtmlWebpackPlugin()
        ]
      }
      
更多的配置
      
       plugins: [
          new HtmlWebpackPlugin({
            title: 'My App',
            filename: 'admin.html',
            template:'header.html',
            inject: 'body',
            favicon:'./images/favico.ico',
            minify:true,
            hash:true,
            cache:false,
            showErrors:false,
            "chunks": {
            "head": {
              "entry": "assets/head_bundle.js",
              "css": [ "main.css" ]
            },
            xhtml:false
          })
        ]

### extract-text-webpack-plugin
提取样式插件

        var ExtractTextPlugin = require("extract-text-webpack-plugin");
        new ExtractTextPlugin("[name].[hash].css")
        
                
## 优化插件
需要在生产打包时进行额外的处理,比如压缩js代码.
就要用到内置插件UgilifyJsPugin
    
        plugins:[
            new webpack.optimize.UglifJsPlugin()
        ]
        
在生产打包会有更多处理,在vue-cli中有以下配置
        
        new webpack.optimize.CommonsChunkPlugin({
              name: 'vendor',
              minChunks: function (module, count) {
                // any required modules inside node_modules are extracted to vendor
                return (
                  module.resource &&
                  /\.js$/.test(module.resource) &&
                  module.resource.indexOf(
                    path.join(__dirname, '../node_modules')
                  ) === 0
                )
              }
            }),
            // extract webpack runtime and module manifest to its own file in order to
            // prevent vendor hash from being updated whenever app bundle is updated
            new webpack.optimize.CommonsChunkPlugin({
              name: 'manifest',
              chunks: ['vendor']
            }),
            
            
总之,webpack配置还挺复杂,值得弄明白,听说以前还有专门的webpack岗位,下面还会就vue-cli这种专业的配置总结一下.
           