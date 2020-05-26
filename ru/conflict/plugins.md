---
title: Плагины
description: Nuxt.js позволяет вам определить плагины JavaScript, которые будут запускаться перед созданием корневого приложения Vue.js. Это особенно полезно при использовании ваших собственных библиотек или внешних модулей.
---

> Nuxt.js позволяет вам определить плагины JavaScript, которые будут запускаться перед созданием корневого приложения Vue.js. Это особенно полезно при использовании ваших собственных библиотек или внешних модулей.

<div class="Alert">
</div>

It is important to know that in any Vue [instance lifecycle](https://vuejs.org/v2/guide/instance.html#Lifecycle-Diagram), only `beforeCreate` and `created` hooks are called **both, from client-side and server-side**. All other hooks are called only from the client-side.




## Внешние пакеты

Возможно, мы захотим использовать внешние пакеты / модули в нашем приложении (один отличный пример - [axios](https://github.com/mzabriskie/axios) ) для выполнения HTTP-запроса как для сервера, так и для клиента.

Во-первых, мы должны установить его через npm:

```bash
npm install --save axios
```

Тогда мы можем использовать это непосредственно в наших компонентах страницы:

```html
<template>
  <h1>{{ title }}</h1>
</template>

<script>
import axios from 'axios'

export default {
  async asyncData ({ params }) {
    let { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
</script>
```

## Vue плагины

Если мы хотим использовать плагины Vue, такие как [vue-уведомления,](https://github.com/se-panfilov/vue-notifications) для отображения уведомлений в нашем приложении, нам нужно настроить плагин перед запуском приложения.

Мы создаем файл `plugins/vue-notifications.js` :

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

Затем мы добавляем путь к файлу внутри ключа `plugins` нашего `nuxt.config.js` :

```js
export default {
  plugins: ['~/plugins/vue-notifications']
}
```

Чтобы узнать больше о ключе конфигурации `plugins` , проверьте [API плагинов](/api/configuration-plugins) .

### ES6 плагины

Если плагин находится в `node_modules` и экспортирует модуль ES6, вам может потребоваться добавить его в `transpile` сборки с транспортом:

```js
module.exports = {
  build: {
    transpile: ['vue-notifications']
  }
}
```

Вы можете обратиться к документации по сборке [конфигурации](/api/configuration-build/#transpile) для получения дополнительных параметров сборки.

## Внедрить в $ root & context

Иногда вы хотите сделать функции или значения доступными в приложении. Вы можете вставить эти переменные в экземпляры Vue (на стороне клиента), в контекст (на стороне сервера) и даже в хранилище Vuex. Это соглашение, чтобы префикс этих функций с `$` .

### Внедрить в экземпляры Vue

Внедрение контекста в экземпляры Vue работает аналогично тому, как это делается в стандартных приложениях Vue.

`plugins/vue-inject.js` :

```js
import Vue from 'vue'

Vue.prototype.$myInjectedFunction = string => console.log('This is an example', string)
```

`nuxt.config.js` :

```js
export default {
  plugins: ['~/plugins/vue-inject.js']
}
```

Теперь вы можете использовать эту функцию во всех ваших компонентах Vue.

`example-component.vue` :

```js
export default {
  mounted () {
    this.$myInjectedFunction('test')
  }
}
```

### Введите в контекст

Внедрение контекста в экземпляры Vue работает аналогично тому, как это делается в стандартных приложениях Vue.

`plugins/ctx-inject.js` :

```js
export default ({ app }, inject) => {
  // Set the function directly on the context.app object
  app.myInjectedFunction = string => console.log('Okay, another function', string)
}
```

`nuxt.config.js` :

```js
export default {
  plugins: ['~/plugins/ctx-inject.js']
}
```

Теперь функция доступна, когда у вас есть доступ к `context` (например, в `asyncData` и `fetch` ).

`ctx-example-component.vue` :

```js
export default {
  asyncData (context) {
    context.app.myInjectedFunction('ctx!')
  }
}
```

### Комбинированный впрыск

Если вам нужна функция в `context` , экземплярах Vue и, возможно, даже в хранилище Vuex, вы можете использовать функцию `inject` , которая является вторым параметром экспортируемой функции плагинов.

Внедрение контента в экземпляры Vue работает аналогично тому, как это делается в стандартных приложениях Vue. `$` Будет автоматически добавлен к функции.

`plugins/combined-inject.js` :

```js
export default ({ app }, inject) => {
  inject('myInjectedFunction', string => console.log('That was easy!', string))
}
```

`nuxt.config.js` :

```js
export default {
  plugins: ['~/plugins/combined-inject.js']
}
```

Теперь функцию можно использовать из `context` , через `this` в экземплярах Vue и через `this` в `actions` / `mutations` магазина.

`ctx-example-component.vue` :

```js
export default {
  mounted () {
    this.$myInjectedFunction('works in mounted')
  },
  asyncData (context) {
    context.app.$myInjectedFunction('works with context')
  }
}
```

`store/index.js` :

```js
export const state = () => ({
  someValue: ''
})

export const mutations = {
  changeSomeValue (state, newValue) {
    this.$myInjectedFunction('accessible in mutations')
    state.someValue = newValue
  }
}

export const actions = {
  setSomeValueToWhatever ({ commit }) {
    this.$myInjectedFunction('accessible in actions')
    const newValue = 'whatever'
    commit('changeSomeValue', newValue)
  }
}

```

## Только на стороне клиента

Некоторые плагины могут работать **только в браузере,** потому что им не хватает поддержки SSR. В этих ситуациях вы можете использовать опцию `mode` : `client` в `plugins` чтобы добавить плагин только на стороне клиента.

Пример:

`nuxt.config.js` :

```js
export default {
  plugins: [
    { src: '~/plugins/vue-notifications', mode: 'client' }
  ]
}
```

`plugins/vue-notifications.js` :

```js
import Vue from 'vue'
import VueNotifications from 'vue-notifications'

Vue.use(VueNotifications)
```

Если вам нужно импортировать некоторые библиотеки в плагин только на *стороне сервера* , вы можете проверить, установлена ли для переменной `process.server` значение `true` .

Кроме того, если вам нужно узнать, находитесь ли вы внутри сгенерированного приложения (с помощью `nuxt generate` ), вы можете проверить, установлено ли для `process.static` значение `true` . Это только случай во время и после поколения.

Вы также можете объединить обе опции, чтобы попасть в точку, когда страница рендерится сервером с `nuxt generate` перед сохранением ( `process.static && process.server` ).

**Примечание** : Начиная с Nuxt.js 2.4, `mode` был введен как опция `plugins` для указания типа плагина, возможные значения: `client` или `server` . `ssr: false` будет адаптирован к `mode: 'client'` и устарел в следующей основной версии.

Пример:

`nuxt.config.js` :

```js
export default {
  plugins: [
    { src: '~/plugins/both-sides.js' },
    { src: '~/plugins/client-only.js', mode: 'client' },
    { src: '~/plugins/server-only.js', mode: 'server' }
  ]
}
```

### Назовите обычный плагин

Если предполагается, что плагин запускается только на стороне клиента или сервера, в качестве расширения файла плагина можно использовать `.client.js` или `.server.js` , файл будет автоматически включен в соответствующую сторону.

Пример:

`nuxt.config.js` :

```js
export default {
  plugins: [
    '~/plugins/foo.client.js', // only in client side
    '~/plugins/bar.server.js', // only in server side
    '~/plugins/baz.js' // both client & server
  ]
}
```
