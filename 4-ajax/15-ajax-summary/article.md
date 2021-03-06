# Таблица транспортов и их возможностей

Здесь мы подведём итог раздела, сравним транспорты и их возможности.

[cut]

## Способы опроса сервера

Основные способы опроса сервера:

1. **Частые опросы** -- регулярно к серверу отправляется запрос за данными. Сервер тут же отвечает на него, возвращая данные, если они есть. Если нет -- получается, что запрос был зря.

    Этот способ очень лёгок в реализации, но приводит к большому количеству лишних запросов, поэтому мы его далее не рассматриваем.
2. **Длинные опросы** -- к серверу отправляется запрос за данными. Сервер не отвечает на него, пока данные не появятся. Когда данные появились -- ответ с ними отправляется в браузер, и тот тут же делает новый запрос.

    Способ хорош, пока сообщений не слишком много. В идеальном случае соединение почти всё время висит открытым, лишь иногда сервер отвечает на него, доставляя данные в браузер.

    Также удобен в реализации, но даёт большое количество висящих соединений на сервере. Не все сервера хорошо поддерживают это. Например, `Apache` будет есть очень много памяти.
3. **Потоковое соединение** -- открыто соединение к серверу, и через него непрерывно поступают данные.

## Таблица транспортов
Основные характеристики всех транспортов, которые мы обсуждали в деталях, собраны в этой таблице.

Они были детально рассмотрены в предыдущих главах раздела.

<table>
<thead>
<tr>
<th></th>
<th><code>XMLHttpRequest</code></th>
<th><code>IFRAME</code></th>
<th><code>SCRIPT</code></th>
<th><code>EventSource</code></th>
<th><code>WebSocket</code></th>
</tr>
</thead>
<tbody>
<tr>
<th>Кросс-доменность</th>
<td>да, кроме IE9-<a class="link-ref" href="#x1">x1</a></td>
<td>да<a class="link-ref" href="#i1">i1</a></td>
<td>да</td>
<td>да</td>
<td>да</td>
</tr>
<tr>
<th>Методы</th>
<td>Любые</td>
<td>GET / POST</td>
<td>GET</td>
<td>GET</td>
<td>Свой протокол</td>
</tr>
<tr>
<th>COMET</th>
<td>Длинные опросы<a class="link-ref" href="#x2">x2</a></td>
<td>Непрерывное соединение</td>
<td>Длинные опросы</td>
<td>Непрерывное соединение</td>
<td>Непрерывное соединение в обе стороны</td>
</tr>
<tr>
<th>Поддержка</th>
<td>Все браузеры, ограничения в IE9-<a class="link-ref" href="#x3">x3</a></td>
<td>Все браузеры</td>
<td>Все браузеры</td>
<td>Кроме IE</td>
<td>IE 10, FF11, Chrome 16, Safari 6, Opera 12.5<a class="link-ref" href="#w1">w1</a></td>
</tr>
</tbody>
</table>

Пояснения:

**`XMLHttpRequest`**

<ul>
<li id="x1">В IE8-9 поддерживаются кросс-доменные GET/POST запросы с ограничениями через <code>XDomainRequest</code>.</li>
<li id="x2">Можно говорить об ограниченной поддержке непрерывного соединения через <code>onprogress</code>, но это событие вызывается не чаще чем в 50ms и не гарантирует получение полного пакета данных. Например, сервер может записать "Привет!", а событие вызовется один раз, когда браузер получил "При". Поэтому наладить обмен пакетами сообщений с его помощью затруднительно.
</li>
<li id="x3">Многие возможности современного стандарта включены в IE лишь с версии 10.</li>
</ul>

**`IFRAME`**

<ul>
<li id="i1">Во всех современных браузерах и IE8 кросс-доменность обеспечивает <code>postMessage</code>. В более старых браузерах возможны решения через <code>window.name</code> и хэш.</li>
</ul>

**`WebSocket`**

<ul><li id="w1">Имеется в виду поддержка окончательной редакции протокола <a href="http://tools.ietf.org/html/rfc6455">RFC 6455</a>. Более старые браузеры могут поддерживать черновики протокола. IE9- не поддерживает <code>WebSocket</code>.</li></ul>

Существует также нестандартный транспорт, не рассмотренный здесь:

- XMLHttpRequest с флагом `multipart`, только для Firefox.

    При указании свойства `xhr.multipart = true` и специального multipart-формата ответа сервера, Firefox инициирует `onload` при получении очередной части ответа. Ответ может состоять из любого количества частей, досылаемых по инициативе сервера. Мы не рассматривали его, так как Firefox поддерживает другие, более кросс-браузерные и стандартные транспорты.

В современных браузерах поддерживается новый метод [fetch](/fetch), в качестве замены XMLHttpRequest ([полифилл](https://github.com/github/fetch)).
