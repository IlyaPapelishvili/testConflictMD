---
title: Асинхронные данные в компонентах NuxtJS?
description: Асинхронные данные в компонентах NuxtJS?
---

Поскольку у компонентов нет метода `asyncData` , вы не можете напрямую получить асинхронную сторону сервера данных внутри компонента. Чтобы обойти это ограничение, у вас есть два основных варианта:

1. Сделайте вызов API в `mounted` хуке и установите свойства данных при загрузке. *Недостаток: не работает для рендеринга на стороне сервера.*
2. компоненты не имеют метод `asyncData` , вы не можете напрямую получить асинхронные данные
3. Make the API call in the `asyncData` or `fetch` methods of the page component and pass the data as props to the sub components. Server rendering will work fine. *Downside: the `asyncData` or `fetch` of the page might be less readable because it's loading the data for other components*.
