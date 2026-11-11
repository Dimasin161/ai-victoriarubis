# Handoff — последнее состояние

Обновлено: 2026-11-10 (вечер MSK)

## Что сделано в этой сессии
- Правки личности сохранены в историю проекта двумя коммитами на ветке
  `feature/grisha-identity` (от `b225293`):
  - `1d22f70` — Прописал личность Гриши и профиль Димы (CLAUDE.md, core/USER.md)
  - `ed87516` — Добавил сторожа агента (scripts/agent-watchdog.sh)
- Рабочее дерево чистое, ветка локальная.

## Открытый хвост
- Ветка НЕ отправлена на GitHub и PR не открыт.
  Причина (проверено): локально нет git-учётки — `git push` падает с
  `could not read Username for 'https://github.com'`, `gh` не установлен,
  `~/.git-credentials` нет, GITHUB_TOKEN в окружении нет.
  Рабочий путь: GitHub MCP (`push_files` в ветку `feature/grisha-identity`
  репозитория `Dimasin161/ai-victoriarubis`, затем `create_pull_request`).
  Предыдущие PR (#10-#13) делались именно так.
- Дима просил «перегрузись» — сессия перезапущена убийством tmux-сессии
  `agent`, подъём делает служба `agent-watchdog.service` (активна).

## Факты про инфраструктуру (проверено)
- Репозиторий: `Dimasin161/ai-victoriarubis` (приватный, в поиске GitHub не
  виден). Публичные репозитории Димы: `Charp`, `git3_Dimasin`.
  `rubisvika123-png/ai-victoriarubis` — исходный публичный проект-основа.
- Сторож: `agent-watchdog.service` (root), скрипт
  `/root/ai-victoriarubis/scripts/agent-watchdog.sh`, лог `~/watchdog.log`.
  Перезапуск tmux-сессии `agent` через `/root/ai-victoriarubis/start.sh`.

## Проверено 10.11.2026, вечер — отправка на GitHub РЕШЕНА
- Репозиторий `Dimasin161/ai-victoriarubis` сейчас PUBLIC (не private, как
  записано выше), форк от `rubisvika123-png/ai-victoriarubis`, открытых PR нет.
- Секретов в индексе нет: корневой `.mcp.json` с PAT закрыт `.gitignore`;
  трекается только `dashi-plugin-claude-code/plugin/.mcp.json` — без токенов.
- Токен в корневом `.mcp.json` — READ-ONLY. Проверено двумя способами:
  - `git push` → 403 `Permission to Dimasin161/ai-victoriarubis.git denied`
  - `POST /git/blobs` → 403 `Resource not accessible by personal access token`
  (чтение работает: `GET /contents/README.md` → 200)
- Вывод: GitHub MCP `push_files` тоже не сработает — тот же токен.
  Нужно дать токену права Contents: Read and write + Pull requests:
  Read and write (или выдать новый токен). Ждём Диму.
- `core/handoff.md` в git никогда не трекался — остаётся локальным.

## Итог 10.11.2026, вечер
- Дима выдал токену права Contents: Read and write + Pull requests:
  Read and write (режим Only select repositories → ai-victoriarubis).
  Проверка `POST /git/blobs` → 201. Push прошёл.
- Ветка `feature/grisha-identity` отправлена на GitHub.
- Открыт PR #1 «Личность Гриши, профиль Димы и сторож агента»:
  https://github.com/Dimasin161/ai-victoriarubis/pull/1
  (нумерация с 1 — у форка своя, PR #10-13 были в исходном репозитории.)
- Открытый вопрос к Диме: закрывать ли репозиторий от посторонних (сейчас public).
- Push делаю так: токен из корневого `.mcp.json`, URL вида
  https://x-access-token:$TOKEN@github.com/Dimasin161/ai-victoriarubis.git

## Итог вечера 10.11.2026 (финал)
- PR #1 смержен (merge commit `4ff3272`), локальный `main` подтянут
  fast-forward, рабочее дерево чистое.
- Ветка `feature/grisha-identity` осталась и локально, и на GitHub —
  не удаляю без явного "да" Димы.
- Репозиторий решено пока оставить публичным (см. core/decisions.md).

## 11.11.2026 — поставлена резервная копия команды (team-backup)
- Навык НЕ лежал по ссылке, которую дал Дима
  (`Dimasin161/agent.nosik.viktoria-backup` оказался пустым).
  Настоящий источник: `rubisvika123-png/rubis-agent-skills`, папка `team-backup`.
- Хранилище копий: `Dimasin161/agent.nosik.viktoria-backup` (private, проверено).
- Установлено: `~/.agents-backup/{backup.sh,restore.sh}`,
  клон хранилища `~/.agents-backup-repo`, `git config --global
  credential.helper store` + `~/.git-credentials` (chmod 600).
- Тесты навыка (`tests/test.sh`) прошли: ALL TESTS PASSED.
- Первая копия отправлена: 273 файла, 5.1M, агент один — ai-victoriarubis.
  Проверено через API: в хранилище папка `snapshot`, репозиторий private.
- Расписание: crontab `30 3 * * *` с TB_NOTIFY_ENV=
  /root/ai-victoriarubis/secrets/channel.env, лог `~/.agents-backup/backup.log`.
  Проверен прогон в урезанном окружении cron (env -i) — работает.
- Hermes и `~/second-brain` на сервере нет, TB_SHARED не нужен.
- Allowlist Telegram отдельным файлом не хранится (ID лежат в
  secrets/channel.env), поэтому шаг навыка про state/telegram/*.json неприменим.


## 11.11.2026 — создан второй агент: маркетолог «Лиза» (/root/ai-marketing)
- Папка — копия моей (rsync, без state/telegram, handoff.md, decisions.md).
  node_modules, .mcp.json с GitHub-токеном, GROQ-ключ, Stop-хук reply-guard —
  перенеслись. Хук использует $CLAUDE_PROJECT_DIR, правок не потребовал.
- CLAUDE.md — шаблон Димы, адаптирован: «Вика»→«Дима» везде, мужской род,
  его каналы и ниша. Имя агента оставил «Лиза» (Дима оставил плейсхолдер пустым).
- materials/ — 7 заготовок (examples, audience, formats, hooks, product,
  README, reference/marketingskills/README).
- Разводка ресурсов: AGENT_ID=marketing, порт 8100 (у меня 8099), свой
  state/telegram, tmux `agent-marketing`, /etc/systemd/system/agent-marketing.service
  (enabled+active), свой лог ~/watchdog-marketing.log.
- Бот: @marketingnosik_bot, id 9039333262. getMe → ok.
- Баг, найденный при запуске: в новой папке Claude Code показывает окно
  «Is this a project you created or one you trust?» и ждёт нажатия. Сторож
  этого экрана не знал — агент висел молча. Починено в обоих сторожах
  (коммит 6c3841b, ушёл в PR #2).
- Ограничение Telegram: бот не может написать первым. sendMessage → 400
  «chat not found», пока Дима не нажмёт Start у @marketingnosik_bot.
  Сквозная проверка «агент ответил в чате» — только после его первого сообщения.
- Папка ai-marketing содержит .git с тем же origin. Её собственные файлы
  (CLAUDE.md, materials/) в репозиторий не коммитил — они попадают в ночную копию.
- 11.11.2026, 20:57 MSK: Дима нажал Start и написал боту — Лиза ответила.
  Сквозная проверка закрыта, агент работает.
- 11.11.2026, 21:00 MSK: Дима попросил перезагрузиться. Перезапуск = kill
  tmux-сессии `agent`, подъём делает agent-watchdog.service. Открытый хвост
  на момент перезапуска: PR #2 (правило проверки + фикс сторожа) не смержен.
- 11.11.2026, 22:18 MSK: Дима попросил свести присмотр в одного сторожа.
  В AGENTS в scripts/agent-watchdog.sh добавлен [agent-marketing]="/root/ai-marketing",
  agent-watchdog.service перезапущен (коммит 8d69027, ветка fix/pravilo-proverki).
  Проверка глазами: убил tmux-сессию agent-marketing → лог ~/watchdog.log:
  19:18:02 restarted agent-marketing, 19:18:04 confirmed dev-channels dialog,
  19:18:06 startup complete; в панели «bypass permissions on», процесс
  claude ... server:dashi-channel живой.
  Хвосты: ~/watchdog-marketing.log больше не пишется (второго сторожа нет);
  /root/ai-marketing/scripts/agent-watchdog.sh — старая копия, никем не запускается,
  не удалял (красная зона). У комнаты маркетолога есть и agent-marketing.service —
  он тоже поднимает сессию; конфликта нет (кто успел, тот и поднял), но
  дополнительные умения — окна, лимит, зачистка дублей — только у сторожа.
- 11.11.2026, 22:23 MSK: Дима попросил убрать старую копию сторожа из
  /root/ai-marketing/scripts/. Выяснилось: это НЕ лишний файл, а тот же
  файл проекта — ai-marketing — клон того же репозитория, scripts/agent-watchdog.sh
  в нём отслеживается git. Удаление показалось бы в git как удаление файла
  проекта и при любом коммите из этой папки вынесло бы сторожа из репозитория
  (а мой живой сторож запускается именно из репозитория).
  Поэтому сделал безопасный эквивалент: сбросил местные правки
  (git checkout -- scripts/agent-watchdog.sh) — у файла больше нет своего
  списка агентов и своего имени лога, он равен версии проекта.
  Бэкап прежнего содержимого: scratchpad/agent-watchdog-marketing-копия-до-сброса.sh.
  Проверено: agent-watchdog.service active (pid 495128, запущен из
  /root/ai-victoriarubis), обе комнаты в tmux живы, из папки маркетолога
  ничего не запускается.
- 11.11.2026, 22:35 MSK: поднял общую память команды — /root/second-brain
  (README, memory.md, agents.tsv, bin/gbrain, bin/gbrain-session-context,
  bin/require-gbrain-write.py, tests/). Метки: тех-спец, маркетолог.
  Хуки в ~/.claude/settings.json (бэкап старого файла — second-brain/log/).
  В CLAUDE.md у меня и у маркетолога прописан живой раздел общей памяти.
  Проверено вживую: тесты 21+11 зелёные; маркетолог по команде «запомни:»
  записал строку со своей меткой, по «забудь:» — удалил (журнал подтверждает);
  claude -p из обеих папок видит факты и свою метку.
  Себя перезапускаю последним — чтобы подхватить хуки и новый CLAUDE.md.
