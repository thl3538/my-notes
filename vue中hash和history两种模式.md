# vue中hash和history两种模式

1. ##### hash

   hash即‘#/xxx’，是浏览器用来做页面定位的，例如a标签的描点功能，使用 window.location.hash 可以读取当前页面的hash值,也可以写入，hashchange事件可以响应hash的的改变，有两个特点： 1、改变hash值，浏览器不会重载页面，但会在历史访问里增加一条纪录 2、刷新重载页面时，hash值不会传给服务器端，浏览器访问的URL是http://wenyan.51easymaster.com/#/WYHome， 实际上向服务器发起请求的URL是http://wenyan.51easymaster.com/

   vue利用了浏览器的hash特性实现了前端路由（vue-router hash模式）。 当用户访问http://wenyan.51easymaster.com 时，服务器返回一个html文件（以及打包好的js文件和css文件），当访问 [wenyan.51easymaster.com/#/WYHome](http://wenyan.51easymaster.com/#/WYHome) 和http://wenyan.51easymaster.com/#/answerDetail 时浏览器就会根据hash值（#/WYHome 和 #/answerDetail）请求对应的JSON数据渲染对应页面。整个项目只有一个html文件，vue-router内部监听了hashchange事件响应hash值改变，根据hash值的不同渲染不同的视图。

   ##### history

   HTML5 History Interface 中history对象新增了两个方法 pushState() 和 popState()。 这两个方法应用于浏览器的历史记录栈，它们提供了对历史记录进行修改的功能。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会重载页面。

   vue-router的 history 模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。使用history模式，URL是http://wenyan.51easymaster.com/user/id 这个样子的，使用history模式要注意的是前端的 URL 必须和实际向后端发起请求的 URL 一致而且还需要后台配置支持，如访问http://wenyan.51easymaster.com/user/id 时如果后端缺少对 /user/id 的路由处理，将返回 404 错误。官方给出的方案是你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。具体请参阅[文档](https://router.vuejs.org/zh/guide/essentials/history-mode.html#后端配置例子)

   
   