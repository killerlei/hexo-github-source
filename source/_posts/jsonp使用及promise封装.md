---
title: jsonp使用及promise封装
date: 2017-10-05 11:38:12
tags:
    -跨域
    -jsonp
    -promise
    -异步同步
---

1.
[jsonp](https://github.com/webmodules/jsonp)简单封装了跨域请求方法jsonp,利用script标签不受同源策略的限制,达到ajax的效果.

2.

        jsonp(url, opts, fn)

        url (String) url to fetch
        opts (Object), optional
            param (String) name of the query string parameter to specify the callback (defaults to callback)
            timeout (Number) how long after a timeout error is emitted. 0 to disable (defaults to 60000)
            prefix (String) prefix for the global callback functions that handle jsonp responses (defaults to __jp)
            name (String) name of the global callback functions that handle jsonp responses (defaults to prefix + incremented counter)
        fn callback
            The callback is called with err, data parameters.

        If it times out, the err will be an Error object whose message is Timeout.

        Returns a function that, when called, will cancel the in-progress jsonp request (fn won't be called).

    opts 中里面属性都有默认值,可以不用设置,但是注意param是前后端协议好的请求字段,默认值为callback,后端要根据这个字段拿到函数名,也就是另一个属性name,即jsonp原理中js中已经定义好的函数(jsonp内部会分配给全局对象对应属性,有name则是name值,如果没有则会根据prefix自动分配,并赋值fn).
<!-- more -->

3.这种用回调函数异步编程的方式可以利用ES6的promise实现同步编程,更加直观.
    举例来讲

    //构造函数接受两个参数,分别代表已完成/未完成两种状态变化,返回一各实例,可以在其then方法里传入成功时处理函数和失败时处理函数

        var promise = new Promise(function(resolve,reject){
                异步操作代码......
                resolve(value)
                reject(error)
        }
        promise.then(function(value){......},function(err){....})


4.利用promise改造jsonp

    import originJSONP from 'jsonp'
    export default function jsonp(url, data, option) {
      url += (url.indexOf('?') < 0 ? '?' : '&') + param(data)
      return new Promise((resolve, reject) => {
        originJSONP(url, option, (err, data) => {
          if (!err) {
            resolve(data)
          } else {
            reject(err)
          }
        })
      })
    }
    function param(data) {
      let url = ''
      for (var k in data) {
        let value = data[k] !== undefined ? data[k] : ''
        url += `&${k}=${encodeURIComponent(value)}`
      }
      return url ? url.substring(1) : ''
    }


5.使用(在vue中使用)

    import jsonp from './xxxxx'

    data(){
        return {
            data:''
        }
    },
    methods:{
        getData(){
            let url='**********'
            let data = {xxxxxxx:xxxxx}
            let option = {xxxxx:xxxxx}
            jsonp(url,data,option).then((res) => {
                 if (res.code === 0) {
                    this.data = res.data.list
                 }
            })
        }
    }


