# Установка и настройка панели управления Marzban
Marzban — это инструмент управления прокси-серверами, который позволит вам без знаний программирования создать собственный ВПН (прокси) сервер на базе Xray-core с удобный графическим интерфейсом.

> В этом документе будут важные обновления, касающиеся установки и настройки Marzban. Если что-то не работает или процесс установки отличается от того что вы видели в видео - тут будет актуальная инструкция.

## VPS для панели.
Для установки панели нам понадобится VPS сервер. Приобрести его можно в <a href="https://bit.ly/3rOqvPE)" target="_blank">ishosting (ссылка)</a>.
В сервисе доступны 36+ локаций. Если вам не нужна какая-то конкретная страна, то выбирайте ту что ближе к вам.

## Если что-то пошло не так.
Если что-то перестало работать, Marzban всегда можно полностью удалить и установить заново, для этого в терминале введите следующую команду:
```sh
marzban uninstall
```
Она полностью удалит Marzban  с сервера. И вы сможете сделать все заново.

## Подготовка сервера, подключение создание и подключение поддомнена.
Для доступа к дашборду панели по защищенному протоколу https нам нужно подключить домен или поддомен и создать ssl сертификат. Бесплатный поддомен можно получить на сервисе DuckDNS <a href="https://www.duckdns.org/" target="_blank">(ссылка)</a>.

В поле "sub domain" вставляете любое слово или рандомный набор латинских букв, вообще не важно какой, жмете add domain. После этого для вас будет создан поддомен вида youword.duckdns.org, где "youword" - то слово или те буквы, которые вы ввели.

![Удаление Marzban](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/marzban/duck-dns.jpg)


В это поле вставляете ip адрес вашего сервера и нажимаете update ip.

## Создание SSL сертификатов.
Нужно установить несколько важных пакетов, для этого введите следующие команды:
``` sh
apt update
apt install curl
apt install socat
```
Далее скачиваем утилиту, которая автоматически создаст нам сертификат после запуска. В этой строчке надо заменить "your@mail.com" на ваш эмейл адрес. Можно написать что угодно, никаких кодов подтверждения туда не приходит.

```sh
curl https://get.acme.sh | sh -s email=your@mail.com
```
Далее создаем переменную в которой будет хранится ваш поддомен:
```sh
export DOMAIN=youword.duckdns.org
```
И запускаем утилиту, которая создаст сертификат:
```sh
mkdir -p /var/lib/marzban/certs

~/.acme.sh/acme.sh \
  --issue --force --standalone -d "$DOMAIN" \
  --fullchain-file "/var/lib/marzban/certs/$DOMAIN.cer" \
  --key-file "/var/lib/marzban/certs/$DOMAIN.cer.key"
 ```
 После завершения работы утилиты в папке /var/lib/marzban/certs/ будут созданы два файла с именами youword.duckdns.org.cer и youword.duckdns.org.cer.key - это и есть файлы сертификата.
 
 ## Устанавливаем панель Marzban
 Делается это всего одной командой:
 ```sh
 sudo bash -c "$(curl -sL https://github.com/Gozargah/Marzban-scripts/raw/master/marzban.sh)" @ install
 ```
После завершения работы скрипта установки, вы увидите логи работы Marzban, просто нажмите CTRL+C чтобы прекратить их отображение.

## Подключаем SSL сертификат
Так как доступ к дашборду Marzban возможен только через защищенное соединение, нам необходимо подключить только что созданные сертификаты для перехода сайта панели на https. Для этого открываем и редактируем файл .env:
```sh
nano /opt/marzban/.env
```
 Там надо найти следующие строчки, убрать перед ними знак # и вставить путь до файлов сертификата и указать адрес сайта панели:
 ```sh
UVICORN_SSL_CERTFILE = "/var/lib/marzban/certs/youword.duckdns.org.cer"
UVICORN_SSL_KEYFILE = "/var/lib/marzban/certs/youword.duckdns.org.cer.key"
XRAY_SUBSCRIPTION_URL_PREFIX = "https://youword.duckdns.org"
```
Обратите внимание на первые две строчки, часть "/var/lib/marzban/certs/" трогать не нужно, это путь до папки с файлами сертификатов, "youword.duckdns.org" нужно заменить на ваш поддомен (или домен, если он у вас есть). В первой строчке файл имеет расширение .cer, во второй строчке он имеет расширение .cer.key. Чтобы сохранить изменения и выйти из  редактора нажмите CTRL+X, потом y и Enter. \
Если вы забыли имена сертефикатов, то их можно посмотреть этой командой:
```sh
ls /var/lib/marzban/certs/
```

## Завершение настройки сервера.
Перезапустите Marzban 
```sh
marzban restart
```
И создайте логин и пароль пользователя панели:
```sh
marzban cli admin create --sudo
```

Чтобы подключится к панели, введите в адресной строке браузера
```sh
https://youword.duckdns.org:8000/dashboard/
```
https:// в начале обязательно, "youword.duckdns.org" надо заменить на имя вашего поддомена (или домена).

## Настройка подключений.
По умолчанию в Marzban доступно создание подключений только по протоколу shadowsocks. Для того чтобы добавить возможность создания подключений по другим протоколам, в правом верхнем углу нажмите на значок шестеренки, откроются настройки подключений, удалите все из поля "Конфигурация" и вставьте туда слудующий код:
```sh
{
  "log": {
    "loglevel": "warning"
  },
  "routing": {
    "rules": [
      {
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "BLOCK",
        "type": "field"
      }
    ]
  },
  "inbounds": [
    {
      "tag": "VMESS TCP NOTLS",
      "listen": "0.0.0.0",
      "port": 445,
      "protocol": "vmess",
      "settings": {
        "clients": []
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "none"
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "TROJAN TCP NOTLS",
      "listen": "0.0.0.0",
      "port": 444,
      "protocol": "trojan",
      "settings": {
        "clients": []
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "none"
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "VLESS TCP REALITY",
      "listen": "0.0.0.0",
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "tcp",
        "tcpSettings": {},
        "security": "reality",
        "realitySettings": {
          "show": false,
          "dest": "github.com:443",
          "xver": 0,
          "serverNames": [
            "github.com"
          ],
          "privateKey": "вставить сюда privateKey",
          "shortIds": [
            "вставить сюда shortIds"
          ]
        }
      },
      "sniffing": {
        "enabled": true,
        "destOverride": [
          "http",
          "tls",
          "quic"
        ]
      }
    },
    {
      "tag": "Shadowsocks TCP",
      "listen": "0.0.0.0",
      "port": 1080,
      "protocol": "shadowsocks",
      "settings": {
        "clients": [],
        "network": "tcp,udp"
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "tag": "DIRECT"
    },
    {
      "protocol": "blackhole",
      "tag": "BLOCK"
    }
  ]
}
```
Этот код даст возможность создавать подключения по протоколам VLESS, TROJAN, VMESS и Shadowsocks.
Долее нажмите "Сохранить" и "Перезагрузить ядро", закройте окно настроек и перезагрузите страницу. Теперь можно создавать ключи доступа и подключать устройства.
Сейчас стабильно во всех регионах и на всех устройствах работают VLESS и TROJAN, поэтому я рекомендую использовать один из них. \

При комаровании этого куска кода в блоке который отвечает за протокол VLESS TCP REALITY нужно указать параметры privateKey и shortIds. Для получения этих параметров спользуйте две команды:
```sh
docker exec marzban-marzban-1 xray x25519

openssl rand -hex 8
```

## Клиенты для разный разных операционных систем.
### Windows
V2RayN:\
https://github.com/2dust/v2rayN/releases

### MacOS
V2Box\
FoxRay

### Linux
Nekoray\
https://github.com/MatsuriDayo/nekoray/releases

### IOS
Streisand:\
https://apps.apple.com/ru/app/streisand/id6450534064

### Android
V2rayNG\
Устанавливается из Google Play, очень сильно похож на Nekobox по функционалу, лучше использовать его.\
https://play.google.com/store/apps/details?id=com.v2ray.ang

NekoBox:\
https://github.com/MatsuriDayo/NekoBoxForAndroid/releases

## Полезные ссылки:
Официальный гитхаб проекта Marzban:\
https://github.com/Gozargah/Marzban/tree/master\

Документация Marzban:\
https://gozargah.github.io/marzban/en/docs/introduction

Поддержите наш проект: