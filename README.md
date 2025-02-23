# Openconnect Server ansible role

Эта роль разворачивает и настраивает OpenConnect Server (ocserv) на Debian‑серверах, обеспечивая безопасный VPN‑доступ с возможностью выбора между локальной аутентификацией (через файл паролей) и аутентификацией через OpenID Connect (OIDC). Роль автоматически настраивает необходимые параметры сети, включая IP‑пересылку и NAT, а также генерирует самоподписанный SSL‑сертификат при отсутствии предоставленного.

! ПРОТЕСТИРОВАНО ТОЛЬКО PLAIN !

## Особенности

- **Установка и настройка ocserv:** Автоматическая установка ocserv и первичная настройка его основных параметров.
- **Двойной режим аутентификации:** Поддержка как локальной аутентификации (plain с файлом паролей), так и аутентификации через OIDC.
- **Генерация самоподписанных SSL‑сертификатов:** Если сертификаты отсутствуют, роль автоматически создаёт SSL‑сертификат и приватный ключ.
- **Настройка IP‑пересылки и NAT:** Конфигурация iptables для обеспечения корректной маршрутизации VPN‑трафика.
- **Гибкая конфигурация групп:** Возможность задавать индивидуальные настройки маршрутов, DNS, таймаутов сессии и ограничений подключений для различных групп пользователей.
- **Управление через Ansible:** Простая интеграция и настройка с использованием переменных, что позволяет адаптировать роль под требования конкретной инфраструктуры.

## Переменные

Все параметры, необходимые для настройки, определяются в файле `defaults/main.yml`:

## Использование роли
1. Настройте переменные роли
Пример конфигурации с plain аунтификацией
`group_vars/vpn_servers.yml`

```yaml
---
ocserv_enable_oidc: false
ocserv_local_users:
  admin: "adminpasswd"
  admin2: "adminpasswd2"

ocserv_vpn_subnet: "10.1.0.0/24"
ocserv_ipv4_netmask: "255.255.255.0"

ocserv_network_interface: "ens3"

ocserv_visible_routes:
  - "1.0.0.0/24"
```

2. **Создайте playbook**

Пример файла `vpn_setup.yml`:

```yaml
- hosts: vpn_servers
  become: yes
  roles:
    - ocserv
```

3. **Запустите playbook**

Выполните команду:

```bash
ansible-playbook vpn_setup.yml -i inventory/hosts
```

## Подключение

Используем пакет openconnect

```bash
sudo dnf install openconnect # для Fedora
# сначала надо ввести свой пароль от локальной машины, потом пароль от vpn
sudo openconnect --protocol=anyconnect --user=<USER> --passwd-on-stdin <IP_OR_DOMAIN> 
```

## Требования

- Debian или производные (например, Ubuntu).
- Ansible версии 2.9 или выше.
- Доступ с правами root или возможность использовать sudo.

## TODO
- Группы для локальных пользователей
- Тестирование и дебаг OIDC конфигурации
- Добавление новых вариантов авторизации
- сделать как можно больше параметров настраиваемыми
- CA
