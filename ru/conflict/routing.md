---
title: Маршрутизация
description: Nuxt.js использует файловую систему для генерации маршрутов ваших веб-приложений.
---

> Nuxt.js автоматически генерирует конфигурацию [vue-router](https://github.com/vuejs/vue-router) на основе вашего дерева файлов Vue в каталоге `pages` .

<div class="Alert Alert--grey">
</div>

Для навигации по страницам мы рекомендуем использовать компонент [`<nuxt-link>`](/api/components-nuxt-link) .




Например:

```html
<template>
  <nuxt-link to="/">Home page</nuxt-link>
</template>
```

## Основные маршруты

Это дерево файлов:

```bash
pages/
--| user/
-----| index.vue
-----| one.vue
--| index.vue
```

автоматически сгенерирует:

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

## Динамические маршруты

Чтобы определить динамический маршрут с параметром, вам нужно определить файл .vue ИЛИ каталог с **префиксом подчеркивания** .

<div class="Promo__Video">
  <a href="https://vueschool.io/lessons/nuxtjs-dynamic-routes?friend=nuxt" target="_blank">
    <p class="Promo__Video__Icon">Посмотрите бесплатный урок о <strong>динамических маршрутах</strong> в Vue School</p>
  </a>
</div>

Это дерево файлов:

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

автоматически сгенерирует:

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

As you can see the route named `users-id` has the path `:id?` which makes it optional, if you want to make it required, create an `index.vue` file in the `users/_id` directory instead.

<div class="Alert Alert--orange">
</div>

**Предупреждение:** динамические маршруты игнорируются командой `generate` : [API Configuration generate](/api/configuration-generate#routes)




### Проверьте параметры маршрута

Nuxt.js позволяет вам определять метод валидатора внутри вашего динамического компонента маршрута.

В этом примере: `pages/users/_id.vue`

```js
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
```

Если метод validate не возвращает `true` или `Promise` который принимает значение `true` , или выдает ошибку, Nuxt.js автоматически загрузит страницу ошибки 404 или страницу ошибки 500 в случае ошибки.

Дополнительная информация о методе validate: [API Pages validate](/api/pages-validate)

## Вложенные маршруты

Nuxt.js позволяет вам создавать вложенный маршрут, используя дочерние маршруты vue-router.

Чтобы определить родительский компонент вложенного маршрута, вам нужно создать файл Vue с тем **же именем, что и каталог,** содержащий ваши дочерние представления.

<div class="Alert Alert--orange">
</div>

<b>Предупреждение:</b> не забудьте включить `<nuxt-child/>` в родительский компонент (файл <code>.vue</code> ).




Это дерево файлов:

```bash
pages/
--| users/
-----| _id.vue
-----| index.vue
--| users.vue
```

автоматически сгенерирует:

```js
router: {
  routes: [
    {
      path: '/users',
      component: 'pages/users.vue',
      children: [
        {
          path: '',
          component: 'pages/users/index.vue',
          name: 'users'
        },
        {
          path: ':id',
          component: 'pages/users/_id.vue',
          name: 'users-id'
        }
      ]
    }
  ]
}
```

## Динамические Вложенные Маршруты

Этот сценарий не должен часто случаться, но это возможно с Nuxt.js: наличие динамических детей внутри динамических родителей.

Это дерево файлов:

```bash
pages/
--| _category/
-----| _subCategory/
--------| _id.vue
--------| index.vue
-----| _subCategory.vue
-----| index.vue
--| _category.vue
--| index.vue
```

автоматически сгенерирует:

```js
router: {
  routes: [
    {
      path: '/',
      component: 'pages/index.vue',
      name: 'index'
    },
    {
      path: '/:category',
      component: 'pages/_category.vue',
      children: [
        {
          path: '',
          component: 'pages/_category/index.vue',
          name: 'category'
        },
        {
          path: ':subCategory',
          component: 'pages/_category/_subCategory.vue',
          children: [
            {
              path: '',
              component: 'pages/_category/_subCategory/index.vue',
              name: 'category-subCategory'
            },
            {
              path: ':id',
              component: 'pages/_category/_subCategory/_id.vue',
              name: 'category-subCategory-id'
            }
          ]
        }
      ]
    }
  ]
}
```

### Неизвестные динамические вложенные маршруты

Если вы не знаете глубину своей структуры URL, вы можете использовать `_.vue` для динамического сопоставления вложенных путей. Это будет обрабатывать запросы, которые не соответствуют *более конкретному* запросу.

Это дерево файлов:

```bash
pages/
--| people/
-----| _id.vue
-----| index.vue
--| _.vue
--| index.vue
```

Будет обрабатывать такие запросы:

Дорожка | файл
--- | ---
`/` | `index.vue`
`/people` | `people/index.vue`
`/people/123` | `people/_id.vue`
`/about` | `_.vue`
`/about/careers` | `_.vue`
`/about/careers/chicago` | `_.vue`

**Примечание:** обработка страниц 404 теперь соответствует логике страницы `_.vue` . [Подробнее о перенаправлении 404 можно найти здесь](/guide/async-data#handling-errors) .

### Именованные виды

Для отображения именованных представлений вы можете использовать `<nuxt name="top"/>` или `<nuxt-child name="top"/>` в макете / странице. Чтобы указать именованный вид страницы, нам нужно расширить конфигурацию маршрутизатора в файле `nuxt.config.js` :

```js
export default {
  router: {
    extendRoutes (routes, resolve) {
      const index = routes.findIndex(route => route.name === 'main')
      routes[index] = {
        ...routes[index],
        components: {
          default: routes[index].component,
          top: resolve(__dirname, 'components/mainTop.vue')
        },
        chunkNames: {
          top: 'components/mainTop'
        }
      }
    }
  }
}
```

Требуется расширить интересующий маршрут двумя `components` свойств и `chunkNames` . Именованное представление в этом примере конфигурации имеет имя `top` .

Чтобы увидеть пример, взгляните на [пример named-views](/examples/named-views) .

### СПА отступление

Вы также можете включить резервные SPA для динамических маршрутов. Nuxt.js выведет дополнительный файл, который совпадает с `index.html` , который будет использоваться в `mode: 'spa'` . Большинство служб статического хостинга можно настроить на использование шаблона SPA, если не найдено ни одного файла. Это не будет включать в себя `head` информацию или любой HTML, но он все равно будет разрешать и загружать данные из API.

We enable this in our `nuxt.config.js` file:

```js
export default {
  generate: {
    fallback: true, // if you want to use '404.html' instead of the default '200.html'
    fallback: 'my-fallback/file.html' // if your hosting needs a custom location
  }
}
```

### Локальный доступ к параметрам маршрута

Вы можете получить доступ к текущим параметрам маршрута на своей локальной странице или компоненте, ссылаясь на `this.$route.params.{parameterName}` . Например, если у вас была динамическая страница пользователей ( `users\_id.vue` ) и вы хотели получить доступ к параметру `id` для загрузки информации о пользователе или процессе, вы можете получить доступ к переменной следующим образом: `this.$route.params.id` .

#### Реализация для Surge

Surge [может обрабатывать](https://surge.sh/help/adding-a-custom-404-not-found-page) как `200.html` и `404.html` . `generate.fallback` по умолчанию имеет значение `200.html` , поэтому менять его не нужно.

#### Реализация для GitHub Pages и Netlify

GitHub Pages и Netlify распознают файл `404.html` автоматически, поэтому все, что нам нужно сделать, это установить для `generate.fallback` значение `true` !

#### Реализация для Firebase хостинга

Firebase Hosting [can handle](https://firebase.google.com/docs/hosting/full-config#404) the `404.html` file automatically, so setting `generate.fallback` to `true` will render the error page with a default response code of 404.

## Переходы

Nuxt.js использует компонент [`<transition>`](http://vuejs.org/v2/guide/transitions.html#Transitioning-Single-Elements-Components) чтобы позволить вам создавать удивительные переходы / анимации между вашими маршрутами.

### Глобальные настройки

<div class="Alert Alert--nuxt-green">
</div>

<b>Информация:</b> по умолчанию имя перехода Nuxt.js - `"page"` .




Чтобы добавить плавный переход на каждую страницу вашего приложения, нам нужен файл CSS, который используется всеми нашими маршрутами, поэтому мы начнем с создания файла в папке `assets` .

Наша глобальная css in `assets/main.css` :

```css
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-to {
  opacity: 0;
}
```

Затем мы добавляем его путь к массиву `css` в нашем файле `nuxt.config.js` :

```js
export default {
  css: [
    '~/assets/main.css'
  ]
}
```

Дополнительная информация о ключе перехода: [переход API Configuration](/api/pages-transition)

### Настройки страницы

Вы также можете определить пользовательский переход для конкретной страницы с помощью свойства `transition` .

Мы добавляем новый класс в наш глобальный css в `assets/main.css` :

```css
.test-enter-active, .test-leave-active {
  transition: opacity .5s;
}
.test-enter, .test-leave-active {
  opacity: 0;
}
```

Затем мы используем свойство transition, чтобы определить имя класса, которое будет использоваться для этого перехода страницы:

```js
export default {
  transition: 'test'
}
```

Дополнительная информация о свойстве transition: [переход API страниц](/api/pages-transition)

## Промежуточное

> Промежуточное программное обеспечение позволяет определять пользовательские функции, которые можно запускать перед отображением страницы или группы страниц.

**Shared middleware should be placed in the `middleware/` directory.** The filename will be the name of the middleware (`middleware/auth.js` will be the `auth` middleware). You can also defined page-specific middleware by using a function directly, see [anonymous middleware](/api/pages-middleware#anonymous-middleware).

Промежуточное программное обеспечение получает [контекст в](/api/context) качестве первого аргумента:

```js
export default function (context) {
  context.userAgent = process.server ? context.req.headers['user-agent'] : navigator.userAgent
}
```

В универсальном режиме промежуточное программное обеспечение будет вызываться на стороне сервера один раз (при первом запросе к приложению Nuxt или при обновлении страницы) и на стороне клиента при переходе к дальнейшим маршрутам. В режиме SPA промежуточное программное обеспечение будет вызываться на стороне клиента при первом запросе и при переходе к дальнейшим маршрутам.

Промежуточное программное обеспечение будет выполнено последовательно в следующем порядке:

1. `nuxt.config.js` (в порядке в файле)
2. Согласованные макеты
3. Совпадающие страницы

Промежуточное программное обеспечение может быть асинхронным. Для этого просто верните `Promise` или используйте второй аргумент `callback` :

`middleware/stats.js`

```js
import axios from 'axios'

export default function ({ route }) {
  return axios.post('http://my-stats-api.com', {
    url: route.fullPath
  })
}
```

Затем в вашем `nuxt.config.js` используйте ключ `router.middleware` :

`nuxt.config.js`

```js
export default {
  router: {
    middleware: 'stats'
  }
}
```

Теперь промежуточное ПО `stats` будет вызываться при каждом изменении маршрута.

Вы также можете добавить свое промежуточное программное обеспечение (даже несколько) к определенному макету или странице:

`pages/index.vue` или `layouts/default.vue`

```js
export default {
  middleware: ['auth', 'stats']
}
```

Чтобы увидеть реальный пример использования промежуточного программного обеспечения, см. [Пример-auth0](https://github.com/nuxt/example-auth0) на GitHub.
