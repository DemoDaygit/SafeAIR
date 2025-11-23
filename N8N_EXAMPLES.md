# 🚀 Примеры N8N Workflows для SAFE AIR

Готовые примеры обработки заявок с использованием различных интеграций.

---

## Пример 1: Отправка Email уведомления

### 📧 Простая отправка письма в Telegram и Email

**Workflow:**
```
Webhook (получить заявку)
  ↓
Email Send (отправить на support@safe-air.com)
  ↓
Telegram Send Message (отправить в чат)
  ↓
Response (вернуть успех)
```

### Конфигурация узла Email Send:

```javascript
// Параметры:
Email: support@safe-air.com
Subject: Новая заявка на консультацию
HTML Body:
```
```html
<h2>🆕 Новая заявка на консультацию</h2>

<p><strong>Клиент:</strong> {{ $json.contact.name }}</p>
<p><strong>Телефон:</strong> {{ $json.contact.phone }}</p>
<p><strong>Email:</strong> {{ $json.contact.email }}</p>
<p><strong>Город:</strong> {{ $json.contact.city }}</p>

<hr/>

<p><strong>Тип клиента:</strong> {{ $json.request.client_type }}</p>
<p><strong>Услуга:</strong> {{ $json.request.service_type }}</p>
<p><strong>Количество блоков:</strong> {{ $json.request.equipment_count }}</p>

<p><strong>Сообщение:</strong></p>
<p>{{ $json.request.message }}</p>

<hr/>

<p><small>Получено: {{ $json.metadata.timestamp }}</small></p>
```
```
```

### Конфигурация узла Telegram Send Message:

```javascript
// Параметры:
Chat ID: ваш_ID_чата (или переменная)
Message Type: Text
Text:
```
```
📞 Новая заявка на консультацию!

👤 Клиент: {{ $json.contact.name }}
☎️ Телефон: {{ $json.contact.phone }}
📧 Email: {{ $json.contact.email }}
📍 Город: {{ $json.contact.city }}

🏢 Тип клиента: {{ $json.request.client_type }}
🔧 Услуга: {{ $json.request.service_type }}
⚙️ Кол-во блоков: {{ $json.request.equipment_count }}

💬 Сообщение:
{{ $json.request.message }}

⏰ {{ $json.metadata.date_formatted }} {{ $json.metadata.time_formatted }}
```
```
```

---

## Пример 2: Сохранение в Google Sheets

### 📊 Автоматическое добавление данных в таблицу

**Workflow:**
```
Webhook
  ↓
Google Sheets (Append)
  ↓
Email (подтверждение)
  ↓
Response
```

### Конфигурация Google Sheets узла:

1. **Выберите** Google Sheets из узлов
2. **Авторизуйтесь** с вашим Google аккаунтом
3. **Настройте:**

```
Operation: Append
Spreadsheet: Выберите вашу таблицу "SAFE AIR Leads"
Sheet: "Applications" (или "Заявки")
Columns to set:
  ├─ Date: {{ $json.metadata.date_formatted }}
  ├─ Time: {{ $json.metadata.time_formatted }}
  ├─ Name: {{ $json.contact.name }}
  ├─ Phone: {{ $json.contact.phone }}
  ├─ Email: {{ $json.contact.email }}
  ├─ City: {{ $json.contact.city }}
  ├─ Client Type: {{ $json.request.client_type }}
  ├─ Service: {{ $json.request.service_type }}
  ├─ Equipment Count: {{ $json.request.equipment_count }}
  └─ Message: {{ $json.request.message }}
```

### Готовые колонки для таблицы:

| Дата | Время | ФИО | Телефон | Email | Город | Тип клиента | Услуга | Кол-во | Сообщение |
|------|-------|-----|---------|-------|-------|------------|--------|--------|-----------|
| 15.01 | 14:30 | Иван П. | +7999... | ivan@ | Москва | family | maintenance | 2 | Нужна чистка |

---

## Пример 3: Сохранение в базу данных

### 💾 Добавление в PostgreSQL или MySQL

**Workflow:**
```
Webhook
  ↓
PostgreSQL/MySQL (Insert)
  ↓
Response
```

### Конфигурация MySQL узла:

```
Credentials: Выберите ваше подключение к DB
Query Type: Insert

Table: leads

Columns:
  name: {{ $json.contact.name }}
  phone: {{ $json.contact.phone }}
  email: {{ $json.contact.email }}
  city: {{ $json.contact.city }}
  client_type: {{ $json.request.client_type }}
  service_type: {{ $json.request.service_type }}
  equipment_count: {{ $json.request.equipment_count }}
  message: {{ $json.request.message }}
  created_at: {{ $json.metadata.timestamp }}
  page_url: {{ $json.metadata.page_url }}
```

### SQL схема для создания таблицы:

```sql
CREATE TABLE IF NOT EXISTS leads (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  phone VARCHAR(20) NOT NULL,
  email VARCHAR(255),
  city VARCHAR(100),
  client_type ENUM('family', 'business', 'public'),
  service_type VARCHAR(50),
  equipment_count INT DEFAULT 1,
  message TEXT,
  page_url VARCHAR(500),
  created_at DATETIME,
  processed BOOLEAN DEFAULT FALSE,
  notes TEXT,
  created_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  INDEX idx_phone (phone),
  INDEX idx_created_at (created_at)
);
```

---

## Пример 4: Отправка в Bitrix24 CRM

### 🔗 Интеграция с CRM для отслеживания

**Workflow:**
```
Webhook
  ↓
HTTP Request (добавить lead в Bitrix24)
  ↓
Response
```

### Конфигурация HTTP Request узла:

```
Method: POST
URL: https://ВАШ_ДОМЕН.bitrix24.ru/rest/1/YOUR_WEBHOOK_CODE/crm.lead.add

Headers:
  Content-Type: application/json

Body:
{
  "fields": {
    "NAME": "{{ $json.contact.name }}",
    "PHONE": [
      {
        "VALUE": "{{ $json.contact.phone }}",
        "VALUE_TYPE": "MOBILE"
      }
    ],
    "EMAIL": [
      {
        "VALUE": "{{ $json.contact.email }}",
        "VALUE_TYPE": "WORK"
      }
    ],
    "COMMENTS": "Город: {{ $json.contact.city }}\nТип: {{ $json.request.client_type }}\nУслуга: {{ $json.request.service_type }}\n\n{{ $json.request.message }}",
    "SOURCE_ID": "WEBSITE",
    "SOURCE_DESCRIPTION": "SAFE AIR Landing Page"
  }
}
```

---

## Пример 5: Умная маршрутизация по типам заявок

### 🎯 Разные действия для разных типов клиентов

**Workflow:**
```
Webhook
  ↓
IF (client_type = family?)
  ├─ YES → Email на personal@safe-air.com
  │         → Google Sheets "Personal"
  │
  ├─ NO: IF (client_type = business?)
  │   ├─ YES → Email на business@safe-air.com
  │   │         → Google Sheets "Business"
  │   │         → Slack уведомление
  │   │
  │   └─ NO → Email на government@safe-air.com
  │           → Google Sheets "Government"
  │           → Создать тикет в JIRA
  ↓
Response
```

### Конфигурация IF узла:

```
Condition 1:
  Field: {{ $json.request.client_type }}
  Operator: equals
  Value: family

Then: (отправить на личный email)

Condition 2:
  Field: {{ $json.request.client_type }}
  Operator: equals
  Value: business

Then: (отправить на бизнес email + Slack)

Condition 3:
  Field: {{ $json.request.request.client_type }}
  Operator: equals
  Value: public

Then: (отправить на гос. email + JIRA)
```

---

## Пример 6: Автоматический ответ на Telegram

### 💬 Отправка автоматического ответа клиенту

**Workflow:**
```
Webhook
  ↓
Telegram Send Message (клиенту)
  ↓
Email Save
  ↓
Response
```

**Telegram сообщение клиенту:**

```
Используйте узел Telegram Send Message с URL формулой:

tg://resolve?phone={{ $json.contact.phone }}

Или если у вас есть Telegram ID, используйте:

Message Type: Text
Chat ID: {{ $json.contact.telegram_id }}
Text:

✅ Спасибо {{ $json.contact.name }}!

Мы получили вашу заявку на услугу:
🔧 {{ $json.request.service_type }}

Наш менеджер свяжется с вами в ближайшее время по телефону:
{{ $json.contact.phone }}

Если у вас есть срочные вопросы, напишите нам в этот чат или позвоните:
📞 8-800-555-01-23

SAFE AIR Team 🌬️
```

---

## Пример 7: Интеграция с Slack

### 💼 Отправка уведомлений в Slack канал

**Workflow:**
```
Webhook
  ↓
Slack Send Message
  ↓
Response
```

### Конфигурация Slack узла:

```
Channel: #leads или #notifications
Message Type: Block Kit / Text

Message:

{
  "blocks": [
    {
      "type": "header",
      "text": {
        "type": "plain_text",
        "text": "📞 Новая заявка!"
      }
    },
    {
      "type": "section",
      "fields": [
        {
          "type": "mrkdwn",
          "text": "*Клиент:*\n{{ $json.contact.name }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Телефон:*\n{{ $json.contact.phone }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Email:*\n{{ $json.contact.email }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Город:*\n{{ $json.contact.city }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Тип клиента:*\n{{ $json.request.client_type }}"
        },
        {
          "type": "mrkdwn",
          "text": "*Услуга:*\n{{ $json.request.service_type }}"
        }
      ]
    },
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": "*Сообщение:*\n{{ $json.request.message }}"
      }
    },
    {
      "type": "context",
      "elements": [
        {
          "type": "mrkdwn",
          "text": "⏰ {{ $json.metadata.timestamp }} | IP: {{ $json.metadata.user_agent }}"
        }
      ]
    }
  ]
}
```

---

## Пример 8: Логирование и аналитика

### 📊 Запись всех заявок для анализа

**Workflow:**
```
Webhook
  ↓
SET (форматирование данных)
  ↓
Log (логирование)
  ↓
DB Save (сохранение в analytics таблицу)
  ↓
Response
```

### Узел SET для форматирования:

```javascript
// Добавьте информацию для аналитики

Name: formatted_request
Value:

{
  "request_id": "{{ $json.metadata.form_id }}_{{ Date.now() }}",
  "client": {
    "name": "{{ $json.contact.name }}",
    "phone": "{{ $json.contact.phone }}",
    "email": "{{ $json.contact.email }}",
    "city": "{{ $json.contact.city }}"
  },
  "service": "{{ $json.request.service_type }}",
  "client_type": "{{ $json.request.client_type }}",
  "timestamp": "{{ $json.metadata.timestamp }}",
  "source": "{{ $json.metadata.source }}",
  "referrer": "{{ $json.metadata.referrer }}",
  "timezone": "{{ $json.metadata.timezone }}"
}
```

---

## Пример 9: Отправка SMS уведомления

### 📱 SMS уведомление клиенту через Twilio

**Workflow:**
```
Webhook
  ↓
Twilio Send SMS
  ↓
Email Save
  ↓
Response
```

### Конфигурация Twilio узла:

```
From Number: ваш номер Twilio
To: {{ $json.contact.phone }}

Message:

Спасибо {{ $json.contact.name }}! Мы получили вашу заявку на {{ $json.request.service_type }}. Наш менеджер свяжется с вами в ближайшее время. SAFE AIR 🌬️
```

---

## Пример 10: Комплексный workflow (все в одном)

### 🎯 Полный цикл обработки заявки

```
Webhook (получить заявку)
  ↓
Validate (проверить данные)
  ↓
Save to DB (сохранить в БД)
  ↓
Notify Team (отправить команде)
  ├─ Email
  ├─ Slack
  ├─ Telegram
  │
Save to CRM (добавить в CRM/Sheets)
  ├─ Bitrix24
  ├─ Google Sheets
  │
Auto Response (ответить клиенту)
  ├─ Email подтверждение
  ├─ SMS (если номер валидный)
  │
Log Analytics (логирование)
  ├─ Analytics table в DB
  ├─ Slack analytics
  │
Response (вернуть успех клиенту)
```

---

## 🛠️ Полезные функции JavaScript в N8N

### Преобразование данных:

```javascript
// Форматирование даты
{{ new Date($json.metadata.timestamp).toLocaleString('ru-RU') }}

// Преобразование типа клиента в читаемый формат
{{ {
  'family': 'Частное лицо',
  'business': 'Компания',
  'public': 'Гос. организация'
}[$json.request.client_type] }}

// Проверка наличия email
{{ $json.contact.email ? $json.contact.email : 'не указан' }}

// Форматирование номера телефона
{{ $json.contact.phone.replace(/(\d{1})(\d{3})(\d{3})(\d{2})(\d{2})/, '+$1 ($2) $3-$4-$5') }}

// Количество символов в сообщении
{{ $json.request.message ? $json.request.message.length : 0 }}
```

### Условная логика:

```javascript
// Определить приоритет по типу клиента
{{ $json.request.client_type === 'public' ? 'HIGH' : 'NORMAL' }}

// Проверить количество оборудования
{{ parseInt($json.request.equipment_count) > 5 ? 'Много' : 'Мало' }}

// Составить описание услуги
{{ `Клиент ${$json.contact.name} из ${$json.contact.city} запросил ${$json.request.service_type} для ${$json.request.equipment_count} блока(ов)` }}
```

---

## ⚡ Советы и трюки

1. **Используйте variables** для часто меняющихся значений (email, Slack channel, и т.д.)
2. **Добавьте Error Handler узлы** для обработки ошибок в каждом workflow
3. **Используйте Logs** для отладки - добавляйте после каждого важного узла
4. **Кэшируйте результаты** если интегрируетесь с внешними APIs
5. **Используйте "Test" режим** перед включением workflow в production
6. **Резервируйте данные** - сохраняйте в БД, даже если отправка в CRM не удалась

---

**Версия:** 1.0
**Последнее обновление:** 2025-01-15

Удачи с интеграцией! 🚀
