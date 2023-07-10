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
