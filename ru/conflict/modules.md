---
title: Модули
description: Модули являются расширениями Nuxt.js, которые могут расширять его основные функциональные возможности и добавлять бесконечные интеграции.
---

> Модули являются расширениями Nuxt.js, которые могут расширять его основные функциональные возможности и добавлять бесконечные интеграции.

## Введение

Разрабатывая приложения промышленного уровня с помощью Nuxt, вы скоро обнаружите, что основной функциональности фреймворка недостаточно. Nuxt может быть расширен с помощью параметров конфигурации и плагинов, но поддержание этих настроек в нескольких проектах утомительно, многократно и занимает много времени. С другой стороны, поддержка потребностей каждого проекта из коробки сделает Nuxt очень сложным и сложным в использовании.

Это одна из причин, почему Nuxt предоставляет модульную систему более высокого порядка, которая позволяет легко расширять ядро. Модули - это просто **функции** , которые вызываются последовательно при загрузке Nuxt. Фреймворк ждет завершения каждого модуля, прежде чем продолжить. Таким образом, модули могут настраивать практически любой аспект Nuxt. Благодаря модульному дизайну Nuxt (основанному на Tapable веб- [пакета](https://github.com/webpack/tapable) ) модули могут легко регистрировать хуки для определенных точек входа, таких как инициализация компоновщика. Модули также могут переопределять шаблоны, настраивать загрузчики веб-пакетов, добавлять библиотеки CSS и выполнять множество других полезных задач.

Лучше всего, модули Nuxt могут быть включены в пакеты npm. Это позволяет легко использовать их в разных проектах и делиться ими с сообществом Nuxt, помогая создать экосистему высококачественных надстроек Nuxt.

Модули хороши, если вы:

- Являетесь членом **гибкой команды,** которая должна быстро начать новые проекты.
- Устали **заново изобретать** колесо для решения общих задач, таких как интеграция Google Analytics.
- Прекрасный энтузиаст **Open Source,** который хотел бы легко **поделиться** своей работой с сообществом.
- Являются членами **корпоративной** компании, которая ценит **качество** и возможность **повторного использования** .
- Часто сталкиваются с короткими сроками и не имеют времени копаться в деталях каждой новой библиотеки или интеграции.
- Устали от серьезных изменений в низкоуровневых интерфейсах и нуждаются в вещах, которые **просто работают ™** .

## Список модулей Nuxt.js

Команда Nuxt.js предлагает **официальные** модули:

- [@ nuxt / http](https://http.nuxtjs.org) : легкий и универсальный способ делать HTTP-запросы, основанный на [ky-universal](https://github.com/sindresorhus/ky-universal)
- [@ nuxtjs / axios](https://axios.nuxtjs.org) : безопасная и простая интеграция Axios с Nuxt.js для выполнения HTTP-запросов
- [@ nuxtjs / pwa](https://pwa.nuxtjs.org) : Supercharge Nuxt с проверенным, обновленным и стабильным решением PWA
- [@ nuxtjs / auth](https://auth.nuxtjs.org) : модуль аутентификации для Nuxt.js, предлагающий различные схемы и стратегии

Список модулей Nuxt.js, созданных сообществом, доступен по адресу https://github.com/topics/nuxt-module.

## Написать основной модуль

Как уже упоминалось, модули являются просто простыми функциями. Они могут быть упакованы в виде модулей npm или напрямую включены в исходный код проекта.

**Модули / simple.js**

```js
export default function SimpleModule (moduleOptions) {
  // Write your code here
}

// REQUIRED if publishing the module as npm package
// module.exports.meta = require('./package.json')
```

**`moduleOptions`**

Это объект, переданный с помощью массива `modules` пользователем, мы можем использовать его для настройки его поведения.

**`this.options`**

Вы можете напрямую получить доступ к параметрам Nuxt, используя эту ссылку. Это содержимое `nuxt.config.js` пользователя со всеми параметрами по умолчанию, назначенными для. Может использоваться для общих параметров между модулями.

**`this.nuxt`**

Это ссылка на текущий экземпляр Nuxt. Обратитесь к [документации класса Nuxt для доступных методов](/api/internals-nuxt) .

**`this`**

Context of modules. Please look into the [ModuleContainer](/api/internals-module-container) class docs for available methods.

**`module.exports.meta`**

Эта строка **обязательна,** если вы публикуете модуль в виде пакета npm. Nuxt внутренне использует мета для лучшей работы с вашим пакетом.

**nuxt.config.js**

```js
export default {
  modules: [
    // Simple usage
    '~/modules/simple'

    // Passing options directly
      ['~/modules/simple', { token: '123' }]
  ]
}
```

Затем мы сообщаем Nuxt загрузить некоторые конкретные модули для проекта с необязательными параметрами в качестве параметров. Пожалуйста, обратитесь к документации по [конфигурации модулей](/api/configuration-modules) для получения дополнительной информации!

## Асинхронные модули

Не все модули будут делать все синхронно. Например, вы можете захотеть разработать модуль, который требует извлечения некоторого API или выполнения асинхронного ввода-вывода. Для этого Nuxt поддерживает асинхронные модули, которые могут возвращать Promise или вызывать обратный вызов.

## Только для сборки Модули

Usually, modules are only required during development and build time. Using `buildModules` helps to make production startup faster and also significantly decreasing `node_modules` size for production deployments. If you are a module author, It is highly recommended to suggest users installing your package as a `devDependency` and use `buildModules` instead of `modules` for `nuxt.config.js`.

Ваш модуль является `buildModule` если:

- Это обеспечивает серверное программное обеспечение
- Он должен зарегистрировать хук времени выполнения Node.js (как часовой)
- Это влияет на поведение vue-renderer или использование ловушки от `server:` или `vue-renderer:` namespace
- Все остальное, что находится за пределами области действия веб-пакета (подсказка: плагины и шаблоны скомпилированы и находятся в области видимости веб-пакета)

<div class="Alert Alert--orange">
</div>

<b>NOTE:</b> If you are going to offer using <code>buildModules</code> please mention that this feature is only available since Nuxt <b>v2.9</b>. Older users should upgrade Nuxt or use the <code>modules</code> section.




### Используйте async / await

```js
import fse from 'fs-extra'

export default async function asyncModule () {
  // You can do async works here using `async`/`await`
  const pages = await fse.readJson('./pages.json')
}
```

### Вернуть обещание

```js
import axios from 'axios'

export default function asyncModule () {
  return axios.get('https://jsonplaceholder.typicode.com/users')
    .then(res => res.data.map(user => '/users/' + user.username))
    .then((routes) => {
      // Do something by extending Nuxt routes
    })
}
```

## Общие фрагменты

### Варианты верхнего уровня

Иногда удобнее использовать параметры верхнего уровня при регистрации модулей в `nuxt.config.js` . Это позволяет нам комбинировать несколько источников опций.

**nuxt.config.js**

```js
export default {
  modules: [
    ['@nuxtjs/axios', { anotherOption: true }]
  ],

  // axios module is aware of this by using `this.options.axios`
  axios: {
    option1,
    option2
  }
}
```

**module.js**

```js
export default function (moduleOptions) {
  // `options` will contain option1, option2 and anotherOption
  const options = Object.assign({}, this.options.axios, moduleOptions)

  // ...
}
```

### Предоставить плагины

Обычно модули предоставляют один или несколько плагинов при добавлении. Например, модуль [bootstrap-vue](https://bootstrap-vue.js.org) должен был бы зарегистрироваться в Vue. В таких ситуациях мы можем использовать помощник `this.addPlugin` .

**plugin.js**

```js
import Vue from 'vue'
import BootstrapVue from 'bootstrap-vue/dist/bootstrap-vue.esm'

Vue.use(BootstrapVue)
```

**module.js**

```js
import path from 'path'

export default function nuxtBootstrapVue (moduleOptions) {
  // Register `plugin.js` template
  this.addPlugin(path.resolve(__dirname, 'plugin.js'))
}
```

### Шаблонные плагины

Зарегистрированные шаблоны и плагины могут использовать [шаблоны lodash](https://lodash.com/docs/4.17.4#template) для условного изменения вывода зарегистрированных плагинов.

**plugin.js**

```js
// Set Google Analytics UA
ga('create', '<%= options.ua %>', 'auto')

<% if (options.debug) { %>
// Dev only code
<% } %>
```

**module.js**

```js
import path from 'path'

export default function nuxtBootstrapVue (moduleOptions) {
  // Register `plugin.js` template
  this.addPlugin({
    src: path.resolve(__dirname, 'plugin.js'),
    options: {
      // Nuxt will replace `options.ua` with `123` when copying plugin to project
      ua: 123,

      // conditional parts with dev will be stripped from plugin code on production builds
      debug: this.options.dev
    }
  })
}
```

### Добавить библиотеку CSS

Если ваш модуль будет предоставлять библиотеку CSS, убедитесь, что выполнили проверку, если пользователь уже включил библиотеку, чтобы избежать дублирования, и добавьте **опцию, чтобы отключить** библиотеку CSS в модуле.

**module.js**

```js
export default function (moduleOptions) {
  if (moduleOptions.fontAwesome !== false) {
    // Add Font Awesome
    this.options.css.push('font-awesome/css/font-awesome.css')
  }
}
```

### Выбрасывать активы

<!-- todo: up2date? -->

Мы можем зарегистрировать плагины веб-пакетов для создания ресурсов во время сборки

**module.js**

```js
export default function (moduleOptions) {
  const info = 'Built by awesome module - 1.3 alpha on ' + Date.now()

  this.options.build.plugins.push({
    apply (compiler) {
      compiler.plugin('emit', (compilation, cb) => {
        // This will generate `.nuxt/dist/info.txt' with contents of info variable.
        // Source can be buffer too
        compilation.assets['info.txt'] = { source: () => info, size: () => info.length }

        cb()
      })
    }
  })
}
```

### Зарегистрировать пользовательские веб-загрузчики

Мы можем сделать то же самое, что `build.extend` в `nuxt.config.js` используя `this.extendBuild` .

**module.js**

```js
export default function (moduleOptions) {
    this.extendBuild((config, { isClient, isServer }) => {
      // `.foo` Loader
      config.module.rules.push({
        test: /\.foo$/,
        use: [...]
      })

      // Customize existing loaders
      // Refer to source code for Nuxt internals:
      // https://github.com/nuxt/nuxt.js/tree/dev/packages/builder/src/webpack/base.js
      const barLoader = config.module.rules.find(rule => rule.loader === 'bar-loader')
  })
}
```

## Выполнять задачи на определенных хуках

Вашему модулю может потребоваться выполнять действия только при определенных условиях, а не только во время инициализации Nuxt. Мы можем использовать мощную систему хуков Nuxt.js для выполнения задач по конкретным событиям (на основе [Hable](https://github.com/jsless/hable) ). Nuxt будет ждать вашей функции, если он вернет Promise или определен как `async` .

Вот несколько основных примеров:

```js
export default function myModule () {
  this.nuxt.hook('modules:done', (moduleContainer) => {
    // This will be called when all modules finished loading
  })

  this.nuxt.hook('render:before', (renderer) => {
    // Called after the renderer was created
  })

  this.nuxt.hook('build:compile', async ({ name, compiler }) => {
    // Called before the compiler (default: webpack) starts
  })

  this.nuxt.hook('generate:before', async (generator) => {
    // This will be called before Nuxt generates your pages
  })
}
```

## Команды модуля

**экспериментальный**

Начиная с `v2.4.0` , вы можете добавлять пользовательский nuxt команд через пакет стоимость Nuxt модуля. Для этого вы должны следовать API `NuxtCommand` при определении вашей команды. Простой пример, гипотетически размещенный в `my-module/bin/command.js` выглядит следующим образом:

```js
#!/usr/bin/env node

const consola = require('consola')
const { NuxtCommand } = require('@nuxt/cli')

NuxtCommand.run({
  name: 'command',
  description: 'My Module Command',
  usage: 'command <foobar>',
  options: {
    foobar: {
      alias: 'fb',
      type: 'string',
      description: 'Simple test string'
    }
  },
  run (cmd) {
    consola.info(cmd.argv)
  }
})
```

Несколько примечаний здесь. Во-первых, обратите внимание на вызов `/usr/bin/env` для получения исполняемого файла Node. Также обратите внимание, что синтаксис модуля ES нельзя использовать для команд, если вы вручную не включили [`esm`](https://github.com/standard-things/esm) в свой код.

Next, you'll notice how `NuxtCommand.run()` is used to specify the settings and behavior of the command. Options are defined in `options`, which get parsed via [`minimist`](https://github.com/substack/minimist). Once arguments are parsed, `run()` is automatically called with the `NuxtCommand` instance as first parameter.

В приведенном выше примере `cmd.argv` используется для получения проанализированных аргументов командной строки. В `NuxtCommand` есть больше методов и свойств - будет предоставлена документация по ним, поскольку эта функция будет дополнительно протестирована и улучшена.

To make your command recognizable by the Nuxt CLI, list it under the `bin` section of your package.json, using the `nuxt-module` convention, where `module` relates to your package's name. With this central binary, you can use `argv` to further parse more `subcommands` for your command if you desire.

```js
{
  "bin": {
    "nuxt-foobar": "./bin/command.js"
  }
}
```

Как только ваш пакет установлен (через NPM или Yarn), вы сможете выполнить `nuxt foobar ...` в командной строке.

<div class="Alert">
</div>

Есть намного больше хуков и возможностей для модулей. Пожалуйста, прочитайте [Nuxt Internals,](/api/internals) чтобы узнать больше о nuxt-internal API.



