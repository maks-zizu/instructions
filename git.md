﻿## Git

- `git checkout dev` переключение на dev
- `git checkout -b dev` создание новой ветки и переключение на нее
- `git push origin dev` отправки изменений (добавление новой ветки) в GitHub репозиторий
- `git checkout somebranch`переключение на ветку без создания новой
- `git status` просмотр внесенных изменений
- `git add -A` добавление содержимого в индекс для последующего коммита
- `git commit -m 'some comment'` создание коммита
- `git push origin somebranch` отправка изменений в ветке, внесенных в коммит на локальном компьютере, в GitHub репозиторий
- `git pull origin dev` скачивание всех изменений из GitHub репозитория к себе в ветку на локальный компьютер
- `git fetch` --> `git reset --hard origin/dev` --> `git clean -fd` отменить все локальные изменения _(в т.ч. удалить файлы!)_
- `git revert НОМЕР-КОММИТА` --> `git push origin somebranch` отмена коммита и отправка изменений в репозиторий GitHub

`git config pull.rebase false` # объединить
`git config pull.rebase true` # перебазировать
`git config только pull.ff` # только быстрая перемотка вперед
Вы можете заменить «git config» на «git config --global», чтобы установить предпочтение по умолчанию для всех репозиториев.
Вы также можете передать --rebase, --no-rebase,
или --ff-only в командной строке, чтобы переопределить настроенное значение по умолчанию для каждого вызова.

### Git - new project initialization from GitHub repository

- `gitHub.com -> New repository -> Select owner & name -> uploading an existing file` создание нового проекта на GitHub
- `git clone https://github.com/***.git` - клонирование репозитория GitHub на локальный компьютер

### Git - new project initialization on local device

- `git init` инициализация git при создании нового проект на локальном компьютере
- `git branch -M master` переименование ветки в master
- `git branch` проверка имеющихся веток
- `git remote add origin https://github.com/***.git` соединение ветки с сервером (если не клонировали репозитотрий из GitHub)
- `git push -u origin master` отправка изменений на сервер в ветку master

  ***

## Project setup / start

- `npm init -y` инициализация проекта и генерация _package.json_
- `npx create-gitignore node` формирование файла .gitignore
- `npm i` установка всех пакетов проекта при его копировании из GitHub на локальный компьютер
- `npm i -D eslint` // `npx eslint --init` // `eslint --init` commonjs, react, browser/node,

## Установка и подключение пакетов БД

- `npm i sequelize sequelize-cli pg pg-hstore` установка библиотеки Sequelize ORM и драйверов для работы с СУБД PostgreSQL (в любом порядке)
- `.sequelizerc` создание файла конфигурации для sequelize в корне проекта (для изменения путей по умолчанию) в этот файл вставляем:

```
const path = require('path')
module.exports = {
'config': path.resolve('db','config', 'database.json'),
'models-path': path.resolve('db', 'models'),
'seeders-path': path.resolve('db', 'seeders'),
'migrations-path': path.resolve('db', 'migrations')
};
```

- `npx sequelize init` установка и инициализация CLI-утилиты для работы с Sequelize ORM
- папка `./db/config/database.json`
  ```
    "username": "postgres" свое название ,
    "password": "postgres" свое название,
    "database": свое название,
    "dialect": "postgres",
  ```
- `npx sequelize db:create` создание бд (и далее работа с sequelize)

- `const { Sequelize } = require('sequelize')` подключение Sequelize ORM
- `const { sequelize } = require('./models')` подключение Sequelize CLI
- `const db = require('../db/models')` подключение всех моделей

- `"dbr": "npx sequelize db:drop && npx sequelize db:create && npx sequelize db:migrate && npx sequelize db:seed:all"` дроп и создание базы

### Sequelize & model methods

- `async function testConnection() {await.sequelize.authenticate()}` функция для тестирования соединенния с БД (обернуть await в try/catch)

(После установки Sequelize, создания .sequelizerc, и изменения конфигурации)

- `npx sequelize` все команды
- `npx sequelize db:create` создание бд
- `npx sequelize model:create --name User --attributes name:text,age:integer` создание модели User с полями name и age и миграции
  После создания надо отредактировать файлы миграции и модели.
  Миграция: добавить/изменить колонки, к зависимым колонкам добавить `references: { model: '...s', key: 'id', },` `onDelete: 'CASCADE',`, копи-паст в модель.
  createTable(dropTable)
  Модель: associate — добавить зависимости this.\_(модель, {foreignKey: '\_id'})
- `npx sequelize migration:create --name new-migration` создание новой миграции-
- `npx sequelize db:migrate` запуск всех непримененных миграций по очереди
- `npx sequelize db:migrate:undo` / `...:undo --name XXXXX.js` / `...:undo:all` отмена последней / конкретной / всех миграций
- `npx sequelize seed:create --name xxx`
  Ceed:

  - заполнит таблицу
    await queryInterface.bulkInsert(.bulkDelete)(_мн.ч.) (доб. дату)
    { _ } = require('../models'); await \_.create(.destroy)({})

- `npx sequelize db:seed ==XXXXX.js` / `...:seed:all` запуск конкретного / всех сидеров
- `npx sequelize db:seed:undo /...:all` отмена сидов

- `INSERT: Model.create()` / `SELECT: Model.findAll()` / `UPDATE: Model.update()` / `DELETE: Model.destroy()` CRUD-операции с записями
- `const user = await User.create({name: "Alex", age: 25})` создание экземпляра модели и сохранение его в БД
- `await User.update({ name: "Paul", age: 30},{where: { id: 3 }})` редактирование записи в таблице, подходящей по заданным параметрам
- `await User.destroy({where: { name: "Paul" }});` удаление записи, подходящей по заданным параметрам

- конфигурация файла `.sequelizerc`:

```
   const path = require('path');
   module.exports = {
    'config': path.resolve('db', 'config', 'database.json'),
    'models-path': path.resolve('db', 'models'),
    'seeders-path': path.resolve('db', 'seeders'),
    'migrations-path': path.resolve('db', 'migrations'),
   };
```



### Model references

- установка в модели связи с другой моделью (many-to-one):

```
   statics associate({МОДЕЛЬ-2}) {
     МОДЕЛЬ-1.belongsTo(МОДЕЛЬ-2, {
     	foreignKey: КЛЮЧ
     })
   }
```

- установка в модели связи с другой моделью (one-to-many):

```
   statics associate({МОДЕЛЬ-1}) {
     МОДЕЛЬ-1.hasMany(МОДЕЛЬ-2, {
     	foreignKey: КЛЮЧ
     })
   }
```

- установка в модели связи с другой моделью через третью модель (many-to-many), где КЛЮЧ-1 относится к МОДЕЛИ-1, а КЛЮЧ-2 - к МОДЕЛИ-2:

```
   statics associate({МОДЕЛЬ-2, МОДЕЛЬ-3}) {
     МОДЕЛЬ-1.belongsToMany(МОДЕЛЬ-2, {
     	through МОДЕЛЬ-3,
     	foreignKey: КЛЮЧ-1,
     	otherKey: КЛЮЧ-2
     })
   }
```

- установка в файле миграции внешнего ключа для связи с другой таблицей

```
   someId: {
     type: Sequelize.INTEGER,
     allowNull: false,
     references: {
       model: 'ДРУГАЯ ТАБЛИЦА'
     }
   }
```

--- запрос на получение сырых данных + указание на конкретные поля + указание на связь с другой таблицей (one-to-many / many-to-one)

```
   const result = await МОДЕЛЬ-1.findAll({
     raw: true,
     attributes: ['id', 'name']
     include: МОДЕЛЬ-2
   }
```

--- запрос на получение сырых данных + указание на конкретные поля + указание на связь с другой таблицей (many-to-many) через КЛЮЧ

```
   const result = await МОДЕЛЬ-1.findAll({
     raw: true,
     attributes: ['id', 'name']
     where: {id: КЛЮЧ}
     include: МОДЕЛЬ-2
   }
```

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

```
function renderComponent(reactComponent, props = {}, options = { htmlOnly: false }) {

const reactElement = React.createElement(reactComponent, {
...this.app.locals, // передать app.locals
...this.locals, // передать res.locals
...props, // передать пропсы
});

const html = ReactDOMServer.renderToStaticMarkup(reactElement);

return options.htmlOnly ? html : `<!DOCTYPE html>${html}`
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

```
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
};

module.exports = Layout;
```
```
function Home({ title, name }) {
return (
<Layout title={title}>
html
className вместо class
style задаётся как объект,
</Layout>
);
};
module.exports = Home;
```



#### React SSR: пример ответа для GET запроса
// app.js
Отображаем главную страницу с использованием компонента "Home"

```
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

```
   const response = await fetch('https://some-url.com', {
     method: 'POST',
     headers: {'Content-Type': 'application/json'},
     body: JSON.stringify({myKey: 'my value'})
   })
```

- `const data = await response.json();` чтение тела ответа в JSON-формате

- `event.preventDefault()` отмена действия элемента по умолчанию

- `GET: получить данные` / `POST: отправить` / `PUT: изменить` / `DELETE: удалить` HTTP методы

- `const hash = await bcrypt.hash(req.body.password, 10)` пропустить пароль и сгенерированную соль через алгоритм хэширования 2^10 раз

  ***

### Sessions

- конфигурация сессий в Express:

```
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

## Array/Object clone

`const arrCopy = […arr]` / `... = arr.slice()` / `... = arr.map(el => el)` поверхностное копирование массива
`const objCopy = {...origObj}` / `... = Object.assign({}, origObj)` поверхностное копирование объекта

`const deepArrCopy = JSON.parse(JSON.stringify(arr))` глубокое копирование массива
_(копирование через JSON с оргнаичениями: не поддерживает объекты с циклическими ссылками, Map, Set, Date, RegExp и ArrayBuffer)_

`const deepCopy = JSON.parse(JSON.stringify(arr))` глубокое копирование массива
_(копирование через JSON с ограничениями: теряются значения c undefined, NaN меняется на null)_

`const deepObjCopy = structuredClone(origObj)` глубокое копирование объекта (современный подход, с Node 17.0)

## React + TypeScript

- `npx create-react-app my-app --template typescript` / `npx create-react-app my-app`  создать папку my-app с dev окружением
- `npx create-react-app . --template typescript` / `npx create-react-app .` наполнить текущую папку dev окружением
- https://tailwindcss.ru/docs/installation/using-postcss/
- у html элементов с одинаковым типом должен быть свой key в props
- создаем в components элементы и вставляем в App.tsx
- https://fakestoreapi.com/docs
- https://fakestoreapi.com/products/1
-