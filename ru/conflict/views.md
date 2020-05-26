---
title: Взгляды
description: В разделе «Представления» описывается все, что вам нужно для настройки данных и представлений для определенного маршрута в приложении Nuxt.js («Документ», «Макеты», «Страницы» и «Заголовок HTML»).
---

> Раздел «Представления» описывает все, что вам нужно для настройки данных и представлений для определенного маршрута в приложении Nuxt.js (шаблон приложения, макеты, страницы и заголовок HTML).

![nuxt-видовые схемы](/nuxt-views-schema.svg)

## Шаблон приложения

> Вы можете настроить шаблон приложения HTML, используемый Nuxt.js, для включения скриптов или условных классов CSS.

Чтобы изменить шаблон, создайте файл `app.html` в папке src вашего проекта. (который является корневым каталогом проекта по умолчанию).

Шаблон по умолчанию, используемый Nuxt.js:

```html
<!DOCTYPE html>
<html {{ HTML_ATTRS }}>
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

Одним из вариантов использования пользовательского шаблона приложения является добавление условных классов CSS для IE:

```html
<!DOCTYPE html>
<!--[if IE 9]><html lang="en-US" class="lt-ie9 ie9" {{ HTML_ATTRS }}><![endif]-->
<!--[if (gt IE 9)|!(IE)]><!--><html {{ HTML_ATTRS }}><!--<![endif]-->
  <head {{ HEAD_ATTRS }}>
    {{ HEAD }}
  </head>
  <body {{ BODY_ATTRS }}>
    {{ APP }}
  </body>
</html>
```

<!-- TODO: Load polyfills here? -->

## Макеты

Макеты очень полезны, когда вы хотите изменить внешний вид вашего приложения Nuxt.js. Хотите ли вы включить боковую панель или иметь разные макеты для мобильных и настольных ПК

### Макет по умолчанию

Вы можете расширить основной макет, добавив файл `layouts/default.vue` . Он будет использоваться для всех страниц, для которых не указан макет.

<div class="Alert Alert--nuxt-green">
</div>

<b>Информация:</b> Обязательно добавьте компонент `<nuxt/>` при создании макета, чтобы фактически включить компонент страницы.




Макет по умолчанию, который выходит из коробки, имеет длину всего три строки и просто отображает компонент страницы:

```html
<template>
  <nuxt/>
</template>
```

### Пользовательский Макет

Каждый файл ( *верхнего уровня* ) в каталоге `layouts` создаст собственный макет, доступный со свойством `layout` в компонентах страницы.

Допустим, мы хотим создать макет блога и сохранить его в `layouts/blog.vue` :

```html
<template>
  <div>
    <div>My blog navigation bar here</div>
    <nuxt/>
  </div>
</template>
```

Затем мы должны указать страницам (т.е. `pages/posts.vue` ) использовать ваш собственный макет:

```html
<template>
<!-- Your template -->
</template>
<script>
export default {
  layout: 'blog'
  // page component definitions
}
</script>
```

Дополнительная информация о свойстве `layout` : <a href="/api/pages-layout" data-md-type="link">`layout` страниц API</a>

Посмотрите [демонстрационное видео,](https://www.youtube.com/watch?v=YOKnSTp7d38) чтобы увидеть собственные макеты в действии.

<!-- TODO: Scoped styles best practice -->

### Страница ошибок

Страница ошибки - это *компонент страницы,* который всегда отображается при возникновении ошибки (это не происходит при рендеринге на стороне сервера).

<div class="Alert Alert--orange">
</div>

<b>Предупреждение.</b> Хотя этот файл находится в папке <code>layouts</code> , его следует рассматривать как <b>страницу</b> .




Как упоминалось выше, этот макет является особенным, так как вы **не должны** включать `<nuxt/>` в его шаблон. Вы должны видеть этот макет как компонент, отображаемый при возникновении ошибки ( `404` , `500` и т. Д.). Как и в случае с другими компонентами страницы, вы также можете установить собственный макет страницы ошибок также обычным способом.

Исходный код страницы ошибок по умолчанию [доступен на GitHub](https://github.com/nuxt/nuxt.js/blob/dev/packages/vue-app/template/components/nuxt-error.vue) .

Вы можете настроить страницу ошибок, добавив файл `layouts/error.vue` :

```html
<template>
  <div class="container">
    <h1 v-if="error.statusCode === 404">Page not found</h1>
    <h1 v-else>An error occurred</h1>
    <nuxt-link to="/">Home page</nuxt-link>
  </div>
</template>

<script>
export default {
  props: ['error'],
  layout: 'blog' // you can set a custom layout for the error page
}
</script>
```

## страницы

Каждый компонент Page является компонентом Vue, но Nuxt.js добавляет специальные атрибуты и функции, чтобы максимально упростить разработку универсального приложения.

<div class="Promo__Video">
  <a href="https://vueschool.io/lessons/nuxtjs-page-components?friend=nuxt" target="_blank">
    <p class="Promo__Video__Icon">Посмотрите бесплатный урок о <strong>компонентах страницы Nuxt.js</strong> в Vue School</p>
  </a>
</div>

```html
<template>
  <h1 class="red">Hello {{ name }}!</h1>
</template>

<script>
export default {
  asyncData (context) {
    // called every time before loading the component
    // as the name said, it can be async
    // Also, the returned object will be merged with your data object
    return { name: 'World' }
  },
  fetch () {
    // The `fetch` method is used to fill the store before rendering the page
  },
  head () {
    // Set Meta Tags for this Page
  },
  // and more functionality to discover
  ...
}
</script>

<style>
.red {
  color: red;
}
</style>
```

атрибут | Описание | Документация
--- | --- | ---
`asyncData` | Самый важный ключ. Он может быть асинхронным и получать контекст в качестве аргумента. | [Руководство: асинхронные данные](/guide/async-data)
`fetch` | Используется для заполнения магазина перед отображением страницы. Это похоже на метод `asyncData` , за исключением того, что он не устанавливает данные компонента. | [](/api/pages-fetch)[API Страницы `fetch`](/api/pages-fetch)[API Pages `fetch`](/api/pages-fetch)
`head` | Установите определенные теги `<meta>` для текущей страницы. | [](/api/pages-head)[`head` API страниц](/api/pages-head)[API Pages `head`](/api/pages-head)
`layout` | Укажите макет, определенный в каталоге `layouts` . | [](/api/pages-layout)[`layout` страниц API](/api/pages-layout)[API Pages `layout`](/api/pages-layout)
`loading` | Если задано значение `false` , страница не будет автоматически вызывать `this.$nuxt.$loading.finish()` при вводе и `this.$nuxt.$loading.start()` при выходе из него, что позволяет **вручную** контролировать поведение , как показывает [этот пример](/examples/custom-page-loading) . Применяется только в том случае, если `loading` также установлена в `nuxt.config.js` . | [](/api/configuration-loading)[`loading` конфигурации API](/api/configuration-loading)[API Configuration `loading`](/api/configuration-loading)
`transition` | Определяет конкретный переход для страницы. | [](/api/pages-transition)[`transition` API страниц](/api/pages-transition)[API Pages `transition`](/api/pages-transition)
`scrollToTop` | Boolean (по умолчанию: `false` ). Укажите, хотите ли вы, чтобы страница прокручивалась вверх до отображения страницы. Используется для [вложенных маршрутов](/guide/routing#nested-routes) . | [](/api/pages-scrolltotop#the-scrolltotop-property)[Страницы API `scrollToTop`](/api/pages-scrolltotop#the-scrolltotop-property)[API Pages `scrollToTop`](/api/pages-scrolltotop#the-scrolltotop-property)
`validate` | Функция валидатора для [динамических маршрутов](/guide/routing#dynamic-routes) . | [](/api/pages-validate#the-validate-method)[API-страницы `validate`](/api/pages-validate#the-validate-method)[API Pages `validate`](/api/pages-validate#the-validate-method)
`middleware` | Определяет промежуточное программное обеспечение для этой страницы. Промежуточное программное обеспечение будет вызвано перед отображением страницы. | [Руководство: промежуточное ПО](/guide/routing#middleware)

Подробнее об использовании свойств страниц: [API Pages](/api)

## HTML Head

Nuxt.js использует [vue-meta](https://vue-meta.nuxtjs.org/) для обновления заголовка `document head` и `meta attributes` вашего приложения.

Использование `vue-meta` Nuxt.js можно найти [на GitHub](https://github.com/nuxt/nuxt.js/blob/dev/packages/vue-app/template/index.js#L42-L48) .

<div class="Alert Alert--teal">
</div>

<b>Информация:</b> Nuxt.js использует <code>hid</code> вместо ключа <code>vmid</code> по умолчанию для идентификации метаэлементов




### Метатеги по умолчанию

Nuxt.js позволяет вам определять все теги `<meta>` по умолчанию для вашего приложения внутри `nuxt.config.js` . Определите их, используя одно и то же свойство `head` :

Пример пользовательского окна просмотра с пользовательским шрифтом Google:

```js
head: {
  meta: [
    { charset: 'utf-8' },
    { name: 'viewport', content: 'width=device-width, initial-scale=1' }
  ],
  link: [
    { rel: 'stylesheet', href: 'https://fonts.googleapis.com/css?family=Roboto' }
  ]
}
```

Чтобы узнать больше о параметрах, доступных для `head` , взгляните на документацию [vue-meta](https://vue-meta.nuxtjs.org/api/#metainfo-properties) .

Более подробная информация о `head` методе имеется в <a href="/api/configuration-head" data-md-type="link">Configuration API `head`</a> странице.

### Пользовательские метатеги для страницы

Более подробную информацию о методе головки можно найти на [API страницы `head`](/api/pages-head) страницы.
