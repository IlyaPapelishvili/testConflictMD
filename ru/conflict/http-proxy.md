---
title: Как поделиться кросс-ресурсным ресурсом?
description: Как поделиться кросс источником происхождения?
---

[Решение перекрестного совместного использования ресурсов в Nuxt.js](https://github.com/nuxt-community/proxy-module#readme)

```
npm i @nuxtjs/proxy
```

В nuxt.config.js

```
 modules: [
      '@nuxtjs/axios',
      '@nuxtjs/proxy'
  ],
proxy: {
  '/api': {
    target: 'http://example.com',
    pathRewrite: {
      '^/api' : '/'
      }
    }
}
```
