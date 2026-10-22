# ai-workflow

Окружение для работы ИИ-агента над задачами в нескольких проектах (пространствах). Правила для агента — `AGENTS.md`; этот файл — для человека.

## Быстрый старт

1. Склонируй репозиторий в любое место. Корень клона — `$ROOT`.
2. Создай `env.yaml` по `templates/env.yaml`: временная папка, файл секретов, прокси стендов (или `null`), свои переменные с путями к коду.
3. Создай файл секретов по `templates/secrets.yaml` (путь — `env.yaml` → `secrets_file`).
4. Создай пространство: в чате агенту `hook:new-workspace <ws>`, затем заполни `workspaces/<ws>/ws.yaml` (источники кода и права `write` / `ask` / `read`, настройки git, правила пространства, ключи секретов, стенды).
5. Проверь: `bin/check-config` (нужны Python 3 и PyYAML).
6. Создай задачу: `hook:new-task <ws> <задача>`, заполни `task.md` и `task.yaml` (стадия, ветки, права на коммит и push).
7. Попроси агента выполнить задачу `workspaces/<ws>/tasks/<задача>`.

## Структура

```
AGENTS.md            правила агента
env.yaml             переменные этой машины (не коммитится)
secrets.yaml         секреты (не коммитится; путь настраивается)
rules/               общие правила: схемы YAML, worktree, журнал, MR, стенды, тексты
templates/           шаблоны env.yaml, secrets.yaml, ws.yaml, task.yaml, task.md, hook.md, ws-rule.md
hooks/               хуки (hook:<имя>) и реестр автозапуска TRIGGERS.md
bin/check-config     проверка всех YAML по схемам
registry/            учёт worktree (локальный)
infra/nginx-env/     прокси стендов: nginx + панель + envctl
workspaces/<ws>/
  ws.yaml            настройки пространства
  rules/             правила пространства
  tasks/<задача>/    активные задачи: task.yaml, task.md, journal/, worktree, документы
  _archive/<задача>/ завершённые задачи
```

## Стенды

Если внешних портов мало, стенды раздаёт nginx из `infra/nginx-env` под путями `/<имя>/`. Порты и диапазоны — `infra/nginx-env/conf/settings.json`, внешний адрес — `env.yaml` → `proxy.url`. Подробно — `infra/nginx-env/README.md`.

Можно указать в `env.yaml` → `proxy.dir` папку другой копии, если nginx уже запущен оттуда.