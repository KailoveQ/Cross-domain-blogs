![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa2bf51fba82481b8af5b29267b8fda3~tplv-k3u1fbpfcp-watermark.image)
> ## 重要知识点：每节博客都会重复🙉🙉🙉
> ### 口诀🤨：四基两空一对象，还有五个falsy值。
> #### 七种数据类型  
> * number string bool symbol
> * null undefined
> * object
> #### 五个 falsy 值
> * null undefined
> * 0 NaN
> * '' (空字符串)
# 跨域
* 什么是同源
* 什么是跨域
* JSONP 跨域
* CORS 跨域
**跨域**，就是跨越不同的区域，在解释跨域之前，我们先了解一个名词——**同源策略**

浏览器安全的基石是"同源政策"（[same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy)）。很多开发者都知道这一点，但了解得不全面。

本文详细介绍"同源政策"的各个方面，以及如何规避它。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/540ddb63b7ac454ba74bf705e9874de0~tplv-k3u1fbpfcp-watermark.image)
## 一、概述
### 1.1 含义
1995年，同源政策由 Netscape 公司引入浏览器。目前，所有浏览器都实行这个政策。

最初，它的含义是指，A网页设置的 Cookie，B网页不能打开，除非这两个网页"同源"。所谓"同源"指的是"三个相同"。

* 协议相同
* 域名相同
* 端口相同
举例来说，http://www.example.com/dir/page.html这个网址，协议是http://，域名是www.example.com，端口是80（默认端口可以省略）。它的同源情况如下。

* http://www.example.com/dir2/other.html：同源
* http://example.com/dir/other.html：不同源（域名不同）
* http://v2.www.example.com/dir/other.html：不同源（域名不同）
* http://www.example.com:81/dir/other.html：不同源（端口不同）
### 1.2 目的
同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据。

设想这样一种情况：A网站是一家银行，用户登录以后，又去浏览其他网站。如果其他网站可以读取A网站的 Cookie，会发生什么？

很显然，如果 Cookie 包含隐私（比如存款总额），这些信息就会泄漏。更可怕的是，Cookie 往往用来保存用户的登录状态，如果用户没有退出登录，其他网站就可以冒充用户，为所欲为。因为浏览器同时还规定，提交表单不受同源政策的限制。

由此可见，"同源政策"是必需的，否则 Cookie 可以共享，互联网就毫无安全可言了。

1.3 限制范围
随着互联网的发展，"同源政策"越来越严格。目前，如果非同源，共有三种行为受到限制。

（1） Cookie、LocalStorage 和 IndexDB 无法读取。

（2） DOM 无法获得。

（3） AJAX 请求不能发送。

虽然这些限制是必要的，但是有时很不方便，合理的用途也受到影响。下面，我将详细介绍，如何规避上面三种限制。

# 方案一：CORS 
>CORS 跨资源共享（Cross-origin resource sharing）
场景，假设我有两台服务器，都是我自己的，一个叫qq.com,一个叫frank.com,，我们简称为q服务器，和f服务器，现在我f服务器想要获取到q服务器中的friens.json的数据，怎么办呢？

>浏览器说，如果要共享数据，需要提前声明！

哦，那怎么声明呢？

浏览器说，qq.com在响应头里写frank.com可以访问

哦，具体语法呢？
```js
Access-Control-Allow-Origin:http://foo.example
```
浏览器说：都得文档里，去看[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS#%E7%AE%80%E5%8D%95%E8%AF%B7%E6%B1%82)嘛

下面展示我是怎么实现，f服务器也可以访问q服务器的 friends.json
```js
else if(path==='/friends.json'){
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/json;charset=utf-8')
    response.setHeader('Access-Control-Allow-Origin','http://frank.com:9999')   //加这一行
    response.write(fs.readFileSync('./public/friends.json'))
    response.end()
  }
```
对的，就这么简单，只需要服务器后台，在请求头里面加个这个
# 方案二：JSONP
在学习之前，先考虑一个问题。
### 为什么跨域跨域使用CSS，JS，和图片等等？
>答:同源策略限制的是数据访问，我们引用CSS、JS和图片的时候，其实并不知道里面的内容，我们只是在引用。
比如在html里面应用了jQuery的链接，却不知道里面的内容是啥？
你可以引用，你可以使用，但是你不知道里面是什么？
我先用script标签应用百度首页的一个js,然后用AJAX去请求他，把它打出来
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8723d1d7fe9d450ea5460574ea000b3d~tplv-k3u1fbpfcp-watermark.image)
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8460a427733a479ab6e7bd21f62b32ab~tplv-k3u1fbpfcp-watermark.image)
你可以引用他的js,但是你获取不了他的数据
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/689e1311c11b425ea0251ac7afc5717a~tplv-k3u1fbpfcp-watermark.image)
## 简易版本
前辈们想，那我直接打电话给我另一家公司的后台，让他们给我吧friends.json的数据给我放到js里面，我再去调用，那不就可以打出来了么？

好，注意哦，我们把JS变成数据，数据里面包含着我们想要的东西
### 步骤
* frank.com访问qq.com
1. qq.com将数据写到/friends.js
2. frank.com用script标签弓|用/friends.js
3. /friends.js执行，执行什么呢？
4. frank.com事先定义好window.xxx函数
5. /friends.js执行winds。xxx({friends:[...]})
然后frank.com就通过window.xxx获取到数据了

window.xxx就是一个回调啊！

这TM都能想到，人才啊！
这是很多前端工程师一起想出来的。
代码示例：
```js
//server.js  qq的里面写
else if(path==='/friends.js'){
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
    const string=fs.readFileSync('./public/friends.js').toString()
    const data = fs.readFileSync('./public/friends.json').toString()
    const string2 = string.replace('{{data}}',data)
    response.write(string2)
    response.end()
  }
//qq里面还需要写一个与friends.json对应的friends.js  
//但是window.xxx他妈的很可以被占用，那就用函数的方式,这两种选一个就好了
window.xxx= {{data}}       //赋值版本
window.xxx( {{data}} ) 		//函数版本
//可以是JSON 的格式 也可以是xml 的格式

//黑客网站  farank.js
const script= document.createElement('script')    //赋值版本
script.src = 'http://qq.com:8888/friends.js'
script.onload=()=>{
    console.log(window.xxx)
}
document.body.appendChild(script) 

？？？？？？？？？？？？？？？？？？？？？
window.xxx = (data)=>{
    console.log(data)
}
const script= document.createElement('script')
script.src = 'http://qq.com:8888/friends.js'
document.body.appendChild(script) 
```
### 那JSONP 岂不是人人可以访问，可以检查referer，是否包含允许url
![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/97740e858d104ddd980f20822728bc44~tplv-k3u1fbpfcp-watermark.image)
### 那我现在不只要获取好友，我还有获取，动态，生日等等其他信息
那我每次都得起一个XXX 的名字吗，能不能不重复而且还自动生成

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/92bce76d78eb4c2fbcdde989ccdbfda1~tplv-k3u1fbpfcp-watermark.image)

### 可以，我们叫随机数字
先用占位符先站着，让后服务器获取到我们给他发的请求，然后就可以通过这个函数，获取到请求名字，然后吧friends.js 的名字替换为我们随机生成的名字
```js
// console.log(query.functionName)
```
frank.js 这样写:
```js
const random ='frankJSOPCallbackName'+Math.random()
console.log(random)
window[random]=(data)=>{
    console.log(data)
}
const script= document.createElement('script')
script.src = `http://qq.com:8888/friends.js?functionName=${random}`
document.body.appendChild(script) 
script.remove()
```
friends.js这样写：

```js
window['{{xxx}}'] ( {{data}} ) 
```

后台server这样写:
```js
else if(path==='/friends.js'){
      response.statusCode = 200
      // console.log(query.functionName)
      response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
      const string=fs.readFileSync('./public/friends.js').toString()
      const data = fs.readFileSync('./public/friends.json').toString()
      const string2 = string.replace('{{data}}',data).replace('{{xxx}}',query.functionName)
      response.write(string2)
      response.end()
    }
```
## 封装
>###  初级程序员学API,中级程序员学封装
封装为以下格式：
```js
jsonp('url').then(f1,f2)
```
```js
function jsonp(url){
    return new Promise((resolve,rejects)=>{
        const random ='frankJSOPCallbackName'+Math.random()
        window[random]=(data)=>{
            resolve(data)
        }
        const script= document.createElement('script')
        script.src = `${url}?functionName=${random}`
        script.onload=()=>{
            script.remove()
        }
        script.onerror=()=>{
            rejects()
        }
        document.body.appendChild(script)
    })
}
```
调用方式：
```js
//使用
jsonp('http://qq.com:8888/friends.js')
.then((data)=>{
    console.log(data)
})
```
### 约定俗成
所有前端在写JSONP 的这个函数名字，就叫callback,而且后端在实现的时候，也会默认的写成
> script.src = `${url}?callback =${random}`

> const string2 = string.replace('{{data}}',data).replace('{{xxx}}',query.`callback`)

而且我们friends.js 里面的内容```window['{{xxx}}'] ( {{data}} )  ```,就这么点，而且大部分都是要被替换的，我们把他直接写在后台里面不就好了
```js
else if(path==='/friends.js'){
      response.statusCode = 200
      // console.log(query.functionName)
      response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
      //const string=fs.readFileSync('./public/friends.js').toString()
      const string= `window['{{xxx}}'] ({{data}})`
      const data = fs.readFileSync('./public/friends.json').toString()
      const string2 = string.replace('{{data}}',data).replace('{{xxx}}',query.functionName)
      response.write(string2)
      response.end()
    }
 ```
 ![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ef0f81ddc15740259ee607955f23903e~tplv-k3u1fbpfcp-watermark.image)
 
 至此，JSONP 的优化就已经全部结束了！
 ### [源文件链接](https://github.com/KailoveQ/JSONP-1.git)
 ```js
 substring(pathWithQuery.indexOf('?')) }
  var path = parsedUrl.pathname
  var query = parsedUrl.query
  var method = request.method
 ```
# 完美回答面试官 JSONP 是什么
## 回答要分为3部分
答：概念:我们在跨域的时候，由于当前浏览器不支持CORS,或者因为某些条件不支持CORS,我们必须使用另一种方式跨域，于是我们就请求一个js文件，这个文件会执行一个回调，回调里面有就我们数据。

那你这个回调的名字叫什么：名字是可以随机生成的一个随机数，我们把这个随机数以callback的参数传给后台，后台会把函数再次返回给我们并执行

原理：创造一个`<script>` 请求js，js把数据夹带过来，然后我们去拿到这个js的数据：

优点：兼容ie,跨域
缺点：只能发GET，拿不到状态码和响应头

由于它是` <script> `标签，他无法读到像AJAX那样精确的状态。拿不到状态码，也不知道响应头，只能知道是成功还是失败。而且监听的是onland和onerror 这两个事件，由于它是` <script> `标签，所以只能发get请求。