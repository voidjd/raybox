<div align="center">

<img src="https://i.ibb.co/fzr9mNRT/Picsart-26-11-02-12-07-58-510.jpg" width="112" alt="RayBox">

# RayBox

**Сетевой клиент для Android на ядре sing-box с брендингом, который настраивается на стороне сервера.**

[![Release](https://img.shields.io/github/v/release/voidjd/raybox?style=for-the-badge&color=39FF14&labelColor=080C14)](https://github.com/voidjd/raybox/releases)
[![Android](https://img.shields.io/badge/Android-8.0%2B-00E5FF?style=for-the-badge&labelColor=080C14&logo=android&logoColor=white)](#)
[![License](https://img.shields.io/badge/License-MIT-39FF14?style=for-the-badge&labelColor=080C14)](LICENSE)

[Возможности](#возможности) · [Как это работает](#как-это-работает) · [Генератор](#генератор-конфигурации) · [Что читает приложение](#что-читает-приложение) · [Hiddify](#настройка-hiddify-manager) · [3x-ui / Marzban](#настройка-3x-ui-и-marzban) · [Сборка](#сборка) · [English](#english)

</div>

---

## О проекте

RayBox — клиент для подключения к **вашим собственным серверам** или серверам, к которым у вас есть легальный доступ. Вставили ссылку подписки, нажали кнопку, подключились. Без мастеров настройки и лишних экранов.

Главная идея: **оформление приложения задаёт сервер, а не сборка**. Владелец сервиса меняет название, логотип, имена и иконки узлов прямо в панели, и клиент подхватывает это при обновлении подписки. Перекомпилировать APK не нужно.

> **Важно.** RayBox — только программа-клиент. Она не предоставляет серверы, подписки и конфигурации, не продаёт и не рекомендует VPN-провайдеров. Вы сами отвечаете за то, к каким серверам подключаетесь, и за соблюдение законов своей страны. Подробности — в [политике конфиденциальности](https://voidjd.github.io/raybox/privacy.html).

## Возможности

| | Возможность | Что внутри |
|---|---|---|
| 🎨 | **Брендинг с сервера** | Название, логотип, имена и иконки узлов приходят из подписки |
| 📊 | **Данные подписки** | Остаток трафика и срок действия из заголовка `subscription-userinfo` |
| 🔗 | **Протоколы** | VLESS, VMess, Trojan, Shadowsocks, ShadowsocksR, Hysteria2, TUIC, AnyTLS, SOCKS5, HTTP |
| 🚚 | **Транспорты** | TCP, WebSocket, gRPC, HTTP, HTTPUpgrade, XHTTP |
| 📥 | **Импорт** | Ссылка подписки, Base64, Clash YAML, sing-box и V2Ray JSON, отдельные ссылки, QR-код |
| 🔄 | **Автообновление** | Вручную или каждые 6 / 12 / 24 часа |
| 🧩 | **Раздельное проксирование** | Режим «только выбранные» или «все, кроме выбранных». Для каждого приложения можно назначить свой узел |
| 🌐 | **DNS** | Отдельные адреса для proxy- и direct-трафика, стратегия `ipv4_only` / `prefer_ipv4` / `ipv4_and_ipv6` |
| 🔁 | **Задержка и автопереключение** | Проверка задержки узлов, сортировка по ней, автоматическая смена узла при сбое или заметном замедлении |
| 🔍 | **Соединения и диагностика** | Список активных соединений с поиском, журнал, версия ядра, статус наборов правил |
| 🙈 | **Маскирование IP в интерфейсе** | Адреса серверов скрываются на экранах и в журнале (`1.2.**.**`). Это только отображение, трафик оно не меняет |
| 🚩 | **Флаги стран** | Определяются по имени узла и домену |
| 🌍 | **Языки** | Русский, English, 简体中文, فارسی, Azərbaycanca, العربية |
| 🎛 | **Прочее** | Светлая, тёмная и системная темы, блокировка рекламы, прямой доступ к локальной сети, подсказки по энергосбережению, проверка обновлений |

## Как это работает

```text
┌─────────────────┐     ┌────────────────────────┐     ┌──────────────────┐
│ 1. Генератор    │ ──▶ │ 2. Панель / сервер     │ ──▶ │ 3. RayBox        │
│ generator.html  │     │ Hiddify, 3x-ui, Marzban│     │ Android-клиент   │
│                 │     │                        │     │                  │
│ имена, иконки,  │     │ добавляет к ответу     │     │ читает заголовки │
│ скрытые узлы    │     │ подписки заголовок     │     │ и перерисовывает │
│ → JSON          │     │ profile-config         │     │ интерфейс        │
└─────────────────┘     └────────────────────────┘     └──────────────────┘
```

1. Вы собираете JSON в генераторе (работает полностью в браузере, ничего никуда не отправляется).
2. Сервер отдаёт этот JSON в заголовке `profile-config` вместе с подпиской.
3. Приложение при обновлении подписки читает заголовки и применяет оформление.

## Генератор конфигурации

Страница [`generator.html`](https://voidjd.github.io/raybox/generator.html) (в репозитории — папка `web/`) превращает список узлов подписки в готовый JSON.

**Как пользоваться**

1. Откройте подписку (в браузере или через `curl`) и скопируйте её текст целиком. Подойдёт список ссылок `vless://…` или Base64-блок.
2. Вставьте текст в поле «Вставьте текст подписки» и нажмите **«Распарсить»**. В таблице появятся все найденные узлы.
3. Для каждого узла настройте:
   - **Вкл** — снимите галочку, чтобы скрыть узел из подписки;
   - **Имя** — новое отображаемое название (по умолчанию оригинальное);
   - **Иконка** — прямая ссылка `https://…` на изображение (PNG, лучше квадратное).
4. Справа появится JSON. Нажмите **«Копировать JSON»**.
5. Вставьте JSON в панель (см. разделы про [Hiddify](#настройка-hiddify-manager) и [3x-ui / Marzban](#настройка-3x-ui-и-marzban)).

**Как узел получает идентификатор**

Каждый узел определяется по хешу **SHA-256 от ссылки без части после `#`** (то есть без названия). Поэтому:

- имя узла на сервере можно менять свободно, привязка иконки и своего названия не сломается;
- если изменить хост, порт, UUID или другие параметры, хеш станет другим, и узел придётся настроить в генераторе заново.

**Что получится на выходе**

```json
{
  "visibility": true,
  "hidden_names": ["Старое имя узла"],
  "nodes": {
    "3f2a9c…(sha256)": {
      "name": "🇩🇪 Frankfurt",
      "icon_url": "https://example.com/de.png"
    }
  }
}
```

| Поле | Назначение | Кто обрабатывает |
|---|---|---|
| `visibility` | `false` отключает выдачу узлов (подписка получает заглушку) | шаблон сервера |
| `hidden_names` | Имена узлов, которые нужно убрать из выдачи | шаблон сервера |
| `nodes` | Свои имена и иконки по хешу узла | **приложение** (через `profile-config`) |

## Что читает приложение

### Тело подписки

Формат определяется автоматически. Поддерживаются:

| Формат | Протоколы |
|---|---|
| Список ссылок или Base64 | `vless://`, `vmess://`, `hysteria2://` (`hy2://`), `tuic://`, `anytls://`, `ss://`, `ssr://`, `trojan://`, `socks5://`, `http(s)://` |
| Clash YAML (`proxies:`) | ss, ssr, vmess, vless, hysteria2, tuic, trojan, socks5, http |
| sing-box JSON (`outbounds`) | vmess, vless, hysteria2, tuic, shadowsocks, anytls, trojan, socks, http |
| V2Ray / Xray JSON | vmess, vless, trojan, shadowsocks, socks, http |

Reality, WebSocket, gRPC, HTTP/2 и HTTPUpgrade читаются из всех форматов. XHTTP распознаётся в ссылках `vless://`, `vmess://` и `trojan://`. Серверные конфигурации (с `inbounds`) приложение отклоняет с понятным сообщением. Узлы, которые нельзя безопасно перенести в sing-box (например, Hysteria2 с obfs), помечаются как неподдерживаемые.

### Данные подписки

Из заголовка `subscription-userinfo` приложение берёт `upload`, `download`, `total` и `expire` и показывает остаток трафика и дней. Поддерживаются безлимитные по трафику и по сроку подписки.

### Оформление и маршрутизация (`profile-config`)

Настройки читаются из одного из двух источников:

1. HTTP-заголовок ответа `profile-config`;
2. строка в теле подписки, начинающаяся с `#profile-config:` (так делает шаблон Hiddify ниже).

Значением может быть обычный JSON или JSON в Base64. Ключи принимаются и в `camelCase`, и в `snake_case`.

```json
{
  "branding": { "title": "My VPN", "avatar": "https://example.com/logo.png" },
  "global_settings": { "hide_ip": true, "show_flags": false },
  "routing": {
    "direct_geosite": [], "direct_geoip": [], "block_geosite": [],
    "direct_domains": [], "block_domains": [], "proxy_domains": []
  },
  "nodes": {
    "<sha256 узла>": { "name": "Имя", "icon_url": "https://example.com/icon.png" }
  }
}
```

| Ключ | Значение |
|---|---|
| `branding.title` | Название сервиса в приложении |
| `branding.avatar` | Логотип сервиса (URL) |
| `global_settings.hide_ip` | Маскировать IP-адреса в интерфейсе |
| `global_settings.show_flags` | Показывать флаги стран рядом с узлами |
| `routing.*_geosite`, `*_geoip` | Категории наборов правил: напрямую / блокировать |
| `routing.direct_domains`, `block_domains`, `proxy_domains` | Списки доменов: напрямую / блокировать / через узел |
| `nodes` | Свои имя и иконка (`icon_url`, `iconUrl` или `icon`) по хешу узла |

Если `profile-config` нет, приложение работает со стандартным оформлением.

### Наборы правил

В приложение встроены наборы правил sing-box (реклама, Apple, Google, Telegram, GitHub и географические списки). Раз в неделю, а также вручную из «Диагностики», они обновляются из репозитория [KaringX/karing-ruleset](https://github.com/KaringX/karing-ruleset). Файлы проверяются на формат и размер, замена атомарная, при ошибке выполняется откат.

## Настройка Hiddify Manager

Hiddify поддерживает шаблоны Jinja2, поэтому он подходит лучше всего.

**1. Параметры домена.** В панели откройте `Настройки → Домены → Дополнительно` и вставьте JSON из генератора.

**2. Шаблон.** Откройте `Редактор прокси → Шаблоны → Client Sublink Base`, включите **«Переопределить»** и **«Включено»** и вставьте шаблон ниже. Замените `brand_title` и `brand_avatar` на свои.

<details>
<summary><b>Шаблон Jinja2</b></summary>

```jinja2
{# {{ links }} #}
{%- if not ctx.for_parent %}
{% include 'sublink/subscription_header' %}

{% set extra = {} %}
{% if ctx.proxy and ctx.proxy.domains and ctx.proxy.domains[0].extra_params %}
    {% set extra = ctx.proxy.domains[0].extra_params %}
{% endif %}

{% set visibility = extra.get('visibility', true) %}
{% set custom_nodes = extra.get('nodes', {}) %}
{% set hidden_names = extra.get('hidden_names', []) %}

{% set brand_title = "My VPN" %}
{% set brand_avatar = "https://example.com/logo.png" %}

#profile-title: {{ brand_title }}
#profile-config: {{ {"branding": {"title": brand_title, "avatar": brand_avatar}, "global_settings": {"hide_ip": true, "show_flags": false}, "routing": {}, "nodes": custom_nodes} | tojson }}
{%- endif %}

{%- if ctx.user.is_active %}
{% if visibility %}
{%- for t in (links | default('')).split('\n') %}
{%- set ns = namespace(skip=false) %}
{%- for h in hidden_names %}
{%- if h and h in t %}
{%- set ns.skip = true %}
{%- endif %}
{%- endfor %}
{%- if t.strip() and not ns.skip %}
{{ t.strip() }}
{%- endif %}
{%- endfor %}
{% else %}
# Subscription Disabled
{% endif %}
{%- endif %}
```
</details>

**3.** Нажмите **«Сохранить»**, затем **«Применить»**. В приложении обновите подписку.

## Настройка 3x-ui и Marzban

В этих панелях нет шаблонизатора, поэтому заголовки добавляются на уровне обратного прокси. Запрос подписки проходит через Nginx, который дописывает `profile-title` и `profile-config`.

<details>
<summary><b>Пример конфигурации Nginx</b></summary>

```nginx
location /sub/ {
    proxy_pass http://127.0.0.1:2096;   # порт подписок вашей панели

    add_header profile-title "My VPN" always;
    add_header profile-config '{"branding":{"title":"My VPN","avatar":"https://example.com/logo.png"},"global_settings":{"hide_ip":true},"nodes":{"<sha256>":{"name":"Frankfurt","icon_url":"https://example.com/de.png"}}}' always;
}
```
</details>

Замечания:

- JSON в `add_header` должен быть **в одну строку** и в одинарных кавычках;
- заголовок `subscription-userinfo` панель обычно отдаёт сама, Nginx его не трогает;
- скрытие узлов (`hidden_names`) в этой схеме недоступно, так как фильтровать тело ответа Nginx сам не умеет. Просто не включайте лишние узлы в панели;
- после правки выполните `nginx -t && systemctl reload nginx`.

## Сборка

| | |
|---|---|
| Язык | Kotlin |
| Ядро | [sing-box](https://github.com/SagerNet/sing-box) через `libbox` |
| UI | Material 3, ViewBinding, RecyclerView |
| Асинхронность | Kotlin Coroutines, Flow |
| Сканер QR | ZXing (journeyapps) |
| Минимальная версия | Android 8.0 |

```bash
git clone https://github.com/voidjd/raybox.git
cd raybox
./gradlew assembleRelease
```

## Участие в проекте

Pull request'ы приветствуются: форк → ветка `feature/…` → коммит → pull request. Перед отправкой убедитесь, что проект собирается.

## Лицензия

[MIT](LICENSE). Разработчик: Gravitek Software.

---

## English

**RayBox** is an Android network client built on the **sing-box** core. Paste a subscription link and connect. Its main feature is **server-side branding**: the service owner sets the app title, logo, node names and node icons from their panel, and the client applies them on the next subscription update. No APK rebuild is needed.

- **Protocols:** VLESS, VMess, Trojan, Shadowsocks(R), Hysteria2, TUIC, AnyTLS, SOCKS5, HTTP
- **Transports:** TCP, WebSocket, gRPC, HTTP, HTTPUpgrade, XHTTP
- **Subscription data:** remaining traffic and days from the `subscription-userinfo` header
- **Branding:** read from the `profile-config` response header (see [format](#что-читает-приложение))
- **Per-app routing, custom DNS, connection viewer, diagnostics, 6 languages**
- **Config generator:** `generator.html` builds the JSON locally in your browser (nodes are identified by the SHA-256 of their URI without the `#name` part)

RayBox is a client only. It does not provide servers, subscriptions or configurations, and does not endorse any provider. You are responsible for the servers you connect to and for complying with the laws that apply to you.
