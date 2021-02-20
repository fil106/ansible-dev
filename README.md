# ansible-dev

### Start

Для запуска сценария необходимо
1) `vars/` переменные проверить/изменить
2) `playbook.yml` раскоментировать необходимые `roles`
3) `inv.txt` добавить свои хосты
4) `ansible.cfg` изменить пользовтеля
5) `.vaultpass` создать свой, внутри plaintext пароль
6) `dev_users.secret` создать на основе примера `dev_users.secret.example`

В корневой папке запускаем
`ansible-playbook playbook.yml -kK`


### Roles

1) `create-user:` Создаём пользователей из файла `vars/dev_users.secret` он зашифрован, чтобы править `ansible-vault edit vars/dev_users.secret` либо вводите пароль по требованию либо `.vaulpass` с паролем. Использовал готовую роль от `https://github.com/ryandaniels/ansible-role-create-users`
2) `sshd-config:` SSH логинимся только по ключу. Роль `sshd-config` - при помощи jinja2 генерим стандартный шаблон, куда подставляем 2 переменные, копируем на сервер и делаем бэкап на всякий. Ребутим.
3) `install-packages:` Устанавливаем vim, iotop, tcpdump при помощи модуля apt
4) `check-ip:` `https://github.com/ATolkachev/ansible/blob/master/library/get_ip_facts.py` - получаем какой интерфей с белой и серой ip
5) `docker-install:` Ставим docker
6) `docker-redis:` Запускаем контейнер с redis и нужными параметрами
7) `redis:` Запускаем redis в докере
8) `ufw:` Настраиваем firewall
