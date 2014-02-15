Simple OPDS Catalog
Простой OPDS Каталог
Author: Dmitry V.Shelepnev
Версия 0.10.1

Установка Simple OPDS в Fedora:

1. Зависимости.
Требуется Mysql не ниже версии 5 (необходима поддержка хранимых процедур)
Требуется Python не ниже версии 3.3 (используется атрибут zlib.Decompressor.eof, введенный в версии 3.3)

Для работы проекта необходимо установить следующие зависимости:
yum install httpd
yum install mysql
yum install python3
yum install mysql-connector-python3

2. Установка.
Загрузить проект можно с сайта www.sopds.ru. 
Проект имеет следующую структуру:
opds				- каталог проекта (можно задать свое имя каталога)
	py			- каталог с программами на Python
	db			- каталог инициализационные скрипты для создания БД
	conf			- каталог с файлом конфигурации
	README.md		- файл README

Для работы CGI-скрипта необходимо разрешить доступ к каталогу opds, например при помощи следующих директив конфигурационного файла web-сервера Apache httpd.conf:

<Directory "/home/www/opds">
        Options Indexes FollowSymLinks
        AllowOverride All
        Order allow,deny
        Allow from all
</Directory>
Alias   /opds           "/home/www/opds"

3. Конфигурационный файл.
Перед началом работы необходимо внести необходимые настройки в файл конфигурации ./conf/sopds.conf

4. Инициализация базы данных.
Для работы каталога необходимо создать(пересоздать) базу данных sopds при помощи скрипта:
./db/db_create.sh

5. Сканирование каталога с книгами.
Для сканирования каталога с электронными книгами запустить скрипт sopds-scan.py

6. Доступ к OPDS каталогу через WWW.
Для сервера Apache необходимо разрешить запуск cgi-скрипта ./py/sopds.cgi
при помощи директивы, помещенной в .htacess:
  Options ExecCGI
или
  Options +ExecCGI

7. Использование OPDS каталога с устройств поддерживающих OPDS.
Ввести OPDS каталог и следующий URL: your_domain_name/opds/py/sopds.cgi

8. Обновление версий
- Поскольку при переходе от версии к версии возможно изменение структуры БД необходимо пересоздать ее следующей командой
  ./db/db_create.sh
- После пересоздания БД и, как следствие уничтожении сыллок из БД на извлеченные обложки стоит удалить со всем содержимым
  папку covers
  rm -rf covers

9. Настройка конвертации fb2 в EPUB (возможно кому-нибудь нужно)
   - во первых необходимо скачать последнюю версию конвертера fb2toepub например отсюда: http://code.google.com/p/fb2-to-epub-converter/
     к сожалению конвертер не совершенный и не все книги может конвертирвать, но большинство все-таки конвертируется
   - далее, необходимо скопировать архив в папку /opds/fb2toepub и разархивировать 
   - далее, компилируем проект командой make, в результате в папке  unix_dist появится исполняемый файл fb2toepub
   - в конфигурационном файле sopds.conf необходимо задать путь к этому конвертеру, а также путь к временной папке, куда будут помещаться сконвертированные файлы,
     например таким образом:
     fb2toepub=../fb2toepub/unix_dist/fb2toepub
     temp_dir=/tmp
   - В результате OPDS-клиенту будут предоставлятся ссылки на FB2-книгу в формате epub
   
