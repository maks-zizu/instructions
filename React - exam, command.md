1. `npm i` front+back
2. DB create/migrate/seed
3. index.routes.js '/api/users' + users.routes.js (смотрим body/data, delete, post, put)
4. папка `.vscode` -> `settings.json`
   ```json
   {
     "eslint.workingDirectories": ["client", "server"],
     "editor.defaultFormatter": "esbenp.prettier-vscode",
     "prettier.singleQuote": true,
     "editor.formatOnSave": true
   }
   ```
5. `find . -name "node_modules" -exec rm -rf "{}" \;` Скрипт для удаления node_modules рекурсивно

---

# Коротко, какие действия и где:

**ТИПИЗАЦИЯ + REDUX**

      папка /src / feachers
        / users
    1.      / types / User.ts + UserState.ts + UserAction.ts ( exptp )
    2.      / redux / usersReducer.ts ( rxreducer )
    3.  файл /src / store.ts - import usersReducer

---

**ОТРИСОВКА КОМПОНЕНТА**

      / App.tsx
      / users
    4.    / components / UsersList.tsx + UserItem.tsx ( rfce )

---

**УДАЛЕНИЕ 1 ЭЛЕМЕНТА ИЗ КОМПОНЕНТА**

    / users / types / UserAction.ts + action
    / users / UserItem.tsx + button + fn

---

**ИЗМЕНЕНИЕ 1 ПОЛЯ isAdmin**

    / users / types / UserAction.ts + action
    / users / UserItem.tsx + button + fn

---

# Подробно код:

**User.ts (`exptp`)**

```ts
export type User = {
  id: number;
  name: string;
  email: string;
  img: string;
  isAdmin: boolean;
};

export type UserId = User['id'];
```

---

**UserState.ts (`exptp`)**

```ts
export type UsersState = {
  users: User[];
};
```

---

**UserAction.ts (`exptp`)**

```ts
export type UserAction =
  | { type: 'users/load'; payload: User[] }
  | { type: 'users/remove'; payload: UserId }
  | { type: 'users/add'; payload: User }
  | { type: 'users/changeAdminStatus'; payload: UserId };
```

---

**usersReducer.ts (`rxreducer`)**

```ts
const initialState: UsersState = {
  users: [],
};

export default function usersReducer(
  state: UsersState = initialState,
  action: UserAction
): UsersState {
  switch (action.type) {
    case 'users/load':
      return {
        ...state,
        users: action.payload,
      };
    case 'users/remove':
      return {
        ...state,
        users: state.users.filter((user) => user.id !== action.payload),
      };
    case 'users/add':
      return {
        ...state,
        users: [...state.users, action.payload],
      };
    case 'users/changeAdminStatus':
      return {
        ...state,
        users: state.users.map((user) =>
          user.id === action.payload
            ? { ...user, isAdmin: !user.isAdmin }
            : user
        ),
      };
    default:
      return state;
  }
}
```

---

**store.ts**

```ts
import usersReducer // импортировать и вписать в combineReducers

const store = createStore(
combineReducers({ users: usersReducer }),
composeWithDevTools()
);
```

---

**ОТРИСОВКА КОМПОНЕНТА**

1. App.tsx

```tsx
function App(): JSX.Element {
  const dispatch = useDispatch();

  useEffect(() => {
    fetch('/api/users')
      .then((res) => res.json())
      .then((data) => dispatch({ type: 'users/load', payload: data }));
  }, []);

  return (
    <div className="App">
      <UsersList />
    </div>
  );
}
```

---

2. UsersList.tsx (`rfce`)

```tsx
function UsersList(): JSX.Element {
  const { users } = useSelector((store: RootState) => store.users);

  return (
    <div>
      {users.map((user) => (
        <UserItem key={user.id} user={user} />
      ))}
    </div>
  );
}
```

---

3. UserItem.tsx (`rfce`)

```tsx
function UserItem({ user }: { user: User }): JSX.Element {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <img src={user.img} alt="user" />
    </div>
  );
}
```

---

**УДАЛЕНИЕ 1 ЭЛЕМЕНТА ИЗ КОМПОНЕНТА**

1. UserItem.tsx

```tsx
// доб. кнопку с обработчиком onClick
<button type="button" onClick={onHandleRemove}>
  Delete
</button>;

// смотрим бэк - data.message или data.id или еще что
const onHandleRemove = (): Promise<void> => {
  fetch(`/api/users/${value}`, { method: 'DELETE' })
    .then((res) => res.json())
    .then((data) => {
      if (data.message === 'success') {
        // смотря что на бэке - можно не использовать условие
        dispatch({ type: 'users/remove', payload: data.id }); // data.id приходит как строка
      }
    });
};
```

---

**\*ИЗМЕНЕНИЕ 1 ПОЛЯ isAdmin**

1. UserItem.tsx

```tsx
// доб. инпут с обработчиком onChange
<input type="checkbox" checked={user.isAdmin} onChange={onHandleChAdmin} />;

// Если на бэке есть req.body(isAdmin) + res.json(user);
// -> передаем в          body: JSON.stringify({ isAdmin: e.target.checked }),
// ? был и такой вариант  body: JSON.stringify({ user })

const onHandleChAdmin: React.ChangeEventHandler<HTMLInputElement> = (
  e
): Promise<void> => {
  fetch(`/api/users/${user.id}`, {
    method: 'PUT',
    headers: { 'Content-type': 'application/json' },
    body: JSON.stringify({ isAdmin: e.target.checked }),
  })
    .then((res) => res.json())
    .then((data) => {
      dispatch({ type: 'users/changeAdminStatus', payload: data });
    });
};
```
