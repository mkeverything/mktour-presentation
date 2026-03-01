---
theme: default
title: mktour - черновик
info: |
  минимальный каркас презентации mktour для настройки стиля
mdc: true
fonts:
  provider: none
---

# mktour

турнирные сайты без боли

---
layout: section
---

# раздел 1
## у всех хуево, у нас быстро и просто

---

# тест на обычном сайте турниров

создаем турнир на 20 человек и пытаемся провести его вживую.

сценарий:

- регистрация турнира
- добавление участников
- запуск раунда
- внесение результатов

---

# mktour в том же сценарии

раз  
два  
три  
четыре  

вуаля

---
layout: section
---

# раздел 2
## mktour как социальная сеть

---

# кульминация

игрок вносит свой результат сам.

---
layout: section
---

# раздел 3
## с шариком в руках

---

# цена вопроса

показываем прайсы challenge и swiss.

рубрика: "прайс ревил пати"  
три, два, один - лопаем шарик.  
бесплатно.

---
layout: section
---

# раздел 4
## спасибо марку

---

# финал

наш швейцарский коллега марк разработал новый алгоритм швейцарских турниров.

из-за слов "ассимиляция", "полиномиальный", "мэтчинг" речь не озвучиваем.

спасибо.

### шахматные турниры просто и быстро

<div class="mk-kicker">черновая структура - slidev</div>

---
layout: section
---

# раздел 1
## у всех хуево, у нас быстро и просто

---
layout: iframe
url: https://challonge.com/
scale: 0.7
---

# берем обычный сайт

тест: создать турнир на **20 человек** и провести его прямо сейчас.

<div class="mk-step-list">
  <div v-click>1) регистрация турнира</div>
  <div v-click>2) участники</div>
  <div v-click>3) сетка / пары</div>
  <div v-click>4) фиксация результатов</div>
</div>

<!--
live demo (slidev):
- встроенный вариант: layout iframe-right / iframe / iframe-left.
- если сайт блокирует iframe (csp/x-frame-options), переключиться на реальное окно chrome.
- управление слайдами вести из presenter mode: http://localhost:3030/presenter
-->

---

# в интернете таких платформ около 45

мы попробовали почти все.

<div class="mk-quote" v-click>
работают примерно одинаково. <br>
одинаково (хуево).
</div>

---

# сценка: 20 участников

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="mk-card">
    <h3>что происходит</h3>
    <ul>
      <li v-click>длинный путь до старта</li>
      <li v-click>ручные действия в каждом раунде</li>
      <li v-click>ошибки при вводе и пересчете</li>
      <li v-click>задержки для игроков</li>
    </ul>
  </div>
  <div class="mk-card">
    <h3>ощущение</h3>
    <p v-click>организатор борется с интерфейсом, а не проводит турнир.</p>
  </div>
</div>

---
layout: iframe
url: https://beta.mktour.org/
scale: 0.7
---

# и потом:
## mktour

<div class="mk-countdown">
  <span v-click>раз</span>
  <span v-click>два</span>
  <span v-click>три</span>
  <span v-click>четыре</span>
</div>

<div class="mk-hero" v-click>вуаля</div>

---
layout: center
class: text-center
---

# mktour вживую

<!--
если mktour.app недоступен или нужен staging:
- заменить url на локальный/стейдж адрес.
- при блокировке iframe перейти в отдельное окно chrome.
-->

---
layout: section
---

# раздел 2
## mktour как социальная сеть

---

# не только турнирная сетка

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="mk-card">
    <h3>игрок</h3>
    <ul>
      <li v-click>профиль и история</li>
      <li v-click>связи с клубами</li>
      <li v-click>репутация через активность</li>
    </ul>
  </div>
  <div class="mk-card">
    <h3>клуб</h3>
    <ul>
      <li v-click>сообщество вокруг событий</li>
      <li v-click>повторяемость турниров</li>
      <li v-click>возврат участников</li>
    </ul>
  </div>
</div>

---
layout: iframe-left
url: https://beta.mktour.org/
scale: 0.7
---

# кульминация раздела

игрок вносит свой результат сам.

<div class="mk-step-list">
  <div v-click>без посредников</div>
  <div v-click>быстрее закрываются раунды</div>
  <div v-click>организатор держит темп</div>
</div>

---
layout: section
---

# раздел 3
## с шариком в руках

---

# цена вопроса

<div class="grid grid-cols-2 gap-8 mt-8">
  <div class="mk-card">
    <h3>challenge</h3>
    <p v-click>понятный вход для быстрых турниров</p>
  </div>
  <div class="mk-card">
    <h3>swiss</h3>
    <p v-click>стабильный формат для регулярных серий</p>
  </div>
</div>

<p class="mt-8" v-click>
объясняем прайсы двух форматов и почему это выгодно клубу.
</p>

---
layout: center
class: text-center
---

# рубрика
## "прайс ревил пати"

<div class="mk-countdown">
  <span v-click>три</span>
  <span v-click>два</span>
  <span v-click>один</span>
</div>

<div class="mk-hero" v-click>лопаем шарик</div>

---
layout: center
class: text-center
---

# бесплатно

<div class="mk-subhero" v-click>аплодисменты</div>

---
layout: section
---

# раздел 4
## спасибо марку

---

# наш швейцарский коллега марк

разработал новый алгоритм швейцарских турниров <br>
и написал по нему речь.

<div class="mk-card mt-8" v-click>
  <p>
    но из-за слов "ассимиляция", "полиномиальный", "мэтчинг"
    речь сегодня не озвучиваем.
  </p>
</div>

---
layout: center
class: text-center
---

# финал
## спасибо

<div class="mk-kicker mt-6">mktour.org</div>

