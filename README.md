# ansible-dev

### Задание

> Есть условный свежеустановленный сервер с убунту 20.04 на борту. Там 2 сетевых интерфейса (на одном белый ip, на другом серый). Есть ssh доступ по белому ip по логину (root) + паролю. Серый ip не известен. Написать сценарий автоматизации, который сделает следующее:
> Добавит несколько (больше 2х) пользователей на сервер с их ключами
> Даст всем новым пользователям возможность повышения прав до root
> Добавит несколько стандартных пакетов в систему (vim, iotop, tcpdump)
> Отключит в ssh доступ по паролю (оставит доступ только по ключам)
> Сменит стандартный порт ssh
> Установит redis и запустит его на сером интерфейсе
> Настроит доступ на сервер и в редис только по серой сети
> Запретит любой доступ на сервер по белой сети, кроме ответов на ping

### Тестовый стенд

1) При помощи `Vagrant` устанавливал 2 виртуалки Ubuntu 20.04, 1 - Master-ansible, 2 - Для тестов. `Vagrantfile` - в репе есть. На виртуалке при старте 3 сетевухи, первую `nat` надо отключить + серая сеть через сетевуху на ПК + белая сеть через bridge intnet.
2) На Master-ansible установить необходимые пакеты 
```bash
sudo apt install python3-pip -y
pip3 install ansible
```
Перезапускаем сессию `source ~/.bashrc`

3) Получить репозиторий
```bash
git clone https://github.com/fil106/ansible-dev.git ansible
```

### Start

Для запуска сценария необходимо
1) `vars/` переменные проверить/изменить, при необходимости добавляем сюда свои переменные. После в `templates` используем
2) `playbook.yml` раскомментировать необходимые `roles`
3) `inv.txt` добавить свои хосты
4) `ansible.cfg` изменить пользовтеля (в данном случае пользователь vagrant т.к. виртуалки поднимаем через vagrant)
5) `.vaultpass` создать свой, внутри plaintext пароль
6) `dev_users.secret` создать на основе примера `dev_users.secret.example`, где 
* ssh-key - генерим ssh-key под нужным пользователем или под своим но копируем в /home/<имя пользователя>/.ssh. Генерируем при помощи `ssh-keygen`. И вставляем необходимый ключ в данное поле.
* password - генерим пароль `mkpasswd --method=SHA-512`, чтоб не светить пароль. Можно и без шифрования.
* Остальные параметры можно смотреть тут - https://github.com/ryandaniels/ansible-role-create-users
* Можно добавлять несколько пользователей, для этого просто по правилам yaml добавляем ещё один элемент через "-" соблюдая отступы.

В корневой папке запускаем:
1) Если не хотим светить пароли и создавать файл `.vaultpass` и создавать переменную `ansible_sudo_pass` в `ansible.cfg` то: `ansible-playbook playbook.yml -kK`, где
  ```
  -k, --ask-pass - ask for connection password
  -K, --ask-become-pass - ask for privilege escalation password
  ```
2) Если создали `.vaultpass` и переменную `ansible_sudo_pass`, то: `ansible-playbook playbook.yml`


### Roles

1) `create-user:` Создаём пользователей из файла `vars/dev_users.secret` он зашифрован, чтобы править `ansible-vault edit vars/dev_users.secret` либо вводите пароль по требованию либо `.vaulpass` с паролем. Использовал готовую роль от `https://github.com/ryandaniels/ansible-role-create-users`
2) `sshd-config:` SSH логинимся только по ключу. Роль `sshd-config` - при помощи jinja2 генерим стандартный шаблон, куда подставляем 2 переменные, копируем на сервер и делаем бэкап на всякий. Ребутим.
3) `install-packages:` Устанавливаем vim, iotop, tcpdump при помощи модуля apt
4) `check-ip:` `https://github.com/ATolkachev/ansible/blob/master/library/get_ip_facts.py` - получаем какой интерфей с белой и серой ip. Без данной роли не будут изменены конфиги ssh, redis и iptables - конфиги будут по умолчанию. (Отказался от данного варианта в пользу пункта ниже)
* `check-ip:` - решил определять серую сеть след. образом, известно, что коннектимся по белой ip соответственно `ansible_all_ipv4_addresses` - знает все ip интерфейсов, и `ansible_host` - текущий host ip. Соответственно исключаем из ansible_all_ipv4_addresses ansible_host и получаем серую ip. Логика работает соответственно только если 2 интерфейса на машине.
5) `docker-install:` Ставим docker
6) `docker-redis:` Запускаем контейнер с redis и нужными параметрами
7) `redis:` Запускаем redis в докере
8) `iptables:` Настраиваем firewall, **ВАЖНО, для корректной работы необходимо запускать с включенной ролью `check-ip` т.к. `private_ip` получаем оттуда**
