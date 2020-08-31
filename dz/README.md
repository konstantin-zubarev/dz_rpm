### Стенд для занятие по управление пакетами и дистрибьюцие софта.

#### Создим свой RPM пакет

На данном стенде создам свой RPM из исходников. Для примера расмотрим пакет NGINX и соберем его с поддержкой openssl.
Загрузим SRPM пакет NGINX для дальнейшей работы над ним:
```
# wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
```
При установке такого пакета в домашней директории создается древо каталогов для
сборки:
```
# rpm -i nginx-1.14.1-1.el7_4.ngx.src.rpm
```
Также нужно скачать и разархивировать последний исходники для openssl - он
потребуется при сборке
```
# wget https://www.openssl.org/source/latest.tar.gz
# tar -xvf latest.tar.gz
```
Заранее поставим все зависимости чтобы в процессе сборки не было ошибок
```
# yum-builddep rpmbuild/SPECS/nginx.spec
```
Поправить сам spec файл чтобы NGINX собирался с необходимыми нам опциями
```
# vi rpmbuild/SPECS/nginx.spec
```
Внесем поправку в блок %build такую запись --with-openssl=/root/openssl-1.1.1g /
Соберем RPM пакет
```
# rpmbuild -bb rpmbuild/SPECS/nginx.spec
```
После сборки RPM пакета проверим и установим
```
# yum localinstall -y rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm
# systemctl start nginx
# systemctl status nginx
```
#### Создим свой репозиторий и разместим там ранее собранный RPM

Создадим деректорию repo
```
[root@packages ~]# mkdir /usr/share/nginx/html/repo
```
Копируем туда RPM
```
# cp rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/
# wget http://www.percona.com/downloads/percona-release/redhat/0.1-6/percona-release-0.1-6.noarch.rpm -O /usr/share/nginx/html/repo/percona-release-0.1-6.noarch.rpm
```
Инициализируем репозиторий командой:
```
# createrepo /usr/share/nginx/html/repo/
```
Для прозрачности настроим в NGINX доступ к листингу каталога. Добавим в блоке location / директиву autoindex on
```
# vi /etc/nginx/conf.d/default.conf
```
Проверяем синтаксис и перезапускаем NGINX:
```
# nginx -t
# nginx -s reload
```
Добавим свой репозиторий в /etc/yum.repos.d:
```
echo -e "[otus] \nname=otus-linux \nbaseurl=http://localhost/repo \ngpgcheck=0 \nenabled=1" > /etc/yum.repos.d/otus.repo

```
Убедимся что репозиторий подключился и посмотрим что в нем есть:
```
# yum repolist enabled | grep otus
```
Выгружаю vagrant box в vagrant cloud.

#### Для проверки ДЗ
Развернем vagrantfile и запустим команду
```
yum install percona-release
```
Во время установки можно увидить из какого репозитория (otus) будет производиться установка.

Ссылка на дополнительную информацию
- [Сборка RPM](http://wiki.rosalab.ru/ru/index.php/%D0%A1%D0%B1%D0%BE%D1%80%D0%BA%D0%B0_RPM_-_%D0%B1%D1%8B%D1%81%D1%82%D1%80%D1%8B%D0%B9_%D1%81%D1%82%D0%B0%D1%80%D1%82)
