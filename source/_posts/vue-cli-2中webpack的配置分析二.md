---
title: vue-cli-2中webpack的配置分析二
date: 2017-10-08 10:44:20
tags: [webpack,vue-cli] 
categories: [front]
---
这片也是转载,[叶家伟的博客](http://www.cnblogs.com/ye-hcj/p/7077796.html),这里有一系列的vue-cli中webpack分析文章,可以移步阅读.
另外还找到另外一片博客[vue-cli的webpack模板项目配置文件分析](http://blog.csdn.net/hongchh/article/details/55113751)
加上前面滴滴那篇介绍,对比读起来肯定会理解的快.
总之,网上有已有大量的学习资料,前人栽树,后人乘凉,感谢.
## config/index.js
        
        // see http://vuejs-templates.github.io/webpack for documentation.
        // path是node.js的路径模块，用来处理路径统一的问题
        var path = require('path')
        
        module.exports = {
            // 下面是build也就是生产编译环境下的一些配置
            build: {
                // 导入prod.env.js配置文件，只要用来指定当前环境，详细见(1)
                env: require('./prod.env'),
                // 下面是相对路径的拼接，假如当前跟目录是config，那么下面配置的index属性的属性值就是dist/index.html
                index: path.resolve(__dirname, '../dist/index.html'),
                // 下面定义的是静态资源的根目录 也就是dist目录
                assetsRoot: path.resolve(__dirname, '../dist'),
                // 下面定义的是静态资源根目录的子目录static，也就是dist目录下面的static
                assetsSubDirectory: 'static',
                // 下面定义的是静态资源的公开路径，也就是真正的引用路径
                assetsPublicPath: '/',
                // 下面定义是否生成生产环境的sourcmap，sourcmap是用来debug编译后文件的，通过映射到编译前文件来实现
                productionSourceMap: true,
                // Gzip off by default as many popular static hosts such as
                // Surge or Netlify already gzip all static assets for you.
                // Before setting to `true`, make sure to:
                // npm install --save-dev compression-webpack-plugin
                // 下面是是否在生产环境中压缩代码，如果要压缩必须安装compression-webpack-plugin
                productionGzip: false,
                // 下面定义要压缩哪些类型的文件
                productionGzipExtensions: ['js', 'css'],
                // Run the build command with an extra argument to
                // View the bundle analyzer report after build finishes:
                // `npm run build --report`
                // Set to `true` or `false` to always turn it on or off
                // 下面是用来开启编译完成后的报告，可以通过设置值为true和false来开启或关闭
                // 下面的process.env.npm_config_report表示定义的一个npm_config_report环境变量，可以自行设置
                bundleAnalyzerReport: process.env.npm_config_report
            },
            dev: {
                // 引入当前目录下的dev.env.js，用来指明开发环境，详见(2)
                env: require('./dev.env'),
                // 下面是dev-server的端口号，可以自行更改
                port: 8080,
                // 下面表示是否自定代开浏览器
                autoOpenBrowser: true,
                assetsSubDirectory: 'static',
                assetsPublicPath: '/',
                // 下面是代理表，作用是用来，建一个虚拟api服务器用来代理本机的请求，只能用于开发模式
                // 详见(3)
                proxyTable: {},
                // CSS Sourcemaps off by default because relative paths are "buggy"
                // with this option, according to the CSS-Loader README
                // (https://github.com/webpack/css-loader#sourcemaps)
                // In our experience, they generally work as expected,
                // just be aware of this issue when enabling this option.
                // 是否生成css，map文件，上面这段英文就是说使用这个cssmap可能存在问题，但是按照经验，问题不大，可以使用
                // 给人觉得没必要用这个，css出了问题，直接控制台不就完事了
                cssSourceMap: false
            }
        }
<!-- more -->        
## prod.env.js 
        
         module.exports = {
                // 作用很明显，就是导出一个对象，NODE_ENV是一个环境变量，指定production环境
                NODE_ENV: '"production"'
            }

## dev.env.js
        
         // 首先引入的是webpack的merge插件，该插件是用来合并对象，也就是配置文件用的，相同的选项会被覆盖，至于这里为什么多次一举，可能另有他图吧
            var merge = require('webpack-merge')
            // 导入prod.env.js配置文件
            var prodEnv = require('./prod.env')
            // 将两个配置对象合并，最终结果是 NODE_ENV: '"development"'
            module.exports = merge(prodEnv, {
                NODE_ENV: '"development"'
            })
            
## proxyTable用法
           
           vue-cli使用这个功能是借助http-proxy-middleware插件，一般解决跨域请求api
               proxyTable: {
                   '/list': {
                       target: 'http://api.xxxxxxxx.com', -> 目标url地址
                       changeOrigin: true, -> 指示是否跨域
                       pathRewrite: {
                       '^/list': '/list' -> 可以使用 /list 等价于 api.xxxxxxxx.com/list
                       }
                   }
               }
            
            