---
title: webpack总结二
date: 2017-10-07 16:41:33
tags:
    -webpack
---
### css
webpack提供了两个工具处理样式表 --> css-loader 和 style-loader
css-loader 可以使用类似@import或url(...)的方法实现require的功能
style-loader将所有计算后的样式加入到页面中
两者缺一不可(多个加载器中间用!分割,从右向左执行,也可写成数组形式)
    
    {test:/\.css$/,
    loader:'style!css'  
    //也可以写成
    loader:['style','css']
    }
<!-- more -->

### css预处理
比如 less-loader/sass-loader/sylus-loader
以less为例

先npm i less-loader  --dev
    
    
    {
            test: /\.(less|css)$/,
            use:[ 'style-loader','css-loader','less-loader'],
    },
    
    
### postcss  
有好几个功能,这里只介绍为css代码添加前缀来适应不同的浏览器
 先安装postcss-loader 和 依赖的插件autoprefixer
 在配置中(webpack2中配置方式好像变化了,注意)
 
    module: {
            rules: [
              {test: /\.css$/, loader: 'style!css!postcss'}
            ]
          },
    postcss: [require('autoprefixer')]  //声明依赖的插件   

    
### entry(入口)
可以是字符串,数组,对象
1. 只有一个入口
2. 添加彼此互不依赖文件用数组,如['./xx/a.js', './xx/b.js'],
最后打包时会在bundle.js后面添加b.js
3. 如果是多页面应用(非spa),则为每个页面生成一个bundle文件,


    entry:{
            'indexEntry' : './src/index,js',
            'pageAEntry' : './src/pageA.js'
           },
    output:{
            path:'./dist',
            filename:'[name].js' //name取自entry的属性键名
          }  
           

            
### output(输出)            
两个选项

    
    output: {
            path: './dist',   //webpack打包后文件存放位置
            publicPath: 'http://cdn...'  //生产环境下  静态资源访问地址,比如要把打包后的文件放到cnd上,就可以配置这个选项
            }