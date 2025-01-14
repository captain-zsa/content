---
excluded:
  - js/promise
---

### Теория

Для начала вспомним работу оригинального `Promise.all`.

Он принимает коллекцию промисов, начинает одновременно их выполнять и возвращает новый промис. Если все переданные промисы выполнятся, возвращаемый промис тоже выполнится и в нём будет лежать массив результатов, причём в том же порядке. Но! Если какой-то промис вылетел с ошибкой, то `Promise.all` прекращает работу раньше и возвращаемый промис будет отклонён.

Таким образом у нас есть два сценария:

- позитивный, когда все промисы завершились успешно. Тут в ответ придёт массив результатов с сохранением очерёдности;
- негативный, когда какой-то промис завершился с ошибкой. Тут `Promise.all` не будет ждать завершение оставшихся, а сразу перейдёт в состояние `rejected` с полученной ошибкой.

### Подсказки

Сначала попробуйте решить самостоятельно. Но, если не получается, то вот подсказки:

1. Где-то нужно хранить результаты и как-то отслеживать, что все промисы завершились.
2. Возвращаться должен тоже промис.
3. По завершению каждого промиса не забываем:
  - запомнить результат под правильным индексом;
  - отметить, что активных промисов стало меньше на один.
4. Случай, когда у нас произошла ошибка, обрабатывать отдельно не нужно. Возвращаемый промис автоматически перейдёт в состояние `rejected`.

### Решение

Напишем код и прокомментируем каждую строчку.

```jsx
// На вход к нам приходит массив промисов
Promise.all = (promises) => {
  // Если передали пустой массив или строку
  if (promises.length === 0) {
    return Promise.resolve([])
  }

  // Здесь будем хранить результаты успешно завершённых промисов
  const results = []

  // Количество промисов, которые осталось выполнить.
  // Пока ещё не выполнился ни один промис
  let rest = promises.length

  // Возвращаем новый промис
  return new Promise((resolve, reject) => { // <= добавить reject, так он должен обрабатывать ошибки
    // Проходимся по списочку
    promises.forEach((promise, index) => {
      Promise.resolve(promise) // <= промисами могут быть и другие типы данных

        // Если промис завершается успешно
        .then((result) => {
          // Кладём его в наше хранилище. При этом сохраняем индекс,
          // под которым он был в массиве `promises`
          results[index] = result

          // Стало меньше на один невыполненный промис
          rest -= 1

          // Если активных промисов больше нет, резолвим результат
          if (rest === 0) resolve(results)
        }, reject) // <= достаточно передать reject как функцию
    })
  })
}
```

### Тесты

Теперь протестируем наш полифил

```jsx
// Сделаем искусственную задержку
const delay = (timeout) => new Promise((resolve) => setTimeout(resolve, timeout))

// И создадим несколько промисов для тестирования
const fetchUsers = () => delay(1000).then(() => [ 'Маша', 'Петя', 'Оля' ])
const problemPromise = delay(2000).then(() => { throw 'Houston, we have a problem' })
const stringPromise = delay(5000).then(() => 'Lorem ipsum dolor sit amet')

// Прогоним позитивный случай
Promise
  .all([ fetchUsers(), stringPromise ])
  .then(console.log)
/*
[
  [ 'Маша', 'Петя', 'Оля' ],
  'Lorem ipsum dolor sit amet'
]
*/

// Прогоним негативный случай, когда возникает ошибка
Promise
  .all([ fetchUsers(), problemPromise, stringPromise ])
  .then(console.log)
/* Uncaught (in promise) Houston, we have a problem */
```
