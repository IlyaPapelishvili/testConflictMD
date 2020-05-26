---
title: Команды и Развертывание
description: Nuxt.js поставляется с набором полезных команд, как для разработки, так и для производства.
---

> Nuxt.js поставляется с набором полезных команд, как для разработки, так и для производства.

## Список команд

команда | Описание
--- | ---
nuxt | Запустите сервер разработки на localhost: 3000 с горячей перезагрузкой.
нукст билд | Создайте свое приложение с помощью веб-пакета и уменьшите JS & CSS (для производства).
пустой старт | Запустите сервер в производственном режиме (после запуска `nuxt build` ).
генерировать нукст | Создайте приложение и сгенерируйте каждый маршрут в виде файла HTML (используется для статического хостинга).

#### аргументы

Вы можете использовать `--help` с любой командой, чтобы получить подробное описание. Общие аргументы:

- **`--config-file` или `-c` :** указать путь к файлу `nuxt.config.js` .
- **`--spa` или `-s` :** запускает команду в режиме SPA и отключает рендеринг на стороне сервера.
- **`--unix-socket` или `-n` :** указать путь к сокету UNIX.

#### Крючки

крюк | Задача
--- | ---
`cli:buildError` | Захватывает ошибки сборки в режиме разработки и отображает их на экране загрузки

#### Использование в package.json

Вы должны поместить эти команды в `package.json` :

```json
"scripts": {
  "dev": "nuxt",
  "build": "nuxt build",
  "start": "nuxt start",
  "generate": "nuxt generate"
}
```

Затем вы можете запустить свои команды с помощью `npm run <command>` (пример: `npm run dev` ).

<div class="Alert Alert--nuxt-green">
</div>

<b>Про совет:</b> передать аргументы НПМ команд, вам нужно дополнительное <code>--</code> имя скрипта (пример: <code>npm run dev -- --spa</code> ).




## Среда разработки

Чтобы запустить Nuxt в режиме разработки с горячей перезагрузкой:

```bash
nuxt
// OR
npm run dev
```

## Развертывание производства

Nuxt.js позволяет вам выбрать один из трех режимов развертывания приложения: SSR, Static Generated или SPA.

### Развертывание на стороне сервера (Universal SSR)

Чтобы развернуть, вместо запуска `nuxt` , вы, вероятно, захотите собрать его раньше времени. Следовательно, сборка и запуск - это отдельные команды:

```bash
nuxt build
nuxt start
```

Вы также можете установить `server.https` в вашем `nuxt.config.js` с тем же набором параметров, которые были переданы в [`https.createServer`](https://nodejs.org/api/https.html) , если вы решите обслуживать Nuxt.js в режиме HTTPS. Unix-сокеты также доступны, если вы установите параметр `server.socket` в `nuxt.config.js` (или `-n` в [CLI](https://nuxtjs.org/guide/commands#list-of-commands) ). При использовании [сокетов Unix](https://en.wikipedia.org/wiki/Berkeley_sockets) , не устанавливайте параметры `host` и `port` иначе параметр `socket` игнорируется.

Рекомендуется следующее: `package.json` :

```json
{
  "name": "my-app",
  "dependencies": {
    "nuxt": "latest"
  },
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start"
  }
}
```

Примечание: мы рекомендуем помещать `.nuxt` в `.npmignore` или `.gitignore` .

### Статическое сгенерированное развертывание (предварительно обработанное)

Nuxt.js дает вам возможность разместить ваше веб-приложение на любом статическом хостинге.

Чтобы сгенерировать наше веб-приложение в статические файлы:

```bash
npm run generate
```

Это создаст папку `dist` со всем внутри, готовым для развертывания на статическом хостинге.

To return a non-zero status code when a page error is encountered and let the CI/CD fail the deployment or build, you can use the `--fail-on-error` argument.

```bash
npm run generate --fail-on-error

// OR

yarn generate --fail-on-error
```

Если у вас есть проект с [динамическими маршрутами](/guide/routing#dynamic-routes) , взгляните на [конфигурацию создания,](/api/configuration-generate) чтобы сообщить Nuxt.js, как генерировать эти динамические маршруты.

<div class="Alert">
</div>

При генерации вашего веб-приложения с `nuxt generate` [контекст,](/api/context) заданный [asyncData](/guide/async-data) и [fetch](/guide/vuex-store#the-fetch-method) , не будет иметь `req` и `res` .




### Развертывание одностраничных приложений (SPA)

`nuxt generate` still needs its SSR engine during build/generate time while having the advantage of having all our pages pre rendered, and have a high SEO and page load score. The content is generated at *build time*. For example, we can't use it for applications where content depends on user authentication or a real time API (at least for the first load).

Идея SPA проста! Когда режим SPA включен с использованием `mode: 'spa'` или флага `--spa` , и мы запускаем сборку, генерация автоматически начинается после сборки. Это поколение содержит общие мета-ссылки и ссылки на ресурсы, но не содержимое страницы.

Итак, для развертывания SPA вы должны сделать следующее:

- Измените `mode` в `nuxt.config.js` на `spa` .
- Запустите `npm run build` .
- Разверните созданную папку `dist/` на свой статический хостинг, такой как Surge, GitHub Pages или nginx.

Другой возможный метод развертывания - использовать Nuxt в качестве промежуточного программного обеспечения в средах в режиме `spa` . Это помогает снизить нагрузку на сервер и использует Nuxt в проектах, где SSR невозможен.

<div class="Alert">
</div>

Прочтите наши [часто задаваемые вопросы](/faq) и найдите отличные примеры развертывания на популярных хостах.



