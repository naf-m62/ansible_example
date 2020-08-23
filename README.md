# ansible_example
Пример деплоя на несколько серверов. В примере используются ansible, vagrant ansistrano 

## Установить vagrant, ansible и ansistrano
vagrant:
1) sudo apt install virtualbox
2) sudo apt install vagrant
3) vagrant --version

ansible:
1) sudo add-apt-repository -y ppa:rquillo/ansible
2) sudo apt-get update
3) sudo apt-get install ansible -y

ansistrano
1) ansible-galaxy install ansistrano.deploy ansistrano.rollback

## Добавить виртуальные машины
Создать папку и в ней создать вируальную машину командой
* vagrant init centos/7

В Vagrantfile прокинуть порт:
* config.vm.network "forwarded_port", guest: 80, host: 8888, host_ip: "127.0.0.1"   

Можно сделать не через проброс порта, а через назначение ip
* config.vm.network "private_network", ip: "192.168.33.10"

Поднять виртуальную машину 
* vagrant up
 
## Создать файлы конфигураций для подключения к каждой виртуальной машине
Чтобы ansible мог подключиться к виртульной машине нужно настроить ssh-подключение

Для каждой виртуальной машины создать файлы с ssh подключением:
* ssh1.cfg 
* ssh2.cfg

C помощью команды получаем настройки подключения по ssh:
* vagrant ssh-config

Переименовать хост с default на любой другой(в примере: vagrant1/2) и записать в файл ssh1.cfg. То же самое для других серверов

В основном инвентори файле(hosts) разделить по группам и передать файлы в качестве аргументов для подключения к виртульным машинам

Проверка подключения: ansible -m ping all -i {путь к основному инвентори файлу} 

## ansible playbook
Cначала запустить apache.yml. Он установит apache на виртуальные машины и произведет базовую настройку
* sudo ansible-playbook -i hosts hooks/apache.yml

Потом уже деплой. Используя ansistrano подключим сценарии before(создаются папки для проекта, если их нет) 
и after(обновляет SELinux права для папки, иначе будет 403 ответ)  
* sudo ansible-playbook -i hosts deploy.yml

Роллбэк:
sudo ansible-playbook -i hosts rollback.yml