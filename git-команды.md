# Git: команды от простого к сложному

## 1. Создание репозитория

```bash
git init                                    # создать новый репозиторий в текущей папке
git clone https://github.com/ЛОГИН/репо.git # скачать существующий репозиторий с GitHub
```

---

## 2. Проверка состояния

```bash
git status              # что изменилось, что готово к коммиту, что игнорируется
git status --ignored    # то же + показать игнорируемые файлы
git diff                # показать незакоммиченные изменения в файлах
git diff --staged       # показать изменения, добавленные в staging (попадут в коммит)
git log --oneline       # история коммитов — кратко (хеш + сообщение)
git log --oneline -10   # последние 10 коммитов
git log --oneline --graph --all  # история всех веток с визуализацией ответвлений
git log --format="%H %s" -3       # полные хеши + сообщения последних 3 коммитов
git show a1b2c3d        # показать содержимое конкретного коммита
git show --stat a1b2c3d # показать какие файлы изменились в коммите
git rev-parse HEAD      # полный хеш последнего коммита
git branch -a           # список всех веток (локальных и удалённых)
git remote -v           # показать подключённые удалённые репозитории
```

---

## 3. .gitignore

Файл `.gitignore` в корне проекта — список файлов и папок, которые git не отслеживает.

**Синтаксис:**

| Правило | Значение |
|---|---|
| `node_modules/` | Игнорировать папку |
| `*.log` | Игнорировать все файлы с расширением .log |
| `/config.local.js` | Игнорировать только в корне проекта |
| `!important.txt` | Исключение — НЕ игнорировать |
| `src/**/debug.js` | Файл на любой глубине внутри src |
| `# текст` | Комментарий |

**Что игнорировать:** `node_modules/`, `dist/`, `build/`, `.env`, `*.log`, `.DS_Store`, `thumbs.db`, `.vscode/`

**Команды:**

```bash
git check-ignore -v file.txt     # проверить, игнорируется ли файл (и каким правилом)
git rm --cached file.txt         # перестать отслеживать файл, но оставить на диске
git rm --cached -r dir/          # то же для папки
```

Если файл уже в коммите — добавление в `.gitignore` не сработает. Нужен `git rm --cached`, затем коммит.

Готовые шаблоны: https://github.com/github/gitignore

---

## 4. Добавление и коммит

```bash
git add file.html            # добавить конкретный файл в staging
git add .                    # добавить все изменения в staging
git commit -m "описание"     # зафиксировать изменения
git commit -m "feat: kanban" # пример: тип + что сделано
```

**Правила коммитов:** после каждого завершённого изменения, не накапливать, описание — что и зачем.

---

## 5. Откат изменений

### Незакоммиченные (изменения в рабочей папке)

```bash
git checkout -- file.html    # вернуть файл к последнему коммиту (изменения пропадут!)
git checkout -- .            # вернуть все файлы к последнему коммиту
git restore file.html        # то же, что checkout -- (новая команда)
```

### Закоммиченные, но НЕ запушенные

```bash
git reset --soft HEAD~1      # удалить последний коммит, изменения в staging
git reset --mixed HEAD~1     # удалить последний коммит, изменения в рабочей папке
git reset --hard HEAD~1      # удалить последний коммит И изменения полностью (опасно!)
git reset --soft HEAD~3      # откатить 3 коммита, изменения в staging
```

### Уже запушенные

```bash
git revert a1b2c3d           # создать новый коммит, отменяющий a1b2c3d (безопасно)
git revert HEAD              # отменить последний коммит
```

### Восстановить удалённый файл

```bash
git checkout a1b2c3d -- file.html   # взять файл из конкретного коммита
```

---

## 6. Ветки

```bash
git branch                        # список локальных веток
git branch -a                     # все ветки (локальные + удалённые)
git branch feature-name           # создать ветку (не переходить)
git checkout -b feature-name      # создать ветку и перейти в неё
git checkout main                  # перейти в ветку main
git switch main                    # то же, что checkout (новая команда)
git branch -D feature-name         # удалить ветку
git branch -M main                 # переименовать текущую ветку в main
```

### Перенос изменений между ветками

```bash
git merge feature-name             # влить feature-name в текущую ветку (merge-коммит)
git rebase main                    # переиграть текущую ветку поверх main (линейная история)
git cherry-pick a1b2c3d            # перенести конкретный коммит в текущую ветку
git cherry-pick a1b2c3d e4f5g6h   # перенести несколько коммитов
git checkout feature-name -- file.html  # взять один файл из другой ветки без слияния
```

`merge` — безопасно, сохраняет историю. `rebase` — чистая история, но **не применять к запушенным веткам!**

### Конфликты при merge/rebase

```bash
git status              # показывает конфликтующие файлы
# реши конфликт в файле вручную
git add file.html       # пометить конфликт как решённый
git commit              # завершить merge
git merge --abort       # отменить начатый merge
git rebase --abort      # отменить начатый rebase
```

---

## 7. Stash (временное хранение)

```bash
git stash                  # спрятать незакоммиченные изменения
git stash pop              # вернуть последние спрятанные изменения и удалить из stash
git stash apply            # вернуть, но оставить в stash
git stash list             # показать список stash-записей
git stash drop             # удалить последнюю stash-запись
git stash drop stash@{2}   # удалить конкретную stash-запись
```

---

## 8. Удалённые репозитории (GitHub)

### Подключение и отключение

```bash
git remote add origin https://github.com/ЛОГИН/репо.git   # привязать удалённый репозиторий
git remote -v                                              # проверить связь
git remote remove origin                                   # отключить удалённый репозиторий
```

### Push (отправить на GitHub)

```bash
git push -u origin main          # первый push — запомнить связь ветки с удалённой
git push                         # последующие push
git push -u origin feature-name  # запушить другую ветку
```

### Pull (получить с GitHub)

```bash
git fetch                        # скачать изменения, не вливать (безопасно — посмотреть сначала)
git pull                         # скачать и влить изменения (fetch + merge)
git pull origin main             # явно указать remote и ветку
git pull origin main --rebase    # скачать + наложить свои коммиты сверху (вместо merge)
```

### Конфликты при push

```bash
git pull origin main --rebase    # подтянуть чужие коммиты + наложить свои сверху
git push                         # повторить push
```

### Авторизация

GitHub требует **Personal Access Token** вместо пароля:
Settings → Developer settings → Personal access tokens → Generate new token

---

## 9. Восстановление потерянного

### Потерянный коммит

```bash
git reflog                         # история всех действий (даже удалённых коммитов)
git reset --hard HEAD@{1}          # вернуться к предыдущему состоянию из reflog
git checkout -b recovery a1b2c3d   # создать ветку из потерянного коммита
```

### Удалённая ветка

```bash
git reflog                                  # найти хеш последнего коммита ветки
git checkout -b branch-name a1b2c3d         # пересоздать ветку из найденного хеша
git branch branch-name HEAD@{1}             # быстро — если удалена недавно
```

`reflog` хранит историю ~90 дней.

---

## 10. Шпаргалка по ситуациям

| Ситуация | Команда | Опасность |
|---|---|---|
| Изменил файл, хочу вернуть | `git checkout -- file` | Средняя |
| Удалил файл случайно | `git checkout hash -- file` | Низкая |
| Отменить последний коммит (локально) | `git reset --soft HEAD~1` | Низкая |
| Удалить коммит и изменения | `git reset --hard HEAD~1` | Высокая |
| Отменить запушенный коммит | `git revert hash` | Низкая |
| Потерял коммит | `git reflog` → `git reset --hard HEAD@{n}` | Средняя |
| Поэкспериментировать | Создать ветку | Низкая |
| Переключиться, изменения не готовы | `git stash` | Низкая |
| Нужен один коммит из другой ветки | `git cherry-pick hash` | Низкая |
| Нужен один файл из другой ветки | `git checkout branch -- file` | Низкая |
