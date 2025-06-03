# Ansible Role: install-alloy

Роль предназначена для автоматизированной установки и настройки [Grafana Alloy](https://grafana.com/docs/alloy/) на хостах любой инфраструктуры с помощью Ansible.

## 📌 Основная идея

Вместо ручного добавления экспортеров Prometheus и настройки на каждом сервере, эта роль позволяет централизованно и быстро развернуть Grafana Alloy — мощный агент, объединяющий функциональность Prometheus и Loki.

После применения роли хост готов к мониторингу:
- достаточно включить нужные `.alloy.disabled` конфигурации,
- изменить IP/порты под свою инфраструктуру,
- и Alloy сразу начнёт собирать метрики или логи.

## 📁 Структура роли
ansible-scripts/
└── roles/
    └── install-alloy/
        ├── defaults/
        │   └── main.yml
        ├── files/
        │   ├── alloy-1.7.5-1.amd64.deb         # Локальный deb-файл (если нет интернета)
        │   ├── blackbox.alloy.disabled         # Пример конфигурации blackbox exporter
        │   ├── cadvisor.alloy.disabled         # Пример конфигурации cadvisor
        │   ├── docker_logs.alloy.disabled      # Пример конфигурации логов docker для Loki
        │   ├── node.alloy.disabled             # node exporter
        │   ├── self.alloy.disabled             # self-monitoring Alloy
        │   └── service/
        │       └── alloy.service               # systemd unit-файл
        ├── handlers/
        │   └── main.yml
        ├── tasks/
        │   └── main.yml
        ├── templates/
        │   ├── alloy.j2                        # основной шаблон конфигурации
        │   ├── config.alloy.j2                 # дополнительный шаблон, если используется
        │   └── override.conf.j2                # опциональный systemd override
        └── vars/
            ├── Debian.yml
            └── main.yml

## 🔧 Поддерживаемые конфигурации Alloy

Примеры включаемых модулей:

| Название            | Назначение                       |
|---------------------|----------------------------------|
| `blackbox`          | Проверка доступности HTTP/HTTPS  |
| `cadvisor`          | Метрики из Docker контейнеров    |
| `docker_logs`       | Логи Docker контейнеров для Loki |
| `node`              | Системные метрики                |
| `self`              | Мониторинг самого Alloy          |

Каждый из них представлен как `.alloy.disabled` файл — для активации достаточно переименовать файл, убрав `.disabled`.

## ⚙️ Пример использования роли

```yaml
- name: Install Alloy and configure monitoring agent
  hosts: all
  become: true
  roles:
    - install-alloy
```

#⚠️ ВНИМАНИЕ!
В репозитории отсутствует файл
`files/alloy-1.7.5-1.amd64.deb` — это локальный `.deb-файл`, который можно использовать при отсутствии доступа к интернету.
Вы можете скачать и установить последнюю версию самостоятельно, так как мне неудалось его добавить, из-за ограничения в размере репозитория.