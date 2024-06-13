---
title: "Popover API"
description: "Нативный способ создавать всплывающие элементы."
authors:
  - solarrust
contributors:
  - skorobaeus
related:
  - html/dialog
  - html/button
  - a11y/role-tooltip
keywords:
  - всплывающее окно
  - всплывающая подсказка
  - модальное окно
  - модалка
tags:
  - article
---

## Кратко

Удобный способ создавать всплывающие элементы. Новая возможность HTML. Имеет много встроенных фишек типа закрытия по клику на странице или по нажатию клавиши <kbd>Esc</kbd>.

Можно управлять как при помощи атрибутов HTML, так и через JavaScript.

## Пример

```html
<button
  popovertarget="my-popover"
  popovertargetaction="toggle"
>
  Всплываем!
</button>

<div id="my-popover" popover>
  <p>🛳️</p>
</div>
```

## Как понять

_Поповер_ — это любые всплывающие элементы в интерфейсе: всплывающие подсказки (тултипы), раскрывающиеся меню, превью товаров и другие.

В веб-дизайне есть большая группа _оверлеев (overlays)_. Это любой элемент, который появляется поверх остального содержимого интерфейсов. Дальше, внутри оверлеев, круг элементов сужается до более конкретных _попапов (popups)_. Обычно это небольшие окна, которые появляются автоматически. Вот тут-то и появляются поповеры, а ещё диалоговые окна (привет, [`<dialog>`](/html/dialog/)).

![Схема устройства оверлеев и поповеров, подробное описание перед ней.](./images/popups.png)

Долгое время в HTML не существовало простого способа реализовать поповеры. Самодельные решения требовали большого внимания к мелочам, часто не учитывали всех сценариев использования и были недоступными. Теперь, при помощи нескольких атрибутов, можно создать удобный и доступный для всех всплывающий элемент.

Преимущества:

- **всплытие на верхний уровень** — элемент всегда будет отображаться выше всех остальных, больше не нужно возиться с [`z-index`](/css/z-index/);
- **простое закрытие** — клик за пределами элемента закроет его и вернёт фокус на последний активный элемент на странице;
- **фокус внутри поповера** — при использовании клавиатуры следующий элемент, попавший в фокус, будет внутри поповера;
- **поддержка клаватуры** — по нажатию на клавишу <kbd>Esc</kbd> поповер закроется;
- **семантическая связь** — привязка поповера к элементу, который её вызывает.

## Как пишется

### Обязательные атрибуты

- `popover` — указывает, что блок теперь ведёт себя как поповер;
- `id` — уникальный идентификатор блока, используется для показа поповера;
- `popovertarget` — связывает элемент, например, кнопку или другой HTML-тег, с поповером. В качестве значения используется `id` элемента, который хотим показать.

```html
<button popovertarget="my-popover">
  Показать поповер
</button>

<div id="my-popover" popover>
  <p>Я — простой, но крутой поповер 😎</p>
</div>
```

<iframe title="Обязательные атрибуты" src="demos/required/" height="320"></iframe>

<aside>

🤫 Есть разговоры, что в дальнейшем атрибут `popovertarget` будет заменён на `invoketarget`. Но пока у него плохая браузерная поддержка. Используйте `popovertarget`.

</aside>

### Кнопка закрытия

По умолчанию элемент с `popovertarget` открывает и закрывает поповер. Это поведение можно изменить с помощью атрибута `popovertargetaction`.

Возможные значения:

- `toggle` — значение по умолчанию, применяется, если атрибут не прописан. Переключает состояние поповера между открытым и закрытым;
- `show` — открывает поповер;
- `hide` — закрывает поповер.

Таким образом, можно создать отдельные кнопки для открытия и закрытия элемента.

В примере ниже кнопка «Показать поповер» будет только открывать всплывающий элемент, но не закрывать его. Для закрытия внутрь добавлена соответствующая кнопка.

```html
<button popovertarget="my-popover" popovertargetaction="show">
  Показать поповер
</button>

<div id="my-popover" popover>
  <button popovertarget="my-popover" popovertargetaction="hide">
    <span aria-hidden="true">❌</span>
    <span class="sr-only">Закрыть</span>
  </button>

  <p>Я — поповер, который нельзя закрыть той же кнопкой</p>
</div>
```

<iframe title="Кнопка закрытия" src="demos/targetaction/" height="350"></iframe>

### Значения `popover`

На самом деле у атрибута `popover` есть два значения:

- `auto` — значение по умолчанию, не требуется прописывать явно. Одновременно показывается только один. Поповер может быть закрыт по клику за пределами элемента;
- `manual` — можно показать несколько одновременно. Поповер не может быть закрыт по клику за его пределами.

В демо ниже два поповера с разными значениями. Покликайте по ним, посмотрите на разницу в их поведении.

```html
<button popovertarget="popover-auto">
  <code>popover="auto"</code>
</button>

<div id="popover-auto" popover="auto">
  <p>Закрываю другие всплывашки. Легко закрываюсь сам.</p>
</div>

<button popovertarget="popover-manual">
  <code>popover="manual"</code>
</button>

<div id="popover-manual" popover="manual">
  <p>Не закрываю другие. Без кнопки не закроюсь.</p>
</div>
```

<iframe title="Разница значений атрибута popover" src="demos/manual/" height="500"></iframe>

## Стилизация

Стили поповера по умолчанию:

```css
[popover] {
  position: fixed;
  width: fit-content;
  height: fit-content;
  inset: 0px;
  margin: auto;
  border: solid;
  padding: 0.25em;
}
```

Переопределяйте их по необходимости.

Можно стилизовать подложку открытого поповера при помощи псевдокласса `::backdrop`.

```css
[popover]::backdrop {
  background-color: rgb(255 255 255 / 0.5);
}
```

<iframe title="Стилизация подложки" src="demos/backdrop/" height="320"></iframe>

## Анимации

Можно анимировать открытие и закрытие всплывающего элемента. Для этого потребуется описать несколько состояний.

При помощи директивы `@starting-style` задаём стили, от которых будет начинаться анимация открытия:

```css
@starting-style {
  [popover]:popover-open {
    opacity: 0;
    rotate: 1turn;
  }
}
```

Затем прописываем стили для открытого элемента:

```css
[popover]:popover-open {
  opacity: 1;
  rotate: 0turn;
  transition: rotate .5s, opacity .5s, display .5s, overlay .5s;
}
```

И в конце пишем конечные стили при закрытии окна:

```css
[popover]:not(:popover-open) {
  scale: 0;
  transition: scale .3s, display .3s, overlay .3s;
}
```

Обратите внимание на свойство `overlay`, которое используется внутри [`transition`](/css/transition/). Это ключевое слово необходимо для анимации всплытия элемента из верхнего слоя.

<iframe title="Анимация всплывающей подсказки" src="demos/animation/" height="320"></iframe>

## Отличия от `<dialog>`

В чём отличие поповера от такого же нового [`<dialog>`](/html/dialog/)? Кажется, что оба создают всплывающие окна.

Сам по себе элемент с атрибутом `popover` не несёт никакой семантической нагрузки. Вы можете самостоятельно добавить ему нужного смысла при помощи [ARIA-ролей](/a11y/aria-roles/). К примеру, [`tooltip`](/a11y/role-tooltip/).

Диалог, открытый в режиме модального окна, не может быть закрыт по клику за пределами элемента. Поповер, напротив, поддерживает такую возможность.

Также модальное окно блокирует остальную страницу под ним. Поповер так не поступает.

## Методы JavaScript

Для большинства сценариев работы с поповерами совсем не нужен JavaScript. Если есть такая необходимость, используйте следующие методы:

- `.showPopover()` — открыть поповер.
- `.hidePopover()` — закрыть поповер.
- `.togglePopover()` — переключить состояние поповера между открытым и закрытым.

```js
document.getElementById('my-popover').showPopover()
```

Проверить, что поповер открыт, можно при помощи `.matches(':popover-open')`, `.checkVisibility()` или `event.newState === 'open'`.