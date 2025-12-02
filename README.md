# Ansible Playbook для установки Uptime Kuma

Этот проект представляет собой Ansible Playbook для автоматической установки и настройки [Uptime Kuma](https://github.com/louislam/uptime-kuma) — инструмента мониторинга доступности.

Playbook предназначен для автоматической установки и настройки ТОЛЬКО на чистом сервере (VPS/VDS) с операционной системой на базе Debian или Ubuntu. Предполагается, что сервер только что создан и не содержит пользовательских данных. В связи с этим playbook обновляет систему и устанавливает необходимое программное обеспечение без проверки на наличие уже установленных пакетов.

## Структура проекта

- `install_kuma.yml` — основной playbook для установки Uptime Kuma.
- `inventory.yml` — файл инвентаря для указания целевых хостов.
- `group_vars/all.yml` — переменные группы, включая домен.
- `tasks/` — директория с задачами:
  - `update_system.yml` — обновление системы.
  - `install_packages.yml` — установка необходимых пакетов.
  - `install_acme.yml` — установка и настройка acme.sh для SSL-сертификатов.
  - `setup_compose.yml` — настройка Docker Compose.
  - `start_kuma.yml` — запуск Uptime Kuma.
  - `configure_nginx.yml` — настройка Nginx.
  - `configure_firewall.yml` — настройка брандмауэра.

## Предварительные требования

1. **Ansible**: Убедитесь, что Ansible установлен на вашей локальной машине.
2. **Целевые хосты**: Укажите серверы с ОС на базе Debian/Ubuntu в файле [`inventory.yml`](inventory.yml).
3. **Домен**: Направьте домен на ваш сервер и укажите его в файле [`group_vars/all.yml`](group_vars/all.yml):

```yaml
domain: "uptime.example.com"
```

## Установка

1. Склонируйте репозиторий:

```bash
git clone <URL-репозитория>
cd AnsibleUptimeKuma
```

2. Укажите целевые хосты в `inventory.yml`.

3. Запустите playbook:

```bash
ansible-playbook -i inventory.yml install_kuma.yml
```

## Описание задач

### 1. Обновление системы

Файл: `tasks/update_system.yml`

Обновляет все пакеты до последних версий.

### 2. Установка пакетов

Файл: `tasks/install_packages.yml`

Устанавливает необходимые пакеты: `curl`, `cron`, `nginx`, `ufw` и Docker.

- curl - для загрузки скриптов.
- cron - для периодического обновления SSL-сертификатов (зависимость от acme.sh).
- nginx - для обратного проксирования.
- ufw - для настройки брандмауэра.

### 3. Настройка SSL

Файл: `tasks/install_acme.yml`

- Устанавливает acme.sh.
- Выпускает SSL-сертификат для указанного домена.

### 4. Настройка Docker Compose

Файл: `tasks/setup_compose.yml`

- Создает директорию `/srv/uptimekuma`.
- Создает файл `compose.yml` для запуска Uptime Kuma.

### 5. Запуск Uptime Kuma

Файл: `tasks/start_kuma.yml`

Запускает Uptime Kuma с помощью Docker Compose.

### 6. Настройка Nginx

Файл: `tasks/configure_nginx.yml`

- Удаляет стандартную конфигурацию Nginx.
- Создает конфигурацию для проксирования запросов к Uptime Kuma.
- Перезагружает Nginx.

### 7. Настройка брандмауэра

Файл: `tasks/configure_firewall.yml`

- Разрешает порты 22 (SSH), 80 (HTTP) и 443 (HTTPS).
- Включает UFW.

## Примечания

- Убедитесь, что ваш домен точно указывает на сервер перед запуском playbook. (можно проверить с помощью `ping <ваш_домен>` с сервера на который вы устанавливаете kuma).
- После установки Uptime Kuma будет доступен по адресу: `https://<ваш_домен>`.

## Лицензия

Этот проект распространяется под лицензией [MIT](./LICENSE).
