Домашнее задание
Systemd
Выполнить следующие задания и подготовить развёртывание результата выполнения с использованием Vagrant и Vagrant shell provisioner (или Ansible, на Ваше усмотрение):
1. Написать service, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова (файл лога и ключевое слово должны задаваться в /etc/sysconfig);
2. Из репозитория epel установить spawn-fcgi и переписать init-скрипт на unit-файл (имя service должно называться так же: spawn-fcgi);
3. Дополнить unit-файл httpd (он же apache) возможностью запустить несколько инстансов сервера с разными конфигурационными файлами;

Каждая из работ находится в отдельном каталоге:
- looker - сервис мониторинга по ключевому слову
- myhttpd - запуск нескольких инстансов сервера httpd
- spawn - замена init-скрипта на unit-файл для spawn-fcgi

1. looker
Представляет из себя файл Vagrant с playbook Ansible
запуск из директории looker:

vagrant up

После запуска, авторизоваться логин/пароль: vagrant
Выждать 30 секунд или больше и просмотреть последние сообщения в журнале /var/log/messages 

sudo tail -f /var/log/messages

Искомое слово и целевой журнал задается в файле looker

Для решения задания было создано два unit сервиса. Один представляет собой таймер (timer.timer), который запускает другой сервис watch.service (каждые 30 секунд). watch.service имеет тип oneshot (Type=oneshot), что удобно для выполнения скриптов. Сервис выполняет одно задание и завершаются. watch.service выполняет скрипт watch.sh из директории /var/opt/ с параметрами из файла looker (ключевое слово и журнал). При нахождении ключевого слова - строчка с этим ключевым словом помещается в журнал /var/log/messages.

Журнал, который мы анализируем представляет из себя файл с наполнением Lorem Ipsum в тексте которого было размещено ключевое слово.

==============================================

2. spawn
Представляет из себя файл Vagrant с playbook Ansible
Запуск из директории spawn

vagrant up

Результат можно отследить через команду

sudo systemctl | grep spawn 

Для достижения цели мы используем файл jinja2 с конфигурацией и файл с unit сервисом для spawn-cfgi 
После установки spawn-cfgi, создаем unit-сервис
Всё довольно просто.


==============================================

3. myhttpd

Представляет из себя файл Vagrant с playbook Ansible
запуск из директории myhttpd

vagrant up 

Проверка результата:

авторизоваться логин/пароль: vagrant

выполнить команду

sudo systemctl | grep httpd

Для достижения цели были использованы шаблоны jinja2 c конфигурацией и стартовой страничкой для каждого сервиса
Сервисы подняты на портах 80, 81 и 82

Чтобы получить стартовую страничку с каждого из сервисом можно выполнить команду

curl http://localhost:НОМЕР_ПОРТА


==============================================









