---
title: Vuex Store
description: Использование магазина для управления состоянием важно для каждого большого приложения. Вот почему Nuxt.js реализует Vuex в своем ядре.
---

> Использование магазина для управления состоянием важно для каждого большого приложения. Вот почему Nuxt.js реализует [Vuex](https://vuex.vuejs.org/en/) в своем ядре.

<div class="Promo__Video">
  <a href="https://vueschool.io/lessons/utilising-the-vuex-store-nuxtjs?friend=nuxt" target="_blank">
    <p class="Promo__Video__Icon">Посмотрите бесплатный урок о <strong>Nuxt.js и Vuex</strong> в Vue School</p>
  </a>
</div>

## Активировать магазин

Nuxt.js будет искать каталог `store` , если он существует, он будет:

1. Импорт Vuex,
2. Добавьте параметр `store` в корневой экземпляр Vue.

Nuxt.js позволяет выбирать между **2 режимами магазина** . Вы можете выбрать тот, который вы предпочитаете:

- **Модули:** каждый файл `.js` внутри каталога `store` преобразуется в [модуль пространства имен](http://vuex.vuejs.org/en/modules.html) ( `index` является корневым модулем).
- **Классический ( **не рекомендуется** ):** `store/index.js` возвращает метод для создания экземпляра хранилища.

Regardless of the mode, your `state` value should **always be a `function`** to avoid unwanted *shared* state on the server side.

## Режим модулей

> Nuxt.js позволяет вам иметь каталог `store` с каждым файлом, соответствующим модулю.

Для начала экспортируйте состояние как функцию, а мутации и действия как объекты в `store/index.js` :

```js
export const state = () => ({
  counter: 0
})

export const mutations = {
  increment (state) {
    state.counter++
  }
}
```

Затем вы можете получить файл `store/todos.js` :

```js
export const state = () => ({
  list: []
})

export const mutations = {
  add (state, text) {
    state.list.push({
      text,
      done: false
    })
  },
  remove (state, { todo }) {
    state.list.splice(state.list.indexOf(todo), 1)
  },
  toggle (state, todo) {
    todo.done = !todo.done
  }
}
```

Магазин будет создан так:

```js
new Vuex.Store({
  state: () => ({
    counter: 0
  }),
  mutations: {
    increment (state) {
      state.counter++
    }
  },
  modules: {
    todos: {
      namespaced: true,
      state: () => ({
        list: []
      }),
      mutations: {
        add (state, { text }) {
          state.list.push({
            text,
            done: false
          })
        },
        remove (state, { todo }) {
          state.list.splice(state.list.indexOf(todo), 1)
        },
        toggle (state, { todo }) {
          todo.done = !todo.done
        }
      }
    }
  }
})
```

И в ваших `pages/todos.vue` , используя модуль `todos` :

```html
<template>
  <ul>
    <li v-for="todo in todos">
      <input :checked="todo.done" @change="toggle(todo)" type="checkbox">
      <span :class="{ done: todo.done }">{{ todo.text }}</span>
    </li>
    <li><input @keyup.enter="addTodo" placeholder="What needs to be done?"></li>
  </ul>
</template>

<script>
import { mapMutations } from 'vuex'

export default {
  computed: {
    todos () {
      return this.$store.state.todos.list
    }
  },
  methods: {
    addTodo (e) {
      this.$store.commit('todos/add', e.target.value)
      e.target.value = ''
    },
    ...mapMutations({
      toggle: 'todos/toggle'
    })
  }
}
</script>

<style>
.done {
  text-decoration: line-through;
}
</style>
```

> Метод модуля также работает для определений верхнего уровня без реализации подкаталога в каталоге `store`

Пример для состояния: вы создаете файл `store/state.js` и добавляете следующее

```js
export default () => ({
  counter: 0
})
```

И соответствующие мутации могут быть в файле `store/mutations.js`

```js
export default {
  increment (state) {
    state.counter++
  }
}
```

### Файлы модуля

При желании вы можете разбить файл модуля на отдельные файлы: `state.js` , `actions.js` , `mutations.js` и `getters.js` . Если вы сохраняете файл `index.js` с состоянием, геттерами и мутациями, имея один отдельный файл для действий, он также будет правильно распознаваться.

> Примечание. При использовании модулей с разделенными файлами вы должны помнить, что при использовании функций стрелок `this` доступно только лексически. Лексическая область видимости означает, что `this` всегда ссылается на владельца функции стрелки. Если функция стрелки не содержится, то `this` будет неопределенным. Решение состоит в том, чтобы использовать «нормальную» функцию, которая производит свою собственную область и, таким образом, имеет `this` наличии.

### Плагины

Вы можете добавить дополнительные плагины в хранилище (в режиме модулей), поместив их в файл `store/index.js` :

```js
import myPlugin from 'myPlugin'

export const plugins = [ myPlugin ]

export const state = () => ({
  counter: 0
})

export const mutations = {
  increment (state) {
    state.counter++
  }
}
```

Больше информации о плагинах: [документация Vuex](https://vuex.vuejs.org/en/plugins.html) .

## Метод получения

> Метод `fetch` используется для заполнения хранилища перед отображением страницы, он похож на метод `asyncData` за исключением того, что он не устанавливает данные компонента.

Более подробная информация о методе выборки: [API Pages fetch](/api/pages-fetch) .

## Действие nuxtServerInit

If the action `nuxtServerInit` is defined in the store and the mode is `universal`, Nuxt.js will call it with the context (only from the server-side). It's useful when we have some data on the server we want to give directly to the client-side.

Например, допустим, у нас есть сеансы на стороне сервера, и мы можем получить доступ к подключенному пользователю через `req.session.user` . Чтобы предоставить аутентифицированному пользователю наш магазин, мы обновляем наш `store/index.js` следующим образом:

```js
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}
```

> Если вы используете режим *Модули* магазина Vuex, только первичный модуль (в `store/index.js` ) получит это действие. Вам нужно будет связать действия вашего модуля оттуда.

[Контекст](/api/context) передается `nuxtServerInit` в качестве 2-го аргумента, он такой же, как `asyncData` или метод `fetch` .

Если `nuxt generate` , `nuxtServerInit` будет выполняться для каждого сгенерированного динамического маршрута.

> Примечание. Асинхронные действия `nuxtServerInit` должны возвращать Promise или использовать async / await, чтобы позволить серверу `nuxt` ожидать их.

```js
actions: {
  async nuxtServerInit({ dispatch }) {
    await dispatch('core/load')
  }
}
```

## Строгий режим Vuex

Строгий режим включен по умолчанию в режиме разработки и отключен в производственном режиме. Чтобы отключить строгий режим в dev, следуйте приведенному ниже примеру в `store/index.js` :

`export const strict = false`

## Классический режим

> Эта функция устарела и будет удалена в Nuxt 3.

Чтобы активировать хранилище в классическом режиме, мы создаем файл `store/index.js` который должен экспортировать метод, который возвращает экземпляр Vuex:

```js
import Vuex from 'vuex'

const createStore = () => {
  return new Vuex.Store({
    state: () => ({
      counter: 0
    }),
    mutations: {
      increment (state) {
        state.counter++
      }
    }
  })
}

export default createStore
```

> Нам не нужно устанавливать `vuex` поскольку он поставляется с Nuxt.js.

Теперь мы можем использовать `this.$store` внутри наших компонентов:

```html
<template>
  <button @click="$store.commit('increment')">{{ $store.state.counter }}</button>
</template>
```
