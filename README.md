# DeminMaksim-7-1-ansible-hw
Создаени список хостов

inventory.ini
```
[kafka]
kafka ansible_host=213.21.15.199

[kafka:vars]
ansible_port=5212
ansible_user=maksim
ansible_ssh_private_key_file=~/.ssh/id_rsa
```
И sudo-пароль в vault.yml
```
ansible-vault create group_vars/all/vault.yml
```
# Задание 1

Выполните действия, приложите файлы с плейбуками и вывод выполнения.

Напишите три плейбука. При написании рекомендуем использовать текстовый редактор с подсветкой синтаксиса YAML.

Плейбуки должны:

Скачать какой-либо архив, создать папку для распаковки и распаковать скаченный архив. Например, можете использовать официальный сайт и зеркало Apache Kafka. 
При этом можно скачать как исходный код, так и бинарные файлы, запакованные в архив — в нашем задании не принципиально.
Установить пакет tuned из стандартного репозитория вашей ОС. Запустить его, как демон — конфигурационный файл systemd появится автоматически при установке. 
Добавить tuned в автозагрузку.
Изменить приветствие системы (motd) при входе на любое другое. 
Пожалуйста, в этом задании используйте переменную для задания приветствия. Переменную можно задавать любым удобным способом.

# Решение:

## Задание 1-1

[01_download_kafka.yml](Task1/01_download_kafka.yml)

Выполняем в консоли 
```
ansible-playbook -i inventory.ini 01_download_unpack.yml --ask-vault-pass
```
Результат 
```
PLAY [Task 1.1 - Download and unpack Apache Kafka archive] *********************

TASK [Gathering Facts] *********************************************************
ok: [kafka]

TASK [Ensure download directory exists] ****************************************
ok: [kafka]

TASK [Download Kafka archive from Apache official distribution] ****************
ok: [kafka]

TASK [Ensure extract directory exists] *****************************************
ok: [kafka]

TASK [Unpack archive into extract directory] ***********************************
ok: [kafka]

PLAY RECAP *********************************************************************
kafka                      : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```  

<img width="1174" height="1601" alt="image_2026-01-05_17-17-10" src="https://github.com/user-attachments/assets/d676d04f-dbd2-4086-854d-0feef9ac686c" />


## Задание 1-2

 [02_tuned.yml](Task1/02_tuned.yml)
    
Выполняем в консоли 
```
ansible-playbook -i inventory.ini 02_tuned.yml --ask-vault-pass
```
Результат
```
PLAY [Task 1.2 - Install tuned, start service, and enable at boot] *************

TASK [Gathering Facts] *********************************************************
ok: [kafka]

TASK [Enable Ubuntu Universe repository (required for tuned on Ubuntu)] ********
changed: [kafka] => (item=universe)
changed: [kafka] => (item=jammy-updates universe)
changed: [kafka] => (item=jammy-security universe)

TASK [Update package cache (Debian/Ubuntu)] ************************************
ok: [kafka]

TASK [Update package cache (RHEL/Fedora/CentOS)] *******************************
skipping: [kafka]

TASK [Install tuned] ***********************************************************
changed: [kafka]

TASK [Ensure tuned service is started and enabled] *****************************
ok: [kafka]

PLAY RECAP *********************************************************************
kafka                      : ok=5    changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0  

```

## Задание 1-3

[03_motd.yml](Task1/03_motd.yml)

Выполняем в консоли 
```
ansible-playbook -i inventory.ini 03_motd.yml --ask-vault-pass
```
Результат
```
PLAY [Task 1.3 - Set custom motd using a variable] *****************************

TASK [Gathering Facts] *********************************************************
ok: [kafka]

TASK [Set /etc/motd] ***********************************************************
changed: [kafka]

PLAY RECAP *********************************************************************
kafka                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
<img width="1208" height="666" alt="image_2026-01-05_17-13-41" src="https://github.com/user-attachments/assets/b9436a4a-b613-4099-af5d-d206fdad468d" />


# Задание 2

Выполните действия, приложите файлы с модифицированным плейбуком и вывод выполнения.

Модифицируйте плейбук из пункта 3, задания 1. В качестве приветствия он должен установить IP-адрес и hostname управляемого хоста, пожелание хорошего дня системному администратору.

# Решение:

## Задание 2-1

[03_motd_dynamic.yml](Task2/03_motd_dynamic.yml)

Выполняем в консоли 
```
ansible-playbook -i inventory.ini 03_motd_dynamic.yml --ask-vault-pass
```
Результат
```
PLAY [Task 2 - Set motd with IP, hostname and a greeting for sysadmin] *********

TASK [Gathering Facts] *********************************************************
ok: [kafka]

TASK [Set /etc/motd (dynamic)] *************************************************
changed: [kafka]

PLAY RECAP *********************************************************************
kafka                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
<img width="1193" height="717" alt="image_2026-01-05_17-14-20" src="https://github.com/user-attachments/assets/f165e16a-7910-43c7-9bed-5559fd11c4a4" />


# Задание 3

Выполните действия, приложите архив с ролью и вывод выполнения.

Ознакомьтесь со статьёй «Ansible - это вам не bash», сделайте соответствующие выводы и не используйте модули shell или command при выполнении задания.

Создайте плейбук, который будет включать в себя одну, созданную вами роль. Роль должна:

Установить веб-сервер Apache на управляемые хосты.
Сконфигурировать файл index.html c выводом характеристик каждого компьютера как веб-страницу по умолчанию для Apache. Необходимо включить CPU, RAM, величину первого HDD, IP-адрес. Используйте Ansible facts и jinja2-template. Необходимо реализовать handler: перезапуск Apache только в случае изменения файла конфигурации Apache.
Открыть порт 80, если необходимо, запустить сервер и добавить его в автозагрузку.
Сделать проверку доступности веб-сайта (ответ 200, модуль uri).
В качестве решения:

предоставьте плейбук, использующий роль;
разместите архив созданной роли у себя на Google диске и приложите ссылку на роль в своём решении;
предоставьте скриншоты выполнения плейбука;
предоставьте скриншот браузера, отображающего сконфигурированный index.html в качестве сайта.

# Решение 

## Задание 3-1
[role](Task3/apache_facts_page_role/roles/apache_facts_page)

site_role.yml [site_role.yml](Task3/apache_facts_page_role/roles/apache_facts_page/site_role.yml)

![photo_2026-01-05_18-13-00](https://github.com/user-attachments/assets/a04bf43b-91e5-4ad6-9b9c-3023c8a48c92)

Выполняем в консоли
```
ansible-playbook -i inventory.ini site_role.yml --ask-vault-pass
```
Результат
```
PLAY [Task 3 - Deploy Apache using role and show host facts page] **************

TASK [Gathering Facts] *********************************************************
ok: [kafka]

TASK [apache_facts_page : Set Apache by OS family] *****************************
ok: [kafka]

TASK [apache_facts_page : Install Apache] **************************************
changed: [kafka]

TASK [apache_facts_page : Gather service facts] ********************************
ok: [kafka]

TASK [apache_facts_page : Open TCP/80 in firewalld] ****************************
skipping: [kafka]

TASK [apache_facts_page : Open TCP/80 in UFW] **********************************
changed: [kafka]

TASK [apache_facts_page : Ensure Apache is enabled and started] ****************
ok: [kafka]

TASK [apache_facts_page : Size primary disk from ansible_devices] **************
ok: [kafka]

TASK [apache_facts_page : Deploy Apache config drop-in] ************************
changed: [kafka]

TASK [apache_facts_page : Enable config on Debian conf-available -> conf-enabled] ***
changed: [kafka]

TASK [apache_facts_page : Deploy index.html] ***********************************
changed: [kafka]

TASK [apache_facts_page : Verify website is reachable locally 200] *************
ok: [kafka]

RUNNING HANDLER [apache_facts_page : Restart Apache] ***************************
changed: [kafka]

PLAY RECAP *********************************************************************
kafka                      : ok=12   changed=6    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```
[role](Task3/apache_facts_page_role/roles/apache_facts_page)
<img width="1258" height="535" alt="image_2026-01-05_17-50-46" src="https://github.com/user-attachments/assets/8b144747-95b6-42ed-80ce-969ab3b5a020" />

