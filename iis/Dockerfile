FROM microsoft/iis


ARG PORT_NUM
ARG DB_USER
ARG DB_PASSWORD
ARG DB_HOST

RUN mkdir c:\site

ADD https://ja.wordpress.org/wordpress-4.8.2-ja.zip /
ADD https://download.microsoft.com/download/C/A/F/CAF5E118-4803-4D68-B6B5-A1772903D119/VSU4/vcredist_x64.exe /build/vc_redist.x64.exe
ADD http://windows.php.net/downloads/releases/php-5.6.32-nts-Win32-VC11-x64.zip /

RUN powershell -command \
    Expand-Archive -Path c:\php-5.6.32-nts-Win32-VC11-x64.zip -DestinationPath c:\php\ ; \
    Remove-Item c:\php-5.6.32-nts-Win32-VC11-x64.zip -Force

ADD php.ini /php/php.ini

RUN powershell -command \
    Expand-Archive -Path c:\wordpress-4.8.2-ja.zip -DestinationPath c:\site\ ; \
    Remove-Item c:\wordpress-4.8.2-ja.zip -Force

RUN powershell -command \
    dir c:\site\wordpress

RUN powershell -command \
    chcp 65001 ; \
    $WP_CONFIG = $(Get-Content "c:\site\wordpress\wp-config-sample.php") ; \
    $WP_CONFIG = $WP_CONFIG.Replace('database_name_here','wordpress') ; \
    $WP_CONFIG = $WP_CONFIG.Replace('username_here','%DB_USER%') ; \
    $WP_CONFIG = $WP_CONFIG.Replace('password_here','%DB_PASSWORD%') ; \
    $WP_CONFIG = $WP_CONFIG.Replace('localhost','%DB_HOST%') ; \
    $Utf8NoBomEncoding = New-Object System.Text.UTF8Encoding($False) ; \
    [System.IO.File]::WriteAllLines('c:\site\wordpress\wp-config.php', "$WP_CONFIG" , $Utf8NoBomEncoding)

RUN powershell -NoProfile -Command \
    Import-module IISAdministration; \
    New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:%PORT_NUM%:"

EXPOSE ${PORT_NUM}

RUN setx path "%PATH%;c:\php"
RUN setx pathext "%PATHEXT%;.php"
RUN start /w C:\build\vc_redist.x64 /q /norestart
RUN powershell.exe -executionpolicy bypass "Add-WindowsFeature Web-CGI" 
RUN %windir%\system32\inetsrv\appcmd set config /section:system.webServer/fastCGI /+[fullPath='c:\php\php-cgi.exe'] 
RUN %windir%\system32\inetsrv\appcmd set config /section:system.webServer/handlers /+[name='PHP_via_FastCGI',path='*.php',verb='*',modules='FastCgiModule',scriptProcessor='c:\php\php-cgi.exe',resourceType='Either'] 
RUN %windir%\system32\inetsrv\appcmd.exe set config -section:system.webServer/defaultDocument /+"files.[value='index.php']" /commit:apphost
