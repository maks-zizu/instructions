## Array/Object clone

`const arrCopy = […arr]` / `... = arr.slice()` / `... = arr.map(el => el)` поверхностное копирование массива
`const objCopy = {...origObj}` / `... = Object.assign({}, origObj)` поверхностное копирование объекта

`const deepArrCopy = JSON.parse(JSON.stringify(arr))` глубокое копирование массива
_(копирование через JSON с оргнаичениями: не поддерживает объекты с циклическими ссылками, Map, Set, Date, RegExp и ArrayBuffer)_

`const deepCopy = JSON.parse(JSON.stringify(arr))` глубокое копирование массива
_(копирование через JSON с ограничениями: теряются значения c undefined, NaN меняется на null)_

`const deepObjCopy = structuredClone(origObj)` глубокое копирование объекта (современный подход, с Node 17.0)

-
