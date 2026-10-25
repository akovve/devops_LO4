# devops_LO4

# Лабораторная работа №4. Ansible Basics

## Цель работы

Научиться использовать Ansible для установки веб-сервера Nginx на удалённую виртуальную машину и копирования HTML-файла.

## Ход работы

### 1. Подготовка окружения

Были подготовлены две виртуальные машины Ubuntu: управляющая и целевая. Для взаимодействия между ними использовалось SSH-подключение.

В `crontab` была отключена задача запуска скрипта `weatherperm.sh` из предыдущей лабораторной работы.

<img width="1280" height="740" alt="telegram-cloud-photo-size-2-5399970722823740829-y" src="https://github.com/user-attachments/assets/4b8e04d0-5784-4970-9da2-a468576a9a95" />


### 2. Создание Inventory

Создан файл `inventory.ini` с адресом целевой машины:

```ini
[webservers]
10.0.2.16 ansible_become_exe=/usr/bin/sudo.ws
```

### 3. Создание HTML-файла

Создан файл `index.html` со следующим содержимым:

```html
<HTML>
<BODY>
<h1>Hello from Ansible playbook!</h1>
</BODY>
</HTML>
```

### 4. Создание Playbook

Создан файл `playbook.yml`, который устанавливает Nginx и копирует HTML-файл на целевую машину.

```yaml
---
- name: Deploy nginx
  hosts: webservers
  become: yes
  become_method: sudo
  remote_user: runner

  tasks:
    - name: Install nginx
      ansible.builtin.apt:
        name: nginx
        state: latest
        update_cache: yes

    - name: Copy index
      ansible.builtin.copy:
        src: ./index.html
        dest: /var/www/html/index.nginx-debian.html
        owner: root
        group: root
        mode: '0666'
```
<img width="1280" height="740" alt="telegram-cloud-photo-size-2-5399970722823740829-y" src="https://github.com/user-attachments/assets/0cd6d314-a1a2-4814-ab3b-0c98df6f2e2f" />

### 5. Проверка Playbook

Выполнен тестовый запуск:

```bash
ansible-playbook playbook.yml -i inventory.ini -K --check
```
<img width="1280" height="524" alt="telegram-cloud-photo-size-2-5400187979449441686-y" src="https://github.com/user-attachments/assets/f4291164-8738-4d24-9a65-7d3693756ec6" />


По результатам проверки задачи завершились без ошибок:

- `ok = 3`
- `changed = 0`
- `unreachable = 0`
- `failed = 0`

### 6. Проверка веб-сервера

Для проверки доступности веб-сервера выполнена команда:

```bash
curl 10.0.2.16
```
<img width="1280" height="637" alt="telegram-cloud-photo-size-2-5400187979449441684-y" src="https://github.com/user-attachments/assets/925d7386-28df-402e-99a8-1ddcc9bfd7e8" />


В ответ получена HTML-страница с текстом:

```html
<h1>Hello from Ansible playbook!</h1>
```

Это подтверждает доступность веб-сервера по указанному адресу и получение подготовленной страницы.

## Вывод

В ходе лабораторной работы был создан Ansible Playbook для установки Nginx и копирования HTML-файла на удалённую виртуальную машину. Выполнена проверка Playbook в тестовом режиме и проверена доступность веб-сервера с помощью `curl`.

В результате были получены практические навыки работы с Ansible, Inventory, Playbook и удалённым управлением сервером.
