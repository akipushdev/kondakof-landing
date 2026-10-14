# Инструкция по запуску лендинга на поддомене homelighting.kondakof.com

Данный репозиторий является автономным шаблоном для запуска промо-страницы архитектурного освещения **KONDAKOF** на поддомене:
👉 **`https://homelighting.kondakof.com/`**

> ⚠️ **ВАЖНО:**  
> У компании Дмитрия Кондакова на основном домене `kondakof.com` работает живой корпоративный сайт на WordPress.  
> **НЕЛЬЗЯ** менять основные A-записи домена `@` на GitHub, иначе главный сайт компании сломается и перестанет открываться!  
> Подключение через поддомен `homelighting` — это 100% безопасный способ, при котором основной сайт работает как обычно, а лендинг работает на GitHub Pages.

---

## 🚀 БЫСТРЫЙ СТАРТ ЗА 3 ШАГА

### ШАГ 1. Создайте свою копию этого репозитория
1. В правом верхнем углу страницы нажмите зелёную кнопку **«Use this template»** → **«Create a new repository»** (или кнопку **Fork**).
2. Назовите репозиторий, например: `homelighting-kondakof` (или `kondakof-landing`).
3. Нажмите **Create repository from template**. Теперь у вас в аккаунте GitHub есть собственная копия сайта.

---

### ШАГ 2. Включите GitHub Pages в своём репозитории
1. В вашем новом репозитории перейдите во вкладку **Settings** (вверху) → в левом меню выберите раздел **Pages**.
2. В блоке **Build and deployment**:
   - **Source**: `Deploy from a branch`
   - **Branch**: выберите ветку `main` и папку `/ (root)`
   - Нажмите **Save**.
3. В блоке **Custom domain**:
   - Впишите поддомен: `homelighting.kondakof.com`
   - Нажмите **Save**.
4. Поставьте галочку **Enforce HTTPS** (станет активна в течение 10–15 минут после того, как пропишете DNS).

---

### ШАГ 3. Добавьте ОДНУ запись в DNS домена kondakof.com
Зайдите в панель управления DNS, где куплен домен `kondakof.com` (Reg.ru, Beget, Cloudflare, Namecheap и т.д.):

Нажмите **«Добавить запись»** и укажите:
- **Тип записи:** `CNAME`
- **Имя поддомена / Хост (Subdomain / Host):** `homelighting`
- **Значение / Данные / Назначение (Target / Canonical name):** `ваш_логин_github.github.io.` *(обратите внимание: с точкой на конце, если требует регистратор)*
- **TTL:** `3600` (или Авто)

> 💡 **Пример:**  
> Если ваш аккаунт на GitHub называется `danila-dev`, то значение будет: `danila-dev.github.io.`.

После этого в течение 15–30 минут DNS обновятся, и страница сразу заработает по адресу:  
👉 **`https://homelighting.kondakof.com/`**  
*(а презентация в PDF для скачивания — по ссылке `https://homelighting.kondakof.com/presentation.pdf`)*.

---

## 📱 КАК РАБОТАЕТ ПРИЁМ ЗАЯВОК В TELEGRAM
В форме уже всё подключено и проверено:
- Клиент заполняет опросник (материал фасада, зоны подсветки, контакты) и нажимает «Отправить заявку».
- Браузер в фоновом режиме через Telegram Bot API отправляет структурированный бриф **напрямую в Telegram лично Дмитрию Кондакову** (`chat_id: 332066778`) через бота `@Automaticnodered_bot`.
- Клиенту не нужно переходить в Телеграм и нажимать Enter, всё происходит прямо на сайте в 1 клик.

*Если в будущем захотите поменять бота или чат — параметры находятся в `index.html` на строках `~670` (`botToken` и `chatId`).*

### 🔒 Опционально: Как скрыть токен Telegram-бота через Google Apps Script (без бэкенда)
Сейчас всё работает напрямую из браузера. Но если вы не хотите, чтобы токен бота был виден в коде страницы (`View Source`), можно за 3 минуты сделать бесплатный защитный шлюз на Google Drive, который заодно сохраняет все лиды в Google Таблицу:

1. **Создайте Google Таблицу:**
   - Откройте Google Таблицы и создайте файл, например: *«Лиды KONDAKOF»*.
2. **Откройте редактор скриптов:**
   - В верхнем меню таблицы нажмите: **Расширения** → **Apps Script**.
3. **Вставьте код скрипта (токен будет спрятан внутри):**
   ```javascript
   function doPost(e) {
     try {
       var data = JSON.parse(e.postData.contents);
       
       // 1. Автоматически сохраняем заявку в Google Таблицу
       var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
       sheet.appendRow([
         new Date(),
         data.source || '',
         data.name || '',
         data.contact || '',
         data.facade || '',
         data.zones || '',
         data.stage || '',
         data.comment || '',
         data.utm || ''
       ]);
       
       // 2. Отправляем сообщение в Telegram Дмитрию
       var botToken = "7819822579:AAHV7bWcrxkGl9X-kXbSL_Bos3XrzMDmCvQ";
       var chatId = "332066778";
       
       var response = UrlFetchApp.fetch("https://api.telegram.org/bot" + botToken + "/sendMessage", {
         method: "post",
         contentType: "application/json",
         payload: JSON.stringify({
           chat_id: chatId,
           text: data.text,
           parse_mode: "Markdown"
         })
       });
       
       return ContentService.createTextOutput(JSON.stringify({ ok: true }))
         .setMimeType(ContentService.MimeType.JSON);
     } catch (err) {
       return ContentService.createTextOutput(JSON.stringify({ ok: false, error: err.toString() }))
         .setMimeType(ContentService.MimeType.JSON);
     }
   }
   ```
4. **Опубликуйте веб-приложение:**
   - Нажмите синюю кнопку вверху: **Развернуть (Deploy)** → **Новое развертывание (New deployment)**.
   - Выберите тип: **Веб-приложение (Web app)**.
   - В поле *У кого есть доступ (Who has access)* выберите: **Все (Anyone)**.
   - Нажмите **Развернуть** и скопируйте полученный URL (вида `https://script.google.com/macros/s/AKfycb.../exec`).
5. **В файле `index.html`:**
   - Замените адрес в функции `fetch(...)` с Telegram API на полученный Google Script URL.
   - Теперь токен бота надёжно скрыт на серверах Google, а вы получаете автоматическую CRM-таблицу со всеми клиентами!

---

## 📊 КАК ПЕРЕВЕСТИ САЙТ НА СВОЮ АНАЛИТИКУ

### 1. Яндекс.Метрика
1. Создайте счётчик в [metrika.yandex.ru](https://metrika.yandex.ru) для адреса `homelighting.kondakof.com` (включите Вебвизор).
2. Скопируйте ваш номер счётчика (например: `98765432`).
3. В файле `index.html`:
   - В теге `<head>` (строки `~14-25`) замените старый номер `113591050` на ваш номер.
   - В скриптах внизу страницы (строки `~610` и `~710`) замените номер `113591050` на ваш номер в функциях `ym(...)`.
4. В панели Яндекс Метрики в разделе **«Цели»** добавьте 2 цели (тип: *JavaScript-событие*):
   - Идентификатор: `open_modal` (открытие брифа)
   - Идентификатор: `lead_success` (успешная отправка заявки в Telegram — *главная цель для рекламы в Яндекс Директ!*).

### 2. Google Analytics 4 (GA4)
1. В [analytics.google.com](https://analytics.google.com) скопируйте ваш **Measurement ID** (`G-XXXXXXXXXX`).
2. В файле `index.html` перед закрывающим тегом `</head>` вставьте код тега GA4:
```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```
3. Для отслеживания конверсий добавьте вызовы событий в скрипте:
   - При открытии окна: `if (typeof gtag === 'function') gtag('event', 'open_brief_modal');`
   - При отправке заявки: `if (typeof gtag === 'function') gtag('event', 'generate_lead');`

---

## 🎯 ЗАПУСК РЕКЛАМЫ С UTM-МЕТКАМИ
Сайт автоматически считывает любые UTM-метки из ссылки и передаёт их Дмитрию в Telegram вместе с заявкой (он сразу видит, с какой рекламной кампании и по какому ключу пришёл клиент).

Пример ссылки для рекламы:
```text
https://homelighting.kondakof.com/?utm_source=yandex&utm_medium=cpc&utm_campaign=msk_novariga&utm_term=podsvetka_doma
```
