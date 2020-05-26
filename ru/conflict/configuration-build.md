---
title: API: свойство сборки
description: Nuxt.js позволяет вам настроить конфигурацию веб-пакета для создания вашего веб-приложения, как вы хотите.
---

> Nuxt.js позволяет вам настроить конфигурацию веб-пакета для создания вашего веб-приложения, как вы хотите.

## анализировать

> Nuxt.js использует [webpack-bundle-analyzer,](https://github.com/webpack-contrib/webpack-bundle-analyzer) чтобы позволить вам визуализировать ваши пакеты и как их оптимизировать.

- Тип: `Boolean` или `Object`
- По умолчанию: `false`

Если объект, посмотрите доступные свойства [здесь](https://github.com/webpack-contrib/webpack-bundle-analyzer#options-for-plugin) .

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    analyze: true,
    // or
    analyze: {
      analyzerMode: 'static'
    }
  }
}
```

<div class="Alert Alert--teal">
</div>

**Info:** you can use the command `yarn nuxt build --analyze` or `yarn nuxt build -a` to build your application and launch the bundle analyzer on [http://localhost:8888](http://localhost:8888). If you are not using `yarn` you can run the command with `npx`.




## галдеж

> Настройте конфигурацию Babel для файлов JavaScript и Vue. `.babelrc` по умолчанию игнорируется.

- Тип: `Object` См `babel-loader` [варианты](https://github.com/babel/babel-loader#options) и `babel` [варианта](https://babeljs.io/docs/en/options)

- По умолчанию:

    ```js
    {
      babelrc: false,
      cacheDirectory: undefined,
      presets: ['@nuxt/babel-preset-app']
    }
    ```

Цели по умолчанию для [@ nuxt / babel-preset-app](https://github.com/nuxt/nuxt.js/blob/dev/packages/babel-preset-app/src/index.js) `ie: '9'` в `client` сборке и « `node: 'current'` в `server` сборке.

### пресеты

- Тип: `Function`
- Аргумент:
    1. `Object` : {isServer: true | ложный }
    2. `Array` :
        - имя предустановки `@nuxt/babel-preset-app`
        - [`options`](https://github.com/nuxt/nuxt.js/tree/dev/packages/babel-preset-app#options) `@nuxt/babel-preset-app`

**Note**: The presets configured in `build.babel.presets` will be applied to both, the client and the server build. The target will be set by Nuxt accordingly (client/server). If you want configure the preset differently for the client or the server build, please use `presets` as a function:

> Мы **настоятельно рекомендуем** использовать предустановки по умолчанию вместо настройки ниже

```js
export default {
  build: {
    babel: {
      presets({ isServer }, [ preset, options ]) {
        // change options directly
        options.targets = isServer ? ... :  ...
        options.corejs = ...
        // return nothing
      }
    }
  }
}
```

Или переопределите значение по умолчанию, возвращая весь список пресетов:

```js
export default {
  build: {
    babel: {
      presets ({ isServer }, [ preset, options ]) {
        return [
          [
            preset, {
              buildTarget: isServer ? 'server' : 'client',
              ...options
            }],
          [
            // Other presets
          ]
        ]
      }
    }
  }
}
```

## кэш

- Тип: `Boolean`
- По умолчанию: `false`
- ⚠️ экспериментальная

> Включить кеш-память [terser-webpack-plugin](https://github.com/webpack-contrib/terser-webpack-plugin#options) и [cache-loader](https://github.com/webpack-contrib/cache-loader#cache-loader)

## crossorigin

- Тип: `String`

- По умолчанию: `undefined`

    Настройте атрибут `crossorigin` `<link rel="stylesheet">` и `<script>` в сгенерированном HTML.

    Больше информации: [атрибуты настроек CORS](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_settings_attributes)

## cssSourceMap

- Тип: `boolean`
- По умолчанию: `true` для dev и `false` для производства.

> Включает поддержку исходной карты CSS

## devMiddleware

- Тип: `Object`

Посмотрите [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware) для доступных опций.

## DevTools

- Тип: `boolean`
- По умолчанию: `false`

Configure whether to allow [vue-devtools](https://github.com/vuejs/vue-devtools) inspection.

Если вы уже активировали через nuxt.config.js или другим способом, devtools включается независимо от флага.

## простираться

> Расширьте конфигурацию веб-пакета вручную для клиентских и серверных пакетов.

- Тип: `Function`

Расширение вызывается дважды, один раз для комплекта сервера и один раз для комплекта клиента. Аргументы метода:

1. Конфигурационный объект Webpack,
2. Объект со следующими ключами (все логические, кроме `loaders` ): `isDev` , `isClient` , `isServer` , `loaders` .

<div class="Alert Alert--orange">
</div>

**Предупреждение:** `isClient` и `isServer` предоставляемые в, отделены от ключей, доступных в [`context`](/api/context) . Они **не** устарели. Не используйте `process.client` и `process.server` здесь, поскольку они не `undefined` в данный момент.




Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    extend (config, { isClient }) {
      // Extend only webpack config for client-bundle
      if (isClient) {
        config.devtool = 'source-map'
      }
    }
  }
}
```

Если вы хотите узнать больше о нашей конфигурации веб-пакетов по умолчанию, взгляните на наш [каталог веб-пакетов](https://github.com/nuxt/nuxt.js/tree/dev/packages/webpack/src/config) .

### погрузчики в выдвинутом

`loaders` та же структура объектов, что и у [build.loaders](#loaders) , так что вы можете изменять параметры загрузчиков в `extend` .

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    extend (config, { isClient, loaders: { vue } }) {
      // Extend only webpack config for client-bundle
      if (isClient) {
        vue.transformAssetUrls.video = ['src', 'poster']
      }
    }
  }
}
```

## extractCSS

> Включает общее извлечение CSS с использованием [рекомендаций](https://ssr.vuejs.org/en/css.html) Vue Server Renderer.

- Тип: `Boolean`
- По умолчанию: `false`

С помощью [`extract-css-chunks-webpack-plugin`](https://github.com/faceyspacey/extract-css-chunks-webpack-plugin/) все ваши CSS-файлы будут извлечены в отдельные файлы, обычно по одному на компонент. Это позволяет кэшировать ваш CSS и JavaScript по отдельности и стоит попробовать, если у вас много глобального или общего CSS.

<div class="Alert Alert--teal">
</div>

**Примечание:** до Vue 2.5.18 была ошибка, из-за которой при использовании этой опции удалялся критический импорт CSS.




Вы можете извлечь все ваши CSS в один файл. Для этого есть обходной путь:

<div class="Alert Alert--orange">Extract Не рекомендуется распаковывать все в один файл. Извлечение в несколько файлов CSS лучше для кэширования и изоляции перед загрузкой. Это также может повысить производительность страницы, загрузив и разрешив только те ресурсы, которые необходимы.</div>

```js
export default {
  build: {
    extractCSS: true,
    optimization: {
      splitChunks: {
        cacheGroups: {
          styles: {
            name: 'styles',
            test: /\.(css|vue)$/,
            chunks: 'all',
            enforce: true
          }
        }
      }
    }
  }
}
```

## имена файлов

> Настройте имена файлов пакета.

- Тип: `Object`

- По умолчанию:

    ```js
    {
      app: ({ isDev }) => isDev ? '[name].js' : '[contenthash].js',
      chunk: ({ isDev }) => isDev ? '[name].js' : '[contenthash].js',
      css: ({ isDev }) => isDev ? '[name].css' : '[contenthash].css',
      img: ({ isDev }) => isDev ? '[path][name].[ext]' : 'img/[contenthash:7].[ext]',
      font: ({ isDev }) => isDev ? '[path][name].[ext]' : 'fonts/[contenthash:7].[ext]',
      video: ({ isDev }) => isDev ? '[path][name].[ext]' : 'videos/[contenthash:7].[ext]'
    }
    ```

Этот пример изменяет причудливые имена чанка на числовые идентификаторы ( `nuxt.config.js` ):

```js
export default {
  build: {
    filenames: {
      chunk: ({ isDev }) => isDev ? '[name].js' : '[id].[contenthash].js'
    }
  }
}
```

Чтобы понять немного больше об использовании манифестов, взгляните на эту [документацию веб-пакета](https://webpack.js.org/guides/code-splitting/) .

## friendlyErrors

- Тип: `Boolean`
- По умолчанию: `true` (наложение включено)

Включает или отключает наложение, предоставляемое [FriendlyErrorsWebpackPlugin](https://github.com/nuxt/friendly-errors-webpack-plugin)

## hardSource

- Тип: `Boolean`
- По умолчанию: `false`
- ⚠️ экспериментальная

Включает [HardSourceWebpackPlugin](https://github.com/mzgoddard/hard-source-webpack-plugin) для улучшенного кэширования

## hotMiddleware

- Тип: `Object`

См. [Webpack-hot-middleware](https://github.com/glenjamin/webpack-hot-middleware) для доступных опций.

## html.minify

- Тип: `Object`
- По умолчанию:

```js
{
  collapseBooleanAttributes: true,
  decodeEntities: true,
  minifyCSS: true,
  minifyJS: true,
  processConditionalComments: true,
  removeEmptyAttributes: true,
  removeRedundantAttributes: true,
  trimCustomFragments: true,
  useShortDoctype: true
}
```

**Attention:** If you make changes to `html.minify`, they won't be merged with the defaults!

Конфигурация для плагина [html-minifier,](https://github.com/kangax/html-minifier) используемого для минимизации HTML-файлов, созданных в процессе сборки (будет применяться для *всех режимов* ).

## показатель

> Показать индикатор сборки для замены горячего модуля в разработке (доступно в `v2.8.0+` )

- Тип: `Boolean`
- По умолчанию: `true`

![nuxt-билд-индикатор](https://user-images.githubusercontent.com/5158436/58500509-93ba0f80-8197-11e9-8524-e115c6d32571.gif)

## погрузчики

> Настройте параметры встроенных загрузчиков веб-пакетов Nuxt.js.

- Тип: `Object`
- По умолчанию:

```js
{
  file: {},
  fontUrl: { limit: 1000 },
  imgUrl: { limit: 1000 },
  pugPlain: {},
  vue: {
    transformAssetUrls: {
      video: 'src',
      source: 'src',
      object: 'src',
      embed: 'src'
    }
  },
  css: {},
  cssModules: {
    localIdentName: '[local]_[hash:base64:5]'
  },
  less: {},
  sass: {
    indentedSyntax: true
  },
  scss: {},
  stylus: {},
  vueStyle: {}
}
```

> Примечание. Помимо указания конфигураций в `nuxt.config.js` , его также можно изменить с помощью [build.extend.](#extend)

### loaders.file

> Больше подробностей в [опциях загрузчика файлов](https://github.com/webpack-contrib/file-loader#options) .

### loaders.fontUrl и loaders.imgUrl

> Больше подробностей в [настройках url-загрузчика](https://github.com/webpack-contrib/url-loader#options) .

### loaders.pugPlain

> More details are in [pug-plain-loader](https://github.com/yyx990803/pug-plain-loader) or [Pug compiler options](https://pugjs.org/api/reference.html#options).

### loaders.vue

> Больше подробностей в [опциях vue-loader](https://vue-loader.vuejs.org/options.html) .

### loaders.css и loaders.cssModules

> Больше подробностей в [настройках css-загрузчика](https://github.com/webpack-contrib/css-loader#options) . Примечание: cssModules - это параметры загрузчика для использования [CSS-модулей.](https://vue-loader.vuejs.org/guide/css-modules.html#css-modules)

### loaders.less

> You can pass any Less specific options to the `less-loader` via `loaders.less`. See the [Less documentation](http://lesscss.org/usage/#command-line-usage-options) for all available options in dash-case.

### loaders.sass и loaders.scss

> См. [Документацию Node Sass](https://github.com/sass/node-sass/blob/master/README.md#options) для всех доступных опций Sass. Примечание: `loaders.sass` предназначен для [синтаксиса с отступом Sass](http://sass-lang.com/documentation/file.INDENTED_SYNTAX.html)

### loaders.vueStyle

> Больше подробностей в [опциях vue-style-loader](https://github.com/vuejs/vue-style-loader#options) .

## оптимизация

- Тип: `Object`

- По умолчанию:

    ```js
    {
      minimize: true,
      minimizer: [
        // terser-webpack-plugin
        // optimize-css-assets-webpack-plugin
      ],
      splitChunks: {
        chunks: 'all',
        automaticNameDelimiter: '.',
        name: undefined,
        cacheGroups: {}
      }
    }
    ```

Значение по умолчанию для `splitChunks.name` равно `true` в режиме `dev` или `analyze` .

Вы можете установить для `minimizer` настроенный массив плагинов или установить для параметра `minimize` значение `false` чтобы отключить все минимизаторы. ( `minimize` по умолчанию отключено для разработки)

См. [Оптимизация веб-пакетов](https://webpack.js.org/configuration/optimization) .

## optimizeCSS

- Тип: `Object` или `Boolean`
- По умолчанию:
    - `false`
    - `{}` когда включен extractCSS

Оптимизировать параметры плагина CSSAssets.

Смотрите [NMFR / optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin) .

## параллельно

- Тип: `Boolean`
- По умолчанию: `false`
- ⚠️ экспериментальная

> Включить [загрузчик потоков](https://github.com/webpack-contrib/thread-loader#thread-loader) в сборке веб-пакетов

## плагины

> Добавить плагины веб-пакета

- Тип: `Array`
- По умолчанию: `[]`

Пример ( `nuxt.config.js` ):

```js
import webpack from 'webpack'
import { version } from './package.json'
export default {
  build: {
    plugins: [
      new webpack.DefinePlugin({
        'process.VERSION': version
      })
    ]
  }
}
```

## postcss

> Настройка плагинов [PostCSS Loader](https://github.com/postcss/postcss-loader#usage) .

- Тип: `Array` (устаревший, переопределит значения по умолчанию), `Object` (рекомендуется), `Function` или `Boolean`

    **Примечание:** Nuxt.js применил [PostCSS Preset Env](https://github.com/csstools/postcss-preset-env) . По умолчанию он включает функции [Stage 2](https://cssdb.org/) и [Autoprefixer](https://github.com/postcss/autoprefixer) , вы можете использовать `build.postcss.preset` для его настройки.

- По умолчанию:

    ```js
    {
      plugins: {
        'postcss-import': {},
        'postcss-url': {},
        'postcss-preset-env': this.preset,
        'cssnano': { preset: 'default' } // disabled in dev mode
      },
      order: 'presetEnvAndCssnanoLast',
      preset: {
        stage: 2
      }
    }
    ```

Ваши пользовательские настройки плагина будут объединены с плагинами по умолчанию (если вы не используете `Array` вместо `Object` ).

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    postcss: {
      plugins: {
        // Disable `postcss-url`
        'postcss-url': false,
        // Add some plugins
        'postcss-nested': {},
        'postcss-responsive-type': {},
        'postcss-hexrgba': {}
      },
      preset: {
        autoprefixer: {
          grid: true
        }
      }
    }
  }
}
```

Если конфигурация postcss является `Object` , то `order` можно использовать для определения порядка плагинов:

- Тип: `Array` (упорядоченные имена плагинов), `String` (имя предустановленного порядка), `Function`
- По умолчанию: `cssnanoLast` (поставить `cssnano` последним)

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    postcss: {
      // preset name
      order: 'cssnanoLast',
      // ordered plugin names
      order: ['postcss-import', 'postcss-preset-env', 'cssnano']
      // Function to calculate plugin order
      order: (names, presets) => presets.cssnanoLast(names)
    }
  }
}
```

### плагины postcss и nuxt-tailwindcss

Если вы хотите применить плагин postcss (например, postcss-pxtorem) к конфигурации nuxt-tailwindcss, вам нужно изменить порядок и загрузить первый tailwindcss.

**Эта настройка не влияет на nuxt-purgecss.**

Пример ( `nuxt.config.js` ):

```js
import { join } from 'path'

export default {
  // ...
  build: {
    postcss: {
      plugins: {
        tailwindcss: join(__dirname, 'tailwind.config.js'),
        'postcss-pxtorem': {
          propList: [
            '*',
            '!border*',
          ]
        }
      }
    }
  }
}
```

## профиль

- Тип: `Boolean`
- По умолчанию: включено аргументом командной строки `--profile`

> Включить профилировщик в [WebpackBar](https://github.com/nuxt/webpackbar#profile)

## publicPath

> Nuxt.js позволяет загружать файлы dist в CDN для максимальной производительности, просто установите `publicPath` в CDN.

- Тип: `String`
- По умолчанию: `'/_nuxt/'`

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    publicPath: 'https://cdn.nuxtjs.org'
  }
}
```

Затем при запуске `nuxt build` загрузите содержимое `.nuxt/dist/client` в свой CDN и вуаля!

## тихо

> Подавляет большую часть журнала вывода сборки

- Тип: `Boolean`
- По умолчанию: Включено, когда [std-env](https://github.com/blindmedia/std-env) обнаруживает `CI` или `test` среду

## splitChunks

- Тип: `Object`

- По умолчанию:

    ```js
    {
      layouts: false,
      pages: true,
      commons: true
    }
    ```

Если разделить коды для `layout` , `pages` и `commons` (общие библиотеки: vue | vue-loader | vue-router | vuex ...).

## SSR

> Создает специальный пакет веб-пакетов для рендера SSR.

- Тип: `Boolean`
- По умолчанию: `true` для универсального режима и `false` для спа-режима

Этот параметр автоматически устанавливается в зависимости от значения `mode` если он не указан.

## styleResources

- Тип: `Object`
- По умолчанию: `{}`

<div class="Alert Alert--orange">
</div>

**Предупреждение:** это свойство устарело. Пожалуйста, используйте вместо этого [style-resources-module](https://github.com/nuxt-community/style-resources-module/) для улучшения производительности и лучшего DX!




Это полезно, когда вам нужно внедрить некоторые переменные и миксины в свои страницы без необходимости каждый раз импортировать их.

Nuxt.js использует https://github.com/yenshih/style-resources-loader для достижения этой цели.

Необходимо указать паттерны / путь , который вы хотите включить для данных предварительных процессоров: `less` , `sass` , `scss` или `stylus`

Здесь нельзя использовать псевдонимы пути ( `~` и `@` ), вам нужно использовать относительные или абсолютные пути.

`nuxt.config.js` :

```js
{
  build: {
    styleResources: {
      scss: './assets/variables.scss',
      less: './assets/*.less',
      // sass: ...,
      // scss: ...
      options: {
        // See https://github.com/yenshih/style-resources-loader#options
        // Except `patterns` property
      }
    }
  }
}
```

## шаблоны

> Nuxt.js позволяет вам предоставлять свои собственные шаблоны, которые будут отображаться на основе конфигурации Nuxt. Эта функция особенно полезна для использования с [модулями](/guide/modules) .

- Тип: `Array`

Пример ( `nuxt.config.js` ):

```js
export default {
  build: {
    templates: [
      {
        src: '~/modules/support/plugin.js', // `src` can be absolute or relative
        dst: 'support.js', // `dst` is relative to project `.nuxt` dir
        options: { // Options are provided to template as `options` key
          live_chat: false
        }
      }
    ]
  }
}
```

Шаблоны отображаются с использованием [`lodash.template`](https://lodash.com/docs/#template) вы можете узнать больше об их использовании [здесь](https://github.com/learn-co-students/javascript-lodash-templates-v-000) .

## terser

- Тип: `Object` или `Boolean`
- По умолчанию:

```js
{
  parallel: true,
  cache: false,
  sourceMap: false,
  extractComments: {
    filename: 'LICENSES'
  },
  terserOptions: {
    output: {
      comments: /^\**!|@preserve|@license|@cc_on/
    }
  }
}
```

Варианты плагинов Terser. Установите в `false` чтобы отключить этот плагин.

`sourceMap` будет включен, когда webpack `config.devtool` соответствует `source-?map`

Смотрите [webpack-contrib / terser-webpack-plugin](https://github.com/webpack-contrib/terser-webpack-plugin) .

## transpile

- Тип: `Array<String | RegExp | Function>`
- По умолчанию: `[]`

Если вы хотите перенести определенные зависимости с помощью Babel, вы можете добавить их в `build.transpile` . Каждый элемент в transile может быть именем пакета, строкой или объектом регулярного выражения, совпадающим с именем файла зависимости.

Начиная с `v2.9.0` , вы также можете использовать функцию для условного переноса, функция получит объект ( `{ isDev, isServer, isClient, isModern, isLegacy }` ):

```js
{
  build: {
    transpile: [
      ({ isLegacy }) => isLegacy && 'ky'
    ]
  }
}
```

## vueLoader

> Примечание: эта конфигурация была удалена начиная с Nuxt 2.0, используйте вместо этого [`build.loaders.vue`](#loaders) .

- Тип: `Object`

- По умолчанию:

    ```js
    {
      productionMode: !this.options.dev,
      transformAssetUrls: {
        video: 'src',
        source: 'src',
        object: 'src',
        embed: 'src'
      }
    }
    ```

> Укажите параметры [Vue Loader](https://vue-loader.vuejs.org/options.html) .

## смотреть

> Вы можете предоставить свои пользовательские файлы для просмотра и восстановления после изменений. Эта функция особенно полезна для использования с [модулями](/guide/modules) .

- Тип: `Array<String>`

```js
export default {
  build: {
    watch: [
      '~/.nuxt/support.js'
    ]
  }
}
```

## FollowSymlinks

> По умолчанию процесс сборки не сканирует файлы внутри символических ссылок. Это логическое значение включает их, что позволяет использовать символические ссылки внутри таких папок, как, например, папка «pages».

- Тип: `Boolean`

```js
export default {
  build: {
    followSymlinks: false
  }
}
```
