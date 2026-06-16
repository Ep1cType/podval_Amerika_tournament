# Инструкция по деплою на GitHub Pages

## Структура файлов в репозитории

```
твой-репозиторий/
├── index.html       ← сайт
└── results.json     ← данные турнира ({}  по умолчанию)
```

---

## Шаг 1 — Создать репозиторий на GitHub

1. Зайди на https://github.com/new
2. Придумай название, например `billiard-tournament`
3. Сделай репозиторий **Public** (иначе GitHub Pages не работает на бесплатном плане)
4. Нажми «Create repository»

---

## Шаг 2 — Загрузить файлы

Самый простой способ — через веб-интерфейс GitHub:

1. В репозитории нажми **Add file → Upload files**
2. Загрузи оба файла: `index.html` и `results.json`
3. Нажми **Commit changes**

---

## Шаг 3 — Включить GitHub Pages

1. Зайди в репозиторий → **Settings → Pages** (левое меню)
2. В разделе **Source** выбери: **Deploy from a branch**
3. Branch: **main**, папка: **/ (root)**
4. Нажми **Save**
5. Через 1–2 минуты сайт появится по адресу:
   `https://ВАШ_ЛОГИН.github.io/billiard-tournament/`

---

## Шаг 4 — Прописать конфиг в index.html

Открой `index.html` и в самом начале скрипта найди блок `CONFIG`:

```javascript
const CONFIG = {
  GITHUB_OWNER: 'ВАШ_ЛОГИН',       // ← твой логин на GitHub
  GITHUB_REPO:  'ВАШ_РЕПОЗИТОРИЙ', // ← название репо (billiard-tournament)
  RESULTS_FILE: 'results.json',
  ADMIN_PASSWORD_HASH: '5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8'
  // ^ это SHA-256 от слова 'password' — замени на свой пароль (см. ниже)
};
```

---

## Шаг 5 — Поставить свой пароль

Текущий пароль по умолчанию: `password` (само собой, надо сменить).

Чтобы получить хэш своего пароля:

1. Открой любую страницу в браузере
2. Открой консоль (F12 → Console)
3. Вставь и выполни:

```javascript
crypto.subtle.digest('SHA-256', new TextEncoder().encode('ТВОЙ_ПАРОЛЬ'))
  .then(b => console.log([...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,'0')).join('')))
```

4. Скопируй получившуюся строку и вставь в `ADMIN_PASSWORD_HASH`

---

## Шаг 6 — Создать GitHub токен (Personal Access Token)

Токен нужен чтобы сайт мог сохранять результаты в репозиторий.

1. Зайди на https://github.com/settings/tokens/new
2. Название: `billiard-tournament`
3. Expiration: выбери нужный срок (или No expiration)
4. Permissions (Fine-grained token):
   - Repository access → Only selected repositories → выбери свой репо
   - Repository permissions → **Contents → Read and write**
5. Нажми **Generate token**
6. **Скопируй токен сразу** — потом он не будет показан

Токен вводится на сайте при входе как администратор и сохраняется в localStorage браузера.

---

## Как это работает

```
Посетители:
  Открывают сайт → данные загружаются из results.json в репо → только просмотр

Ты (администратор):
  Жмёшь «Войти» → вводишь пароль → вводишь GitHub токен (один раз)
  → вводишь результат матча → сайт делает PUT запрос в GitHub API
  → results.json обновляется в репозитории
  → все посетители видят актуальные данные
```

---

## Частые вопросы

**Можно ли сайт сделать приватным?**
На бесплатном GitHub Pages нет. Сайт публичный, данные в репо тоже видны всем. Для бильярдного турнира это обычно нормально.

**Что если токен истёк?**
Создай новый на github.com/settings/tokens, войди на сайте снова.

**Данные пропали после сброса браузера?**
Данные хранятся в репозитории (results.json), а не в браузере. Они никуда не денутся. Токен хранится в localStorage — при очистке браузера нужно будет ввести его снова при входе.

**Можно поделиться с другими?**
Да, просто отправь ссылку вида `https://ВАШ_ЛОГИН.github.io/billiard-tournament/` — они увидят актуальную таблицу только для просмотра.
