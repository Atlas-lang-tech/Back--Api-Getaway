# Kong DB-less Configuration Documentation

## Формат конфигурации
- `_format_version: "2.1"` — версия декларативного формата Kong.
- `_transform: true` — автоматическая трансформация конфигурации.

---

## Services
Определяет микросервисы, которые проксирует Kong.

Пример:
```yaml
services:
  - name: my-service
    url: http://my-microservice:3000
```

name — имя сервиса в Kong.

url — адрес микросервиса, на который будет идти проксирование.

## Routes

Маршруты, через которые клиенты обращаются к сервисам.

Пример:
```yaml
routes:
  - name: private-route
    paths:
      - /api/private
    strip_path: false
```
paths — пути, которые будут обрабатываться через Kong.

strip_path — оставлять ли путь при проксировании.

protocols — поддерживаемые протоколы (http, https).

## Plugins

Плагины применяются к сервисам или маршрутам.

Пример JWT:
```yml
plugins:
  - name: jwt
    config:
      uri_param_names: []
      cookie_names: []
      run_on_preflight: true
      key_claim_name: sub
      secret_is_base64: false
      anonymous: null
```
uri_param_names — отключаем получение токена через query-параметры.

cookie_names — отключаем проверку токена через cookies.

run_on_preflight — обрабатывать preflight-запросы.

key_claim_name — поле в JWT, идентифицирующее пользователя.

anonymous — null, потому что не используем anonymous consumer.

## JWT Secrets

Определяет секреты для верификации JWT.

Пример:
```yml
jwt_secrets:
  - consumer: null
    key: my-jwt-key
    algorithm: HS256
```
consumer — null, если не используем consumers.

key — секрет для проверки подписи токена.

algorithm — алгоритм подписи (HS256, RS256 и т.д.).

## Публичные и приватные маршруты

Публичные (/api/{service}/public) — без JWT.

Приватные (/api/{service}/private) — проверяются через JWT.

Запуск Kong DB-less
```zsh
docker-compose up -d
```

Kong подхватит конфигурацию из kong.yml и начнёт проксировать сервисы.

JWT-проверка происходит на уровне Kong, дополнительная проверка внутри сервиса нужна только для ролей или авторизации.
