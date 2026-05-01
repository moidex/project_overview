# Клиентские приложения Moidex

## Общее описание

Клиентская часть Moidex объединяет все пользовательские интерфейсы продукта:
CRM для управления автомойками, мобильное приложение для работы с телефона,
Moidex Lite для персонала на посту, онлайн-запись для клиентов, лендинги и
клиентскую документацию.

Вместе эти приложения закрывают полный путь пользователя: от первого знакомства
с продуктом и подключения автомойки до ежедневной работы с заказами, расписанием,
клиентами, тарифами, уведомлениями и поддержкой.

---

## Технологический стек

| Категория | Технологии | Назначение |
|-----------|------------|------------|
| **Web-приложения** | React, TypeScript, Vite, Next.js | CRM Web, Moidex Lite, лендинги, Booking App |
| **Mobile** | Expo, React Native, Expo Router | CRM Mobile и мобильные сценарии |
| **Документация** | Docusaurus, MD/MDX | Клиентский портал инструкций и FAQ |
| **State management** | TanStack Query, Zustand, AsyncStorage | Серверное состояние, локальные сессии, настройки |
| **UI** | Tailwind CSS, NativeWind, shadcn, Ant Design, custom ui-kit | Интерфейсы CRM, лендингов и мобильных приложений |
| **Формы и валидация** | React Hook Form, Zod, phone input utilities | Заказы, профили, заявки, настройки |
| **Аналитика** | Яндекс Метрика | События, цели, page views, маркетинговая аналитика |
| **PWA / Push** | Service Worker, Expo Notifications, Web Push | PWA CRM Web, push-уведомления CRM Mobile |
| **Тестирование** | Vitest, Jest, Testing Library, MSW, happy-dom | Unit/integration проверки в CRM Web, CRM Mobile и Booking App |
| **Деплой** | GitHub Actions, Яндекс Object Storage | Сборка и публикация клиентских приложений |

---

## Инфраструктура деплоя

Основные клиентские приложения публикуются как статические SPA/PWA или мобильные
сборки через GitHub Actions. Веб-артефакты размещаются в Яндекс Object Storage,
а мобильные Android-сборки публикуются как APK/AAB и сопровождаются публичной
update policy.

Для web-приложений и документации build загружается в bucket Яндекс Object Storage
и затем раздается пользователям через CDN Яндекс Cloud.

```text
GitHub (push) -> GitHub Actions -> Build -> Яндекс Object Storage / APK-AAB artifacts
```

Для статических приложений используется раздельное кэширование: HTML-файлы
публикуются без долгого кэша, а ассеты загружаются с immutable cache. Для тестовых
и production-окружений используются отдельные домены, bucket'ы и backend base URL.

---

## Клиентские приложения

### 1. CRM Web

**Проект:** CRM Web  
**Тип:** Web / PWA  
**Статус:** Production  
**Аудитория:** владельцы автомоек, управляющие, администраторы, персонал  
**Основной стек:** React, TypeScript, Vite, TanStack Query, Zustand, Tailwind CSS,
Ant Design, custom `@shared/ui-kit`, Vitest

CRM Web — основное клиентское приложение платформы Moidex для управления автомойками
и сетями автомоек. Приложение закрывает операционный контур станции: расписание,
заказы, клиенты, тарифы, услуги, сотрудники, зарплаты, настройки, интеграции,
биллинг и маркетинговые инструменты.

**Основной функционал:**

- Авторизация через Яндекс OAuth, логин/пароль, Telegram WebApp и MAX mini-app flows
- Выбор и создание станции, onboarding wizard для первичной настройки
- Календарь заказов, расписание станции, мультистанционный режим
- Создание, редактирование, отмена и завершение заказов
- История заказов, фильтры, экспорт и работа с клиентской базой
- Управление тарифами, допуслугами, категориями автомобилей и ценами
- Настройки станции: график, боксы, администраторы, юрданные, уведомления
- Интеграции с Яндексом, Moidex Lite, Moidex Booking и корпоративными договорами
- Биллинг, подписки и paywall-функции
- PWA, service worker, push-уведомления и notification routing

**Архитектура:** feature-sliced структура с разделами `app`, `pages`, `features`,
`entities`, `widgets`, `shared`. Доступ контролируется через route guards,
station/network guards, permissions, positions и subscription entitlements.

**Интеграции:** CRM backend API, Яндекс OAuth, Яндекс.Карты/геокодер,
Яндекс Метрика, push API, `/logger`, `/error`.

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Качество:** Vitest, Testing Library, MSW, ESLint, Prettier, coverage thresholds.
Есть собственный ui-kit и дизайн-токены, при этом сохраняется активная миграция
с Ant Design.

---

### 2. CRM Mobile

**Проект:** CRM Mobile  
**Тип:** Mobile / Android / Expo  
**Статус:** Production  
**Аудитория:** владельцы, администраторы и сотрудники автомоек  
**Основной стек:** Expo SDK, React Native, Expo Router, TypeScript, React Query,
Zustand, NativeWind, Expo Notifications, Jest

CRM Mobile — мобильное приложение для оперативного управления станцией с телефона.
Приложение закрывает сценарии просмотра показателей, работы с заказами, расписанием,
слотами, тарифами, профилем и push-уведомлениями.

**Основной функционал:**

- Авторизация через Яндекс ID с callback `moidexcrm://auth/callback`
- Служебный вход по логину/паролю для проверки и demo-сценариев
- Главный экран станции: показатели за день, активные заказы, выручка, занятость
- Просмотр, создание, обработка и отмена заказов
- Deep link `/order/:orderId/:action` для быстрых действий по заказу
- Расписание, блокировка и разблокировка слотов
- Управление тарифами и допуслугами
- Профиль пользователя, настройки, developer/debug menu для admin
- Push-уведомления через Expo Notifications
- Android update policy с soft/hard режимами обновления

**Архитектура:** Expo Router маршруты в `app/`, feature-sliced структура в `src/`,
общий SSOT-слой `shared` для web/mobile контрактов, типов и общей
бизнес-логики.

**Интеграции:** backend CRM API, Яндекс OAuth, Expo push token, update policy JSON,
Яндекс Object Storage для APK/update-policy.

**Сборка и релиз:** GitHub Actions собирает Android-артефакты APK и AAB.
APK используется для прямой установки, AAB — для публикации в RuStore.

---

### 3. Moidex Lite

**Проект:** Moidex Lite  
**Тип:** Web-приложение для персонала станции  
**Статус:** Production  
**Аудитория:** сотрудники и администраторы автомоек  
**Основной стек:** React, TypeScript, Vite, TanStack Query, Axios, Tailwind CSS

Moidex Lite — облегченный операционный интерфейс для персонала автомойки. Он
ориентирован на быстрый мобильный доступ к расписанию станции и обработку заказов
без перегруженного CRM-интерфейса.

**Основной функционал:**

- Просмотр расписания станции на сегодня, завтра и послезавтра
- Отображение свободных, занятых и заблокированных слотов
- Автоматическое обновление расписания
- Просмотр деталей заказа
- Смена статусов заказа: принят, забронирован, в работе, завершен, отменен
- Создание заказа в свободный слот
- Выбор автомобиля из справочника марок и моделей
- Визуальное отображение источника заказа, включая Яндекс
- Light/dark theme и mobile-oriented drawer UI

**Интеграции:** backend API через `VITE_BASE_API_URL`, endpoints станции, заказов,
статусов и справочника автомобилей; error reporting через `/error`; Яндекс Метрика.

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Особенности:** клиентская авторизация в приложении не реализована; доступ строится
вокруг ссылки со `stationId`. Автоматических тестов в проекте не обнаружено.

---

### 4. Moidex Booking App

**Проект:** Moidex Booking App  
**Тип:** Web booking / статическое Next.js-приложение  
**Статус:** Production  
**Аудитория:** конечные клиенты автомоек  
**Основной стек:** Next.js App Router, React, TypeScript, Tailwind CSS, Zustand,
TanStack Query, Axios, Vitest

Moidex Booking App — клиентский интерфейс онлайн-записи на автомойку. Приложение
позволяет автовладельцу найти мойку, выбрать автомобиль, тариф, допуслуги, слот,
оставить контактные данные и создать заказ без звонка.

**Основной функционал:**

- Marketplace-страница со списком активных станций
- Публичные SEO-страницы автомоек `/wash/[id]/`
- Booking flow: автомобиль, тариф, слот, допуслуги, контакты, госномер
- Создание заказа через backend API
- Страница успешной записи с summary, share и ICS-файлом календаря
- Просмотр, отмена и редактирование заказа

**Интеграции:** backend Moidex Booking API, frontend config из Яндекс Object Storage,
Яндекс Метрика, error reporting через `/error`.

**SEO:** static export, sitemap, robots, canonical URLs, JSON-LD `WebSite`
и `FAQPage`.

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Особенности:** приложение ориентировано на быстрый публичный сценарий записи:
клиент выбирает станцию, услугу, время и оставляет контактные данные для создания
заказа.

---

### 5. Русский лендинг Moidex

**Проект:** Русский лендинг Moidex  
**Тип:** Marketing website / SPA с prerender  
**Статус:** Production  
**Аудитория:** владельцы автомоек, детейлингов, сетей, B2B-партнеры  
**Основной стек:** React, TypeScript, Vite, Tailwind CSS, shadcn, Playwright prerender

Русский лендинг Moidex представляет CRM, онлайн-запись и интеграцию с Яндекс
Заправками для B2B-аудитории автомоечного бизнеса.

**Основные страницы и сценарии:**

- Главная CRM-страница с УТП, возможностями, тарифами, интеграциями и FAQ
- `/booking` — оффер онлайн-записи без комиссии
- `/integrations/yandex-fuel` — отдельная страница подключения к Яндекс Заправкам
- `/legal/oferta` — публичная оферта
- Глобальная форма заявки с отправкой на backend `/email/send`

**SEO и аналитика:** robots, sitemap, Яндекс verification, hreflang, canonical,
OG/Twitter, JSON-LD, Яндекс Метрика, CTA/FAQ/tab events.

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Особенности:** лендинг объединяет продуктовые офферы CRM, онлайн-записи и
интеграции с Яндекс Заправками в единую B2B-точку входа.

---

### 6. Международный лендинг Moidex

**Проект:** Международный лендинг Moidex  
**Тип:** International marketing website  
**Статус:** Production  
**Аудитория:** владельцы и операторы автомоек на международных рынках  
**Основной стек:** React, TypeScript, Vite, Tailwind CSS, shadcn

Международный лендинг продвигает подключение автомоек к Moidex CRM / Яндекс Заправки:
онлайн-запись, управление заказами, клиентами, тарифами, загрузкой, оплатой и
отчетностью.

**Основной функционал:**

- Multi-entry build для `/`, `/en`, `/kz`, `/uz`
- Локализация RU, EN, KZ/KK, UZ
- Описание форматов автомоек: классическая, робот-мойка, самообслуживание
- Интерактивный сценарий How it works
- CRM feature grid, FAQ, финальный CTA
- Лид-форма с источником по локали

**Интеграции:** отправка заявок на backend `/email/send`, ассеты из Яндекс Cloud
Storage.

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Особенности:** международная версия поддерживает отдельные языковые entrypoints
и локализованные заявки для разных рынков.

---

### 7. Docusaurus / клиентская документация

**Проект:** Docusaurus  
**Тип:** Documentation portal  
**Статус:** Production  
**Аудитория:** клиенты Moidex: владельцы, управляющие, администраторы и сотрудники  
**Основной стек:** Docusaurus, React, TypeScript, MD/MDX, Docker/nginx

Docusaurus-портал — клиентская документация Moidex. Он объясняет, как пользоваться
CRM, Moidex Lite, Moidex Booking, тарифами, заказами, сотрудниками, настройками
станции, интеграциями и FAQ.

**Основной функционал:**

- Пошаговый quick start для клиентов
- Инструкции по CRM-интерфейсу, заказам, слотам и тарифам
- Разделы по сотрудникам, ролям и графикам смен
- Интеграции: Яндекс Заправки, Moidex Booking, Moidex Lite, корпоративные договоры
- FAQ и troubleshooting
- Генерация `all-docs.txt` и `all-docs.json` для AI-support сценариев

**Деплой:** GitHub Actions публикует build в bucket Яндекс Object Storage,
раздача идет через CDN Яндекс Cloud.

**Особенности:** документация зависит от внешних скриншотов и ассетов в Яндекс Cloud
Storage. Отдельного unit/e2e тестирования нет; качество проверяется сборкой
Docusaurus и broken links policy.

---

## Сводная таблица клиентских приложений

| # | Приложение | Тип | Аудитория |
|---|------------|-----|-----------|
| 1 | CRM Web | Web / PWA | Владельцы, администраторы, персонал |
| 2 | CRM Mobile | Mobile / Android | Владельцы, администраторы, сотрудники |
| 3 | Moidex Lite | Lightweight web client | Персонал автомойки |
| 4 | Moidex Booking App | Public booking web app | Конечные клиенты |
| 5 | Русский лендинг | Marketing website | B2B-клиенты и партнеры |
| 6 | Международный лендинг | International marketing website | B2B-клиенты международных рынков |
| 7 | Docusaurus | Documentation portal | Клиенты Moidex |

---

## Общие frontend-сервисы и shared-слой

Ключевой общий слой `shared` используется как SSOT
для `CRM Web` и `CRM Mobile`.

**Общие зоны ответственности:**

- доменные типы и API-контракты;
- общая бизнес-логика без UI;
- правила навигации и presentation helpers;
- order-history logic;
- tariffs logic;
- weather/mobile-update shared logic.

Правило архитектуры: `shared` не зависит от `web` или `crm-mobile`, а web/mobile
могут зависеть от `shared`. Это снижает риск расхождения контрактов между
платформами и упрощает развитие кросс-платформенных сценариев.

---

## Интеграция с backend-платформой

Клиентские приложения работают с backend-платформой Moidex через HTTP API.
Основные зоны интеграции:

- авторизация и профиль пользователя;
- станции, заказы, расписание и слоты;
- тарифы, услуги и категории автомобилей;
- создание и редактирование заказов;
- push-уведомления и update policy;
- логирование клиентских ошибок;
- отправка заявок с лендингов;
- получение документации и ассетов из Яндекс Cloud Storage.

---

## Авторизация, доступы и пользовательские данные

В клиентской экосистеме используются разные модели доступа в зависимости от
назначения приложения:

| Приложение | Модель доступа |
|------------|----------------|
| CRM Web | Яндекс OAuth, login/password, Telegram/MAX flows, RBAC/ABAC, station/network guards |
| CRM Mobile | Яндекс OAuth, служебный login/password, локальная session state |
| Moidex Lite | Доступ по ссылке со `stationId`, без отдельного login flow в клиенте |
| Booking App | Публичный booking flow для создания и управления записью |
| Лендинги | Публичные страницы и форма заявки |
| Docusaurus | Публичная клиентская документация |

---

## Аналитика, SEO и лидогенерация

Клиентская экосистема использует SEO и аналитику как часть продуктового контура:

- Яндекс Метрика в CRM Web, Moidex Lite, Booking App и русском лендинге;
- SPA page views и цели для CTA, FAQ, tabs, order creation;
- SEO-страницы Booking App для публичных страниц автомоек;
- robots, sitemap, canonical, hreflang и JSON-LD в маркетинговых приложениях;
- lead forms на лендингах с отправкой заявки на backend `/email/send`;
- Docusaurus sitemap и публичный клиентский self-service портал.

---

## Тестирование и контроль качества

| Приложение | Проверки |
|------------|----------|
| CRM Web | Vitest, React Testing Library, ESLint |
| CRM Mobile | Jest, Testing Library React Native, ESLint |
| Booking App | Vitest, React Testing Library, ESLint |
| Moidex Lite | ESLint |
| Лендинги | ESLint |
| Docusaurus | TypeScript |

Уровень тестового покрытия различается между проектами. Наиболее зрелые автоматические
проверки есть в CRM Web, CRM Mobile и Booking App. В Moidex Lite, лендингах и
Docusaurus качество в основном обеспечивается type-check, lint/build pipeline и
ручной проверкой.
