# Добавление кастомных провайдеров в Keycloak через Docker

## Подробное руководство по добавлению .jar-модулей

### 1. Подготовка структуры папок

Перед запуском контейнера создайте следующую структуру:

/ваш_проект/
├── docker-compose.yml
├── providers/    ← сюда кладём .jar файлы (провайдеры расширения и тп)
└── themes/      ← кастомные темы (опционально)




### 2. Настройка docker-compose.yml

Ваш пример с пояснениями:

```yaml
version: '3.8'

services:
  keycloak:
    image: quay.io/keycloak/keycloak:latest
    container_name: keycloak
    environment:
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres/keycloakdb
      KC_DB_USERNAME: postgres
      KC_DB_PASSWORD: postgres
      KC_HOSTNAME_STRICT: false
      KC_PROXY_HEADERS: xforwarded
      KC_PROXY: edge
      KC_HTTP_ENABLED: true
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    volumes:
      - ./themes:/opt/keycloak/themes       # Монтирование кастомных тем
      - ./providers:/opt/keycloak/providers # Монтирование кастомных провайдеров
    ports:
      - "80:8080"
    command: start-dev
    depends_on:
      - postgres

  postgres:
    image: postgres:13
    environment:
      POSTGRES_DB: keycloakdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```


3. Добавление кастомных провайдеров
Поместите ваши .jar-файлы в папку providers:

```bash
cp /путь/к/вашему/провайдеру.jar ./providers/
```

Перезапустите Keycloak для применения изменений

4. Проверка загруженных провайдеров
Войдите в админ-консоль Keycloak.

Перейдите в меню "Realm settings" → "Themes".

В разделе "Providers" должны отображаться ваши кастомные провайдеры.

5. Особенности работы
Типы провайдеров:

Authenticator
SPI (Service Provider Interface)
Event Listener
Storage Provider
Логирование:

Проверьте логи на предмет ошибок загрузки:

Некоторые провайдеры могут требовать полного перезапуска контейнера.



7. Устранение проблем
Если провайдеры не загружаются:

Убедитесь, что .jar-файлы:

Имеют правильную структуру
Совместимы с версией Keycloak
Не повреждены


8. Профилактические меры
Всегда проверяйте совместимость версий
Документируйте добавляемые провайдеры
Регулярно обновляйте провайдеры
Используйте теги версий для .jar-файлов
