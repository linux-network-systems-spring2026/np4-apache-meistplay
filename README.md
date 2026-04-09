[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/MdG_xLg_)
# Лабораторная работа №16 (NP4)
## Веб-сервер: практика установки и настройки Apache в Linux

 
> Дисциплина: Linux Network Systems  
> Уровень: базовый + расширенный (optional)

---

## Содержание

- [Лабораторная работа №16 (NP4)](#лабораторная-работа-16-np4)
  - [Веб-сервер: практика установки и настройки Apache в Linux](#веб-сервер-практика-установки-и-настройки-apache-в-linux)
  - [Содержание](#содержание)
  - [1. Цель и результаты обучения](#1-цель-и-результаты-обучения)
  - [2. Задачи лабораторной работы](#2-задачи-лабораторной-работы)
  - [3. Требования к среде](#3-требования-к-среде)
  - [4. Важное замечание про VPS/VDS](#4-важное-замечание-про-vpsvds)
  - [5. Часть 1. Подготовка виртуальной машины](#5-часть-1-подготовка-виртуальной-машины)
    - [1.1 Загрузка Ubuntu Server ISO](#11-загрузка-ubuntu-server-iso)
    - [1.2 Создание VM в VirtualBox](#12-создание-vm-в-virtualbox)
    - [1.3 Сеть: NAT + Host-Only](#13-сеть-nat--host-only)
  - [6. Часть 2. Установка Ubuntu Server и базовых инструментов](#6-часть-2-установка-ubuntu-server-и-базовых-инструментов)
  - [7. Часть 3. Установка Apache2 и проверка страницы по умолчанию](#7-часть-3-установка-apache2-и-проверка-страницы-по-умолчанию)
    - [3.1 Установка Apache](#31-установка-apache)
    - [3.2 Настройка брандмауэра (UFW)](#32-настройка-брандмауэра-ufw)
    - [3.3 Проверка с хостовой машины](#33-проверка-с-хостовой-машины)
  - [8. Часть 4. Изменение порта Apache на 8080](#8-часть-4-изменение-порта-apache-на-8080)
    - [4.1 Изменить `ports.conf`](#41-изменить-portsconf)
    - [4.2 Обновить виртуальный хост по умолчанию](#42-обновить-виртуальный-хост-по-умолчанию)
    - [4.3 Перезапустить Apache](#43-перезапустить-apache)
    - [4.4 Проверка](#44-проверка)
    - [4.5 Возврат к порту 80](#45-возврат-к-порту-80)
  - [9. Часть 5 (дополнительно). Одновременная работа на 80 и 8080](#9-часть-5-дополнительно-одновременная-работа-на-80-и-8080)
    - [5.1 Добавить второй `Listen`](#51-добавить-второй-listen)
    - [5.2 Создать второй vhost для 8080](#52-создать-второй-vhost-для-8080)
  - [10. Часть 6. Локальные домены через hosts](#10-часть-6-локальные-домены-через-hosts)
  - [11. Часть 7. Виртуальные хосты a1.com, b2.com, c3.com](#11-часть-7-виртуальные-хосты-a1com-b2com-c3com)
    - [7.1 Каталоги и `index.html`](#71-каталоги-и-indexhtml)
    - [7.2 Конфигурации Virtual Hosts](#72-конфигурации-virtual-hosts)
    - [7.3 Активация сайтов](#73-активация-сайтов)
    - [7.4 Проверка](#74-проверка)
  - [12. Часть 8. CGI-скрипт Python + ASCII-art](#12-часть-8-cgi-скрипт-python--ascii-art)
    - [8.1 Включить CGI-модуль](#81-включить-cgi-модуль)
    - [8.2 Установить `pip` и `art`](#82-установить-pip-и-art)
    - [8.3 Создать CGI-скрипт](#83-создать-cgi-скрипт)
    - [8.4 (Опционально) CGI вне `/cgi-bin`](#84-опционально-cgi-вне-cgi-bin)
  - [13. Альтернативный режим сети: Bridged Adapter](#13-альтернативный-режим-сети-bridged-adapter)
  - [14. Критерии самопроверки](#14-критерии-самопроверки)
  - [15. Контрольные вопросы](#15-контрольные-вопросы)
  - [Итог](#итог)

---

## 1. Цель и результаты обучения

**Цель:** освоить практику установки, конфигурирования и администрирования Apache в Linux; понять базовые принципы виртуального хостинга и выполнения серверных скриптов.

**После выполнения работы студент умеет:**
- устанавливать Apache2 через `apt`;
- проверять доступность веб-сервера по сети;
- менять порт прослушивания и откатывать изменения;
- настраивать несколько виртуальных хостов на одном сервере;
- запускать CGI-сценарий Python в Apache;
- применять те же операции в реальной VPS-среде.

---

## 2. Задачи лабораторной работы

1. Установить Apache2.
2. Проверить стандартную страницу Apache с хостовой машины.
3. Изменить порт с `80` на `8080` и убедиться, что сервер работает.
4. *(Дополнительно)* Включить одновременную работу на `80` и `8080`.
5. Добавить в `hosts` записи: `a1.com`, `b2.com`, `c3.com`.
6. Проверить разрешение имён через `ping`.
7. Создать отдельные каталоги сайтов и уникальные `index.html`.
8. Настроить Virtual Hosts для каждого домена.
9. Включить `cgid`, установить Python-библиотеку `art`, выполнить CGI-скрипт с ASCII-артом.

---

## 3. Требования к среде

- ПК с Windows, RAM от 4 ГБ (рекомендуется 8 ГБ), свободно от 15 ГБ.
- Oracle VirtualBox (актуальная версия).
- Интернет-соединение.
- Базовые навыки CLI и редактирования текстовых файлов.

---

## 4. Важное замечание про VPS/VDS

Все команды и конфигурации в этой работе соответствуют реальному администрированию VPS.  
Разница только в доступе:
- в облаке вы получаете готовый IP и SSH-доступ;
- в лабораторной среде сервер разворачивается локально в VirtualBox.

---

## 5. Часть 1. Подготовка виртуальной машины

### 1.1 Загрузка Ubuntu Server ISO

1. Перейдите на [ubuntu.com/download/server](https://ubuntu.com/download/server).
2. Скачайте **Ubuntu Server 24.04 LTS**.
3. Сохраните ISO (например, в «Загрузки»).

### 1.2 Создание VM в VirtualBox

Параметры:
- **Имя:** `Ubuntu-Apache-Lab`
- **ISO:** загруженный Ubuntu Server
- **Тип:** `Linux`
- **Версия:** `Ubuntu (64-bit)`
- **Опция:** `Skip Unattended Installation`
- **RAM:** `2048 MB`
- **Диск:** `15–20 GB`, динамический

### 1.3 Сеть: NAT + Host-Only

Нужно два адаптера:
- **Adapter 1 = NAT** (интернет в VM).
- **Adapter 2 = Host-Only** (доступ к серверу с Windows-хоста).

Почему так: NAT удобен для исходящих подключений VM, Host-Only даёт изолированный прямой доступ хост ↔ VM.

---

## 6. Часть 2. Установка Ubuntu Server и базовых инструментов

Пройдите мастер установки Ubuntu Server:
- `Try or Install Ubuntu Server`
- `Continue without updating`
- стандартный `Ubuntu Server` (не minimized)
- OpenSSH: **включить** `Install OpenSSH server`
- после установки: `Reboot Now`

После входа в систему:

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install net-tools isc-dhcp-client -y
```
- SSH‑сервер уже должен быть установлен и запущен. Проверим его статус:
```bash
sudo systemctl status ssh
```
Если служба не активна, запустите и добавьте в автозагрузку:
```bash
sudo systemctl enable --now ssh
```
```bash
ip -br link
ifconfig enp0s8
sudo ip link set enp0s8 up
sudo dhclient -v enp0s8
ip addr show | grep inet
```

Зафиксируйте IP интерфейса `enp0s8` (обычно `192.168.56.x`) — он нужен в последующих шагах.

---

## 7. Часть 3. Установка Apache2 и проверка страницы по умолчанию

### 3.1 Установка Apache

```bash
sudo apt install apache2 -y
sudo systemctl status apache2
```


###  3.2 Настройка брандмауэра (UFW)
UFW (Uncomplicated Firewall) — простой интерфейс для управления правилами iptables.
Просмотрим доступные профили приложений:
```bash
sudo ufw app list
```
Должны отображаться Apache, Apache Full, Apache Secure и OpenSSH.
Разрешим только необходимый трафик:
```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Apache Full'   # Разрешает и HTTP (80), и HTTPS (443)
```
Включим брандмауэр:
```bash
sudo ufw enable
```
Проверим статус:

```bash
sudo ufw status
```
Пример вывода:
text
Status: active

To                |         Action |    From
--                |         ------   |   ----
OpenSSH          |          ALLOW   |    Anywhere
Apache Full                ALLOW     |  Anywhere
OpenSSH (v6)     |          ALLOW    |   Anywhere (v6)
Apache Full (v6) |          ALLOW    |   Anywhere (v6)



### 3.3 Проверка с хостовой машины

Откройте в браузере:

```text
http://<IP_ВМ>
```

Должна открыться страница **Apache2 Ubuntu Default Page**.

*(Опционально в VM):*
```bash
sudo apt install lynx -y
lynx http://127.0.0.1
```

---

## 8. Часть 4. Изменение порта Apache на 8080

### 4.1 Изменить `ports.conf`

```bash
sudo nano /etc/apache2/ports.conf
```

Заменить:

```text
Listen 80
```

на:

```text
Listen 8080
```

### 4.2 Обновить виртуальный хост по умолчанию

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Заменить:

```text
<VirtualHost *:80>
```

на:

```text
<VirtualHost *:8080>
```

### 4.3 Перезапустить Apache

```bash
sudo systemctl restart apache2
```

### 4.4 Проверка

```text
http://<IP_ВМ>:8080
```

### 4.5 Возврат к порту 80

Сделайте обратные замены в:
- `/etc/apache2/ports.conf`
- `/etc/apache2/sites-available/000-default.conf`

И перезапустите:

```bash
sudo systemctl restart apache2
```

---

## 9. Часть 5 (дополнительно). Одновременная работа на 80 и 8080

### 5.1 Добавить второй `Listen`

```bash
sudo nano /etc/apache2/ports.conf
```

Должно быть:

```text
Listen 80
Listen 8080
```

### 5.2 Создать второй vhost для 8080

```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/001-default-8080.conf
sudo nano /etc/apache2/sites-available/001-default-8080.conf
```

Изменить:

```text
<VirtualHost *:80>
```

на:

```text
<VirtualHost *:8080>
```

Активировать:

```bash
sudo a2ensite 001-default-8080.conf
sudo systemctl restart apache2
```

Проверить:
- `http://<IP_ВМ>`
- `http://<IP_ВМ>:8080`

Если есть ошибка:

```bash
sudo tail -f /var/log/apache2/error.log
```

---

## 10. Часть 6. Локальные домены через hosts

На Windows (Notepad от имени администратора) откройте:

```text
C:\Windows\System32\drivers\etc\hosts
```

Добавьте:
>замените на свой IP-адрес
```text 
192.168.56.103  a1.com
192.168.56.103  www.a1.com
192.168.56.103  b2.com
192.168.56.103  www.b2.com
192.168.56.103  c3.com
192.168.56.103  www.c3.com
```

> Замените IP на фактический IP вашей VM.

Проверка:

```text
ping a1.com
ping b2.com
ping c3.com
```

На этом этапе в браузере `http://a1.com` ещё может открывать дефолтный сайт Apache — это ожидаемо до настройки Virtual Hosts.

---

## 11. Часть 7. Виртуальные хосты a1.com, b2.com, c3.com

### 7.1 Каталоги и `index.html`

```bash
sudo mkdir -p /var/www/a1.com
sudo mkdir -p /var/www/b2.com
sudo mkdir -p /var/www/c3.com

sudo chown -R $USER:$USER /var/www/a1.com
sudo chown -R $USER:$USER /var/www/b2.com
sudo chown -R $USER:$USER /var/www/c3.com

echo '<h1>Welcome to a1.com!</h1><p>This site is located in the directory /var/www/a1.com.</p>' > /var/www/a1.com/index.html
echo '<h1>Welcome to b2.com!</h1><p>This site is located in the directory /var/www/b2.com.</p>' > /var/www/b2.com/index.html
echo '<h1>Welcome to c3.com!</h1><p>This site is located in the directory /var/www/c3.com.</p>' > /var/www/c3.com/index.html
```

### 7.2 Конфигурации Virtual Hosts

Создайте `/etc/apache2/sites-available/a1.com.conf`:

```apache
<VirtualHost *:80>
    ServerName a1.com
    ServerAlias www.a1.com
    DocumentRoot /var/www/a1.com
    ErrorLog ${APACHE_LOG_DIR}/a1.com_error.log
    CustomLog ${APACHE_LOG_DIR}/a1.com_access.log combined
</VirtualHost>
```

Создайте `/etc/apache2/sites-available/b2.com.conf`:

```apache
<VirtualHost *:80>
    ServerName b2.com
    ServerAlias www.b2.com
    DocumentRoot /var/www/b2.com
    ErrorLog ${APACHE_LOG_DIR}/b2.com_error.log
    CustomLog ${APACHE_LOG_DIR}/b2.com_access.log combined
</VirtualHost>
```

Создайте `/etc/apache2/sites-available/c3.com.conf`:

```apache
<VirtualHost *:80>
    ServerName c3.com
    ServerAlias www.c3.com
    DocumentRoot /var/www/c3.com
    ErrorLog ${APACHE_LOG_DIR}/c3.com_error.log
    CustomLog ${APACHE_LOG_DIR}/c3.com_access.log combined
</VirtualHost>
```

### 7.3 Активация сайтов

```bash
sudo a2ensite a1.com.conf
sudo a2ensite b2.com.conf
sudo a2ensite c3.com.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

### 7.4 Проверка

- `http://a1.com`
- `http://b2.com`
- `http://c3.com`

Каждый домен должен показывать свой контент.

---

## 12. Часть 8. CGI-скрипт Python + ASCII-art

### 8.1 Включить CGI-модуль

```bash
sudo a2enmod cgid
sudo systemctl restart apache2
```

### 8.2 Установить `pip` и `art`

```bash
sudo apt install python3-pip -y
sudo pip3 install --break-system-packages art
```

### 8.3 Создать CGI-скрипт

Создайте файл `/usr/lib/cgi-bin/test.py`:

```python
#!/usr/bin/python3
from art import *

Art = text2art("TEST", font='block', chr_ignore=True)
print('Content-Type: text/plain')
print('')
print('This is my test!')
print(Art)
```

Выдайте права:

```bash
sudo chmod 755 /usr/lib/cgi-bin/test.py
```

Проверка: 
> install curl in windows
> winget install curl

```text
curl -H "Host: a1.com" http://192.168.56.103/cgi-bin/test.py
```

Если виден исходный код вместо результата, проверьте:
- `cgid` включён;
- корректный shebang `#!/usr/bin/python3`;
- права `chmod 755`.

### 8.4 (Опционально) CGI вне `/cgi-bin`

Можно настроить выполнение CGI в `DocumentRoot` через `Options +ExecCGI` и `AddHandler cgi-script .py`, но в рамках базовой работы это не требуется.

---

## 13. Альтернативный режим сети: Bridged Adapter

Если нужен доступ к VM с других устройств локальной сети:

1. Выключите VM: `sudo shutdown now`.
2. VirtualBox → **Settings → Network → Adapter 2**.
3. Смените `Host-Only` на `Bridged Adapter`.
4. Выберите физический сетевой интерфейс.
5. Запустите VM и используйте новый IP от роутера (например, `192.168.1.100`).

---

## 14. Критерии самопроверки

Лабораторная выполнена корректно, если:
- Apache установлен и активен;
- страница доступна по IP;
- порт `8080` работает после изменения конфигурации;
- (optional) одновременно работают `80` и `8080`;
- `a1.com`, `b2.com`, `c3.com` резолвятся через `hosts`;
- каждый домен отдаёт собственный `index.html`;
- CGI-скрипт выполняется и выводит ASCII-art.

---

## 15. Контрольные вопросы

1. **Что такое веб-сервер и зачем он нужен?**  
   Веб-сервер принимает HTTP-запросы клиентов и возвращает веб-ресурсы (HTML, изображения, стили, скрипты).

2. **Что такое рабочая директория веб-сервера?**  
   `DocumentRoot` — каталог с файлами сайта (по умолчанию в Apache: `/var/www/html`).

3. **Где основные настройки Apache?**
   - `/etc/apache2/apache2.conf`
   - `/etc/apache2/ports.conf`
   - `/etc/apache2/sites-available/`
   - `/etc/apache2/mods-available/`

4. **Что такое виртуальные хосты и как их настраивать?**  
   Позволяют одному Apache обслуживать несколько доменов; настраиваются отдельными файлами `<VirtualHost>` + `ServerName` + `DocumentRoot`, активируются `a2ensite`.

5. **Что такое `hosts` и где расположен?**
   - Windows: `C:\Windows\System32\drivers\etc\hosts`
   - Linux: `/etc/hosts`  
   Используется для локального сопоставления домена и IP без DNS.

6. **Базовая структура HTML-файла**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Заголовок страницы</title>
</head>
<body>
  Содержимое страницы
</body>
</html>
```



---

## Итог

В этой лабораторной работе вы прошли путь от нулевой установки Apache до многосайтовой конфигурации и выполнения серверного Python-скрипта. Полученный набор команд и практик напрямую переносится на реальные VPS-сценарии администрирования.
