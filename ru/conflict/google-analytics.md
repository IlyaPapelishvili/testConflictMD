---
title: Как использовать Google Analytics?
description: Как использовать Google Analytics в моем приложении NuxtJS?
---

Сначала проверьте [официальный модуль Google Analytics](https://github.com/nuxt-community/analytics-module) на наличие Nuxt.js.

В противном случае, чтобы использовать [Google Analytics](https://www.google.com/analytics/) с приложением Nuxt.js, мы рекомендуем вам создать файл `plugins/ga.js` :

```js
/* eslint-disable */

export default ({ app }) => {
  /*
  ** Only run on client-side and only in production mode
  */
  if (process.env.NODE_ENV !== 'production') return
  /*
  ** Include Google Analytics Script
  */
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','https://www.google-analytics.com/analytics.js','ga');
  /*
  ** Set the current page
  */
  ga('create', 'UA-XXXXXXXX-X', 'auto')
  /*
  ** Every time the route changes (fired on initialization too)
  */
  app.router.afterEach((to, from) => {
    /*
    ** We tell Google Analytics to add a `pageview`
    */
    ga('set', 'page', to.fullPath)
    ga('send', 'pageview')
  })
}
```

> Замените `UA-XXXXXXXX-X` на свой идентификатор отслеживания Google Analytics.

Затем мы сообщаем Nuxt.js импортировать его в наше основное приложение:

`nuxt.config.js`

```js
export default {
  plugins: [
    { src: '~plugins/ga.js', mode: 'client' }
  ]
}
```

Вуаля, Google Analytics интегрирована в ваше приложение Nuxt.js и будет отслеживать каждый просмотр страницы!

<div class="Alert Alert--nuxt-green">
</div>

<b>Информация:</b> вы можете использовать этот метод для любой другой службы отслеживания.



