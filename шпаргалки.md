## Project setup / start

- `npm init -y` --- инициализация проекта и генерация *package.json*
- `npx create-gitignore node` --- формирование файла .gitignore
   npm i -D eslint
- `npx eslint --init` --- установка линтера ESLint
- `npm i sequelize pg pg-hstore sequelize-cli` || (npm i sequelize sequelize-cli pg pg-hstore) - Так у Артема на лекции леопардов --- установка библиотеки Sequelize ORM и драйверов для работы с СУБД PostgreSQL
- `.sequelizerc` --- создание файла конфигурации для sequelize в корне проекта (для изменения путей по умолчанию) в этот файл вставляем то, что ниже
const path = require('path')
module.exports = {
 'config': path.resolve('db', 'config', 'database.json'),
 'models-path': path.resolve('db', 'models'),
 'seeders-path': path.resolve('db', 'seeders'),
 'migrations-path': path.resolve('db', 'migrations')
}

- `npx sequelize init` --- установка и инициализация CLI-утилиты для работы с Sequelize ORM

- `npm i express` --- установка фреймворка для создания серверов
- `npm i -D nodemon` --- установка утилиты nodemon для перезапуска серверного приложения при изменениях в файловой системе


- `npm i morgan` --- установка библиотеки для логирования HTTP-запросов
- `config/config.js` --- создание папки config в корне проекта с файлом конфигурации сервера (внтури функция confing для middlewares, routes)

- `npm i @babel/core @babel/preset-env @babel/preset-react @babel/register react react-dom` --- установка Babel и React
- `.babelrc` --- создание файла конфигурации для babel в корне проекта с содержимым `{"presets": ["@babel/preset-env", "@babel/preset-react"]}`

- `npm i cookie-parser` --- установка пакета для чтения заголовка запроса cookie на сервере
- `npm i express-session` --- установка пакета для работы с сессиями на сервере
- `npm i session-file-store` --- установка пакета для создания хранилища сессий
- `npm i bcrypt` --- установка библиотеки для хэширования строк

- `npm install --save-dev jest` --- установка пакета тестирования Jest

- `npm i` --- установка всех пакетов проекта при его копировании из GitHub на локальный компьютер

- `.env --- создание секретного файла, внутри пишем PORT = 3000` (npm i dotenv)- у Юры
- `npm install dotenv --save` --- установка пакета для чтения секретного файла env

- `"main": app.js"` --- изменить имя главного файла в проектном файле package.json
- `"scripts":
   - `"start": "node app.js"` --- изменить скрипт старта в проектном файле package.json
   - `"dev": "nodemon app.js -e js,jsx,json --ignore ./sessions"` --- изменить скрипт запуска разработки в проектном файле package.json
   - `"dbr": "npx sequelize db:drop && npx sequelize db:create && npx sequelize db:migrate && npx sequelize db:seed:all"` --- дроп и создание базы



   ------------------------------------------------------------------------------


## Require - file and module loader (import)

- `const fs = require('fs')` --- подключение модуля файловой системы
- `const path = require('path')` --- подключение модуля для работы с путями в файловой системе

- `const { Sequelize } = require('sequelize')` --- подключение Sequelize ORM
- `const { sequelize } = require('./models')` --- подключение Sequelize CLI
- `const db = require('../db/models')` --- подключение всех моделей

- `const express = require('express')` --- поключение фреймворка Express
- `const morgan = require("morgan")` --- подключение библиотеки morgan

- `require('@babel/register')` --- подключение Babel
- `const React = require('react')` --- подключение React
- `const ReactDOMServer = require('react-dom/server')` --- подключение ReactDOMServer

- `const config = require('./config/config')` --- подключение файла конфигурации сервера

- `const router = require('express').Router()` --- создание роутера - подключение фреймворка Express и вызов метода Router
- `const someRouter = require('./routes/some.route.js')` --- подключение одного из роутеров в app.js

- `const cookieParser = require('cookie-parser')` --- подключение cookie-parser
- `const session = require('express-session')` --- подключение express-session
- `const FileStore = require('session-file-store')(session)` --- подключение хранилища сессий
- `const bcrypt = require('bcrypt')` --- подключение bcrypt


   ------------------------------------------------------------------------------


## Express server setup

- `const app = express()` --- создание серверного приложения
- `const router = express.Router()` --- создание роутера
- `const PORT = process.env.PORT ?? 3000` --- уставновка порта сервера
- `app.listen(PORT, () => console.log('Server started at ${PORT} port') ` --- запуск порта
- `config(app)` --- вызов конфигурации сервера с передачей в нее серверного приложения
- `app.use('/somelink', someRouter)` --- подключение одного из роутеров (маршрутизация)


## Express answers

- `res.send(text)` --- отправка текста с кодом 200 и завершение ответа
- `res.json({ user: 'tobi' })` --- отправка JSON с кодом 200 и завершение ответа
- `res.end()` --- завершение ответа
- `res.status(403)`--- установка кода, но не завершение ответа
- `res.status(500).json({ error: 'message' })` --- установка кода 500, отправка JSON и завершение ответа
- `res.status(404).end()` --- установка кода и завершение ответа
- `res.redirect('/other-route')` --- переадресование клиента и завершение ответа
- `res.render(view, {data})` --- рендер шаблона, отправка html и завершение ответа
- `res.write('<!DOCTYPE html>')` --- отправка первой строки HTML-документа, но не завершение ответа


## Express middlewares

- `function someMid(req, res, next) {....... next()}` --- создание middleware
- `app.use(express.urlencoded({ extended: true }))` --- чтение данных из тела запроса
- `app.use(express.json())` --- чтение JSON-данных из тела запроса
- `app.use(morgan("dev"))` --- вывод в консоли сервера информации о входящих запросах и ответах на них
- `app.use(express.static(path.join(__dirname, 'public')))` --- подключение статики: файлы в папке public доступны браузеру (автоматическая маршрутизация)
- `app.use(cookieParser())` --- чтения заголовка запроса cookie на сервере и создание на нем объекта req.cookies
- `app.use(session(sessionConfig))` --- генерация сессий и отправка куки с id сессии на клиент (создание объекта req.session)


   ------------------------------------------------------------------------------


## AJAX, Fetch API

- `const response = await fetch('https://some-url.com')` --- отправка GET запроса (возвращает Promise)

- отправка POST запроса:
```
   const response = await fetch('https://some-url.com', {
     method: 'POST',
     headers: {'Content-Type': 'application/json'},
     body: JSON.stringify({myKey: 'my value'})
   })
```

- `const data = await response.json();` --- чтение тела ответа в JSON-формате

- `event.preventDefault()` --- отмена действия элемента по умолчанию

- `GET: получить данные` / `POST: отправить` / `PUT: изменить` / `DELETE: удалить` --- HTTP методы

- `const hash = await bcrypt.hash(req.body.password, 10)` --- пропустить пароль и сгенерированную соль через алгоритм хэширования 2^10 раз

   ------------------------------------------------------------------------------


## Sessions

- конфигурация сессий в Express:
```
   const sessionConfig = {
     store: new FileStore(), --- хранилище сессий (работает с пакетом *session-file-store*)
     name: 'user_sid', --- имя куки для хранения id сессии
     secret: process.env.SESSION_SECRET ?? 'test', --- секретное слово для шифрования, может быть любым
     resave: false, --- пересохранять ли куку при каждом запросе
     saveUninitialized: false, --- создавать ли сессию без инициализации ключей в req.session
     cookie: {
       maxAge: 1000 * 60 * 60 * 12,
       httpOnly: true,
     },
  };

```

   ------------------------------------------------------------------------------


## Array/Object clone

`const arrCopy = […arr]` / `... = arr.slice()` / `... = arr.map(el => el)` --- поверхностное копирование массива
`const objCopy = {...origObj}` / `... = Object.assign({}, origObj)` --- поверхностное копирование объекта

`const deepArrCopy = JSON.parse(JSON.stringify(arr))` --- глубокое копирование массива
*(копирование через JSON с оргнаичениями: не поддерживает объекты с циклическими ссылками, Map, Set, Date, RegExp и ArrayBuffer, теряются значения c undefined, NaN меняется на null)*

`const deepObjCopy = structuredClone(origObj)` --- глубокое копирование объекта (современный подход, с Node 17.0)
