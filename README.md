# opism-pr02-grebeniuk
# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) | Гребенюк Катерина Андріївна |
| Група | КБЗІ 2.02 |
| Номер варіанта | 6 |
| Індивідуальний домен |  kpi.ua|
| «Чужий» домен для завдання A.3.1 (варіант ± 20) | openssl.org |
| Середовище виконання | Windows, PowerShell|
| Дата виконання | 06.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**
```text
(Виконано через PowerShell-сокет для kpi.ua згідно з Додатком Г)

```text
Набраний запит:
```text
GET / HTTP/1.1
Host: kpi.ua
Connection: close

```text
Відповідь:
```text
HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 11:05:27 GMT
Content-Type: text/html
Content-Length: 162
Connection: close
Location: [https://kpi.ua/](https://kpi.ua/)
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

<$d = "kpi.ua"
$c = New-Object System.Net.Sockets.TcpClient($d, 80)$s = $c.GetStream()$w = New-Object System.IO.StreamWriter($s)$w.Write("GET / HTTP/1.1`r`nConnection: close`r`n`r`n")
$w.Flush()
(New-Object System.IO.StreamReader($s)).ReadToEnd()$c.Close()>


**Вивід:**

<HTTP/1.1 400 Bad Request
Server: nginx
Date: Tue, 06 Oct 2026 15:36:04 GMT
Content-Type: text/html
Content-Length: 150
Connection: close

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>nginx</center>
</body>
</html>

PS C:\Windows\System32>>


---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

<$d = "kpi.ua"; $c = New-Object System.Net.Sockets.TcpClient($d, 80);$s = $c.GetStream();$w = New-Object System.IO.StreamWriter($s);$w.Write("GET / HTTP/1.1`r`nHost: openssl.org`r`nConnection: close`r`n`r`n"); $w.Flush(); (New-Object System.IO.StreamReader($s)).ReadToEnd();$c.Close()>


**Вивід:**

<HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 15:37:30 GMT
Content-Type: text/html
Content-Length: 162
Connection: close
Location: https://openssl.org/
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>

PS C:\Windows\System32>>


#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

<$d = "kpi.ua"; $c = New-Object System.Net.Sockets.TcpClient($d, 80);$s = $c.GetStream();$w = New-Object System.IO.StreamWriter($s);$w.Write("GET / HTTP/1.1`r`nHost: opism-pr02.invalid`r`nConnection: close`r`n`r`n"); $w.Flush(); (New-Object System.IO.StreamReader($s)).ReadToEnd();$c.Close()>


**Вивід:**

<HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 15:37:55 GMT
Content-Type: text/html
Content-Length: 162
Connection: close
Location: https://opism-pr02.invalid/
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>

PS C:\Windows\System32>>


#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

<$d = "kpi.ua"; $c = New-Object System.Net.Sockets.TcpClient($d, 80);$s = $c.GetStream();$w = New-Object System.IO.StreamWriter($s);$w.Write("GET / HTTP/1.0`r`n`r`n"); $w.Flush(); (New-Object System.IO.StreamReader($s)).ReadToEnd();$c.Close()>


**Вивід:**

<HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 15:40:36 GMT
Content-Type: text/html
Content-Length: 162
Connection: close
Location: https://_/
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>

PS C:\Windows\System32>>


Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

<$d = "kpi.ua"; $c = New-Object System.Net.Sockets.TcpClient($d, 80);$s = $c.GetStream();$w = New-Object System.IO.StreamWriter($s);$w.Write("GET /opism-pr02-12345 HTTP/1.1`r`nHost: $d`r`n`r`nGET / HTTP/1.1`r`nHost: $d`r`nConnection: close`r`n`r`n"); $w.Flush(); (New-Object System.IO.StreamReader($s)).ReadToEnd();$c.Close()>


**Вивід:**

<HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 15:41:17 GMT
Content-Type: text/html
Content-Length: 162
Connection: keep-alive
Location: https://kpi.ua/opism-pr02-12345
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>
HTTP/1.1 301 Moved Permanently
Server: nginx
Date: Tue, 06 Oct 2026 15:41:17 GMT
Content-Type: text/html
Content-Length: 162
Connection: close
Location: https://kpi.ua/
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
</body>
</html>

PS C:\Windows\System32>>


**Кількість отриманих відповідей: 2**

**Коди стану отриманих відповідей: 301, 301**

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

<curl.exe -v --http1.1 http://kpi.ua/ -o $null>


**Вивід:**

<curl: option -o: requires parameter
curl: try 'curl --help' for more information
PS C:\Windows\System32>>


---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** < `iana.org`>

**Підстава для використання резервного ресурсу (заповнюють за потреби): Відсутність утиліти openssl в середовищі Windows**

**Команда:**

<curl.exe -v https://iana.org/>


**Набраний запит:**

<GET / HTTP/1.1
Host: iana.org>


**Вивід:**

<* Host iana.org:443 was resolved.
* IPv6: 2001:500:88:200::8
* IPv4: 192.0.43.8
*   Trying [2001:500:88:200::8]:443...
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server did not agree on a protocol. Uses default.
* Established connection to iana.org (2001:500:88:200::8 port 443) from 2a02:3032:2e0:8068:5cbb:66d5:2cc2:ab8b port 62853
* using HTTP/1.x
> GET / HTTP/1.1
> Host: iana.org
> User-Agent: curl/8.21.0
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< Date: Tue, 06 Oct 2026 15:59:27 GMT
< Server: Apache
< Location: https://www.iana.org/
< Cache-Control: max-age=345600
< Expires: Sat, 10 Oct 2026 15:59:27 GMT
< Content-Length: 229
< Content-Type: text/html; charset=iso-8859-1
<
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.iana.org/">here</a>.</p>
</body></html>
* Connection #0 to host iana.org:443 left intact
PS C:\Windows\System32>
PS C:\Windows\System32>>


---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:**

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 |Server | nginx|Вказує програмне забезпечення веб-сервера, що опрацювало запит | сервер| Сформовано цільовим веб-сервером kpi.ua|
| 2 |Date |Tue, 06 Oct 2026 11:05:27 GMT |Час і дата формування відповіді сервером у форматі UTC |сервер|Синхронізовано системним годинником сервера |
| 3 |Content-Type |Визначає медіа-тип (MIME-тип) тіла відповіді (HTML-документ)|сервер |Задано конфігурацією веб-ресурсу для сторінки помилки/редиректу |
| 4 |Content-Length |162 |Розмір тіла відповіді у батах | сервер|Відображає фактичний обсяг HTML-сторінки перенаправлення |
| 5 |Connection |close |Вказує на закриття TCP-з'єднання після завершення передачі відповіді |сервер |Надійшло у відповідь на інструкцію в нашому запиті |
| 6 |Location |[https://kpi.ua/](https://kpi.ua/) |URL-адреса, на яку здійснюється перенаправлення клієнта | сервер| Визначає новий захищений шлях для перенаправлення (код 301)|



---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

<Неочевидним виявилося те, що навіть за наявності коректного заголовка Host: kpi.ua у завданні A.1 сервер одразу повертає статус HTTP/1.1 301 Moved Permanently і заголовок Location: [https://kpi.ua/](https://kpi.ua/), примусово перенаправляючи весь трафік на захищений протокол HTTPS.>

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

<Найменше утруднень викликало поле Server: nginx, проте визначення походження службових заголовків (Date) іноді вимагає розуміння того, чи генерує їх сам додаток, чи зворотний проксі-сервер (reverse proxy), що стоїть перед ним.>

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

<Яким чином налаштовані правила кешування статичного контенту безпосередньо на рівні балансувальника навантаження перед сервером  nginx.>

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

<Згідно зі специфікацією протоколу HTTP, порожній рядок (два символи переведення рядка \r\n\r\n) сигналізує серверу про повне завершення передачі заголовків запиту, після чого сервер починає його обробку.>

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

<За відсутності поля Host (у завданні A.2) сервер повертає статус 400 Bad Request. У протоколі HTTP/1.1 поле Host є обов'язковим, оскільки на одній IP-адресі може хоститися кілька сайтів ( віртуальні хости), і воно дозволяє маршрутизувати запит до потрібного сайту.>

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

<У завданні A.4 надійшло 2 відповіді з кодами стану 301 Moved Permanently. Це означає підтримку постійного з'єднання (persistent connection / keep-alive), коли через одне відкрите TCP-з'єднання клієнт може надіслати кілька послідовних запитів, що значно прискорює завантаження сторінок з безліччю вкладених елементів завдяки відсутності накладних витрат на повторне рукостискання (TCP handshake).>

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

<Програма curl додала заголовки User-Agent (ідентифікація клієнтського ПЗ) та Accept (формати, які клієнт здатний прийняти), щоб надати серверу розширену інформацію про можливості клієнта.>

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

<У заголовках прямого зв'язку з kpi.ua фігурує рядок Server: nginx, що вказує на наявність веб-сервера/проксі nginx на шляху обробки запитів.>

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 |Strict-Transport-Security: max-age=31536000; includeSubDomains; preload |A.1 |
| 2 |Location: [https://kpi.ua/](https://kpi.ua/) |A.1 |
| 3 | Content-Length: 162|A.1 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):**

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | | 1.1 |301 |162 | — |
| A.2 | поле відсутнє | 1.1 |400 |150 | ні|
| A.3.1 | | 1.1 |301 |162 | так|
| A.3.2 | `opism-pr02.invalid` | 1.1 | 301|162 |так |
| A.3.3 | поле відсутнє | 1.0 |301 |162 |так |

**Висновок за таблицею (2–4 речення):** що саме змінювалося у запиті від проби до проби і як на це реагував сервер.

<У ході виконання проб змінювалося значення заголовка Host та версія протоколу HTTP. Сервер повертає помилку 400 Bad Request виключно за відсутності обов'язкового поля Host у протоколі HTTP/1.1. У разі передачі інших значень Host або старішої версії HTTP/1.0 сервер успішно опрацьовує синтаксис запиту та повертає стандартну переадресацію (301).>

---

## Декларування використання технологій штучного інтелекту

**Чи використовувалися технології ШІ під час виконання роботи:** < ні>

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
