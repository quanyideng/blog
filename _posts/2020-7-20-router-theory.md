---
layout: post
title: 前端路由原理及其实现
---



前端路由跳转原理实现起来其实不难，本质是通过监听 URL 的变化，然后匹配路由规则，在无须刷新页面的情况下，显示对应的页面。目前有两种实现方式：

* Hash 模式
* History 模式

## Hash 模式

`www.example.com/#/` 就是 Hash 路由，当 `#` 后面的哈希值发生变化时，可以通过 `hashchange` 事件来监听到 URL 的变化，从而进行跳转页面，并且无论哈希值如何变化，服务端接收到的 URL 请求永远是 `www.example.com`。

````
window.addEventListener('hashchange', () => {
  // ... 业务逻辑
})
````

Hash 模式相对来说更简单，并且兼容性也更好。

## History 模式

History 模式是 HTML5 新推出的功能，通过 `history.pushState` 和 `history.replaceState` 来改变 URL。

通过 History 模式改变 URL 同样不会引起页面的刷新，但会更新浏览器的历史记录。

````
// 新增历史记录
history.pushState(stateObject, title, URL)
// 替换当前历史记录
history.replaceState(stateObject, title, URL)
````

当用户点击后退按钮时会触发 `popState` 事件

````
window.addEventListener('popstate', e => {
  // e.state 就是 pushState(stateObject) 中的 stateObject
  console.log(e.state)
})
````

### 两种模式对比

- Hash 模式只可以更改 `#` 后面的内容，History 模式可以通过 API 设置任意的同源 URL
- Hash 模式无需后端配置，并且兼容性好。History 模式在用户手动输入地址或者刷新页面的时候会发起 URL 请求，后端需要配置 `index.html` 页面用于匹配不到静态资源的时候。比如当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了。