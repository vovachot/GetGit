# GetGit

GetGit — приложение для поиска и просмотра GitHub-проектов. Серверная часть находится в `worker.js` и рассчитана на **Cloudflare Workers + Cloudflare D1**.

## Требования

- Node.js LTS и npm
- аккаунт Cloudflare
- база Cloudflare D1
- Git (если клонируете репозиторий)

Внешние npm-библиотеки для runtime-кода не нужны: `worker.js` использует Web APIs, доступные в Cloudflare Workers. `wrangler` указан в `devDependencies` как инструмент локального запуска и публикации.

## Установка

```bash
npm install
```

## Настройка базы данных D1

1. Войдите в Cloudflare:

   ```bash
   npx wrangler login
   ```

2. Создайте базу:

   ```bash
   npx wrangler d1 create getgit-db
   ```

3. Скопируйте выданный `database_id` в `wrangler.toml`, заменив `REPLACE_WITH_YOUR_D1_DATABASE_ID`.
4. Worker автоматически создаёт необходимые таблицы при первом запросе, когда доступно подключение `DB`.

## Локальный запуск

```bash
npm run dev
```

Чтобы задать локальные переменные, скопируйте `.dev.vars.example` в `.dev.vars` и заполните нужные значения. Файл `.dev.vars` исключён из Git.

## Публикация

```bash
npm run deploy
```

После публикации настройте секреты, которые действительно используете, например:

```bash
npx wrangler secret put ANTHROPIC_API_KEY
npx wrangler secret put GITHUB_TOKEN
npx wrangler secret put ADMIN_TOKEN
```

Для GitHub OAuth, почтовой отправки, Apple-входа и платёжного провайдера нужны дополнительные параметры. Не задавайте секреты, пока не настроили соответствующую интеграцию. Список переменных ищите в `worker.js`.

## Важно

- Не публикуйте API-ключи, токены, пароли, приватные ключи или заполненный `.dev.vars`.
- В `wrangler.toml` замените placeholder `database_id` до публикации.
- Этот проект использует API и окружение Cloudflare Workers и **не запускается как обычный Node.js-сервер на VPS без переноса/адаптации backend**.
- Размещение на другом хостинге само по себе не гарантирует доступность сайта из России без VPN; это нужно проверять из соответствующей сети.
