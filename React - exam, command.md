1. npm i front+back
2. DB create/migrate/seed
3. index.routes.js '/api/users' + users.routes.js (delete, post, put)

`find . -name "node_modules" -exec rm -rf "{}" \;` Скрипт для удаления node_modules рекурсивно:
--------------------
__ТИПИЗАЦИЯ + REDUX__
папка /src / feachers
	/ users
		/ types / User.ts + UserState.ts + UserAction.ts ( exptp )
		/ redux / usersReducer.ts  ( rxreducer )
файл /src / store.ts - import usersReducer
--------------------
__ОТРИСОВКА КОМПОНЕНТА__
	/ users / UsersList.tsx + UserItem.tsx ( rfce )
/ App.tsx
-----------------
__УДАЛЕНИЕ 1 ЭЛЕМЕНТА ИЗ КОМПОНЕНТА__
	/ users / types / UserAction.ts   + action
	/ users / UserItem.tsx   + button + fn
-----------------
__ИЗМЕНЕНИЕ 1 ПОЛЯ isAdmin__
	/ users / types / UserAction.ts   + action
	/ users / UserItem.tsx   + button + fn



---------------
User.ts	 (exptp)
export type User = {
    id: number;
    name: string;
    email: string;
    img: string;
    isAdmin: boolean;
  };
 export type UserId = User['id'];
----------------
UserState.ts  (exptp)
export type UsersState = {
  users: User[];
};
---------------
UserAction.ts  (exptp)
export type UserAction =
  | { type: 'users/load'; payload: User[] } отрисовка компонента
  | { type: 'users/remove'; payload: UserId }
  | { type: 'users/add'; payload: User }
  | { type: 'users/changeAdminStatus'; payload: UserId };
---------------
usersReducer.ts  (rxreducer)
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
---------------
store.ts
import usersReducer
// вписать в combineReducers
const store = createStore(
  combineReducers({ users: usersReducer }),
  composeWithDevTools()
);
---------------
__UsersList.tsx (rfce) ОТРИСОВКА КОМПОНЕНТА__
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
---------------
__UserItem.tsx (rfce) ОТРИСОВКА КОМПОНЕНТА__
function UserItem({ user }: { user: User }): JSX.Element {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <img src={user.img} alt="user" />
    </div>
  );
}
---------------
__App.tsx ОТРИСОВКА КОМПОНЕНТА__
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
---------------
UserItem.tsx  УДАЛЕНИЕ 1 ЭЛЕМЕНТА ИЗ КОМПОНЕНТА

 +    <button type="button" onClick={() => onHandleRemove(user.id)}>
        Delete
      </button>

 +  const onHandleRemove = async (value: UserId): Promise<void> => {
      const res = await fetch(`/api/users/${value}`, {
        method: 'DELETE',
      });

      const data = await res.json();
      if (data.message === 'success') {
        dispatch({ type: 'users/remove', payload: value });
      }
    };

---------------
UserItem.tsx  ИЗМЕНЕНИЕ 1 ПОЛЯ isAdmin
 +    <input
        type="checkbox"
        checked={user.isAdmin}
        onChange={onHandleChAdmin}
      />
    </div>

На бэке   res.json(user);	->передаем в	body: JSON.stringify({ user })
message - нет
 +  const onHandleChAdmin = async (): Promise<void> => {
      const res = await fetch(`/api/users/${user.id}`, {
        method: 'PUT',
        headers: { 'Content-type': 'application/json' },
        body: JSON.stringify({ user }),
      });

      const data = await res.json();
      dispatch({ type: 'users/changeAdminStatus', payload: data });
    };