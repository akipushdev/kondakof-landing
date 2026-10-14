# Инструкция по запуску лендинга KONDAKOF на домене kondakof.com/home-lighting

Данный репозиторий является автономным шаблоном для запуска промо-страницы архитектурного освещения **KONDAKOF** по адресу:
👉 **`https://kondakof.com/home-lighting/`**

---

## 🚀 БЫСТРЫЙ СТАРТ ЗА 3 ШАГА

### ШАГ 1. Заберите этот репозиторий к себе
1. В правом верхнем углу этой страницы нажмите зелёную кнопку **«Use this template»** → **«Create a new repository»** (или кнопку **Fork**).
2. Назовите репозиторий, например: `kondakof-site`.
3. Нажмите **Create repository from template**. Теперь у вас в аккаунте есть собственная независимая копия сайта.

---

### ШАГ 2. Включите GitHub Pages в своём репозитории
1. В вашем новом репозитории перейдите во вкладку **Settings** (вверху) → в левом меню выберите раздел **Pages**.
2. В блоке **Build and deployment**:
   - **Source**: `Deploy from a branch`
   - **Branch**: выберите ветку `main` и папку `/ (root)`
   - Нажмите **Save**.
3. В блоке **Custom domain**:
   - Впишите ваш домен: `kondakof.com`
   - Нажмите **Save**.
4. Поставьте галочку **Enforce HTTPS** (станет активна в течение 10–15 минут после настройки DNS).

---

### ШАГ 3. Настройте DNS у регистратора домена kondakof.com
Зайдите в личный кабинет регистратора, где куплен домен `kondakof.com` (Reg.ru, Beget, Namecheap и т.д.), в раздел **«Управление DNS»** и добавьте записи:

1. **Для основного домена (`@`):** 4 штуки A-записей на IP GitHub:
   - `@` → `185.199.108.153`
   - `@` → `185.199.109.153`
   - `@` → `185.199.110.153`
   - `@` → `185.199.111.153`
2. **Для поддомена `www`:** CNAME-запись:
   - `www` → `ваш_аккаунт_github.github.io.`

После этого страница услуги автоматически будет доступна по прямой ссылке:  
👉 **`https://kondakof.com/home-lighting/`**  
*(а презентация в PDF для скачивания — по ссылке `https://kondakof.com/home-lighting/presentation.pdf`)*.

---

## 📱 КАК РАБОТАЕТ ПРИЁМ ЗАЯВОК В TELEGRAM
В форме уже всё подключено и проверено:
- Клиент заполняет опросник (материал фасада, зоны подсветки, контакты) и нажимает «Отправить заявку».
- Браузер в фоновом режиме через Telegram Bot API отправляет структурированный бриф **напрямую в Telegram лично Дмитрию Кондакову** (`chat_id: 332066778`) через бота `@Automaticnodered_bot`.
- Клиенту не нужно переходить в Телеграм и нажимать Enter, всё происходит прямо на сайте в 1 клик.

*Если в будущем захотите поменять бота или чат — параметры находятся в `index.html` и `home-lighting/index.html` на строках `~670` (`botToken` и `chatId`).*

---

## 📊 КАК ПЕРЕВЕСТИ САЙТ НА СВОЮ АНАЛИТИКУ

### 1. Яндекс.Метрика
1. Создайте счётчик в [metrika.yandex.ru](https://metrika.yandex.ru) для домена `kondakof.com` (включите Вебвизор).
2. Скопируйте ваш номер счётчика (например: `98765432`).
3. В файлах `home-lighting/index.html` и `index.html`:
   - В теге `<head>` (строки `~14-25`) замените старый номер `113591050` на ваш номер.
   - В скриптах внизу страницы (строки `~610` и `~710`) замените номер `113591050` на ваш номер в функциях `ym(...)`.
4. В панели Яндекс Метрики в разделе **«Цели»** добавьте 2 цели (тип: *JavaScript-событие*):
   - Идентификатор: `open_modal` (открытие брифа)
   - Идентификатор: `lead_success` (успешная отправка заявки в Telegram — *главная цель для рекламы в Яндекс Директ!*).

### 2. Google Analytics 4 (GA4)
1. В [analytics.google.com](https://analytics.google.com) скопируйте ваш **Measurement ID** (`G-XXXXXXXXXX`).
2. В файле `home-lighting/index.html` перед закрывающим тегом `</head>` вставьте код тега GA4:
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
3. Для отслеживания конверсий в функциях вызова квиза добавьте события:
   - При открытии окна: `if (typeof gtag === 'function') gtag('event', 'open_brief_modal');`
   - При отправке заявки: `if (typeof gtag === 'function') gtag('event', 'generate_lead');`

---

## 🎯 ЗАПУСК РЕКЛАМЫ С UTM-МЕТКАМИ
Сайт автоматически считывает любые UTM-метки из ссылки и передаёт их Дмитрию в Telegram вместе с заявкой (он сразу видит, с какой рекламной кампании и по какому ключу пришёл клиент).

Пример ссылки для рекламы:
```text
https://kondakof.com/home-lighting/?utm_source=yandex&utm_medium=cpc&utm_campaign=msk_novariga&utm_term=podsvetka_doma
```
