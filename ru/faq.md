# OpenIPC Wiki
[Оглавление](../index.md)

Вопросы и ответы
----------------

### Как настроить авторизацию ssh-сессии по ключу

__На камере__: Откройте ssh сессию и создайте непустой пароль пользователю root. По умолчанию в нашей прошивке у пользователя root пароля нет.
Имейте в виду, что после того как вы создали пароль, все последующие новые ssh сессии до момента настройки авторизации по публичному ключу,
а так же при попытке захода с компьютера, где такого ключа нет, будут требовать авторизации именно с этим паролем. Не забудьте его!
```
passwd
```

__На десктопе__: Скопируйте публичный ключ на камеру, авторизовавшись созданным выше паролем.
```
ssh-copy-id root@192.168.1.666
```

__На камере__: Создайте папку `.ssh` в домашней директории пользователя root и скопируйте в неё файл с хранилищем авторизованных ключей.
```
mkdir ~/.ssh
cp /etc/dropbear/authorized_keys ~/.ssh/
```

__На десктопе__: Откройте новую сессию, чтобы проверить, что авторизация проходит по открытому ключу, без участия пароля.
```
ssh root@192.168.1.666
```

### Majestic

#### Как получить дамп памяти для отладки?
Включите и настройте отсылку Core Dump в меню Majestic > Majestic Debugging.

#### Изображение с камеры имеет розовый оттенок
Вам нужно указать GPIO пины для управления инфракрасным фильтром. Настройки для некоторых камер можно найти [в таблице](https://openipc.org/wiki/en/gpio-settings.html). Если вашей камеры в таблице нет, то вам потребуется утилита [ipctool](https://github.com/OpenIPC/ipctool/releases/download/latest/ipctool).

Прошивка OpenIPC автоматически скачает свежую версию утилиты в каталог /tmp при первом вызове `ipctool`.
На родной прошивке вам потребуется скачать утилиту на камеру самостоятельно, используя имеющие в системе средства: wget, curl, tftp...
Например, скачайте утилиту ipctool на сервер TFTP в локальной сети, затем скачайте ее оттуда на камеру:
```
tftp -g -r ipctool -l /tmp/ipctool 192.168.1.1
chmod +x /tmp/ipctool
/tmp/ipctool
```
При наличии на камере доступа в интернет вы можете попробовать смонтировать публичный NFS шаринг и запустить утилиту с него, без скачивания на камеру:
```
mkdir -p /tmp/utils
mount -o nolock 95.217.179.189:/srv/ro /tmp/utils/
/tmp/utils/ipctool
```
После того как утилита скачана на камеру, выполните команд `ipctool gpio scan` в терминале и пару раз закройте-откройте объектив камеры ладошкой.
Следите за выводом ipctool, чтобы определить пины, отвечающие за управление шторкой ИК фильтра.
Внесите полученные значения в настройки ночного режима Majestic. Если розовый оттенок не исчез, возможно необходимо включить инверсию сигнала сенсора.

Не забудьте добавить модель камеры и найденные значения GPIO в таблицу!

#### Можно-ли вывести данные для настройки автоматической фокусировки линз вместо текущего sample_af в стандартный /metrics?
Нет, это отдельный тяжелый алгоритм, его нет смысла запускать просто так.

#### Копирование файлов с Linux системы на камеру
Очень часто требуется поместить некоторые файлы на камеру. Кроме приведенного выше способа через NFS (Network File System)
можно использовать стандартную комманду scp системы Linux для копирования файлов через SSH соединение:
```
scp ~/myfile root@192.168.1.65:/tmp/
```
Эта команда скопирует myfile из домашней директории в директорию /tmp на камере.
На очень новых системах может возникнуть следующая ошибка:
```
sh: /usr/libexec/sftp-server: not found
scp: Connection closed
```

В таком случае пишите так:
```
scp -O ~/myfile root@192.168.1.65:/tmp/
```

### Как поменять MAC адрес на камере?

__Через веб консоль__: Откройте веб-консоль, выполните команду fw_setenv ethaddr AA:BB:CC:DD:EE:FF, где AA:BB:CC:DD:EE:FF соответствует необходимому адресу.

### Как восстановить камеру если затерт или испорчен u-boot

Во многих современных процессорах камер есть фича fastboot, которая позволяет прошить флэшку даже если она пуста или на ней отсутствует u-boot. При включении платформы если процессор получает специальные команды, то включится режим fastboot, который позволит записать прошивку. Есть несколько программ, которыми можно воспользоваться для восстановления u-boot:

* Для камер на процессорах Hisilicon: HiTool

* Для камер на процессорах Goke: ToolPlatform

* Универсальная утилита на python burn как подпроект OpenIPC:
https://github.com/OpenIPC/burn

Пример запуска для платформы gk7205v300 где u-boot/gk7205v300 - это имя файла с путем:

```
./burn --chip gk7205v300 --file=u-boot/gk7205v300.bin --break; minicom -D /dev/ttyUSB0
```

Команда запускается следующим алгоритмом при подключении через UART:

1. Выключить камеру
2. Запустить комманду burn
3. Включить камеру

Если burn ругается на недостающие модули python, то надо установить приоложенный список этих модулей следующей командой
```
pip install -r requirements.txt
```
