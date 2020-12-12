2020-12-06

Прошиваем сс2538/cc2652/cc1352 через USB-UART в Windows.
(проверено на Windows 7 x64 + python 3.8.6)

Внимание!
Данный способ работает, только если в чипе есть прошивка с поддержкой SBL!
Чистый чип чистый (без прошивки) я не смог прошить этим способом, но по ссылках ниже есть информация об успешной прошивке. 


Внимательно читаем это: https://modkam.ru/?p=1112#comment-2999 
И это: https://mysku.ru/blog/aliexpress/79984.html
Там все разжевано.


Качаем и распаковываем прошивальщик: https://github.com/JelmerT/cc2538-bsl
Качаем и устанавливаем python 3.8.6: http://www.python.org/download

Устанавливаем необходимые дополнения:
pip3 install pyserial
pip3 install intelhex


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


Подключаем cc2538 к USB-UART:

  UART - cc2538
  3.3v - Vcc (!!если подать 5в, чип сдохнет!!!)
    RX - TX (PA1)
    TX - RX (PA0)
   GND - GND


Если в чипе есть прошивка с поддержкой SBL, то нужно активировать бутлоадер:

------------------------------------
Перед подачей питания на cc2538 нужно соединить с GND пины RST и PA7. 
После подачи питания сначала разомкнуть RST-GND, затем PA7-GND.

На оригинальном стике v3 установлена кнопка, замыкающая RST и GND. 

На модифицированном есть две кнопки, с ним все проще - подаете на чип питание, зажимаете 
кнопки RESET и FLASH, отпускаете RESET, затем отпускаете FLASH.
------------------------------------


На стиках cc2652/cc1352 и cc2538 со встроенным USB-UART для включения режима прошивки зажимаете 
кнопки RESET и FLASH, отпускаете RESET, затем отпускаете FLASH.


Прошиваем (укажите свой ком-порт и файл прошивки):
python.exe cc2538-bsl.py -p COM3 -e -w -v MODKAMRU_V3_USB_with_SBL.hex 


Процесс выглядит примерно так:

------------------------------------
C:\Python>python.exe cc2538-bsl.py -p COM3 -e -w -v MODKAMRU_V3_USB_with_SBL.hex
Opening port COM3, baud 500000
Reading data from MODKAMRU_V3_USB_with_SBL.bin
Cannot auto-detect firmware filetype: Assuming .bin
Connecting to target...
CC2538 PG2.0: 512KB Flash, 32KB SRAM, CCFG at 0x0027FFD4
Primary IEEE Address: 00:12:4B:00:0E:0F:3A:10
    Performing mass erase
Erasing 524288 bytes starting at address 0x00200000
    Erase done
Writing 524256 bytes starting at address 0x00200000
Write 232 bytes at 0x0027FEF80
    Write done
Verifying by comparing CRC32 calculations.
    Verified (match: 0x66d84517)
------------------------------------


------------------------------------
https://github.com/egony
https://t.me/Egony