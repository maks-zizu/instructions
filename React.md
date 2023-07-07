## План:
- Create React App — окружение для разработки клиентского React приложения
- TypeScript
- CRA + TypeScript
- пример настроеного CRA + TS + ESLint: `cra-ts-template`
- `find . -name "node_modules" -exec rm -rf "{}" \;` Скрипт для удаления node_modules рекурсивно:

## Create React App (JavaScript):
`Create React App` — среда для изучения React и лучший способ начать создание нового веб-приложения на React.

Инструмент настраивает среду для использования новейших возможностей `JavaScript`, оптимизирует приложение для продакшена и обеспечивает комфорт во время разработки. Вам понадобятся `Node.js` не ниже версии `14.0.0` и `npm` не ниже версии `5.6` на вашем компьютере. Для создания проекта выполните команды:

- команда для инициализации CRA в текущуюю папку (папка должна быть пустой): `npx create-react-app .`
- команда для инициализации CRA в папку 'example': `npx create-react-app example`

### Важные моменты CRA (JavaScript):
- после установки `CRA` автоматически инициализируется скрытая папка `.git`
- `CRA` работает в формате ESM где используется синтаксис `import/export` вместо `require/module.exports`
- `CRA` формирует базовую структура проекта (архитектура папок)
- `ESLint`, `Babel`, `Webpack` входят в состав `CRA` и имеют стандартные настройки
- в React все события инициируются оболочкой `SyntheticEvent`

- Пример простого счётчика на JSX (есть конфликт типов):

```js
import { useState } from "react";

export default function Count() {

  const [count, setCount] = useState(0)
  const [step, setStep] = useState(1)

  const incCount = () => {
    setCount(prevCount => prevCount + step)
  }

  const decCount = () => {
    setCount(prevCount => prevCount - step)
  }

  const changeStep = (event) => {
    setStep(event.target.value)
  }

  return (
    <>
      <button onClick={incCount}>+</button>
      <span> {count} </span>
      <button onClick={decCount}>-</button>
      <input type="number" value={step} onChange={changeStep} />
    </>
  );
}
```

## TypeScript:
`TypeScript` — язык программирования, представленный Microsoft в 2012 году и позиционируемый как средство разработки веб-приложений, расширяющее возможности `JavaScript`. Основным моментом в TS является статическая типизация, минимизация ошибок `времени исполнения (runtime error)` и работа с ошибками `времени компиляции (compile time errors)`

### Типы данных в JS (8 типов, все они динамические):
- `undefined` (неинициализированные переменные, несуществующие свойства объекта, несуществующие элементам массива и так далее)
- `null` (отсутствие какого-либо объектного значения, ничто)
- `boolean` (логическое true или false)
- `number` (любое число, в том числе с плавающей запятой)
- `string` (строка в одинарных или двойных кавычках)
- `BigInt` (число с добавлением 'n' в конец целочисленного литерала или вызовом функции BigInt())
- `symbol` (примитивный тип данных, использующийся для создания уникальных идентификаторов, являются нововведением JavaScript начиная с ECMAScript 2015)
- `object`(тип данных используются для хранения коллекций различных значений и более сложных сущностей)

## TypeScript: как его использовать в работе?

Для глобальной работы `TypeScript` необходим следующий набор окружения:
- Node.js, окружение выполнения JS (https://nodejs.org/en/)
- TypeScript, сам язык (https://www.npmjs.com/package/typescript)
- `ts-node`, пакет для выполнения `.ts` файлов в окружении Node.js

Для клиентской разработки с помощью CRA в конфигурации с `TypeScript` используем команду:
- `npx create-react-app my-app --template typescript`

## Установка TypeScript:
- локальная: `npm i typescript`
- глобальная (рекомендуется): `npm i -g typescript`

- Проверка версии TS (при глобальной установке):
```
tsc -v
```
- Создание файла конфигурации TS (tsconfig.json):
```
tsc --init
```
- Запуск компиляции TS -> JS:
```
tsc fileName.ts
```

## О tsconfig.json:

- Комментарии к настройкам:
```json
{
  "compilerOptions": {
    "target": "es5", // Укажите целевую версию ECMAScript
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ], // Список файлов библиотеки для включения в компиляцию
    "allowJs": true, // Разрешить компиляцию файлов JavaScript
    "skipLibCheck": true, // Пропустить проверку типов всех файлов объявлений
    "esModuleInterop": true, // Отключает импорт пространства имен (импорт * как fs из "fs") и включает импорт в стиле CJS / AMD / UMD (импорт fs из "fs")
    "allowSyntheticDefaultImports": true, // Разрешить импорт по умолчанию из модулей без экспорта по умолчанию
    "strict": true, // Включить все параметры строгой проверки типов
    "forceConsistentCasingInFileNames": true, // Запрещаем ссылки с несогласованным регистром на один и тот же файл.
    "module": "esnext", // Указываем генерацию кода модуля
    "moduleResolution": "node", // Разрешить модули в стиле Node.js
    "isolatedModules": true, // Безоговорочно генерировать импорт для неразрешенных файлов
    "resolveJsonModule": true, // Включить модули, импортированные с расширением .json
    "noEmit": true, // Не выводить вывод (то есть не компилировать код, а только выполнять проверку типа)
    "jsx": "react", // Поддержка JSX в файлах .tsx
    "sourceMap": true, // Создание соответствующего файла .map
    "declaration": true, // Создаем соответствующий файл .d.ts
    "noUnusedLocals": true, // Сообщать об ошибках на неиспользуемых локальных объектах
    "noUnusedParameters": true, // Сообщаем об ошибках неиспользуемых параметров
    "incremental": true, // Включить инкрементную компиляцию путем чтения/записи информации из предыдущих компиляций в файл на диске
    "noFallthroughCasesInSwitch": true // Сообщать об ошибках для случаев падения в инструкции switch
  },
  "include": [
    "src/**/*" // *** Файлы TypeScript должны ввести проверку ***
  ],
  "exclude": ["node_modules", "build"], //  *** Файлы, которые не нужно вводить, проверять ***

// Важно настроить конфигурацию для `ts-node`, т.к. по-умолчанию запуск файла происходит в формате `CommonJS`, что не подходит под современные реалии `ESM`, для этого в `tsconfig.json` добавляем следующие настройки:
  "ts-node": {
    // Tell ts-node CLI to install the --loader automatically, explained below
    "esm": true
  }
}
```

## Установка ts-node (пакет для выполнения .ts файлов в Node без создания файла компиляции):
 - локальная: `npm i ts-node`
 - глобальная (рекомендуется): `npm i -g ts-node`

Важно настроить конфигурацию для `ts-node`, т.к. по-умолчанию запуск файла происходит в формате `CommonJS`, что не подходит под современные реалии `ESM`, для этого в `tsconfig.json` добавляем следующие настройки:

```json
  "ts-node": {
    // Tell ts-node CLI to install the --loader automatically, explained below
    "esm": true
  }
```

- Выполнение `.ts` файлов в `Node.js` (при компиляции не создаётся .js):
```
ts-node fileName.ts
```

## Примеры использования TypeScript:

- Явная установка типов:
```ts
const a: number = 1; // тип переменной 'a' всегда будет числом
const b: string = 'abc'; // тип переменной 'b' всегдв будет строкой
const c: boolean = true; // тип переменной 'c' всегда будет либо true, либо false
let d: number | null = 1; // тип переменной 'd' всегда будет либо число, либо null
const e: Date = new Date(); // тип переменной 'e' всегда будет объектом с текущей датой и временем
const f: string[] = ['a', 'b']; // типа переменной 'f' всегда будет массивом строк
```

- Неявная установка типов:

```ts
const a = 1; // тип переменной 'a' всегда будет числом
const b = 'abc'; // тип переменной 'b' всегдв будет строкой
const c = true; // тип переменной 'c' всегда будет либо true, либо false
```

- Поведение массивов в TS + использование объединения типов:

```ts
const arr1 = [1, 2, 3] // неявно указан массив чисел
const arr2: number[] = [1, 2, 3] // явно указан массив чисел

arr1.push(4) // ошибки нет, т.к. добавляем значение того же типа
arr1.push('a') // ошибка есть, т.к. `arr1` массив чисел, добавление строки невозможно

const arr3 = [1, 2, '3'] // неявно указан массив чисел или строк
const arr4: (number | string)[] = [1, 2, '3'] // явно указан массив чисел или строк (union)

arr4.push(4) // ошибки нет, т.к. добавляем значение того же типа
arr4.push('5') // тут тоже всё ок
arr4.push({}) // ошибка есть, т.к. `arr4` массив чисел и строк, добавление объекта невозможно

// Union не являются собственно типом данных, но они позволяют комбинировать или объединить другие типы.
```

- Тип `any` и чем он плох в разработке (поведение как JS):
```js
let someVar; // тип не указан ни в каком виде, причина появления типа 'any'

someVar = [1, 2, 3] // массив чисел
someVar = 1 // просто число
```

- Классические функции в TS (установка типов параметров и возвращаемого значения):
```js
// очень важно заменить дефолтный any тип в параметрах, на конкретные типы необходимые функции, так же важно явно указать тип возвращаемого значения
function sum(param1: number, param2: number): number {
  return param1 + param2
}

console.log(sum(4, 5)); // 9
```

- Стрелочные функции в TS (установка типов параметров и возвращаемого значения):

```js
const makeArr1 = (param: number): number[] => [param, param, param]

console.log(makeArr1(6)); // [6,6,6]
```

- Формирование массива с фиксированной длинной с установленными типами (кортеж):

```js
const makeArr2 = (param: number): [number, number, number] => [param, param, param]

console.log(makeArr2(7)); // [7,7,7]
```

## CRA + TypeScript:

Отличия от CRA + JS:
- в составе файлов появляется `react-app-env.d.ts`, этот файл ссылается на объявления типов TypeScript, относящиеся к проектам, начатым с помощью CRA
- все компоненты имеют TSX формат
- появляется файл конфигурации `tsconfig.json`, основная его задача описывать поведение TS и правила компиляции в нужную спецификацию JS
- в React + TS все события инициируются оболочкой `SyntheticEvent` и их нужно явно типизировать

- Пример простого счётчика на TSX:

```js
import React, { useState } from "react";

export default function Count() {

  const [count, setCount] = useState(0)
  const [step, setStep] = useState(1)

  // параметр функции нужно явно типизировать, в данном случае типизация React событием "React.MouseEvent"
  const incCount = (event: React.MouseEvent) => {
    setCount(prevCount => prevCount + step)
  }

  // т.к. функция привязана к событию миши, то снова "React.MouseEvent"
  const decCount = (event: React.MouseEvent) => {
    setCount(prevCount => prevCount - step)
  }

  // явно указываем тип для параметра события, в данном случае типизация React события на изменение в теге input "React.ChangeEvent<HTMLInputElement>"
  const changeStep = (event: React.ChangeEvent<HTMLInputElement>) => {
    // т.к. событие подразумевает получения строки из input, то чтобы воспользоваться функцией setStep, аргумент этой функции нужно привести к числу
    const value: string = event.target.value // явное указание типа `string` для переменной `value`
    setStep(Number(value))
  }

  return (
    <>
      <button onClick={incCount}>+</button>
      <span> {count} </span>
      <button onClick={decCount}>-</button>
      <input type="number" value={step} onChange={changeStep} />
    </>
  );
}
```

- Пример компонента отображающего список:

```js
import { useState, useEffect } from 'react';

function ListItems(): JSX.Element {

  // самописный тип для предотвращения типа never в массиве
  type ListItem = {
    name: string
  }

  const [list, setList] = useState<ListItem[]>([])

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(response => response.json())
      .then(data => setList(data))
  }, [])

  return (
    <ul>
      {list && list.map(el => <li>{el.name}</li>)}
    </ul>
  );
}

export default ListItems;
```

## Рекомендуемые настройки ESLint в CRA (ESLint уже есть в состве CRA):

```js
"eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ],
    "rules": {
      "no-restricted-syntax": 0,
      "no-console": 0,
      "implicit-arrow-linebreak": 0,
      "comma-dangle": 0,
      "function-paren-newline": 0,
      "no-param-reassign": 0,
      "no-nested-ternary": 0,
      "no-confusing-arrow": 0,
      "operator-linebreak": 0,
      "no-await-in-loop": 0,
      "object-curly-newline": 0,
      "jsx-a11y/interactive-supports-focus": 0,
      "jsx-a11y/control-has-associated-label": 0,
      "react/jsx-indent": 0,
      "@typescript-eslint/indent": 0,
      "@typescript-eslint/comma-dangle": 0,
      "jsx-a11y/anchor-is-valid": 0,
      "jsx-a11y/label-has-associated-control": 0,
      "react/jsx-one-expression-per-line": 0,
      "jsx-a11y/click-events-have-key-events": 0,
      "default-case": 0,
      "@typescript-eslint/default-param-last": 0,
      "@typescript-eslint/switch-exhaustiveness-check": 2,
      "@typescript-eslint/explicit-function-return-type": [
        2,
        {
          "allowExpressions": true,
          "allowTypedFunctionExpressions": true,
          "allowHigherOrderFunctions": true,
          "allowDirectConstAssertionInArrowFunctions": true
        }
      ],
      "@typescript-eslint/consistent-type-definitions": [
        "error",
        "type"
      ],
      "consistent-return": 0,
      "@typescript-eslint/no-use-before-define": 0,
      "jsx-a11y/no-static-element-interactions": 0
    },
    "parserOptions": {
      "project": [
        "tsconfig.json"
      ]
    }
  }
```

## Типы синтетических событий (SyntheticEvent) в React:
- AnimationEvent
- ClipboardEvent
- DragEvent
- FormEvent
- MouseEvent
- TouchEvent
- WheelEvent
- ChangeEvent
- CompositionEvent
- FocusEvent
- KeyboardEvent
- PointerEvent
- TransitionEvent

Пример с типизацией события c использованием `generic`'a для HTML элемента:

```js
  // React.ChangeEvent — типизация события
  // <HTMLInputElement> — generic HTML элемента
  const cloneTextHandler = (event: React.ChangeEvent<HTMLInputElement>) => {
    setValue(event.target.value)
  }
```
