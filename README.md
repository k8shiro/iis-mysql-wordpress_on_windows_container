# iis-mysql-wordpress_on_windows_container

windowsコンテナ内のIIS上にWordPress立ち上げます。
DBはMySQLで別コンテナで一緒に立ち上がります。

## 使用方法

```
git clone https://github.com/ko-he-/iis-mysql-wordpress_on_windows_container
cd iis-mysql-wordpress_on_windows_container
docker-compose up -d
```

IISのポート番号・DB接続用のユーザー/パスワードはdocker-compose.yml内のargの値で変更可能です。

実行することで、webコンテナ内にIIS・WordPressが立ち上がり、dbにMySQLが立ち上がります。
WordPress側ではDB接続情報が格納されるwp-config.phpの設定まで行われ、MySQLにはWordPressからの接続ユーザの作成・wordpressテーブルの作成・rootユーザのパスワード変更が行われます。

起動後はhttp://<host_ip>:8000/wordpress/wp-adminからブログ名等の初期設定が行えます。


## docker-compose

```
version: '3'
services:
  web:
    build:
      context: ./iis
      dockerfile: Dockerfile
      args:
        - PORT_NUM=8000        # Webサーバー(IIS)のポート番号
        - DB_USER=wordpress    # MySQL接続ユーザ
        - DB_PASSWORD=p@ssw0rd # MySQL接続パスワード
        - DB_HOST=db           # 変更不可
    ports:
      - 8000:8000
    links:
      - db
    tty: true
  db:
    build:
      context: ./mysql
      dockerfile: Dockerfile
      args:
        - ROOT_PASSWORD=p@ssw0rd # MySQL rootユーザパスワード
        - WP_USER=wordpress      # DB_USERと同じものにすること
        - WP_PASSWORD=p@ssw0rd   # DB_PASSWORDと同じものにすること
    tty: true
networks:
  default:
    external:
      name: nat  # 環境に合わせて適宜変更
```

## 注意
`iisl\php.ini`は余分な設定が含まれているかも