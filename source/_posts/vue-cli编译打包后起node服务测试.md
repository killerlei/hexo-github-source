---
title: vue-cli编译打包后起node服务测试
date: 2017-10-08 14:37:32
tags:
    - vue-cli
    - node
    - vue
    - webpack
---
使用vue-cli开发项目中,一直使用dev-server.js这个本地服务,实现从内存中读取文件.

项目完成后,要编译打包生成打包文件,提示不能通过打开file//方式访问,需要另外起服务访问.

但是打包好后,不可能立即放到真实服务器中,还需要通过在本地测试一下,故另开一个node服务.

### 原因
因为打包后生成的文件如script/link等路径都是绝对路径,当然找不到了.

### 解决1 修改配置
如果弄明白vue-cli中webpack的配置,下面这个肯定明白,就是把绝对路径形式改成相对路径,但是不知道对放到真实服务器上有没影响.
<!-- more -->        

       这里有一个万能解决办法：
           1. 将config/index.js 里面的 assetsPublicPath:'/' 改为assetsPublicPath:'./'  
           2. build/util.js里面的
           if (options.extract) {
                     return ExtractTextPlugin.extract({
                                   use: loaders,
                                   fallback: 'vue-style-loader',
                                   publicPath:'/'
           })
           将其中的publicPath改为：publicPath：'../../'就可以了。这样打包出来的路径就是正确的了。
       
           第一个是为了改变js中引入图片的路径，改为./ 就是指在当前路径，这个.代表的路径就是assetsRoot+assetsSubDictionary，我这里定位到dist/static/ ，加上图片前缀img，就可以找到了。
           第二种是为了改变vue文件中使用style样式里面例如background:url('xxx')，这样的路径，因为style最终变成css文件在dist/static/css里面，我们的图片放在dist/static/img中，那么加上../../变成dist目录下，默认的目录前缀是static，img是图片默认前缀，这样就可以定位到图片。


### 解决2 起一个node服务,并定位静态资源入口
1.新建build/prod-server.js
    
    var express = require('express')
    
    var app = express()
    var opn = require('opn')
    var path = require('path')
    
    var distPath = path.join(__dirname,"../dist");
    //静态资源目录入口
    app.use(express.static(distPath));
    
    module.exports = app.listen(8081, function (err) {
        if (err) {
            console.log(err)
            return
        }
        var uri = 'http://localhost:' + 8081+"/"
        console.log('Listening at ' + uri + '\n')
        opn(uri)
    })

直接在项目目录命令行运行 node  prod-server.js  就可以访问了

2 也可以在package.json中设置

        "scripts": {
            "dev": "node build/dev-server.js",
            "build": "node build/build.js",
            "local-server": "node build/prod-server.js"
          },
          
这样也可以直接  npm run local-server             