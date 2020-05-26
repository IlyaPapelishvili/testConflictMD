---
title: конфигурация
description: По умолчанию Nuxt.js настроен на большинство случаев использования. Эта конфигурация по умолчанию может быть перезаписана с помощью файла `nuxt.config.js`.
---

> По умолчанию Nuxt.js настроен на большинство случаев использования. Эта конфигурация по умолчанию может быть перезаписана файлом `nuxt.config.js` .

### строить

This option lets you configure various settings for the `build` step, including `loaders`, `filenames`, the `webpack` config and `transpilation`.

[](/api/configuration-build)[Документация по интеграции `build`](/api/configuration-build)[Documentation about `build` integration](/api/configuration-build)

### CSS

Эта опция позволяет вам определять CSS-файлы, модули и библиотеки, которые вы хотите включить глобально (на каждой странице).

[](/api/configuration-css)[Документация по интеграции `css`](/api/configuration-css)[Documentation about `css` integration](/api/configuration-css)

### DEV

Эта опция позволяет вам определить режим `development` или `production` Nuxt.js (важно, когда вы используете Nuxt.js программно)

[](/api/configuration-dev)[Документация по интеграции с `dev`](/api/configuration-dev)[Documentation about `dev` integration](/api/configuration-dev)

### окр

Эта опция позволяет вам определять переменные среды, доступные как клиенту, так и серверу.

[](/api/configuration-env)[Документация по интеграции `env`](/api/configuration-env)[Documentation about `env` integration](/api/configuration-env)

### генерировать

Эта опция позволяет вам установить значения параметров для каждого динамического маршрута в вашем приложении, который будет преобразован в файлы HTML с помощью Nuxt.js.

[Документация о генерации интеграции](/api/configuration-generate)

### глава

Эта опция позволяет вам определять все метатеги по умолчанию для вашего приложения.

[Документация по интеграции головы](/api/configuration-head)

### погрузка

Эта опция позволяет вам настроить загрузочный компонент, который Nuxt.js использует по умолчанию.

[](/api/configuration-loading)[Документация по `loading` интеграции](/api/configuration-loading)[Documentation about `loading` integration](/api/configuration-loading)

### модули

С помощью этой опции вы можете добавить модули Nuxt.js в ваш проект.

[](/api/configuration-modules)[Документация по интеграции `modules`](/api/configuration-modules)[Documentation about `modules` integration](/api/configuration-modules)

### modulesDir

Эта опция позволяет вам определить папку node_modules вашего приложения Nuxt.js.

[](/api/configuration-modulesdir)[Документация по интеграции `modulesDir`](/api/configuration-modulesdir)[Documentation about `modulesDir` integration](/api/configuration-modulesdir)

### плагины

Эта опция позволяет вам определить плагины JavaScript, которые должны быть запущены перед созданием корневого приложения Vue.js.

[](/api/configuration-plugins)[Документация по интеграции `plugins`](/api/configuration-plugins)[Documentation about `plugins` integration](/api/configuration-plugins)

### ROOTDIR

Эта опция позволяет вам определить рабочую область вашего приложения Nuxt.js.

[](/api/configuration-rootdir)[Документация по интеграции `rootDir`](/api/configuration-rootdir)[Documentation about `rootDir` integration](/api/configuration-rootdir)

### маршрутизатор

С помощью опции `router` вы перезаписываете конфигурацию по умолчанию Nuxt.js Vue Router.

[](/api/configuration-router)[Документация по интеграции `router`](/api/configuration-router)[Documentation about `router` integration](/api/configuration-router)

### сервер

Эта опция позволяет вам настроить переменные соединения для экземпляра сервера вашего приложения Nuxt.js.

[](/api/configuration-server)[Документация по интеграции `server`](/api/configuration-server)[Documentation about `server` integration](/api/configuration-server)

### SRCDIR

Эта опция позволяет вам определить исходный каталог вашего приложения Nuxt.js.

[](/api/configuration-srcdir)[Документация по интеграции `srcDir`](/api/configuration-srcdir)[Documentation about `srcDir` integration](/api/configuration-srcdir)

### реж

Эта опция позволяет вам определять пользовательские каталоги вашего приложения Nuxt.js.

[](/api/configuration-dir)[Документация об интеграции `dir`](/api/configuration-dir)[Documentation about `dir` integration](/api/configuration-dir)

### переход

Эта опция позволяет вам определять свойства по умолчанию для переходов страниц.

[](/api/configuration-transition)[Документация по `transition` интеграции](/api/configuration-transition)[Documentation about `transition` integration](/api/configuration-transition)

## Асинхронная конфигурация

Если вам нужно заполнить некоторые параметры (например, заголовок) асинхронными данными (например, поступающими из API), у вас есть возможность вернуть обещание. Вот пример:

```js
/*
axios-module cannot be used in nuxt.config.js
You need to import axios and configure it again
*/
import axios from 'axios'

export default async () => {
  const data = await axios.get('endpoint')
  return {
    head: {
      title: data.head.title,
      //... rest of config
    }
  }
}
```
