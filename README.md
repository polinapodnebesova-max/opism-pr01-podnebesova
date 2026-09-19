# opism-pr01-podnebesova
# Практична робота № 1

**Дисципліна:** Основи побудови інформаційних систем та мереж

**Тема:** Спостереження за процесом звернення до вебресурсу. Побудова власної моделі рівнів взаємодії

| | |
|---|---|
| **Прізвище, ім'я** |Поднебесова Поліна |
| **Група** |2.02 |
| **Номер варіанта** |20 |
| **Домен варіанта** |haproxy.org |
| **Середовище виконання** |  (Windows)* |
| **Версія curl** | *(curl 8.13.0 (Windows) libcurl/8.13.0 Schannel zlib/1.3.1 WinIDN)* |
| **Дата виконання** |18.09.2026 |

---

## Частина A. Збір експериментальних даних

### A.1. Запит із діагностичним виводом

**Команда:**

```
curl -v https://ВАШ_ДОМЕН
```

**Вивід:**

```
* Host haproxy.org:443 was resolved.
* IPv6: (none)
* IPv4: 51.15.8.218
*   Trying 51.15.8.218:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to haproxy.org (51.15.8.218) port 443
* using HTTP/1.x
> GET / HTTP/1.1
> Host: haproxy.org
> User-Agent: curl/8.13.0
> Accept: */*
>
* Request completely sent off
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
< HTTP/1.1 301 Moved Permanently
< content-length: 0
< location: https://www.haproxy.org/
< alt-svc: h2=":443"; ma=3600
< alt-svc: h3=":443"; ma=3600
< set-cookie: served=1:TLSv1.3+TCP:IPv4
<
* Connection #0 to host haproxy.org left intact
```

---

### A.2. Запит без захисту з'єднання

**Команда:**

```
curl -v http://neverssl.com
```

**Вивід:**

```
* Host neverssl.com:80 was resolved.
* IPv6: (none)
* IPv4: 34.223.124.45
*   Trying 34.223.124.45:80...
* connect to 34.223.124.45 port 80 from 0.0.0.0 port 52025 failed: Connection was aborted
* Failed to connect to neverssl.com port 80 after 74044 ms: Could not connect to server
* closing connection #0
curl: (7) Failed to connect to neverssl.com port 80 after 74044 ms: Could not connect to server
```

---

### A.3. Запит до служби доменних імен

*Windows: `Resolve-DnsName ВАШ_ДОМЕН`*

**Команда (перше виконання):**

```
dig ВАШ_ДОМЕН
```

**Вивід:**

```

Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
haproxy.org                                    A      377   Answer     51.15.8.218
```

**Команда (повторне виконання через 5–7 хвилин):**

```
dig ВАШ_ДОМЕН
```

**Вивід:**

```
Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
haproxy.org                                    A      373   Answer     51.15.8.218
```

**Зафіксовані значення:**

| Параметр | Перше виконання | Повторне виконання |
|---|---|---|
| Час виконання (год:хв) |17:35 |17:41 |
| IP-адреса |51.15.8.218 |51.15.8.218 |
| Значення TTL |377 |373 |

> Якщо друге значення TTL виявилося більшим за перше — це нормально: кеш резолвера встиг оновитися. Зафіксуйте як є.

---

### A.4. Контрольний ресурс

**Команда:**

```
curl -v https://google.com
```

**Вивід:**

```
* Host google.com:443 was resolved.
* IPv6: (none)
* IPv4: 142.250.109.113, 142.250.109.100, 142.250.109.101, 142.250.109.102, 142.250.109.138, 142.250.109.139
*   Trying 142.250.109.113:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* Connected to google.com (142.250.109.113) port 443
* using HTTP/1.x
> GET / HTTP/1.1
> Host: google.com
> User-Agent: curl/8.13.0
> Accept: */*
>
* Request completely sent off
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
< HTTP/1.1 301 Moved Permanently
< Location: https://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-xMv8HuroRIvlHCrT6rh0FQ' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
< Date: Fri, 18 Sep 2026 14:47:26 GMT
< Expires: Sun, 18 Oct 2026 14:47:26 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 220
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000
<
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com left intact
```

---

### A.5. Ресурси з некоректною конфігурацією сертифіката

**Випадок 1**

```
curl -v https://expired.badssl.com
```

```
* Host expired.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
* closing connection #0
curl: (35) schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
```

**Випадок 2**

```
curl -v https://wrong.host.badssl.com
```

```
* Host wrong.host.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
* closing connection #0
curl: (60) schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
```

**Випадок 3**

```
curl -v https://self-signed.badssl.com
```

```
* Host self-signed.badssl.com:443 was resolved.
* IPv6: (none)
* IPv4: 104.154.89.105
*   Trying 104.154.89.105:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
* closing connection #0
curl: (60) schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
```

> Якщо використано альтернативний спосіб із параметром `--resolve` — зазначити це та навести фактичну команду.

---

## Частина B. Власна модель рівнів

**Кількість виділених груп:** _7__

| № | Назва групи (власне формулювання) | Рядки виводу, віднесені до групи | Обґрунтування |
|---|---|---|---|
| 1 |Тіло та вміст сторінки  |```content-length: 0```, HTML-код, текст сторінки |Самі дані та вміст, які сайт надсилає користувачу у відповідь. |
| 2 |Обмін HTTP-заголовками |```> GET / HTTP/1.1, > Host: haproxy.org, < HTTP/1.1 301 Moved Permanently``` |Запити клієнта та відповіді сервера (заголовки, коди відповідей, перенаправлення). |
| 3 |Захист та шифрування (TLS) |```schannel: renegotiating SSL/TLS connection``` ,```ALPN: server accepted http/1.1```|Налаштування безпечного з'єднання, перевірка SSL-сертифіката та узгодження шифрування. |
| 4 |Встановлення TCP-з'єднання |```* Trying 51.15.8.218:443...``` ,```* Connected to haproxy.org (51.15.8.218) port 443```  |Спроба та факт успішного підключення до IP-адреси сервера через порт 443. |
| 5 |Пошук IP-адреси (DNS) |```* Host haproxy.org:443 was resolved., * IPv4: 51.15.8.218``` |Етап, де текстове ім'я сайту перетворюється на IP-адресу. |
| 6 |Результати DNS-запиту |```Type: A, TTL: 377, Section: Answer, IPAddress: 51.15.8.218``` |Інформація з команди ```Resolve-DnsName```: тип запису, час життя кешу (TTL) та адреса. |
| 7 |Налаштування системи та curl |```User-Agent: curl/8.13.0, schannel: disabled automatic use of client certificate``` |Інформація про версію програми ```curl.exe``` та параметри Windows, з якими вона запускається. |

*Групи впорядковано від найближчої до користувача (№ 1) до найближчої до апаратного забезпечення. Зайві рядки вилучити, за потреби — додати.*

**Рядки, які не вдалося віднести до жодної групи:**

| Рядок виводу | Причина утруднення |
|---|---|
|```* Request completely sent off``` |Внутрішнє службове повідомлення ```curl.exe``` про статус відправки, яке не належить до конкретного мережевого протоколу |
|```* Connection #0 to host haproxy.org left intact``` |Технічний статус утиліти про збереження TCP-з'єднання відкритим (Keep-Alive). |
| | |

---

## Контрольні питання

**1. Скільки рядків діагностичного виводу передує отриманню даних сторінки (завдання A.1)?**

> Загалом першому рядку відповіді сервера (```< HTTP/1.1 301 Moved Permanently```) передує 22 рядки виводу (з урахуванням службових логів утиліти ```*```, заголовків HTTP-запиту ```>``` та роздільних порожніх рядків). Якщо рахувати виключно технічні діагностичні повідомлення ```curl``` (позначені зірочкою ```*```), їх кількість становить 16 рядків.

**2. Які рядки наявні у виводі A.1 і відсутні у виводі A.2? Чим це зумовлено?**

> У завдання A.2 запит виконувався на звичайний HTTP (порт 80), який є незахищеним. У ньому в принципі відсутній шар шифрування TLS/SSL та процедури рукостискання (```schannel```, ```ALPN```), які є обов'язковими для HTTPS (порт 443) у виводі A.1. У виводі A.2 з'єднання взагалі не змогло встановитися (```Failed to connect ... Could not connect to server```). Оскільки мережеве TCP-з'єднання обірвалося за таймаутом (74044 ms) ще на етапі ```Trying 34.223.124.45:80...```, утиліта ```curl``` навіть не дійшла до етапу надсилання HTTP-запиту (```> GET /```) та отримання відповіді від сервера (```< HTTP/1.1```).

**3. Звідки у виводі з'явилося значення `443`, якщо його не було вказано в адресі?**

> У мережі для кожної адреси ```https://``` за замовчуванням завжди використовується захищений порт 443. Якби в адресі був звичайний ```http://```, утиліта підставила б порт 80.

**4. Як змінилося значення TTL між двома запитами (A.3)? Що означає це число?**

> Значення TTL зменшилося на 4 секунди (з 377 до 373).

Це число означає Time To Live у секундах — тривалість зберігання DNS-запису в локальному кеші пристрою або провайдера перед повторним запитом до головного DNS-сервера. Під час повторного виконання команди дані беруться з кешу, а лічильник TTL відображає час, що залишився до закінчення терміну дії запису.

**5. Чим відрізняються між собою три причини помилок із завдання A.5? Сформулювати кожну однією фразою.**

| Випадок | Причина недовіри |
|---|---|
| `expired` |```expired.badssl.com``` (```SEC_E_CERT_EXPIRED```): Термін дії SSL-сертифіката вичерпався і він більше не є дійсним. |
| `wrong.host` |```wrong.host.badssl.com``` (```SEC_E_WRONG_PRINCIPAL```): Доменне ім'я в запиті не збігається з доменним ім'ям, для якого видано сертифікат. |
| `self-signed` |```self-signed.badssl.com``` (```SEC_E_UNTRUSTED_ROOT```): Сертифікат підписано самим сервером або невідомим видавцем, відсутнім у списку довірених центрів сертифікації (CA). |

**6. Три рядки з власних виводів, про які не йшлося на лекції 1:**

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 |schannel: disabled automatic use of client certificate |А1 |
| 2 |ALPN: curl offers http/1.1 |А1 |
| 3 |Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000 |А4 |

*Пояснення до цих рядків не потрібне.*

---

## Висновки

*150–300 слів. Спиратися на власні спостереження, а не на матеріал лекції.*

**D.1. Що виявилося неочевидним або несподіваним**

*Назвати конкретно, з посиланням на рядок виводу.*

> Несподіваним була поява рядка ```* schannel: remote party requests renegotiation у виводі curl```. Сервер почав повторно налаштовувати шифрування вже після того, як пішов сам HTTP-запит, хоча логічніше чекати, що TLS повністю завершиться до відправки даних. Також здивував рядок ```Alt-Svc: h3=":443"```, де Google відразу пропонує перейти на ```HTTP/3```, хоча з'єднання йшло через ```HTTP/1.1```.

**D.2. Чому саме така кількість груп у частині B**

*На якій підставі ухвалено рішення. Що змусило б його змінити.*

> Вивід поділено на 7 груп, тому що кожен крок робить свою окрему роботу: шукає IP, підключається, шифрує дані, відправляє запит і отримує відповідь. Об'єднати їх у меншу кількість нехотіла, бо  наприклад, перевірку сертифіката не можна змішати з звичайним HTTP-запитом, бо це різні речі. Змінити кількість груп довелося б у двох випадках: якби сайт працював без шифрування (тоді зникає група TLS) або якби сайт взагалі не відкрився через помилку мережі.

**D.3. Питання, яке залишилося без відповіді**

> Чому при повторному виконанні команди ```Resolve-DnsName``` значення TTL зменшилося лише на 4 секунди, якщо між запитами минуло близько 5 хвилин?

---

## Використання штучного інтелекту

*Розділ обов'язковий. Заповнюється незалежно від того, чи використовувався ШІ. Детальні вимоги — у документі «Політика використання технологій штучного інтелекту».*

**Факт використання:** використано 

**Установлений рівень для цієї роботи:** Р3 — ШІ як співвиконавець

**Фактичний рівень використання:** Р3

### Використані системи

| Система | Версія або модель | Період використання |
|---|---|---|
|Google Gemini |Gemini 2.5 Flash |18.09.2026-19.09.2026 |

### Промпти

*Наводити дослівно, у тому вигляді, у якому запит було надано системі. Переказ не приймається.*

| № | Розділ роботи | Текст промпта |
|---|---|---|
| 1 |частина В |ану глянь, чи всі оці пункти розставлені в порядку від найближчої до користувача (№ 1) до найближчої до апаратного забезпечення |
| 2 |частина С |ось лекція і мої виводи, дай Три рядки з власних виводів, про які не йшлося на лекції 1 |
| 3 |частиа D |дай відповідь на оцу питаннч D.1. Що виявилося неочевидним або несподіваним |

### Дії з отриманим результатом

| № промпта | Що перевірено | Що змінено | Що відхилено і чому |
|---|---|---|---|
| 1 |послідовність розташування 7 етапів виводу |сформульовано остаточний порядок груп |нічого не відхилено, все правильно |
| 2 |відсутність згаданих рядків у лекції 1 |конкретні рядки з виводів |відхилено зайві теоретичні пояснення |
| 3 |дано відповідь на питання |нічого |нічого |

### Підтвердження

Підтверджую, що всі наведені в цьому звіті виводи команд отримано мною особисто внаслідок фактичного виконання відповідних дій, а відомості цього розділу є повними та достовірними.

> Виводи `curl`, `dig` та інші артефакти не можуть бути згенеровані. Це стосується будь-якого рівня використання ШІ.

---

## Примітки виконавця

*(необов'язковий розділ: що не спрацювало, які команди довелося змінити, які виникли труднощі)*

> Якщо не правильно, то буде обідно, я робила таку робуту вперше, і ніколи раніше не бачила схожих завдань. Тому мені помагав ші, бо я хотіла розібратися, і зрозуміти що і як.
