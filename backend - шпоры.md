﻿## Project setup / start

- `npm init -y` инициализация проекта и генерация _package.json_
- `npx create-gitignore node` формирование файла .gitignore
- `npm i` установка всех пакетов проекта при его копировании из GitHub на локальный компьютер
- `npm i -D eslint` // `npx eslint --init` // `eslint --init` commonjs, react, browser/node,

## Установка и подключение пакетов БД

---

## Установка и подключение пакетов - сервер

### express (app)

- `npm i express` установка фреймворка для создания серверов

  - `const express = require('express')`

- https://expressjs.com/en/4x/api.html справочник команд

  - app.get()
  - res.send()
  - res.sendFile()

#### Express server setup

- `const app = express()` создание серверного приложения
- `const router = express.Router()` создание роутера
- `const PORT = 3000` уставновка порта сервера
- `const PORT = process.env.PORT ?? 3000` уставновка порта сервера
- `app.listen(PORT, () => console.log('Server started at ${PORT} port') ` запуск порта
- `config(app)` вызов конфигурации сервера с передачей в нее серверного приложения
- `app.use('/somelink', someRouter)` подключение одного из роутеров (маршрутизация)

#### Express answers

- `res.send(text)` отправка текста с кодом 200 и завершение ответа
- `res.json({ user: 'tobi' })` отправка JSON с кодом 200 и завершение ответа
- `res.end()` завершение ответа
- `res.status(403)`--- установка кода, но не завершение ответа
- `res.status(500).json({ error: 'message' })` установка кода 500, отправка JSON и завершение ответа
- `res.status(404).end()` установка кода и завершение ответа
- `res.redirect('/other-route')` переадресование клиента и завершение ответа
- `res.render(view, {data})` рендер шаблона, отправка html и завершение ответа
- `res.write('<!DOCTYPE html>')` отправка первой строки HTML-документа, но не завершение ответа

#### Express middlewares

Промежуточные функции (повторяемый код).

- `app.use(express.urlencoded({ extended: true }))` чтение (парсинг) данных из тела запроса (полученные из html-формы )
- `app.use(express.json())` чтение JSON-данных из тела запроса
- `app.use(morgan("dev"))` вывод в консоли сервера информации о входящих запросах и ответах на них
- `app.use(express.static(path.join(__dirname, '..', 'public')))` подключение статики: файлы в папке public доступны браузеру (автоматическая маршрутизация)
- `app.use(cookieParser())` чтения заголовка запроса cookie на сервере и создание на нем объекта req.cookies

- `function someMid(req, res, next) {....... next()}` создание middleware
- `app.use(session(sessionConfig))` генерация сессий и отправка куки с id сессии на клиент (создание объекта req.session)

##### Пример кастомного метода для res (отправка компонента, ssr)

// Вспомогательная функция для отправки HTML на основе React-компонента. Чтобы компонент отправить на клиента:

1. `const React = require('react')` из jsx сделать React-элемент
2. `const ReactDOMServer = require('react-dom/server')` из React-элемента сделать комфортную разметку.

```js
function renderComponent(
  reactComponent,
  props = {},
  options = { htmlOnly: false }
) {
  const reactElement = React.createElement(reactComponent, {
    ...this.app.locals, // передать app.locals
    ...this.locals, // передать res.locals
    ...props, // передать пропсы
  });

  const html = ReactDOMServer.renderToStaticMarkup(reactElement);

  return options.htmlOnly ? html : `<!DOCTYPE html>${html}`;
}

function ssr(req, res, next) {
  res.renderComponent = renderComponent;
  next();
}

module.exports = ssr;
```

подключить `app.use(ssr)`
добавить в роутер `res.send(res.renderComponent(ProductItem, { product }, { doctype: false }))`

### nodemon

- `npm i -D nodemon` установка утилиты nodemon для перезапуска серверного приложения при изменениях в файловой системе

  - `"dev": "nodemon app.js -e js,jsx,json --ignore ./sessions"` изменить скрипт запуска разработки в проектном файле package.json (`npm run dev`)

### morgan

- `npm i morgan` установка библиотеки для логирования HTTP-запросов

  - `const morgan = require("morgan")` подключение библиотеки morgan
  - https://expressjs.com/en/resources/middleware/morgan.html
  - https://github.com/expressjs/morgan
  - `app.use(morgan(ctrl + space))`

### Babel и React

Babel - транспилятор, позволяет подключать jsx файлы (JavaScript, в котором можно писать HTML).(отделяет js,html... и логически связывает)

- `npm i @babel/core @babel/preset-env @babel/preset-react @babel/register react react-dom` установка Babel и React
- `require('@babel/register')` подключение Babel
- `const React = require('react')` подключение React
- `const ReactDOMServer = require('react-dom/server')` подключение ReactDOMServer
- `.babelrc` создание файла конфигурации для babel в корне проекта с содержимым `{"presets": ["@babel/preset-env", "@babel/preset-react"]}`

### Папка components

// components/Layout.jsx

```jsx
const React = require('react');

function Layout({ title, children }) {
  return (
    <html lang="en">
      <head>
        <title>{title}</title>
        <link rel="stylesheet" href="style.css" />
        <script src="script.js" />
      </head>
      <body>{children}</body>
    </html>
  );
}

module.exports = Layout;
```

```jsx
function Home({ title, name }) {
  return (
    <Layout title={title}>
      html className вместо class style задаётся как объект,
    </Layout>
  );
}
module.exports = Home;
```

#### React SSR: пример ответа для GET запроса

// app.js
Отображаем главную страницу с использованием компонента "Home"

```js
const ReactDOMServer = require('react-dom/server');
const React = require('react');
const Home = require('./components/Home');

app.get('/', (req, res) => {

// создаём React-элемент на основе React-компонента
const home = React.createElement(Home, пропсы {
title: 'My site',
name: 'John',
});

// рендерим элемент и получаем HTML (в виде строки)
const html = ReactDOMServer.renderToStaticMarkup(home);

// отправляем первую строку нашего HTML-документа
res.write('<!DOCTYPE html>');

// отправляем отрендеренный HTML и закрываем соединение
res.end(html);
});

    или
    const document = `<!DOCTYPE html>${html}`
    res.send(document)
```

###

- `config/config.js` создание папки config в корне проекта с файлом конфигурации сервера (внтури функция confing для middlewares, routes)

- `npm i cookie-parser` установка пакета для чтения заголовка запроса cookie на сервере
- `npm i express-session` установка пакета для работы с сессиями на сервере
- `npm i session-file-store` установка пакета для создания хранилища сессий
- `npm i bcrypt` установка библиотеки для хэширования строк

- `npm install --save-dev jest` установка пакета тестирования Jest

- `.env    создание секретного файла, внутри пишем PORT = 3000` (npm i dotenv)- у Юры
- `npm install dotenv --save` установка пакета для чтения секретного файла env

- `"main": app.js"` изменить имя главного файла в проектном файле package.json
- `"scripts":
- `"start": "node app.js"` изменить скрипт старта в проектном файле package.json (`npm start`)

- `const config = require('./config/config')` подключение файла конфигурации сервера

- `const router = require('express').Router()` создание роутера - подключение фреймворка Express и вызов метода Router
- `const someRouter = require('./routes/some.route.js')` подключение одного из роутеров в app.js

- `const cookieParser = require('cookie-parser')` подключение cookie-parser
- `const session = require('express-session')` подключение express-session
- `const FileStore = require('session-file-store')(session)` подключение хранилища сессий
- `const bcrypt = require('bcrypt')` подключение bcrypt

---

### AJAX, Fetch API

- `const response = await fetch('https://some-url.com')` отправка GET запроса (возвращает Promise)

- отправка POST запроса:

```js
const response = await fetch('https://some-url.com', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ myKey: 'my value' }),
});
```

- `const data = await response.json();` чтение тела ответа в JSON-формате

- `event.preventDefault()` отмена действия элемента по умолчанию

- `GET: получить данные` / `POST: отправить` / `PUT: изменить` / `DELETE: удалить` HTTP методы

- `const hash = await bcrypt.hash(req.body.password, 10)` пропустить пароль и сгенерированную соль через алгоритм хэширования 2^10 раз

  ***

### Sessions

- конфигурация сессий в Express:

```js
   const sessionConfig = {
     store: new FileStore(),    хранилище сессий (работает с пакетом *session-file-store*)
     name: 'user_sid',    имя куки для хранения id сессии
     secret: process.env.SESSION_SECRET ?? 'test',    секретное слово для шифрования, может быть любым
     resave: false,    пересохранять ли куку при каждом запросе
     saveUninitialized: false,    создавать ли сессию без инициализации ключей в req.session
     cookie: {
       maxAge: 1000 * 60 * 60 * 12,
       httpOnly: true,
     },
  };

```

---

## Подключение пакетов - require (import)

- `const fs = require('fs')` подключение модуля файловой системы
- `const path = require('path')` подключение модуля для работы с путями в файловой системе

---
