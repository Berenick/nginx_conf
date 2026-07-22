# Структура Репозитория
systemd_networkd - Папка с конфигурацией systemd_networkd для статических ip адресов /etc/systemd/network  
sites-enabled - Конфигурация nginx в http пространстве  
nginx.conf - Основной конфигурационный файл nginx  


# Подготовка хоста к работе

## Ручные действия:
- Установка ssh сервера 
- Создание пользователя Ansible 

## Автоматизация

### Настройка сети  

#### Задаем конфигурацию /etc/systemd/networkd для выдачи статических адресов вне DHCP
Копируем конфигурационные файлы  
sudo systemctl reload systemd-networkd - перезагрузка конфигурации

#### Задаем параметры ядра для сетевой нагрузки|базы данных

### Деплой nginx
#### Структура папки /app/nginx
[drwxr-xr-x]  .  
└── [drwxr-xr-x]  nginx  
    ├── [lrwxrwxrwx]  libs -> /var/lib/nginx  
    ├── [lrwxrwxrwx]  logs -> /var/log/nginx  
    ├── [lrwxrwxrwx]  nginx -> /etc/nginx  
    ├── [drwxr-xr-x]  nginx_conf [git]  
    │   ├── [-rw-r--r--]  nginx.conf  
    │   ├── [-rw-r--r--]  README.MD  
    │   └── [drw-r--r--]  sites-enabled  
    │       └── [-rw-r--r--]  default  
    ├── [lrwxrwxrwx]  nginx.pid -> /run/nginx.pid  
    └── [drwxr-xr-x]  www - статика  

#### Создаем бэкап предыдущей конфигурации

#### Создаем директорию для статики
sudo mkdir -p /app/nginx/www

#### В /app/nginx/www добавляем статику

#### Копируем репозиторий гита в /app/nginx
git clone <link to repo>

#### Создаем симлинк из основного файла конфигурации
ln -s /app/nginx/nginx.conf /etc/nginx/nginx.conf

#### Создание симлинков для администрирования:
sudo ln -s /var/log/nginx /app/nginx/logs  
sudo ln -s /var/lib/nginx /app/nginx/libs  
sudo ln -s /run/nginx.pid /app/nginx/nginx.pid  
sudo ln -s /etc/nginx /app/nginx/nginx  

#### Создание пользователя и группы nginx
sudo groupadd --system nginx  
sudo useradd -r -g nginx -s /usr/sbin/nologin -M nginx  

#### Настройка прав доступа
sudo chmod 755 /app/nginx/nginx_conf /app/nginx/www  
sudo chown -R root:root /app/nginx/nginx_conf /app/nginx/www  
sudo chmod 644 /app/nginx/nginx_conf/* /app/nginx/www/*  
sudo chown -R nginx:nginx /var/log/nginx  
sudo chmod 750 /var/log/nginx  
sudo chmod 640 /var/log/nginx/*  
sudo chown -R nginx:nginx /var/lib/nginx  
sudo chmod 755 /var/lib/nginx  

#### Проверка конфигурации nginx
nginx -t

#### Запуск и включение nginx в автозагрузку
sudo systemctl enable nginx.service
sudo systemctl start nginx.service

#### Настройка Logrotate

>[!NOTE]
> Переписать через find


