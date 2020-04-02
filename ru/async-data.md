---
title: Асинхронные данные
description: Вы можете получить данные и отобразить их на стороне сервера. Nuxt.js добавляет метод `asyncData`, чтобы позволить вам обрабатывать асинхронные операции перед установкой данных компонента.
---

> Вы можете получить данные и отобразить их на стороне сервера. Nuxt.js добавляет метод `asyncData` чтобы позволить вам обрабатывать асинхронные операции перед инициализацией компонента

<div>
  <a href="https://vueschool.io/courses/async-data-with-nuxtjs?friend=nuxt" target="_blank" class="Promote"></a><a href="https://vueschool.io/courses/async-data-with-nuxtjs?friend=nuxt" target="_blank" class="Promote">
    <img src="/async-data-with-nuxtjs.png" srcset="/async-data-with-nuxtjs-2x.png 2x" alt="AsyncData by vueschool">
    <div class="Promote__Content">
      <h4 class="Promote__Content__Title">Async Data with Nuxt.js</h4>
      <p class="Promote__Content__Description">Learn how to manage asynchronous data with Nuxt.js.</p>
      <p class="Promote__Content__Signature">Video courses made by VueSchool to support Nuxt.js development.</p>
    </div>
  </a><a href="https://vueschool.io/courses/async-data-with-nuxtjs?friend=nuxt" target="_blank" class="Promote">
    <img src="/async-data-with-nuxtjs.png" srcset="/async-data-with-nuxtjs-2x.png 2x" alt="AsyncData by vueschool">
    <div class="Promote__Content">
      <h4 class="Promote__Content__Title">Async Data with Nuxt.js</h4>
      <p class="Promote__Content__Description">Learn how to manage asynchronous data with Nuxt.js.</p>
      <p class="Promote__Content__Signature">Video courses made by VueSchool to support Nuxt.js development.</p>
    </div>
  </a>
</div>

## Метод asyncData

Иногда вы просто хотите получить данные и предварительно обработать их на сервере, не используя хранилище.
`asyncData` вызывается каждый раз перед загрузкой компонента **страницы** .
Он будет вызываться на стороне сервера один раз (при первом запросе к приложению Nuxt) и на стороне клиента при переходе к дальнейшим маршрутам.
Этот метод получает [контекст](/api/context) в качестве первого аргумента, вы можете использовать его для извлечения некоторых данных, и Nuxt.js объединит его с данными компонента.

Nuxt.js автоматически объединит возвращаемый объект с данными компонента.

<div class="Alert Alert--orange">
</div>

У вас **нет** доступа экземпляра компонента через `this` внутри `asyncData` , поскольку он вызывается **перед началом** компоненты.




Nuxt.js предлагает вам разные способы использования `asyncData` . Выберите тот, который вам наиболее знаком:

1. Возвращая `Promise` . Nuxt.js будет ждать разрешения обещания перед рендерингом компонента.
2. Использование [async / await](https://javascript.info/async-await) ( [узнайте больше об этом](https://zeit.co/blog/async-and-await) )

<div class="Alert Alert--grey">
</div>

Мы используем [axios](https://github.com/mzabriskie/axios) для создания изоморфных HTTP-запросов, мы <strong>настоятельно рекомендуем</strong> использовать наш [модуль](https://axios.nuxtjs.org/) axios для ваших проектов Nuxt.




Если вы используете `axios` непосредственно из `node_modules` и используете `axios.interceptors` для добавления перехватчиков для преобразования данных, обязательно создайте экземпляр перед добавлением перехватчиков. Если нет, то при обновлении страницы serverRender перехватчики будут добавляться несколько раз, что приведет к ошибке данных.

```js
import axios from 'axios'
const myaxios = axios.create({
  // ...
})
myaxios.interceptors.response.use(function (response) {
  return response.data
}, function (error) {
  // ...
})
```

### Возвращение обещания

```js
export default {
  asyncData ({ params }) {
    return axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        return { title: res.data.title }
      })
  }
}
```

### Использование async / await

```js
export default {
  async asyncData ({ params }) {
    const { data } = await axios.get(`https://my-api/posts/${params.id}`)
    return { title: data.title }
  }
}
```

### Отображение данных

Результат asyncData будет **объединен** с данными.
Вы можете отображать данные внутри вашего шаблона, как вы привыкли делать:

```html
<template>
  <h1>{{ title }}</h1>
</template>
```

## Контекст

Чтобы увидеть список доступных ключей в `context` , взгляните на <a href="/api/context" data-md-type="link">`context` API Essential</a> .

### Используйте объекты `req` / `res`

Когда `asyncData` вызывается на стороне сервера, у вас есть доступ к объектам `req` и `res` пользовательского запроса.

```js
export default {
  async asyncData ({ req, res }) {
    // Please check if you are on the server side before
    // using req and res
    if (process.server) {
      return { host: req.headers.host }
    }

    return {}
  }
}
```

### Доступ к данным динамического маршрута

Вы также можете использовать параметр `context` для доступа к данным динамического маршрута!
Например, динамические параметры маршрута могут быть получены с использованием имени файла или папки, которые его настроили.
Если вы определили файл с именем `_slug.vue` в папке ваших `pages` , вы можете получить доступ к значению через `context.params.slug` :

```js
export default {
  async asyncData ({ params }) {
    const slug = params.slug // When calling /abc the slug will be "abc"
    return { slug }
  }
}
```

### Прослушивание изменений запроса

The `asyncData` method **is not called** on query string changes by default.
If you want to change this behavior, for example when building a pagination component,
you can set up parameters that should be listened to with the `watchQuery` property of your page component.
Learn more on the <a href="/api/pages-watchquery" data-md-type="link">API `watchQuery` page</a> page.

## Обработка ошибок

Nuxt.js добавляет метод `error(params)` в `context` , который можно вызвать для отображения страницы с ошибкой. `params.statusCode` будет использоваться для визуализации правильного кода состояния со стороны сервера.

Пример с `Promise` :

```js
export default {
  asyncData ({ params, error }) {
    return axios.get(`https://my-api/posts/${params.id}`)
      .then((res) => {
        return { title: res.data.title }
      })
      .catch((e) => {
        error({ statusCode: 404, message: 'Post not found' })
      })
  }
}
```

To customize the error page, take a look at the [views guide](/guide/views#layouts) .
