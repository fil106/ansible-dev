# ansible-dev

1) Создаём пользователей из файла `vars/dev_users.secret` он зашифрован, чтобы править `ansible-vault edit vars/dev_users.secret` либо вводите пароль по требованию либо `.vaulpass` с паролем. Использовал готовую роль от `https://github.com/ryandaniels/ansible-role-create-users`
2) SSH логинимся только по ключу. Роль `sshd-config` - при помощи jinja2 генерим стандартный шаблон, куда подставляем 2 переменные. Ребутим.
3) Redis
