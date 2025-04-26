# 🚀 Keycloak: Быстрый старт с Docker

## Содержание
- [Режим разработки (H2 in-memory)](#-1-режим-разработки-h2-in-memory)
- [Production-режим (с PostgreSQL)](#-2-production-режим-с-postgresql)

---

## 🧪 1. Режим разработки (H2 in-memory)
**⚠️ Только для тестирования! Данные не сохраняются**

### Команда для запуска:
```bash
docker run -d \
  --name keycloak_dev \
  -p 8080:8080 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:latest \
  start-dev
```

### docker-compose
```yaml
version: '3.8'
services:
  keycloak:
    image: quay.io/keycloak/keycloak:latest
    command: ["start-dev"]
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
      KC_HTTP_ENABLED: "true"
    ports:
      - "8080:8080"
    restart: unless-stopped
```


## 🏗 2. Production-режим (с PostgreSQL)
**✅ Рекомендуется для рабочих окружений**

### docker-compose для прода с БД Postgrses
```yaml
version: '3.8'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_DB: keycloak
      POSTGRES_USER: keycloak
      POSTGRES_PASSWORD: your_strong_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - keycloak_network

  keycloak:
    image: quay.io/keycloak/keycloak:latest
    command: ["start"]
    environment:
      KC_DB: postgres
      KC_DB_URL: jdbc:postgresql://postgres:5432/keycloak
      KC_DB_USERNAME: keycloak
      KC_DB_PASSWORD: your_strong_password
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: your_very_strong_admin_password
      KC_HOSTNAME: your.domain.com
      KC_PROXY: edge
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    networks:
      - keycloak_network

volumes:
  postgres_data:

networks:
  keycloak_network:
    driver: bridge
```