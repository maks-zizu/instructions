## Git

- `git checkout dev` -- переключение на dev
- `git checkout -b dev` --- создание новой ветки и переключение на нее
- `git push origin dev` --- отправки изменений (добавление новой ветки) в GitHub репозиторий
- `git checkout somebranch` --- переключение на ветку без создания новой
- `git status` --- просмотр внесенных изменений
- `git add -A` --- добавление содержимого в индекс для последующего коммита
- `git commit -m 'some comment'` --- создание коммита
- `git push origin somebranch` --- отправка изменений в ветке, внесенных в коммит на локальном компьютере, в GitHub репозиторий
- `git pull origin dev` --- скачивание всех изменений из GitHub репозитория к себе в ветку на локальный компьютер
- `git fetch` --> `git reset --hard origin/dev` --> `git clean -fd` --- отменить все локальные изменения *(в т.ч. удалить файлы!)*
- `git revert НОМЕР-КОММИТА` --> `git push origin somebranch` --- отмена коммита и отправка изменений в репозиторий GitHub

`git config pull.rebase false` # объединить
`git config pull.rebase true` # перебазировать
`git config только pull.ff` # только быстрая перемотка вперед
Вы можете заменить «git config» на «git config --global», чтобы установить предпочтение по умолчанию для всех репозиториев.
Вы также можете передать --rebase, --no-rebase,
или --ff-only в командной строке, чтобы переопределить настроенное значение по умолчанию для каждого вызова.

## Git - new project initialization from GitHub repository

- `gitHub.com -> New repository -> Select owner & name -> uploading an existing file` --- создание нового проекта на GitHub
- `git clone https://github.com/***.git` - клонирование репозитория GitHub на локальный компьютер


## Git - new project initialization on local device

- `git init` --- инициализация git при создании нового проект на локальном компьютере
- `git branch -M master` --- переименование ветки в master
- `git branch` --- проверка имеющихся веток
- `git remote add origin https://github.com/***.git` --- соединение ветки с сервером (если не клонировали репозитотрий из GitHub)
- `git push -u origin master` --- отправка изменений на сервер в ветку master
