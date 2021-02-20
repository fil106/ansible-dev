# ansible-dev

1) Создаём пользователей из файла `vars/dev_users.secret` он зашифрован, чтобы править `ansible-vault edit vars/dev_users.secret` либо вводите пароль по требованию либо `.vaulpass` с паролем. Использовал готовую роль от `https://github.com/ryandaniels/ansible-role-create-users`
2) SSH логинимся только по ключу. Роль `sshd-config` - при помощи jinja2 генерим стандартный шаблон, куда подставляем 2 переменные, копируем на сервер и делаем бэкап на всякий. Ребутим.
3) Устанавливаем vim, iotop, tcpdump при помощи модуля apt
4) `https://github.com/ATolkachev/ansible/blob/master/library/get_ip_facts.py` - получаем какой интерфей с белой и серой ip
5) Ставим docker
6) Запускаем redis в докере
7) Настраиваем firewall
