# OTUS PRO Homework 42 Dynamic web

## Домашняя работа 42: Динамический web

### Домашнее задание:
**1. Подготовка рабочего места**   
**2. Развернуть стенд:
* **nginx + php-fpm (wordpress) + python (django) + js(react/angular)**    
**3. На web поднимаем nginx**   
**4. На log настраиваем центральный лог сервер на любой системе на выбор**
* journald;   
* rsyslog;   
* elk.

**5. Настраиваем аудит, следящий за изменением конфигов Nginx**   
Все критичные логи с web должны собираться и локально и удаленно.   
Все логи с nginx должны уходить на удаленный сервер (локально только критичные).   
Логи аудита должны также уходить на удаленную систему.   
   
Формат сдачи ДЗ - vagrant + ansible  

---
## Выполнение задания:
### 1. Подготовка рабочего места:
Выполнение домашнего задания предполагает, что на компьютере установлен Vagrant+VirtualBox   
**[Как установить Vagrant на Debian 12](https://github.com/avlikh/Install_Vagrant_Debian12/blob/main/README.md)**   

Развернем Vagrant-стенд:
  - Создайте папку с проектом и зайдите в нее (например: /opt/otus/log):
```
mkdir -p /opt/otus/log ; cd /opt/otus/log
```
  - Клонируете проект с Github, набрав команду:
```
apt update -y && apt install git -y ; git clone https://github.com/avlikh/Otus_pro_25.git .
```
  - Запустите проект из папки, в которую склонировали проект (в нашем примере /opt/otus/log):
```
vagrant up
```
Результатом выполнения команды vagrant up станет созданные 2 виртуальных машины **web** и **log**, с **Debian12**.   

---
### 3. На web поднимаем nginx**   
### 4. На log настраиваем центральный лог сервер на любой системе на выбор**   
* rsyslog;   
### 5. Настраиваем аудит, следящий за изменением конфигов Nginx**

**При развертывании стенда были выполнены все необходимые настройки через Ansible**  
Нам остается только проверить что все корректно работает.   
Для этого зайдем на машину **web** и повысим свои полномочия до root:
```
vagrant ssh web
```
```
sudo -i
```
**Зайдем на nginx 5 раз:**
```
for i in {1..5}; do curl localhost; done
```
**Сгенерируем ошибку nginx:**
* для этого переместим индексную страницу в папку /var/www/
```
mv /var/www/html/index.nginx-debian.html /var/www/
```
* Зайдем на nginx сервер еще 2 раза:
```
curl localhost && curl localhost
```
* Вернем индексную страницу nginx обратно:
```
mv /var/www/index.nginx-debian.html /var/www/html/
```
**Создадим и удалим файл с именем 'test' в каталоге /etc/nginx:**
```
touch /etc/nginx/test; rm /etc/nginx/test
```
В результате с сервера **web** на rsyslog сервера **log** будет переданы логи nginx (access и error) и auditd (логи auditd записи в папку /etc/nginx)  
Проверим, что сервер log корректно принял логи и разместил из в папке **/var/log/rsyslog/web**  
Для этого зайдем на машину **web** и повысим свои полномочия до root:
```
vagrant ssh web
```
```
sudo -i
```
Проверим что в папке /var/log/rsyslog/web создались 3 файла логов: nginx_access.log, nginx_error.log и auditd.log
```
ls -la /var/log/rsyslog/web
```
<details>
<summary> результат выполнения команды: </summary>

```
drwxr-xr-x 2 root root 4096 Feb 21 15:13 .
drwxr-xr-x 4 root root 4096 Feb 21 15:08 ..
-rw-r----- 1 root adm   989 Feb 21 15:13 auditd.log
-rw-r----- 1 root adm   910 Feb 21 15:08 nginx_access.log
-rw-r----- 1 root adm   420 Feb 21 15:08 nginx_error.log
```
</details>

Посмотрим access-логи Nginx:

```
cat /var/log/rsyslog/web/nginx_access.log
```

<details>
<summary> результат выполнения команды: </summary>

```
2025-02-21T15:08:00+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:00 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1"
2025-02-21T15:08:00+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:00 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1"
2025-02-21T15:08:00+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:00 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1"
2025-02-21T15:08:00+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:00 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1"
2025-02-21T15:08:00+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:00 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.88.1"
2025-02-21T15:08:08+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:08 +0000] "GET / HTTP/1.1" 403 153 "-" "curl/7.88.1"
2025-02-21T15:08:08+00:00 web nginx_access: 127.0.0.1 - - [21/Feb/2025:15:08:08 +0000] "GET / HTTP/1.1" 403 153 "-" "curl/7.88.1"
```
</details>

Посмотрим error-логи Nginx:

```
cat /var/log/rsyslog/web/nginx_error.log
```

<details>
<summary> результат выполнения команды: </summary>

```
2025-02-21T15:08:08+00:00 web nginx_error: 2025/02/21 15:08:08 [error] 2576#2576: *6 directory index of "/var/www/html/" is forbidden, client: 127.0.0.1, server: _, request: "GET / HTTP/1.1", host: "localhost"
2025-02-21T15:08:08+00:00 web nginx_error: 2025/02/21 15:08:08 [error] 2576#2576: *7 directory index of "/var/www/html/" is forbidden, client: 127.0.0.1, server: _, request: "GET / HTTP/1.1", host: "localhost"
```
</details>

Посмотрим audit-логи auditd (внесение изменений в папке /etc/nginx)

```
cat /var/log/rsyslog/web/auditd.log
```

<details>
<summary> результат выполнения команды: </summary>

```
2025-02-21T15:13:59+00:00 web auditd type=SYSCALL msg=audit(1740150839.542:193): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7ffce16937ed a2=941 a3=1b6 items=2 ppid=2647 pid=2673 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts1 ses=5 comm="touch" exe="/usr/bin/touch" subj=unconfined key="nginx-config-change"#035ARCH=x86_64 SYSCALL=openat AUID="vagrant" UID="root" GID="root" EUID="root" SUID="root" FSUID="root" EGID="root" SGID="root" FSGID="root"
2025-02-21T15:13:59+00:00 web auditd type=SYSCALL msg=audit(1740150839.554:194): arch=c000003e syscall=263 success=yes exit=0 a0=ffffff9c a1=5565aca80bf0 a2=0 a3=7f45d3df8f80 items=2 ppid=2647 pid=2674 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts1 ses=5 comm="rm" exe="/usr/bin/rm" subj=unconfined key="nginx-config-change"#035ARCH=x86_64 SYSCALL=unlinkat AUID="vagrant" UID="root" GID="root" EUID="root" SUID="root" FSUID="root" EGID="root" SGID="root" FSGID="root"
```
</details>

Как мы видим nginx и rsyslog сервера **web** корректно отправляют логи на rsyslog сервера **log**.
