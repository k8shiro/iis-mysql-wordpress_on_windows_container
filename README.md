# iis-mysql-wordpress_on_windows_container

windows�R���e�i����IIS���WordPress�����グ�܂��B
DB��MySQL�ŕʃR���e�i�ňꏏ�ɗ����オ��܂��B

## �g�p���@

```
git clone https://github.com/ko-he-/iis-mysql-wordpress_on_windows_container
cd iis-mysql-wordpress_on_windows_container
docker-compose up -d
```

IIS�̃|�[�g�ԍ��EDB�ڑ��p�̃��[�U�[/�p�X���[�h��docker-compose.yml����arg�̒l�ŕύX�\�ł��B

���s���邱�ƂŁAweb�R���e�i����IIS�EWordPress�������オ��Adb��MySQL�������オ��܂��B
WordPress���ł�DB�ڑ���񂪊i�[�����wp-config.php�̐ݒ�܂ōs���AMySQL�ɂ�WordPress����̐ڑ����[�U�̍쐬�Ewordpress�e�[�u���̍쐬�Eroot���[�U�̃p�X���[�h�ύX���s���܂��B

�N�����http://<host_ip>:8000/wordpress/wp-admin����u���O�����̏����ݒ肪�s���܂��B


## docker-compose

```
version: '3'
services:
  web:
    build:
      context: ./iis
      dockerfile: Dockerfile
      args:
        - PORT_NUM=8000        # Web�T�[�o�[(IIS)�̃|�[�g�ԍ�
        - DB_USER=wordpress    # MySQL�ڑ����[�U
        - DB_PASSWORD=p@ssw0rd # MySQL�ڑ��p�X���[�h
        - DB_HOST=db           # �ύX�s��
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
        - ROOT_PASSWORD=p@ssw0rd # MySQL root���[�U�p�X���[�h
        - WP_USER=wordpress      # DB_USER�Ɠ������̂ɂ��邱��
        - WP_PASSWORD=p@ssw0rd   # DB_PASSWORD�Ɠ������̂ɂ��邱��
    tty: true
networks:
  default:
    external:
      name: nat  # ���ɍ��킹�ēK�X�ύX
```

## ����
`iisl\php.ini`�͗]���Ȑݒ肪�܂܂�Ă��邩��