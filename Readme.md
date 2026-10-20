<div align="center">

# 🚀 Pluto Builder

**Сборщик проектов для языка [Pluto](https://pluto-lang.org) — компиляция, LSP, упаковка в `.exe`**

[![Pluto](https://img.shields.io/badge/Pluto-5.4%2F5.5-blue)](https://pluto-lang.org)
[![Platform](https://img.shields.io/badge/platform-Windows-lightgrey)](#)
[![Version](https://img.shields.io/badge/version-v0.6.0-green)](#)
[![License](https://img.shields.io/badge/license-MIT-brightgreen)](#license)

</div>

---

## 📖 О проекте

**Pluto Builder** — это самодостаточный инструмент командной строки, который превращает работу с Pluto из «пишу скрипты» в полноценный цикл разработки:

- 🏗 **Создаёт** структуру проекта одной командой
- 🔨 **Собирает** `.pluto` → `.luac` байт-код
- 👁 **Следит** за изменениями и пересобирает на лету
- 🧠 **Настраивает** VS Code + Pluto Language Server
- 📦 **Упаковывает** в автономный `.exe` (без зависимостей)
- 🐍 **Устанавливает** Python-пакеты автоматически, если их нет

Билдер написан на самом Pluto и может быть скомпилирован **в единый `.exe` без папки `lib/`** — всё встроено через `package.preload`.

---

## ✨ Возможности

| Команда | Что делает |
|---|---|
| `--new <dir>` | Создать проект (структура, шаблоны, `.vscode`, `.gitignore`, `README.md`) |
| `--build` | Скомпилировать `src/*.pluto` → `bin/*.luac` |
| `--run` | Собрать и запустить |
| `--watch` | Автопересборка при изменениях |
| `--clean` | Удалить `bin/` |
| `--list` | Показать файлы к сборке |
| `--check` | Проверить окружение (pluto, plutoc, python, LSP) |
| `--get-lsp` | Скачать LSP + plutoc с GitHub в `tools/lsp/` |
| `--exe` | Упаковать проект в standalone `.exe` |
| `--bundle` | Собрать все `lib/*.pluto` в один `.pluto` |
| `--self-exe` | **Bundle + compile + pack** в один шаг — автономный `.exe` |

**Плюшки:**

- 🎨 Цветной вывод с иконками `✓ · ! ✗`
- 📄 `pluto.json` — конфиг проекта (`src_dir`, `out_dir`, `entry`)
- 🧩 `-np` — старый синтаксис для совместимости
- 🛡 Понятные ошибки вместо 30-строчных стектрейсов `require`
- 🔁 Автоматическая установка `pip` и `lua-to-exe`
- 🌐 Скачивание LSP и `plutoc` через `curl` с fallback на `PowerShell`

---

## 🚀 Установка

### Требования

- **Windows 10/11**
- **[Pluto](https://pluto-lang.org)** в `PATH` — `pluto`, `plutoc`
- **[Python 3.8+](https://www.python.org/downloads/)** — только для `--exe` / `--self-exe`

### Быстрый старт

```bash
# 1. Клонировать
git clone https://github.com/yourname/pluto-builder.git
cd pluto-builder

# 2. Проверить окружение
pluto build.pluto --check

# 3. Создать проект и сразу собрать
pluto build.pluto --new "C:\Games\MyGame" --all --build --get-lsp
```

Готово. Теперь у вас есть:

```
C:\Games\MyGame\
├── bin/                 ← собранный байт-код
├── src/
│   └── main.pluto       ← точка входа
├── lib/                 ← ваши модули
├── tools/
│   ├── build.bat        ← сборка в один клик
│   ├── run.bat          ← собрать и запустить
│   ├── watch.bat        ← автосборка
│   ├── exe.bat          ← собрать .exe
│   ├── get-lsp.bat      ← скачать LSP
│   ├── lsp.bat          ← запустить LSP вручную
│   ├── build.pluto      ← копия билдера
│   ├── lib/             ← модули билдера
│   └── lsp/             ← скачанный LSP + plutoc
├── .vscode/
│   ├── settings.json
│   └── lsp-proxy.json   ← автонастройка LSP
├── .gitignore
├── pluto.json
└── README.md
```

Откройте папку в VS Code — **LSP подхватится автоматически**.

---

## 📚 Использование

### Создание проекта

```bash
# Минимум
pluto build.pluto --new MyGame

# Со всем нужным
pluto build.pluto --new MyGame --all

# Со всем + сразу собрать
pluto build.pluto --new MyGame --all --build

# Со всем + собрать + скачать LSP
pluto build.pluto --new MyGame --all --build --get-lsp
```

### Сборка

```bash
# Собрать проект
pluto build.pluto --build --dir "C:\Games\MyGame"

# Пересобрать всё, даже если .luac уже есть
pluto build.pluto --build --dir . --force

# С подробным выводом plutoc
pluto build.pluto --build --dir . --verbose
```

### Запуск

```bash
pluto build.pluto --run --dir "C:\Games\MyGame"
```

### Автосборка

```bash
pluto build.pluto --watch --dir "C:\Games\MyGame"
```

Следит за `src/*.pluto`, пересобирает при изменении. `Ctrl+C` для остановки.

### Упаковка в `.exe`

```bash
# Обычный проект → .exe
pluto build.pluto --exe --dir "C:\Games\MyGame"

# С конкретным именем
pluto build.pluto --exe --dir . --exe-name MyGame

# С конкретной версией Lua
pluto build.pluto --exe --dir . --lua-version 5.4.6-64
```

Билдер сам:
1. Проверит Python
2. Установит `pip` через `ensurepip` (если нет)
3. Установит `lua-to-exe` через `pip`
4. Соберёт `src/*.pluto` → `bin/*.luac`
5. Упакует в `MyGame.exe`

### Компиляция самого билдера

```bash
# Из папки с build.pluto и lib/
pluto build.pluto --self-exe --exe-name builder
```

Результат: **`builder.exe`** — один файл без `lib/` рядом. Всё встроено.

```bash
# Проверка автономности
builder.exe --version
builder.exe --help
builder.exe --new MyGame --all --build
```

### Работа из папки проекта

После `--new` внутри `tools/` лежат готовые `.bat`:

```bash
cd "C:\Games\MyGame"

tools\get-lsp.bat   :: скачать LSP (один раз)
tools\build.bat     :: собрать
tools\run.bat       :: собрать и запустить
tools\watch.bat     :: автосборка
tools\exe.bat       :: .exe
tools\lsp.bat       :: LSP вручную
```

---

## ⚙️ Конфигурация

### `pluto.json`

```json
{
  "name": "MyGame",
  "version": "0.1.0",
  "entry": "src/main.pluto",
  "src_dir": "src",
  "out_dir": "bin",
  "lib_dir": "lib",
  "target": "pluto"
}
```

Все поля опциональны. Если файла нет — используются дефолты.

### Флаги, переопределяющие конфиг

```bash
--src <path>   # вместо pluto.json → src_dir
--out <path>   # вместо pluto.json → out_dir
--dir <path>   # корень проекта (для --build/--run/--watch/--clean/--exe)
```

### Совместимость с `-np`

Старый синтаксис работает как раньше:

```bash
pluto build.pluto -np "C:\Games\MyGame" 1 1
#                            ^dir        ^vscode ^lsp
```

| Флаг | Значение |
|---|---|
| `1 1` | VSCode + LSP |
| `1 0` | Только VSCode (без LSP-конфига) |
| `0 0` | Голая структура |

---

## 🗂 Структура билдера

```
pluto-builder/
├── build.pluto              ← точка входа
└── lib/
    ├── colors.pluto         ← ANSI-цвета, заголовки
    ├── fs.pluto             ← файловые операции
    ├── args.pluto           ← парсер флагов
    ├── project.pluto        ← чтение pluto.json
    ├── templates.pluto      ← шаблоны генерируемых файлов
    ├── tools.pluto          ← установка tools/ в проект
    ├── help.pluto           ← справка
    ├── commands.pluto       ← new/build/run/clean/watch/list/check/exe/bundle
    ├── exe.pluto            ← упаковка в .exe через lua-to-exe
    ├── lsp.pluto            ← скачивание LSP + plutoc
    └── bundle.pluto         ← склейка модулей в один файл
```

**~1800 строк кода на Pluto.** Каждый модуль отвечает за одну задачу.

---

## 🎯 Пример: игра на Love2D за 5 минут

```bash
# 1. Создать проект
pluto build.pluto --new "C:\Games\TowerDefence" --all --build --get-lsp

# 2. Написать код в src/main.pluto
# 3. Собрать
cd "C:\Games\TowerDefence"
tools\build.bat

# 4. Запустить
tools\run.bat
```

---

## 🐛 Известные ограничения

### Pluto LSP архивирован

Репозиторий [`PlutoLang/pluto-language-server`](https://github.com/PlutoLang/pluto-language-server) **архивирован 2 июня 2025**. Команда `--get-lsp` работает, пока GitHub хранит артефакты в `releases/latest/download/`. Если они исчезнут — замените `LSP_URL` и `PLUTOC_URL` в `lib/lsp.pluto` на зеркало или локальную копию.

### `lua-to-exe` и байт-код

`lua-to-exe` официально принимает только `.lua`-файлы. Билдер **переименовывает** `plutoc.out` → `plutoc.out.lua` перед упаковкой. Обычно `srlua` внутри определяет формат по содержимому, но если ваша версия отказывается — используйте `--lua-version 5.4.6-64` (совпадает с версией Pluto).

### Только Windows

Все команды используют `dir`, `xcopy`, `rmdir`, `forfiles`, `where`, `curl`. Для Linux/macOS нужен отдельный слой в `lib/fs.pluto`. PR приветствуются.

---

## 🧩 Как это работает под капотом

### Bundling

Файл `build_bundled.pluto` собирается так:

```lua
package.preload = package.preload or {}
package.preload["lib.colors"] = function(...)
return [[
-- содержимое lib/colors.pluto
]]
end
package.preload["lib.fs"] = function(...)
-- ...
end

-- ==== build.pluto ====
-- содержимое build.pluto
```

После `plutoc` + `lua-to-exe` получается **один `.exe`**, где все модули уже внутри. `require "lib.colors"` не ищет файл — берёт функцию из `package.preload`.

### Проверка `lib/`

В начале `build.pluto`:

```lua
if not (package.preload and package.preload["lib.colors"]) then
    -- проверяем наличие lib/ на диске
    -- если нет — понятная ошибка и os.exit(1)
end
```

В bundle-режиме проверка пропускается — `package.preload["lib.colors"]` уже есть.

### Автоустановка зависимостей

```lua
1. python --version  →  если нет, ссылка на установку
2. python -m pip --version  →  если нет, python -m ensurepip --upgrade
3. python -c "import lua_to_exe"  →  если нет, pip install lua-to-exe
4. python "_l2e_convert.py" src dst  →  упаковка
```

---

## 🛠 Разработка

```bash
# Клонировать
git clone https://github.com/yourname/pluto-builder.git
cd pluto-builder

# Убедиться, что lib/ на месте
pluto build.pluto --check

# Запустить билдер
pluto build.pluto --help

# Пересобрать .exe после правок
pluto build.pluto --self-exe
```

### Зависимости

Все зависимости — нативные `.pluto`-модули в `lib/`. Ничего скачивать для разработки не нужно. Python требуется только для `--exe` / `--self-exe`.

---

## 🤝 Вклад

Pull requests приветствуются. Для крупных изменений сначала откройте issue.

1. Форкните репозиторий
2. Создайте ветку (`git checkout -b feature/amazing`)
3. Закоммитьте (`git commit -m 'feat: add amazing feature'`)
4. Запушьте (`git push origin feature/amazing`)
5. Откройте PR

**Идеи для PR:**

- [ ] Кроссплатформенность (Linux/macOS)
- [ ] `--target love2d` — шаблон проекта с Love2D
- [ ] Кэш по хэшу содержимого (не пересобирать неизменённые файлы)
- [ ] `--parallel` — параллельная сборка
- [ ] Плагины (пользовательские шаблоны проектов)
- [ ] `--minify` — минификация выходного кода

---

## 📄 Лицензия

MIT — используйте как хотите. См. [LICENSE](LICENSE).

---

## 🔗 Ссылки

- [Pluto Language](https://pluto-lang.org) — официальный сайт
- [Pluto на GitHub](https://github.com/PlutoLang/Pluto) — исходники
- [Pluto Language Server](https://github.com/PlutoLang/pluto-language-server) — LSP (архивирован)
- [lua-to-exe](https://github.com/Water-Run/luaToEXE) — упаковщик
- [srlua](https://github.com/LuaDist/srlua) — glue для Lua-скриптов

---

<div align="center">

**Сделано с ❤ и Pluto.**

Если билдер помог — поставь ⭐ на GitHub!

</div>