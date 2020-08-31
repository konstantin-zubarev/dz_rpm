### Стенд для занятие по управление пакетами и дистрибьюцие софта.
На данном стенде создам свой RPM из исходников. Для примера расмотрим пакет NGINX и соберем его с поддержкой openssl.
Загрузим SRPM пакет NGINX для дальнейшей работы над ним:
```
[root@packages ~]# wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
```
При установке такого пакета в домашней директории создается древо каталогов для
сборки:
```
[root@packages ~]# rpm -i nginx-1.14.1-1.el7_4.ngx.src.rpm
```
Также нужно скачать и разархивировать последний исходники для openssl - он
потребуется при сборке
```
[root@packages ~]# wget https://www.openssl.org/source/latest.tar.gz
[root@packages ~]# tar -xvf latest.tar.gz
```
Заранее поставим все зависимости чтобы в процессе сборки не было ошибок
```
[root@packages ~]# yum-builddep rpmbuild/SPECS/nginx.spec
```
Поправить сам spec файл чтобы NGINX собирался с необходимыми нам опциями
```
[root@packages ~]# vi rpmbuild/SPECS/nginx.spec
```
