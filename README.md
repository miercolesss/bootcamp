# Форма зворотного зв'язку (PWA): інструкція без коду

## 1. Виставити в інтернет (GitHub Pages, безкоштовно, ~5 хв)
1. Зареєструйтесь на github.com → **New repository** → назва `feedback` → Public → Create.
2. Натисніть **uploading an existing file** і перетягніть усі 5 файлів (index.html, manifest.json, sw.js, icon-192.png, icon-512.png) → Commit.
3. **Settings → Pages → Branch: main / root → Save.**
4. Через 1-2 хв сторінка буде тут: `https://ВАШ-НІК.github.io/feedback/`

## 2. Підключити n8n (одна вставка)
1. У n8n створіть workflow з вузлом **Webhook**: метод `POST`, шлях `feedback`, Respond: **Using 'Respond to Webhook' Node**.
2. У налаштуваннях Webhook → Options → **Allowed Origins (CORS)**: `*` (для демо).
3. Додайте вузол **Respond to Webhook** і поверніть JSON: `{ "message": "Прийнято. У цій партії вже є схожі звернення, інженерів повідомлено." }`
4. Скопіюйте **Production URL** вебхука (workflow має бути Active).
5. Відкрийте `index.html` у Блокноті, знайдіть рядок `WEBHOOK_URL:` і замініть текст у лапках на цей URL. Збережіть і ще раз завантажте файл на GitHub.
6. У sw.js змініть `feedback-v1` на `feedback-v2` (щоб телефони взяли нову версію).

Якщо n8n працює на вашому ноутбуці: `cloudflared tunnel --url http://localhost:5678` дає публічну https-адресу.

## 3. Посилання для NFC/QR
`https://ВАШ-НІК.github.io/feedback/?id=A7K-2941&unit=U-114`
- `id`: код виробу (у мітку краще писати випадковий ідентифікатор, а не значущі дані);
- `unit`: код підрозділу (необов'язково, форма запитає один раз і запам'ятає).
Для NFC запишіть це посилання як тип **URL/NDEF** (застосунок NFC Tools). Для QR: будь-який генератор.

## 4. Які дані приходять у n8n (JSON)
`id, ts, serial, unit, node, symptom, when, severity, repeat, comment, photo (base64 або порожньо)`
Далі: вузол Postgres/Google Sheets → Insert.

## 5. Перевірка перед пітчем
1. Відкрийте посилання на телефоні → «Додати на головний екран».
2. Авіарежим → відкрийте застосунок → заповніть → «Відправити»: «Збережено в телефоні», угорі «У черзі: 1».
3. Вимкніть авіарежим → черга порожніє, у n8n з'явилась подія.
