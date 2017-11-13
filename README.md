# iis-mysql-wordpress_on_windows_container

windowsコンテナ内のIIS上にWordPress立ち上げます。
DBはMySQLで別コンテナで一緒に立ち上がります。

## 使用方法

```
git clone https://github.com/ko-he-/iis-mysql-wordpress_on_windows_container
cd iis-mysql-wordpress_on_windows_container
docker-compose up -d
```

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