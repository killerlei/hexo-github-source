---
title: vue开发跨域解决办法
date: 2017-10-15 14:58:20
tags: [跨域, 代理] 
categories: [front]
---
# 跨域

是前端开发绕不开的一个话题,尤其在前后端分离的大趋势下,更加变得家常便饭,比如vue本地开发,调用接口,就要跨域.

前面几篇文章,也有所设计跨域相关指示,这里总结一下,前端使用vue开发解决跨域的问题.

1.因为跨域是浏览器的安全策略,在chorme浏览器中,可以在快捷图标中设置 --disable-web-security,非常方便.但是在最新chorme中还要稍加复杂的设置.

2.使用chorme插件[Allow-Control-Allow-Origin](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi?utm_source=chrome-app-launcher-info-dialog),需要翻墙,实际用起来,效果还不错.偶尔失效.

3.使用jsonp ,前面有文章[jsonp使用及promise封装](https://killerlei.github.io./2017/10/05/jsonp%E4%BD%BF%E7%94%A8%E5%8F%8Apromise%E5%B0%81%E8%A3%85/).

4.使用node代理,如[使用代理解决跨域限制](https://killerlei.github.io./2017/10/06/%E4%BD%BF%E7%94%A8%E4%BB%A3%E7%90%86%E8%A7%A3%E5%86%B3%E8%B7%A8%E5%9F%9F%E9%99%90%E5%88%B6/)

5.使用vue-cli中配置的插件webpack-dev-middleware,实现代理.这要是本文要写的.原理同4,都是使用nodejs做个代理,只是过程不同.

dev.server.js

    var devMiddleware = require('webpack-dev-middleware')(compiler, {
    publicPath: webpackConfig.output.publicPath,
        stats: {
            colors: true,
            chunks: false
        }
    })
    // proxy api requests
    Object.keys(proxyTable).forEach(function (context) {
        var options = proxyTable[context]
        if (typeof options === 'string') {
            options = { target: options }
        }
        app.use(proxyMiddleware(context, options))
    })

config/index.js

    dev: {
        env: require('./dev.env'),
        port: 8081,
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        /*assetsSubDirectory: '/xxxxx',
        assetsPublicPath: '',*/
        proxyTable: proxyConfig.proxyList,
        cssSourceMap: false
     }

config/proxyConfig.js

    module.exports = {
                        proxyList: {
                                '/apiDev': {
                                    //真实跨域接口
                                    target: 'http://xxxxx.com',
                                    changeOrigin: true,
                                    pathRewrite: {
                                        '^/apiDev': ''
                                    }
                                }
                        }
    }
在开发中使用接口如 /apiDev/getAll,会被代理到http://xxxxx.com/getAll.

开发完成后,要上测试或者生产,在换成http://xxxxx.com/getAll. 这部分也可以通过配置实现自动切换,以后会写到.