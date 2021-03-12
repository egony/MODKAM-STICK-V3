2021-01-15

Внимание! 
Устройство боится статического электричества и переплюсовки! 
Устанавливать аккуратно! Контакты не трогать!

Шилд устанавливается на край гребенки GPIO (для Raspbery Pi 2,3,4 это пины 1-10).
Иногда попадаются одноплатники с гребенками, пины которых тоньше, чем нужно. 
В этом случае шилд может болтаться на пинах и контакт может теряться.


====================================
Одноплатники Raspberry Pi, Raspbian:
====================================

https://www.raspberrypi.org/documentation/configuration/uart.md

SSH disabled by default; can be enabled by creating a file with name "ssh" in boot partition


nano /boot/config.txt

добавить строку
enable_uart=1

sudo raspi-config

В открывшемся меню идем в пункт 
5 Interfacing Options Configure connections to peripherals, 

далее выбираем пункт 
Serial Enable/Disable shell and kernel messages on the serial connection 

и отвечаем 
No для Would you like a login shell to be accessible over serial? и 
Yes для Would you like the serial port hardware to be enabled?

Перезагрузить.

ls /dev/ttyA*

/dev/ttyAMA0

Замечание для плат со встроенным bluetooth.
Блютус перехватывает порт ttyAMA0. Следует использовать порт ttyS0. 
Подробности тут:
https://www.circuits.dk/setup-raspberry-pi-3-gpio-uart/

[не проверял!] Можно полностью отключить bluetooth:

nano /boot/config.txt

добавить строку
dtoverlay=pi3-disable-bt 


====================================
Замечания пользователей:
====================================
Добавьте пожалуйста в инструкцию, чтобы если кто-то ещё столкнется не мучался:
Для home-assistant.io 
Подключить sd карту с системой к windows 
В корневом каталоге карты в файле config.txt добавить строки. 
enable_uart=1 
dtoverlay=pi3-disable-bt 
Сохранить, вставить карту обратно в малину. 
При конфигурации плагинов использовать порт /dev/ttyAMA0

====================================
небольшая инструкция для RPI4 и Home assistant (совместное использование блютуз и зигби):
В RPI4:
enable_uart=1
core_freq=500
core_freq_min=500
dtoverlay=miniuart-bt
В Home assistant:
serial:
  - /dev/ttyAMA0
Удалить и установить заново zigbee2mqtt плагин




================================
Одноплатники Orange Pi, Armbian:
================================

# На armbian еще ВОЗМОЖНО нужно доустановить:
sudo apt-get install python2.7


Orange Pi PC:
~~~~~~~~~~~~~
sudo nano /boot/armbianEnv.txt

добавить строку:
overlays=uart3

перезагрузить

ls -l /dev/ttyS3

в конфиге zigbee2mqtt скорректировать порт на
/dev/ttyS3


Orange Pi Zero:
~~~~~~~~~~~~~~~
sudo nano /boot/armbianEnv.txt

добавить строку:
overlays=uart1

перезагрузить

ls -l /dev/ttyS1

в конфиге zigbee2mqtt скорректировать порт на
/dev/ttyS1



==============================
Перепрошивка
==============================

Внимательно читаем это: https://modkam.ru/?p=1112#comment-2999 
И это: https://mysku.ru/blog/aliexpress/79984.html
Там все разжевано.

Прошивки для cc2538, выпущенные до апреля 2020 года должны быть пропатчены для активации бутлоадера по этой 
инструкции: https://modkam.ru/?p=1112#comment-2999
Если есть сомнения, лучше уточнить про поддержку бутлоадера у автора прошивки. 

Если в файле прошивки не активировать бутлоадер, следующая прошивка через UART будет невозможна!

------------------------------------
Ищем в конце файла прошивки строку
:0CFFD400FFFFFFEF000000000000200015
Ее нужно поменять на
:0CFFD400FFFFFFF700000000000020000D
------------------------------------

Устанавливаем прошивальщик:

cd /opt
sudo wget https://github.com/1248/cc2538-prog/archive/master.zip
sudo unzip master.zip #распаковываем
cd /opt/cc2538-prog-master
sudo make


Перепрошивка:

Остановить софт умного дома и zigbee2mqtt.

Зажть кнопки RESET и FLASH, отпустить RESET, затем отпустить FLASH.

cd /opt/cc2538-prog-master
./cc2538-prog -d /dev/ttyS3 -f MODKAMRU_V3_UART-no-flow-control_with_SBL.hex

где /dev/ttyS3 - следует заменить на тот порт, который используется шилдом
