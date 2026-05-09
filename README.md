# AnubisWorld — landing site

Приватний Minecraft 1.12.2 Forge сервер AnubisWorld HiTech.

**Live:** https://vitmostovoy-rgb.github.io/minecraft/
**Discord:** https://discord.gg/gQa9fxJuTa
**Telegram:** https://t.me/anubisworldoffc
**YouTube:** https://www.youtube.com/@l_ANUB1S_l

## Що це

Цей репо — лендинг сторінка сервера. Один файл `docs/index.html` рендериться через GitHub Pages з папки `docs/`. Без білд-кроку.

Сторінка вбудовує три Web Component'и (загружаються з GitHub Pages інших репозиторіїв) — це наш єдиний шлях до Supabase і всіх інтерактивних фіч:

| Widget | Кастом-елемент | Що робить |
|---|---|---|
| **Auth** | `<anubis-auth>` | Email + Discord OAuth, обирання Minecraft-ніку, відновлення паролю |
| **Cabinet** | `<anubis-cabinet>` | Завантаження скіна / плаща, профіль, зміна паролю |
| **Payments** | `<anubis-payments>` | Донат-тарифи (VIP/Premium/Ultra), посилання Mono / Donatello / DonatePay з QR |

Cabinet і Payments відкриваються модалками поверх лендинга — не окремими секціями. Тригер — будь-яке посилання `href="#cabinet"` або `href="#donate"`. Hash залишається в URL для deep-link'ів.

Лаунчер тепер — Electron-форк HeliosLauncher: https://github.com/damanoreshkan-beep/anubis-launcher (Win NSIS + Linux AppImage / pacman / tar.gz). Білдиться у GitHub Releases.

## Стек

- HTML + [Tailwind CSS через CDN](https://tailwindcss.com/) — без білд-кроку
- Клієнтський i18n (RU / UA / EN / DE / PL) — словники в `<script>` блоці нижче розмітки
- Web Components завантажуються з GitHub Pages інших проєктів
- Хостинг — GitHub Pages з `docs/`

## Структура

```
docs/
├── index.html         # єдина сторінка з Tailwind-класами + i18n + JS
└── img/               # картинки (logo, hero, decorations)
```

## Адаптивність

| Брейкпоінт | Що в навбарі |
|---|---|
| < 1024 px | Логотип · Lang · Burger. Повний нав + auth pill + Download CTA — у слайд-даун панелі. |
| 1024–1279 px | Логотип · 8 нав-лінків (gap-5, 13 px) · Lang · Login pill. Download CTA сховано — дублюється з `#download`. |
| 1280+ px | Логотип · 8 нав-лінків (gap-6) · Lang · Login pill · Download CTA. |

Auth pill існує у двох інстансах (один у хедері для lg+, другий — у мобільному меню). Обидва ділять один Supabase-клієнт через документ-подію `anubis-need-supabase` — без race на refresh-token.

## Локальна правка

```bash
cd docs
npx --yes http-server -p 8765 --cors -c-1 -s
# відкрий http://127.0.0.1:8765/
```

Редагуй `docs/index.html` → перезавантажуй. `git push` → GitHub Pages автодеплоїть за ~30 сек.

## Оновити посилання на завантаження лаунчера

Шукай `<section id="download">` → заміни `href` на новий релізний URL з https://github.com/damanoreshkan-beep/anubis-launcher/releases.

## Локалізація

Всі рядки інтернаціоналізовані. Кожен елемент з `data-i18n="<key>"` отримує `textContent` зі словника, а елементи з `data-i18n-html="<key>"` — `innerHTML` (для рядків з тегами).

При додаванні нового ключа — додай його в усі п'ять локалей одночасно (`ru`, `ua`, `en`, `de`, `pl`). Брак ключа в локалі → fallback на `en`.

## Інтеграція з Supabase

Сайт сам не пише код Supabase — він лише вбудовує web components, які мають свій клієнт. Конфіг (project URL + publishable key) передається через атрибути:

```html
<anubis-auth
    supabase-url="https://ckfinpywlpllvhvzagnw.supabase.co"
    supabase-key="sb_publishable_..."
    lang="ru"></anubis-auth>
```

Allowed redirect URLs у Supabase Dashboard → Authentication → URL Configuration мають містити локалхост-адреси для розробки і прод-домен.
