---
title: "`checkbox`"
description: "Роль, которая превращает элемент в чекбокс."
authors:
  - delioncourts
keywords:
  - доступность
  - ARIA
  - checkbox
related:
  - a11y/aria-roles
  - html/input
  - a11y/aria-checked
tags:
  - doka
  - placeholder
---

## Кратко

[ARIA-роль виджета](/a11y/aria-roles/#roli-vidzhetov) для чекбокса.

Эта роль есть у [`<input type="checkbox">`](/html/input/) по умолчанию.

## Пример

```html
<div
  role="checkbox"
  tabindex="0"
  aria-checked="false"
  aria-labelledby="checkbox-name">
</div>
<label id="checkbox-name">Выбери меня</label>
```

## Как пишется

Добавьте к тегу `role="checkbox"`. Лучше, чтобы это были семантически нейтральные теги вроде [`<div>`](/html/div/) и [`<span>`](/html/span/).

Атрибут нужен, когда нет возможности использовать возможности HTML. Например, когда это нужно для кастомного элемента. В этом случае [скринридер](/a11y/screenreaders/) объявит элемент как «чекбокс», без этой роли элемент будет обычным контейнером.

У элементов с `role="checkbox"` обязательно должен быть атрибут [`aria-checked`](/a11y/aria-checked/).

`aria-checked` указывает на текущее состояние чекбокса:

- `false` — чекбокс не выбран.
- `true` — чекбокс выбран.
- `mixed` — несколько чекбоксов выбрано, а какие-то не выбраны.

Чтобы элемент с ролью `checkbox` получал фокус и к нему можно было переместиться с помощью клавиши <kbd>Tab</kbd>, используйте `tabindex="0"`.

Ещё полезно использовать [`aria-labelledby`](/a11y/aria-labelledby/). Этот атрибут связывает кастомный чекбокс и подпись к нему с помощью одинакового ID. Когда пользователь сделает фокус на `<div>`, он услышит его название.

## Как понять

_Чекбокс_ или _флажок_ — это элемент формы, который позволяет пользователю отметить один или несколько вариантов.