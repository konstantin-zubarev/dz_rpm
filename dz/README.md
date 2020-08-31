### Стенд для занятие по управление пакетами и дистрибьюцие софта.

#### Создим свой RPM пакет

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
Внесем поправку в блок %build такую запись --with-openssl=/root/openssl-1.1.1g /
Соберем RPM пакет
```
[root@packages ~]# rpmbuild -bb rpmbuild/SPECS/nginx.spec
```
После сборки RPM пакета проверим и установим
```
[root@packages ~]# yum localinstall -y rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm
[root@packages ~]# systemctl start nginx
[root@packages ~]# systemctl status nginx
```
