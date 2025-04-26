# 🔐 Keycloak: Полное руководство по настройке клиента для начинающих

## 🌟 Основные настройки клиента

### 1. 🏷️ General Settings (Общие настройки)
| Параметр | Значение | Описание |
|----------|----------|----------|
| **Client ID*** | `my_app` | Уникальный идентификатор (латинские буквы, цифры, подчёркивания) |
| **Name** | Мое приложение | Человекочитаемое название (отображается в интерфейсе) |
| **Description** | Продакшен версия | Дополнительное описание назначения клиента |
| **Always display in UI** | ❌ Off | Показывать ли клиент в списках, когда он не используется |

### 2. 🌐 Access Settings (Настройки доступа)
| Параметр | Пример | Подробное объяснение |
|----------|--------|----------------------|
| **Root URL** | `https://shop.example.com` | Базовый URL всего приложения |
| **Home URL** | `https://shop.example.com/home` | Ссылка на главную страницу (для кнопки "Назад") |
| **Valid redirect URIs*** | `https://shop.example.com/auth/callback` | Точные URI для редиректа после авторизации |
| **Valid post logout URIs** | `https://shop.example.com/logout/success` | Куда перенаправлять после выхода из системы |
| **Web origins** | `https://shop.example.com` | Домен для CORS-запросов (без / на конце) |

#### 🔥 Примеры для Web Origins:
```text
*                        - Разрешить все домены (опасно для prod)
https://example.com      - Конкретный домен
https://*.example.com    - Все поддомены example.com
https://shop.example.com https://api.example.com - Несколько доменов
```

## 3. ⚙️ Capability Config

| Параметр                     | Рекомендация | Для чего нужен?                                   |
|------------------------------|--------------|---------------------------------------------------|
| Client authentication         | ✅ On        | Требовать ли client_secret для аутентификации     |
| Authorization                 | ❌ Off       | Включить fine-grained авторизацию (обычно не нужно)|

## 4. 🔄 Authentication Flows

| Поток                     | Вкл? | Когда использовать                               |
|--------------------------|------|-------------------------------------------------|
| Standard flow            | ✅   | Веб-приложения (OAuth 2.0 Authorization Code)  |
| Direct access grants     | ✅   | Мобильные/десктоп приложения                     |
| Implicit flow            | ❌   | Устаревший (небезопасный)                       |
| Service accounts         | ❌   | Для machine-to-machine коммуникации              |
| Device flow              | ❌   | Умные TV/принтеры                               |
| CIBA                     | ❌   | Бесшумная аутентификация                         |

## 5. 🚪 Logout Settings (Подробно)

| Параметр                     | Пример                                   | Объяснение                                      |
|------------------------------|------------------------------------------|------------------------------------------------|
| Front channel logout         | ✅ On                                   | Стандартный выход через браузер                 |
| Front-channel logout URL     | https://app.com/logout                  | URL для подтверждения выхода                     |
| Backchannel logout           | ✅ On                                   | Серверный выход (без участия пользователя)      |
| Backchannel logout URL       | https://api.app.com/bclogout           | Эндпоинт для серверного выхода                  |
| Backchannel session required  | ✅ On                                   | Требовать активную сессию                       |
| Revoke offline sessions      | ❌ Off                                  | Отзывать offline-токены (редко нужно)          |



🛠️ Примеры конфигураций
1. Веб-приложение (React/Angular)
```yaml
Client ID: web_app
Root URL: https://myapp.com
Valid redirect URIs:
  - https://myapp.com/auth/callback
  - https://myapp.com/silent-renew.html
Web origins: https://myapp.com
Standard flow: ✅ On
Implicit flow: ❌ Off
```


2. Мобильное приложение (Android/iOS)
```yaml
Client ID: mobile_app
Valid redirect URIs:
  - com.myapp://oauthredirect
  - io.identityserver.demo:/oauthredirect
Public client: ✅ On
Direct access: ✅ On
```

🚨 Важные советы по безопасности
Всегда используйте HTTPS в продакшене
Никогда не оставляйте * в Valid Redirect URIs для рабочих окружений
Для SPA-приложений используйте PKCE (настройка во вкладке Credentials)
Регулярно ротируйте client_secret (раз в 3-6 месяцев)
Для публичных клиентов (мобильные приложения) отключайте client authentication
