# 📘 Все базовые команды Git — шпаргалка

---

## ⚙️ Настройка Git (один раз на каждой машине)

```bash
# Имя и email автора коммитов
git config --global user.name "Ваше Имя"
git config --global user.email "your_email@example.com"

# Имя ветки по умолчанию
git config --global init.defaultBranch main

# Стратегия pull (без rebase, через merge)
git config --global pull.rebase false

# Ограничения против лимитов Timeweb (только на хостинге)
git config --global pack.threads 1
git config --global pack.windowMemory 100m
git config --global pack.packSizeLimit 100m
git config --global pack.deltaCacheSize 100m

# Проверить все настройки
git config --list --show-origin
```

---

## 🔑 SSH-ключи (один раз на каждой машине)

```bash
# Проверить, есть ли ключ
ls -la ~/.ssh/

# Создать ключ (если нет). На все вопросы — Enter
ssh-keygen -t ed25519 -C "your_email@example.com"

# Скопировать публичный ключ
cat ~/.ssh/id_ed25519.pub
# На Windows: cat ~/.ssh/id_ed25519.pub | clip

# Добавить ключ в GitHub:
# https://github.com/settings/keys → New SSH key

# Проверить связь
ssh -T git@github.com
# Должно: Hi alexon94! You've successfully authenticated...
```

---

## 📁 Инициализация репозитория (один раз на проекте)

```bash
# Перейти в папку проекта
cd ~/gk-mact.ru/public_html

# Удалить старый .git (если был)
rm -rf .git

# Инициализировать заново
git init
git branch -m main

# Создать .gitignore
nano .gitignore
# (вставить содержимое, Ctrl+X → Y → Enter)

# Создать README.md (если нужен)
nano README.md

# Посмотреть, что Git видит
git status

# Первый коммит
git add .
git commit -m "Initial commit"

# Подключить GitHub
git remote add origin git@github.com:alexon94/mact.git
git remote -v

# Пуш
git push -u origin main
# Если rejected (fetch first):
# git push -u origin main --force
```

---

## 📄 Базовый `.gitignore`

```gitignore
# Исключаем всё в корне public_html
/*

# Возвращаем нужное
!/local_new/
!/README.md
!/.gitignore

# Исключения внутри local_new
local_new/fontawesome/
**/*.log
**/*.bak
local_new/include/cookie-banner/counter.txt
**/.DS_Store
**/Thumbs.db
```

---

## 🌿 Ветки

```bash
# Посмотреть все ветки (локальные + удалённые)
git branch -a

# Посмотреть текущую ветку
git branch

# Создать ветку develop от main
git checkout main
git pull origin main
git checkout -b develop
git push -u origin develop

# Переключиться на ветку
git checkout main
git checkout develop

# Создать ветку под задачу
git checkout develop
git pull origin develop
git checkout -b feature/имя-задачи

# Удалить локальную ветку
git branch -d feature/имя-задачи   # мягко
git branch -D feature/имя-задачи   # жёстко

# Удалить ветку на GitHub
git push origin --delete feature/имя-задачи
```

---

## 🔄 Ежедневный workflow (на ПК)

```bash
# Перейти в проект
cd ~/Desktop/mact-git/mact

# Забрать свежее из своей ветки
git checkout develop
git pull origin develop

# Внести правки
# ...

# Посмотреть, что изменилось
git status
git diff

# Добавить в индекс
git add .

# Проверить, что попало
git status

# Создать коммит
git commit -m "fix: краткое описание"

# Отправить на GitHub
git push origin develop
```

---

## 🚀 Merge develop → main (релиз)

```bash
# Забрать свежий develop
git checkout develop
git pull origin develop

# Переключиться на main
git checkout main
git pull origin main

# Слить develop в main
git merge develop -m "Release: описание изменений"

# Отправить в GitHub
git push origin main

# Синхронизировать develop с main (чтобы не расходились)
git checkout develop
git merge main -m "Sync develop with main"
git push origin develop
```

---

## 🖥 Деплой на хостинг

```bash
# Зайти по SSH
ssh ct68626@bitrix420.timeweb.ru

# Перейти в корень сайта
cd ~/gk-mact.ru/public_html

# Проверить, на какой ветке
git branch
# Должно быть: * main

# Вариант 1: мягкий pull
git pull origin main

# Вариант 2: жёсткая синхронизация (если pull падает)
git fetch origin
git reset --hard origin/main

# Очистить кеш Битрикса через админку
```

---

## 🛠 Полезные команды

```bash
# Состояние
git status                    # что изменено
git log --oneline -10         # последние 10 коммитов
git diff                      # что именно изменилось
git remote -v                 # какие remote подключены
git branch -a                 # все ветки

# Проверка .gitignore
cat .gitignore                          # содержимое
git status --ignored                    # что игнорируется
git check-ignore -v путь/к/файлу        # какое правило ловит

# Отмена действий
git rm --cached файл            # убрать из индекса (на диске остаётся)
git reset --soft HEAD~1         # отменить последний коммит (файлы остаются)
git reset HEAD~1                # отменить коммит + убрать из индекса
git checkout -- файл            # вернуть файл к последнему коммиту

# Временное сохранение правок
git stash                       # спрятать правки
git stash pop                   # вернуть правки
git stash list                  # список stash'ей

# Клонирование
git clone git@github.com:alexon94/mact.git
```

---

## 🚨 Решение проблем

```bash
# Пуш падает с "unable to create thread"
git config --global pack.threads 1
git config --global core.compression 0
git push origin main
git config --global core.compression -1

# Pull падает с "divergent branches"
git config --global pull.rebase false
git pull origin main

# Pull падает с "local changes would be overwritten"
git stash
git pull origin main
git stash pop
# Или жёстко:
git fetch origin
git reset --hard origin/main

# SSH "Permission denied (publickey)"
ssh -T git@github.com
# Если не работает — проверить ключ в GitHub

# Файлы уже в коммите, но не должны быть
nano .gitignore                 # добавить правило
git rm -r --cached путь/        # убрать из индекса
git commit -m "Remove from tracking"
git push origin main
```

---

## 🌐 Работа двух разработчиков

```bash
# Начать задачу (от develop)
git checkout develop
git pull origin develop
git checkout -b feature/имя-задачи

# Работа, коммиты
git add .
git commit -m "..."
git push -u origin feature/имя-задачи

# На GitHub: Pull Request feature/имя-задачи → develop
# После merge на GitHub:

# Обновить свой develop
git checkout develop
git pull origin develop

# Удалить локальную ветку
git branch -d feature/имя-задачи

# Подтянуть develop в свою ветку (если работаете параллельно)
git checkout feature/своя-задача
git fetch origin
git merge origin/develop
# Разрешить конфликты, если есть
git push origin feature/своя-задача
```

---

## 📋 Схема веток

```
main       ← стабильный код, деплой на прод (gk-mact.ru)
  ▲
  │ merge (при релизе)
  │
develop    ← рабочая ветка
  ▲
  │ merge (через Pull Request)
  │
feature/*  ← личная ветка под задачу
fix/*      ← личная ветка под багфикс
```

---

## 📋 Шпаргалка

| Что делаете | Команда |
|---|---|
| Забрать свежее | `git pull origin develop` |
| Статус | `git status` |
| История | `git log --oneline -10` |
| Добавить | `git add .` |
| Коммит | `git commit -m "..."` |
| На GitHub | `git push origin develop` |
| Создать ветку | `git checkout -b feature/xxx` |
| Merge в main | `git checkout main && git merge develop` |
| Обновить хостинг | `cd ~/gk-mact.ru/public_html && git pull origin main` |
| Жёсткая синхронизация | `git fetch origin && git reset --hard origin/main` |

---

## ⚠️ Главные правила

1. **Работаете в `develop`**, в `main` — только через merge.
2. **Правки — только локально**, не через SFTP.
3. **Перед работой** — `git pull origin develop`.
4. **На хостинге** — только `git pull origin main`, никаких `checkout develop`.
5. **После `git pull` на хостинге** — очищайте кеш Битрикса.
6. **При создании нового репо на GitHub** не ставьте галочки «Add README», «Add .gitignore», «Choose a license».
7. **Runtime-файлы** (`counter.txt`, `cookie_choices.log`, кеш) — в `.gitignore`.