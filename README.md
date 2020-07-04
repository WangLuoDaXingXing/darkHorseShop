# vue_shop
(项目教程及后台接口数据来至B站“黑马程序员”)
以下是对项目开发过程中，不太熟悉的地方做的笔记
## 项目运行
首先将Vue项目跑起来
+ 在控制台输入
    > vue ui 
+ 在打开的ui面板中的,任务->servce启动项目
+ 在终端使用命令，将API服务器跑起来
    > F:\I LOVE LEARNING\Vue\项目实战\vue电商后台实战课件及源码\3.vue-项目实战day1\素材\vue_api_server\vue_api_server>node .\app.js  
    没有安装依赖包的话需要先安装,命令为  
    > npm install  

相关接口信息在“电商管理后台API接口文档”中
## 登录业务
+ HTTP是无状态的
+ 通过cookie在客户端记录状态
+ 通过session在服务器记录状态
+ 通过token方式维持状态
    - 当前端与后台接口存在跨域问题时，使用token来维持登录状态。这里token主要用来做客户端与服务器之间的身份校验。其原理如下
    > 1) 前端输入用户名和密码并发送到服务器；  
    > 2) 服务器验证通过之后，生成该用户的token并返回给前端；  
    > 3) 前端存储获取到的token，后续所有的请求都携带该token发送请求；
    > 4) 服务器验证token是否通过，以确定作何响应。

## 路由导航守卫控制访问权限
如果用户没有登录，但是直接通过URL访问特定页面，则需要重新导航到登录页
```JavaScript
// 为路由对象添加beforeEach导航守卫
router.beforEach((to, from, next) => {
    // 如果用户访问登录页，直接放行
    if(to.path === '/login') return next()
    // 从sessionStorage中获取到保存到token值
    const tokenStr = window.sessionStorage.getItem('token')
    // 没有token，强制跳转到登录页
    if(!tokenStr) return next('/login')
    next()
})
```
## axios拦截器保证拥有获取数据的权限
axios中有一个interceptors属性，该属性中有一个request拦截器，我们可以通过use函数为请求拦截器挂载一个回调函数。每当客户端通过axios发送请求时，都会优先调用use的回调函数，即，请求在到达服务器之前都会做一些处理。相当于在服务器之前，预处理我们的axios请求。
```JavaScript
// axios请求拦截器
axios.interceptors.request.use(config => {
  console.log(config)

  // 在请求头中添加一个Authorization属性，key值是token值。
  config.headers.Authorization = window.sessionStorage.getItem("token")
  // 在最后必须return config
  return config
})

```
