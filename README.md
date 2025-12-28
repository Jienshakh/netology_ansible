# Домашнее задание к занятию 4 «Работа с roles»



## Описание

Этот playbook автоматизирует развертывание стека для сбора и анализа логов:

*   **ClickHouse** — высокопроизводительная колоночная СУБД для хранения логов
*   **Vector** — сборщик и обработчик логов с отправкой в ClickHouse
*   **Nginx + Lighthouse** — веб-сервер с интерфейсом мониторинга

Архитектура: **Vector** (сбор логов) → **ClickHouse** (хранение) ← **Lighthouse** (визуализация)

## Структура проекта

```
├── inventory/
│   └── prod.yml                   # Инвентарь с хостами
├── requirements.yml               # Зависимости
├── site.yml                       # Основной плейбук
├── uninstall_all.yml              # Playbook для удаления
└──roles/
    ├── clickhouse/                # Роль ClickHouse
    │   ├── defaults/
    │   │   └── main.yml          # Переменные по умолчанию
    │   ├── handlers/
    │   │   └── main.yml          # Обработчики
    │   ├── tasks/
    │   │   └── main.yml          # Задачи
    │   └── vars/
    │       └── config.xml.j2     # Переменные
    │
    ├── vector/                    # Роль Vector
    │   ├── defaults/
    │   │   └── main.yml          # Переменные Vector
    │   ├── handlers/
    │   │   └── main.yml          # Обработчики Vector
    │   ├── tasks/
    │   │   └── main.yml          # Задачи Vector
    │   └── templates/
    │       └── vector.yaml.j2    # Шаблон конфига Vector
    │
    └── lighthouse/                # Роль Lighthouse
        ├── defaults/
        │   └── main.yml          # Переменные Lighthouse
        ├── handlers/
        │   └── main.yml          # Обработчики
        ├── tasks/
        │   └── main.yml          # Задачи
        └── templates/
            ├── nginx.conf.j2     # Шаблон конфига Nginx
            └── lighthouse.conf.j2 # Шаблон конфига Lighthouse
```

## Теги

Playbook поддерживает выборочный запуск компонентов:

*   **`clickhouse`** — установка ClickHouse
*   **`vector`** — установка Vector
*   **`lighthouse`** — установка Lighthouse

## Использование

### Полная установка:
```bash
ansible-playbook -i inventory/prod.yml site.yml
```

### Установка отдельных компонентов:

#### Только ClickHouse
```bash
ansible-playbook -i inventory/prod.yml site.yml --tags clickhouse
```

#### Только Vector
```bash
ansible-playbook -i inventory/prod.yml site.yml --tags vector
```

#### Только Nginx и Lighthouse
```bash
ansible-playbook -i inventory/prod.yml site.yml --tags lighthouse
```

## Удаление

### Полное удаление стека:

```bash
ansible-playbook -i inventory/prod.yml uninstall_all.yml
```

### Выборочное удаление:

#### Удалить только Vector

```bash
ansible-playbook -i inventory/prod.yml uninstall_all.yml --tags vector
```

#### Удалить только ClickHouse

```bash
ansible-playbook -i inventory/prod.yml uninstall_all.yml --tags clickhouse
```
