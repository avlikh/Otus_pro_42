# OTUS PRO Homework 42 Dynamic web

## Домашняя работа 42: Динамический web

### Домашнее задание:
**1. Подготовка рабочего места**   
**2. Развернуть стенд:**
* **nginx + php-fpm (wordpress) + python (django) + js(node js)**    
* **деплой через docker-compose**    
Формат сдачи ДЗ - vagrant + ansible  

---
## Выполнение задания:
### 1. Подготовка рабочего места:
Выполнение домашнего задания предполагает, что на компьютере установлен Vagrant+VirtualBox   
**[Как установить Vagrant на Debian 12](https://github.com/avlikh/Install_Vagrant_Debian12/blob/main/README.md)**   

Развернем Vagrant-стенд:
  - Создайте папку с проектом и зайдите в нее (например: /opt/otus/dynamic-web):
```
mkdir -p /opt/otus/dynamic-web ; cd /opt/otus/dynamic-web
```
  - Клонируете проект с Github, набрав команду:
```
apt update -y && apt install git -y ; git clone https://github.com/avlikh/Otus_pro_42.git .
```
  - Запустите проект из папки, в которую склонировали проект (в нашем примере /opt/otus/dynamic-web):
```
vagrant up
```
Результатом выполнения команды vagrant up станет созданная виртуальная машина **Debian12** с запущенными docker-контейнерами.   

---
### 2. Развернуть стенд    
    
**Для начала нарисуем схему стенда:**  

![diagram](https://github.com/user-attachments/assets/03481ee0-c945-40ff-b351-026febc1b6e8)
     
Итак, стенд был развернут.    
**Осталось, только проверить что сервисы работают и доступны:**    
* Проверим доступность Wordpress. Для этого зайдем на порт 8083 хостовой машины через браузер:
![image](https://github.com/user-attachments/assets/84978775-4ec1-404a-9293-cde3819e14a3)
    
* Проверим доступность Django. Для этого зайдем на порт 8081 хостовой машины через браузер:
![image](https://github.com/user-attachments/assets/0781be40-7473-4685-9546-4db4bd0db6cb)
     
* Проверим доступность Django. Для этого зайдем на порт 8082 хостовой машины через браузер: 
![image](https://github.com/user-attachments/assets/ff1fa1c4-d8f0-4fbe-8b72-dd966351a125)
    
**Все 3 сервиса доступны.**
