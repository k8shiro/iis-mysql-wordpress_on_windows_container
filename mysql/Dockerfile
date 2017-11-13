FROM microsoft/windowsservercore

ADD https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.16-winx64.zip mysql.zip

ARG ROOT_PASSWORD
ARG WP_USER
ARG WP_PASSWORD


RUN echo SET PASSWORD FOR 'root'@'localhost' = PASSWORD('%ROOT_PASSWORD%') ; > c:\mysql-init.txt

RUN type c:\mysql-init.txt

RUN powershell -command \
    Expand-Archive -Path c:\mysql.zip -DestinationPath c:\ ; \
    ren c:\mysql-5.7.16-winx64 c:\MySQL ; \
    New-Item -Path C:\MySQL\data -ItemType directory ; \
    c:\MySQL\bin\mysqld.exe --initialize --console --explicit_defaults_for_timestamp --init-file c:\mysql-init.txt ; \
    c:\MySQL\bin\mysqld.exe --install ; \ 
    Start-Service mysql ; \ 
    Remove-Item c:\mysql.zip -Force

ENV MYSQL c:\\MySQL

EXPOSE 3306

RUN setx PATH /M %PATH%;c:\MySQL\bin

RUN powershell -command \
    Start-Sleep -s 3

RUN MySQL --user=root --password="%ROOT_PASSWORD%" --connect-expired-password -Be "grant all privileges on *.* to %WP_USER%@'%' identified by '%WP_PASSWORD%' with grant option;" & \
    MySQL --user=root --password="%ROOT_PASSWORD%" --connect-expired-password -Be "CREATE DATABASE wordpress CHARACTER SET utf8 COLLATE utf8_bin;"
