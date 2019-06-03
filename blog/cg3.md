### Точки входа cgit

От неаутентифицированных пользователей (для публичных репозиториев)
- Env-переменные (в них HTTP-запрос, прокинутый от веб-сервера к CGI-скрипту cgit)

От аутентифицированных пользователей (для публичных хостингов - практически то же, что неаутентифицированные).
1. Через парсинг вывода библиотеки git
- Пользователи, коммиты, тэги

2. Через filter API (внешние программы-обработчики или скрипты на lua, интерпретируемые встроенным в cgit интерпертатором):
- README-файлы в репозитории (различные форматы - маркдаун, реструктуред и др. -  внешний скрипт)
- Тексты Commit message
- email-адреса (авторов, коммитеров) - например, lua-скрипт с поддержкой внешних аватар
- Имена владельцев репозиториев
- Тексты программ в репозитории (подстветка кода)
- Команды для контроля доступа к репозиториям

3. Через mime types:
- Блобы в репозитории (картинки, html, ...)

Системные настройки / файловая система
- Файлы Git-репозитория (в .git, парсятся функциями из библиотеки git'а)
- Параметры командной строки
- config-файл
- Кэш-файлы

### Что имеет смысл фаззить

- HTTP-запросы
- Мета-данные git-репозиториев (имена пользователей, емейлы, ветки, названия коммитов, тэгов, поля времени)

Мало смысла:
- Содержимое git-репозиториев: обрабатывается внешними скриптами
- Топология коммитов в git-репозиториях: обрабатывается библиотекой git
- Системные настройки, конфиги: как правило у атакующего нет доступа на запись к ним


### Найденные странности

1. cgit.c, 1058: описание virtual-root не соответствует документации. По умолчанию хоть и none, но это не то же самое что "" (пусто)

/* If virtual-root isn't specified in cgitrc, lets pretend
	 * that virtual-root equals SCRIPT_NAME, minus any possibly
	 * trailing slashes.
	 */
	if (!ctx.cfg.virtual_root && ctx.cfg.script_name)
		ctx.cfg.virtual_root = ensure_end(ctx.cfg.script_name, '/');

2. Если парсинг querystring не нашёл url, то используется url из PATH_INFO. И cgit_parse_url вызывается ещё раз..


### Подход

Добавляем чтение env-параметров из stdin
Генерируем корпус валидных входных запросов, добавив вывод в файл и поигравшись с интерфейсом.
Разбиваем лог на отдельные файлы (split -l 1 envlog)
Запускаем компиляцию с афл:
make clean
cd ./git && make clean && cd ..
CC=afl-gcc
Модифицировать Makefile в git: CC = afl-gcc
make
sudo make install




                      american fuzzy lop 2.52b (cgit.cgi)

┌─ process timing ─────────────────────────────────────┬─ overall results ─────┐
│        run time : 2 days, 0 hrs, 14 min, 30 sec      │  cycles done : 21     │
│   last new path : 0 days, 2 hrs, 15 min, 42 sec      │  total paths : 1359   │
│ last uniq crash : none seen yet                      │ uniq crashes : 0      │
│  last uniq hang : 1 days, 15 hrs, 49 min, 41 sec     │   uniq hangs : 1      │
├─ cycle progress ────────────────────┬─ map coverage ─┴───────────────────────┤
│  now processing : 1062* (78.15%)    │    map density : 6.31% / 12.35%        │
│ paths timed out : 0 (0.00%)         │ count coverage : 2.33 bits/tuple       │
├─ stage progress ────────────────────┼─ findings in depth ────────────────────┤
│  now trying : interest 32/8         │ favored paths : 175 (12.88%)           │
│ stage execs : 22.5k/81.8k (27.47%)  │  new edges on : 269 (19.79%)           │
│ total execs : 27.5M                 │ total crashes : 0 (0 unique)           │
│  exec speed : 57.23/sec (slow!)     │  total tmouts : 40.5k (239 unique)     │
├─ fuzzing strategy yields ───────────┴───────────────┬─ path geometry ────────┤
│   bit flips : 191/1.16M, 63/1.16M, 28/1.16M         │    levels : 18         │
│  byte flips : 0/144k, 4/111k, 4/109k                │   pending : 174        │
│ arithmetics : 300/6.22M, 0/491k, 0/19.8k            │  pend fav : 0          │
│  known ints : 60/638k, 17/3.09M, 11/4.74M           │ own finds : 1344       │
│  dictionary : 0/0, 0/0, 207/2.71M                   │  imported : n/a        │
│       havoc : 459/5.70M, 0/0                        │ stability : 100.00%    │
│        trim : 34.89%/57.8k, 22.32%                  ├────────────────────────┘
└─────────────────────────────────────────────────────┘          [cpu000:177%]
[-] PROGRAM ABORT : Fork server is misbehaving (OOM?)
         Location : run_target(), afl-fuzz.c:2381
