# Развёртывание Turbokotiki через Ansible

Ansible-плейбук для развёртывания **web-сайта Turbokotiki** с использованием **Docker** на удалённом сервере через SSH.

Этот проект автоматизирует подготовку инфраструктуры и деплой приложения в соответствии с общепринятыми **практиками DevOps**.

---

# Обзор проекта

Плейбук выполняет следующие действия:

1. Подключается к удалённому серверу через **SSH**
2. Проверяет наличие установленного **Docker**
3. Проверяет наличие установленного **Git**
4. Создаёт директорию проекта на сервере
5. Клонирует репозиторий проекта
6. Запускает приложение с помощью **Docker Compose**

Развёртываемый репозиторий: https://github.com/kawa11Tensh1/Turbikotiki

---

# Структура проекта

```
ansible/
│
├── ansible.cfg
│
├── inventory/
│   ├── hosts.ini
│   └── group_vars/
│       └── all.yml
│
├── playbooks/
│   ├── deploy.yml      # Первоначальное развёртывание
│   └── update.yml      # Обновление приложения после деплоя
│
└── roles/
    ├── deploy/
    │   │
    │   ├── tasks/
    │   │   └── main.yml
    │   │
    │   ├── handlers/
    │   │   └── main.yml
    │   │
    │   ├── defaults/
    │   │   └── main.yml
    │   │
    │   ├── vars/
    │   │   └── main.yml
    │   │
    │   └── meta/
    │       └── main.yml
    └── update/
        │
        ├── tasks/
        │   └── main.yml
        │
        ├── handlers/
        │   └── main.yml
        │
        ├── defaults/
        │   └── main.yml
        │
        ├── vars/
        │   └── main.yml
        │
        └── meta/
            └── main.yml
```

---

# Требования

Перед запуском плейбука убедитесь, что на целевом сервере:

* Установлен Docker
* Установлен Git
* Включён доступ по SSH

Плейбук **автоматически проверит наличие Docker и Git**.

---

# Настройка инвентаря

Отредактируйте файл:

```
inventory/hosts.ini
```

Пример:

```
[turbokotiki]
server ansible_host=IP_СЕРВЕРА ansible_user=root ansible_ssh_pass=password
```

---

# Переменные

Глобальные переменные определены в файле:

```
inventory/group_vars/all.yml
```

Локальные переменные определены в файлах:

```
roles/deploy/vars/main.yml
roles/update/vars/main.yml
```

Пример конфигурации:

```
project_dir: /home/root/turbokotiki
repo_url: https://github.com/kawa11Tensh1/Turbokotiki.git
repo_branch: main
container_name: turbokotiki-site
docker_compose_command: docker compose
app_port: 8080
```

---

# Деплой приложения (Deploy)

Запустите **первоначальное развёртывание** из директории Ansible:

```
cd Turbokotiki-ansible
ansible-playbook playbooks/deploy.yml
```

---

# Обновление приложения (Update)

После успешного деплоя, при внесении изменений в код или конфигурацию, используйте плейбук обновления:

```
cd Turbokotiki-ansible
ansible-playbook playbooks/update.yml
```

> ⚠️ **Важно:** Плейбук `update.yml` следует запускать **только после успешного выполнения `deploy.yml`**, так как он предполагает, что инфраструктура уже подготовлена.

## Что делает `update.yml`:

1. Подключается к серверу через SSH
2. Переходит в директорию проекта `/home/root/turbokotiki`
3. Выполняет `git pull` для получения последних изменений из репозитория
4. Пересобирает и перезапускает контейнеры через Docker Compose
5. Применяет миграции (если предусмотрены)

---

# Процесс развёртывания

Плейбук `deploy.yml` выполняет следующие шаги:

1. Проверка установки Docker
2. Проверка установки Git
3. Создание директории `/home/root/turbokotiki`
4. Клонирование или обновление репозитория
5. Остановка существующих контейнеров
6. Запуск контейнеров через Docker Compose

---

# Доступ к приложению

После успешного деплоя сайт будет доступен по адресу:

```
http://IP_СЕРВЕРА:8080
```

---

# Используемые технологии

* Ansible
* Docker
* Docker Compose
* Nginx
* Git

---

# Автор

Борисова Влада  
Команда Turbokotiki