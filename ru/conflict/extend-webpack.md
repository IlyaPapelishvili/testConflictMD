---
title: Как расширить конфиг вебпака?
description: Как расширить конфигурацию webpack в моем приложении Nuxt.js?
---

Вы можете расширить конфигурацию веб-пакета nuxt с помощью параметра `extend` в вашем `nuxt.config.js` . `extend` вариант на `build` свойства является метод , который принимает два аргумента. Первым аргументом является объект `config` веб-пакета, экспортированный из конфигурации веб-пакета nuxt. Второй параметр - это объект контекста со следующими логическими свойствами: `{ isDev, isClient, isServer, loaders }` .

```js
export default {
  build: {
    extend (config, { isDev, isClient }) {
      // ..
      config.module.rules.push(
        {
          test: /\.(ttf|eot|svg|woff(2)?)(\?[a-z0-9=&.]+)?$/,
          loader: 'file-loader'
        }
      )
      // Sets webpack's mode to development if `isDev` is true.
      if (isDev) { config.mode = 'development' }
    }
  }
}
```

`extend` метод вызывается дважды - один раз для пучка клиента , а другой для расслоения сервера.

## Примеры

#### Настройка конфигурации чанков

Возможно, вы захотите немного изменить [конфигурацию оптимизации](/api/configuration-build#optimization) , избегая перезаписи объекта по умолчанию.

```js
export default {
  build: {
    extend (config, { isClient }) {
      if (isClient) {
          config.optimization.splitChunks.maxSize = 200000;
      }
    }
  }
}
```

#### Запустите ESLint для каждой сборки веб-пакета в среде разработки

Чтобы быть в курсе ошибок стиля кода, вы можете запускать [ESLint](https://github.com/webpack-contrib/eslint-loader) при каждой сборке в среде разработки.

```js
export default {
  build: {
    extend (config, { isDev, isClient }) {
      if (isDev && isClient) {
          config.module.rules.push({
              enforce: 'pre',
              test: /\.(js|vue)$/,
              loader: 'eslint-loader',
              exclude: /(node_modules)/,
          });
      }
    }
  }
}
```
