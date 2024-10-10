# Установка панели 3x-ui. Важные дополнения 
>Разработчики постоянно обновляют панель 3x-ui и чтобы не писать каждый раз новое видео я буду выкладывать здесь все исправления. Если что-то не работает - сначала читаем этот текст, потом идем в камменты за помощью.

## Если что-то пошло не так.
Если что-то не работает, панель всегда можно снести и настроить заново. Для этого в терминале введите следующую команду:
```sh
sudo x-ui
```
После чего выберите пункт **Uninstall**. Панель будет удалена с сервера и ее можно будет заново установить на тот же сервер.

![Удаление панели](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/3x-ui/Uninstall.jpg)

## Установка панели.
При установке панели есть некоторые изменения. Разработчики упростили и одновременно обезопасили установку панели. Все делается как в видео, но теперь не нужно придумывать логин и пароль и разработчики добавили WebBasePath. WebBasePath нужен для того чтобы скрыть стандартный адрес панели управления. Основные шаги делаем как в видео. Вводим команду:
```sh
apt update && apt upgrade -y
```
Далее вводим команду установки панели из гитхаба разработчиков ([ссылка на гитхаб разработчиков](https://github.com/MHSanaei/3x-ui?ysclid=lstfiw45v6910325792)):
```sh
bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)
```
У вас может появится вот такое окно. Просто жмите Enter.
![Окно на розовом фоне](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/3x-ui/pink-display.jpg)

Далее так же как в видео вы задаете порт панели, и все происходит автоматически: создается логин и пароль юзера и добавляется такой параметр как **WebBasePath**.

![Процесс установки](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/3x-ui/webbasepath.jpg)

 Чтобы попасть в панель, нужно ввести в адресную строку браузера
 ```sh
http:// 111.222.333.444:port/WebBasePath
```
Где 111.222.333.444 - ip адрес вашего сервера, port - порт который вы ввели (желательно использовать цифры в диапазоне от 4000 до 65000), WebBasePath - то что указано в выводе команды установки панели, в моем примере это:
```sh
http://111.222.333.444:65000/AFmRdR9PzKSXjcU
```
Далее просто вводите логин и пароль из того же вывода команды установки панели.
Если захотите поменять логин и пароль, это можно сделать в левом меню - Настройки панели - Настройки безопасности. Но пароль лучше оставить тот что есть: он достаточно сложен чтобы его невозможно было подобрать.

## Если вроде все работает, но ip не меняется.
В некоторых случаях при запуске на ПК пропадает соединение, либо не происходит смены ip. То есть значок V2rayN загорается красным, вроде подключение есть но ip не меняется. Это связанно с тем что некоторые расширения браузера начинают конфликтовать с клиентом с которого идет подключение. Просто попробуйте другой браузер - все должно работать.

Если никаких дополнений в браузере нет, то есть два варианта: включить ползунок "Режим ВПН", тогда весь трафик системы будет идти через ВПН.

![Включить впн](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/3x-ui/vpn-mode.jpg)

Либо в настройках операционной системы указать использование системных прокси. Такие браузеры как Firefox используют их по умолчанию, а вот например Google Chrome не использует, так что идем в настройки и ручками указываем использование прокси.
- Отрываем гугл хром и идем в настройки.
- В поле "Поиск настроек" вбиваем слово "прокси"
- Жмем "Открыть настройки прокси сервера для компьютера"
- И делаем как на скриншоте: 127.0.0.1 - это адрес локального хоста, с него идет переадресация трафика на сервер. 10809 - это порт на котором работает прокси на локальном хосте. Этот порт можно узнать в V2rayN в левом нижнем углу (см предыдущий скриншот)

![Настройка прокси в системе](https://raw.githubusercontent.com/ServerTechnologies/youtube-instructions/refs/heads/main/screenshots/3x-ui/proxy-settings.jpg)

## Если не работает Дискорд.
В V2rayN включите ползунок "Режим ВПН" или см. предыдущий пункт.

## Если не работает WhatsApp на ios.
В Streisand настройки - Туннель - ip settings - выбрать только ipv4

## Настройка ssh-ключей и общая безопасность сервера.
Есть видеоурок по безопасности сервера, где я показываю как установить ключи и многое другое:
https://youtu.be/SaVi8Mw4Ci8

## Это соединение не защищено, настройка ssh-ключей и общая безопасность сервера.
Если вы видите окно с предупреждением "Это соединение не защищено. Пожалуйста, воздержитесь от ввода конфиденциальной информации до тех пор, пока не будет активирован TLS для защиты данных...". Это потому что трафик по умолчанию. идет по http, его надо переключить на https.
По решению этой проблемы есть целое видео, в нем информация по общей безопасности сервера, настройки ssh ключей и подключению TLS сертификата с помощью домена (стоит примерно $2-15 за год, бесплатный вариант с freemyip, предложенный в видео, перестал работать в России):
https://youtu.be/SaVi8Mw4Ci8
И еще оно видео только про TLS сертификаты (если нет возможности или желания приобретать домен, вместо freemyip):
https://youtu.be/uQiE3JWE2ig
## Клиенты для разный разных операционных систем.
### Windows
V2RayN:
https://github.com/2dust/v2rayN/releases

### MacOS
V2Box
FoxRay

### IOS
Streisand
https://apps.apple.com/ru/app/streisand/id6450534064

### Android
V2rayNG
Устанавливается из Google Play, очень сильно похож на Nekobox по функционалу, лучше использовать его.
https://play.google.com/store/apps/details?id=com.v2ray.ang

NekoBox:
https://github.com/MatsuriDayo/NekoBoxForAndroid/releases
