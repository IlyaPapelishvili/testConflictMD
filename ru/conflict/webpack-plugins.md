---
title: Как добавить плагины для веб-пакетов?
description: Как добавить плагины веб-пакетов в мое приложение Nuxt.js?
---

В вашем файле `nuxt.config.js` , под опцией `build` , вы можете передавать `plugins` веб-пакета, так же, как вы делали бы это в [файле `webpack.config.js`](https://webpack.js.org/configuration/plugins/) .

In this example we add the webpack built-in [ProvidePlugin](https://webpack.js.org/plugins/provide-plugin/) for automatically loading JavaScript modules (*lodash* and *jQuery*) instead of having to `import` or `require` them everywhere.

```js
import webpack from 'webpack'

export default {
  build: {
    plugins: [
      new webpack.ProvidePlugin({
        // global modules
        '$': 'jquery',
        '_': 'lodash'
      })
    ]
  }
}
```

> Примечание: вам может не понадобиться jQuery в приложении на основе Vue.

С помощью Nuxt вы также можете управлять контекстом выполнения плагинов: если они предназначены для запуска на `client` или в сборках `server` (или дифференцировать сборки `dev` и `prod` ) в [`build.extend`](/api/configuration-build#extend) , где вы также можете вручную передавать плагины веб-пакетов.
