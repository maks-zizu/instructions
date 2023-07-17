- `npm init -y`
- `npx create-gitignore node`
- `eslint --init` (глобальный)
- файл `.env`
  ```
    PORT=4000
    DATABASE_URL=postgres://user:pass@localhost:5432/db-name
    SESSION_SECRET=skelet
  ```
- файл `.gitignore` /sessions , .env
- файл `package.json`
  ```json
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js -e js,jsx,json --ignore ./sessions",
    "db-migrate": "npx sequelize db:migrate",
    "dbr": "npx sequelize db:drop && npx sequelize db:create && npx sequelize db:migrate && npx sequelize db:seed:all"
  },
  ```
## 1. База
- `npm i sequelize sequelize-cli pg pg-hstore`
- файл `.sequelizerc`
  ```
    const path = require('path')
    require('dotenv').config();

    module.exports = {
    'config': path.resolve('db','config', 'database.json'),
    'models-path': path.resolve('db', 'models'),
    'seeders-path': path.resolve('db', 'seeders'),
    'migrations-path': path.resolve('db', 'migrations')
    };
  ```
- `npx sequelize init`
- папка `./db/config/database.json` (postgres) или:
  ```json
  {
    "development": {
      "use_env_variable": "DATABASE_URL"
    },
    "production": {
      "use_env_variable": "DATABASE_URL",
      "dialectOptions": {
        "ssl": {
          "rejectUnauthorized": false
        }
      }
    }
  }
  ```
- `npx sequelize db:create` , `npx sequelize model:create --name --attributes `
  Минимум на две таблицы:
  ```js
    User
    `npx sequelize model:create --name User --attributes name:text,email:text,password:text`

    MediaPost
    `npx sequelize model:create --name MediaPost --attributes user_id:integer,type_id:integer,title:text,image:text`
  ```
  Далее можно скелет с большим количеством таблиц
  ```js
    Type
    `npx sequelize model:create --name Type --attributes title:text`

    CommentToPost
    `npx sequelize model:create --name CommentToPost --attributes user_id:integer,post_id:integer,title:text`

    Like
    `npx sequelize model:create --name Like --attributes user_id:integer,post_id:integer`
  ```
- миграции:
    >User email `unique: true`
    >MediaPost, CommentToPost, Like:
      `references: { model: '...s', key: 'id', }, onDelete: 'CASCADE',`
- модели:
  > User
    `this.hasMany(MediaPost, { foreignKey: 'user_id' });`
    `this.hasMany(CommentToPost, { foreignKey: 'user_id' });`
    `this.hasMany(Like, { foreignKey: 'user_id' });`

  > Type
    `this.hasMany(MediaPost, { foreignKey: 'type_id' });`

  > MediaPost
    `this.belongsTo(User, { foreignKey: 'user_id' });`
    `this.belongsTo(Type, { foreignKey: 'type_id' });`
    `this.hasMany(Like, { foreignKey: 'post_id' });`

  > CommentToPost, Like
    `this.belongsTo(User, { foreignKey: 'user_id' });`
    `this.belongsTo(MediaPost, { foreignKey: 'post_id' });`
- `npx sequelize db:migrate`
- сиды:
  - `npx sequelize seed:create --name xxx`
  - `npx sequelize db:seed:all`

## 2. Сервер
### Пакеты
- `npm i -D nodemon`
- `npm i express morgan @babel/core @babel/preset-env @babel/preset-react @babel/register react react-dom bcrypt cookie-parser express-session session-file-store @faker-js/faker`
  ```
      express
      morgan
      @babel/core @babel/preset-env @babel/preset-react @babel/register react react-dom
      bcrypt
      cookie-parser
      express-session
      session-file-store
      @faker-js/faker
  ```
- `npm install dotenv --save`

- файл `.babelrc` - `{"presets": ["@babel/preset-env", "@babel/preset-react"]}`

- папки:
  `components (views)` (.jsx)
  `middleware` (ssr.js , getUser.js)
  `public` ( /scripts/script.js , /scripts/auth.js, /styles/style.css )
  `config` (serverConfig.js , sessionConfig.js)
  `routes` ( /api , /views )
### 2.1 app.js ___ (запуск сервера)
- `require('@babel/register')`
- `const express = require('express')`
- `const app = express()`
- `const PORT = process.env.PORT ?? 3000`
- `app.listen(PORT, () => {console.log('Server started at ${PORT} port'})`
- запуск `npm run dev`
### 2.2 папка components (views) ___(Layout.jsx)
- Navbar.jsx
- Layout.jsx
  ```jsx
    const React = require('react');
    const Navbar = require('./Navbar');

    function Layout({ title, children }) {
      return (
        <html lang='en'>
          <head>
            <title>{title}</title>
            <link rel='stylesheet' href='style.css' />
            <script src='script.js' />
          </head>
          <body>
            <Navbar />
            {children}
          </body>
        </html>
      );
    }

    module.exports = Layout;
  ```
- Main.jsx
- Log.jsx
- Reg.jsx
- Form.jsx
- PostItem.jsx
  ```jsx
    const React = require('react');

    function PostItem() {
      return (
        <h2>PostItem</h2>
      );
    }

    module.exports = PostItem;
  ```
- PostsList.jsx
  ```jsx
    const React = require('react');
    const Layout = require('./Layout'); (подтянется)
    const PostItem = require('./PostItem'); (подтянется)

    function PostsList({ title }) {
      return (
        <Layout title={title}>
          <h2>Posts</h2>
          <PostItem />
        </Layout>
      );
    }

    module.exports = PostsList;
  ```
### 2.3 папка middleware ___(ssr.js)
```js
  const React = require('react');
  const ReactDOMServer = require('react-dom/server');

  function renderComponent(
    reactComponent,
    props = {},
    options = { htmlOnly: false },
  ) {
    const reactElement = React.createElement(reactComponent, {
      ...this.app.locals,
      ...this.locals,
      ...props,
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
- передать в файл `app.js`
  `const ssr = require('./middleware/ssr');`
  ```js
    app.use(express.urlencoded({ extended: true }));
    app.use(express.json());
    app.use(ssr);
  ```
### 2.4 папка routes ___(main.routes.js)
(отдать компоненты на клиента)
```js
  const router = require('express').Router();
  const Main = require('../components/Main');

  router.get('/', (req, res) => {
    res.send(res.renderComponent(Main, { title: 'Main page' }));
  });

  module.exports = router;
```
### 2.5 app.js ___( app.use('/', Route) )
  ```js
    const mainRoute = require('./routes/main.routes');
    const postsRoute = require('./routes/posts.routes');
    const authRoute = require('./routes/auth.routes');

    app.use('/', mainRoute);
    app.use('/posts', postsRoute);
    app.use('/auth', authRoute);
  ```
### 2.6 Bootstrap
заполнить карточки, страницы
logreg - добавить action , metod
### 2.7 app.js , public
- `app.use(express.static(path.join(__dirname, 'public')));`
- файл `/public/scripts/client.js`
- подключить в Layout `<script defer src="/scripts/client.js"/>`
