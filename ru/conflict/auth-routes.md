---
title: Auth Routes
description: Пример аутентифицированных маршрутов с Nuxt.js
github: Auth-маршруты
livedemo: https://nuxt-auth-routes.gomix.me
liveedit: https://gomix.com/#!/project/nuxt-auth-routes
---

# Документация

> Nuxt.js можно легко использовать для создания аутентифицированных маршрутов.

## Официальный `auth-module`

Если вы хотите реализовать сложные потоки аутентификации, например OAuth2, мы рекомендуем использовать официальный [`auth-module`](https://github.com/nuxt-community/auth-module) авторизации

## Использование Express и сессий

Чтобы добавить функцию сессий в наше приложение, мы будем использовать `express` и `express-session` , для этого нам нужно использовать Nuxt.js программно.

Сначала мы устанавливаем зависимости:

```bash
yarn add express express-session body-parser whatwg-fetch
```

*О `whatwg-fetch` мы поговорим позже.*

Затем мы создаем наш `server.js` :

```js
const { Nuxt, Builder } = require('nuxt')
const bodyParser = require('body-parser')
const session = require('express-session')
const app = require('express')()

// Body parser, to access `req.body`
app.use(bodyParser.json())

// Sessions to create `req.session`
app.use(session({
  secret: 'super-secret-key',
  resave: false,
  saveUninitialized: false,
  cookie: { maxAge: 60000 }
}))

// POST `/api/login` to log in the user and add him to the `req.session.authUser`
app.post('/api/login', function (req, res) {
  if (req.body.username === 'demo' && req.body.password === 'demo') {
    req.session.authUser = { username: 'demo' }
    return res.json({ username: 'demo' })
  }
  res.status(401).json({ error: 'Bad credentials' })
})

// POST `/api/logout` to log out the user and remove it from the `req.session`
app.post('/api/logout', function (req, res) {
  delete req.session.authUser
  res.json({ ok: true })
})

// We instantiate Nuxt.js with the options
const isProd = process.env.NODE_ENV === 'production'
const nuxt = new Nuxt({ dev: !isProd })
// No build in production
if (!isProd) {
  const builder = new Builder(nuxt)
  builder.build()
}
app.use(nuxt.render)
app.listen(3000)
console.log('Server is listening on http://localhost:3000')
```

И мы обновляем наши скрипты `package.json` :

```json
// ...
"scripts": {
  "dev": "node server.js",
  "build": "nuxt build",
  "start": "cross-env NODE_ENV=production node server.js"
}
// ...
```

Примечание: вам нужно будет запустить `npm install --save-dev cross-env` чтобы приведенный выше пример работал. Если вы *не* разрабатываете для Windows, вы можете исключить перекрёстный env из своего `start` скрипта и установить `NODE_ENV` напрямую.

## Использование магазина

Нам нужно глобальное состояние, чтобы сообщить нашему приложению, подключен ли пользователь **через страницы** .

Чтобы позволить Nuxt.js использовать Vuex, мы создаем файл `store/index.js` :

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

// Polyfill for `window.fetch()`
require('whatwg-fetch')

const store = () => new Vuex.Store({

  state: () => ({
    authUser: null
  }),

  mutations: {
    SET_USER (state, user) {
      state.authUser = user
    }
  },

  actions: {
    // ...
  }

})

export default store
```

1. Мы импортируем `Vue` и `Vuex` (включены в Nuxt.js) и говорим Vue использовать Vuex, чтобы позволить нам использовать `$store` в наших компонентах.
2. Мы `require('whatwg-fetch')` чтобы заполнить метод `fetch()` во всех браузерах (см. Репозиторий [fetch](https://github.com/github/fetch) ).
3. Мы создаем нашу мутацию `SET_USER` которая установит `state.authUser` для подключенного пользователя.
4. Мы экспортируем наш экземпляр магазина в Nuxt.js и можем внедрить его в наше основное приложение.

### действие nuxtServerInit ()

Nuxt.js вызовет определенное действие с именем `nuxtServerInit` с контекстом в аргументе, поэтому, когда приложение будет загружено, хранилище будет уже заполнено некоторыми данными, которые мы можем получить с сервера.

В нашем `store/index.js` мы можем добавить действие `nuxtServerInit` :

```js
nuxtServerInit ({ commit }, { req }) {
  if (req.session && req.session.authUser) {
    commit('SET_USER', req.session.authUser)
  }
}
```

Чтобы сделать метод данных асинхронным, Nuxt.js предлагает вам разные способы, выберите тот, который вам наиболее знаком:

1. возвращая `Promise` , Nuxt.js будет ждать разрешения обещания перед тем, как отобразить компонент.
2. Использование предложения [`async` / `await`](https://github.com/lukehoban/ecmascript-asyncawait) ( [узнайте больше об этом](https://zeit.co/blog/async-and-await) ).

### действие login ()

We add a `login` action which will be called from our pages component to log in the user:

```js
login ({ commit }, { username, password }) {
  return fetch('/api/login', {
    // Send the client cookies to the server
    credentials: 'same-origin',
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      username,
      password
    })
  })
  .then((res) => {
    if (res.status === 401) {
      throw new Error('Bad credentials')
    } else {
      return res.json()
    }
  })
  .then((authUser) => {
    commit('SET_USER', authUser)
  })
}
```

### метод logout ()

```js
logout ({ commit }) {
  return fetch('/api/logout', {
    // Send the client cookies to the server
    credentials: 'same-origin',
    method: 'POST'
  })
  .then(() => {
    commit('SET_USER', null)
  })
}
```

## Компоненты страниц

Затем мы можем использовать `$store.state.authUser` в наших компонентах страниц, чтобы проверить, подключен ли пользователь в нашем приложении или нет.

### Перенаправить пользователя, если он не подключен

Давайте добавим `/secret` маршрут, где только подключенный пользователь может видеть его содержимое:

```html
<template>
  <div>
    <h1>Super secret page</h1>
    <router-link to="/">Back to the home page</router-link>
  </div>
</template>

<script>
export default {
  // we use fetch() because we do not need to set data to this component
  fetch ({ store, redirect }) {
    if (!store.state.authUser) {
      return redirect('/')
    }
  }
}
</script>
```

В методе `fetch` мы видим, что мы вызываем `redirect('/')` когда наш пользователь не подключен.
