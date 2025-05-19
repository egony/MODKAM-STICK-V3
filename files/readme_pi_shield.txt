2024-12-14

Внимание! 
Устройство боится статического электричества и переплюсовки! 
Устанавливать аккуратно! Контакты не трогать!

Внимание! 
Аккуратно с разъемом антенны! При отсоединении кабеля разъем может оторваться от платы, если прилагать усилия [и/или выбрать неудачный угол].


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
====================================
В RPI4:
enable_uart=1
core_freq=500
core_freq_min=500
dtoverlay=miniuart-bt
В Home assistant:
serial:
  - /dev/ttyAMA0
Удалить и установить заново zigbee2mqtt плагин

====================================
у меня rpi4 на ubuntu 20.04. сходу не заработало, пришлось вот так делать: 
https://askubuntu.com/a/1325939


====================================
Orange Pi PC:
====================================
sudo nano /boot/armbianEnv.txt

добавить строку:
overlays=uart3

перезагрузить

ls -l /dev/ttyS3

в конфиге zigbee2mqtt скорректировать порт на
/dev/ttyS3


====================================
Orange Pi Zero:
====================================
sudo nano /boot/armbianEnv.txt

добавить строку:
overlays=uart1

перезагрузить

ls -l /dev/ttyS1

в конфиге zigbee2mqtt скорректировать порт на
/dev/ttyS1


====================================
Radxa zero (armbian):
====================================
в /boot/armbianEnv.txt добавляем строку overlays=g12a-radxa-zero-uart-ao-a-on-gpioao-0-gpioao-1 (если overlays уже есть, то добавляем туда g12a-radxa-zero-uart-ao-a-on-gpioao-0-gpioao-1 через пробел от предыдущего значения)
в командной строке выполняем:
sudo systemctl mask serial-getty@ttyAML0.service
sudo sync
sudo reboot now

после перезагрузки можем работать с /dev/ttyAML0 на 8 и 10 пинах гребенки (идентичные Raspberry Pi), но не забываем, что в время загрузки туда летит консольный лог (Zigbee координатору на него пофиг, после загрузки ядра UART свободен от консоли системы)


====================================
Подключение на orange pi 5 plus под Debian12:
====================================
- в /boot/orangepiEnv.txt добавить 
overlays=uart6-m1
- перезагрузится и использовать /dev/ttyS6



==============================
Перепрошивка
==============================

Остановить софт умного дома и zigbee2mqtt.

Далее - по этой инструкции:
https://github.com/egony/cc2652p_E72-2G4M20S1E/wiki/Flashing


