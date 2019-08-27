PostgreSQL 공식 홈페이지 다운로드 페이지 참조. [여기](https://www.postgresql.org/download/)

## yum을 이용한 PostgreSQL 설치

공식 홈페이지에서 적용하고자 하는 버전과 플랫폼을 입력하면 설치를 위한 커멘드가 나온다.
[Redhat 계열 다운로드 페이지](https://www.postgresql.org/download/linux/redhat/)

![image](https://user-images.githubusercontent.com/41457962/63753017-3a27c500-c8ed-11e9-91a6-f86c5afda8af.png)

공식 홈페이지의 가이드에 따라 root 계정 또는 sudo 권한으로 6번 스탭까지 진행하고 나면 서버에 postgres OS 계정이 새로 생성된다.

## 계정확인
postgres 계정으로 접속해 보면 기본 홈 경로가 `/var/lib/pgsql`로 잡혀있는 것을 알 수 있다.
이를 사용하고자 하는 유저 홈 경로로 변경해 주자.

root 계정으로 다음과 같이 실행한다.
```shell
# usermod -d /home/postgres postgres
# mkdir -p /home/postgres
# chown postgres:postgres /home/postgres
```

설정이 끝났으면 postgres 계정으로 다시 접속 후 .bash_profile을 설정해 준다.

```
$ vi ~/.bash_profile
PGHOME=/usr/pgsql-11
PGDATA=/home/postgres/11/data
PGPORT=5432

export PGDATA PGPORT PGHOME
export PATH=$PGHOME/bin:$PATH
export MANPATH=$PGHOME/share/man:$MANPATH
export LD_LIBRARY_PATH=$PGHOME/lib:$LD_LIBRARY_PATH
export PGCLIENTENCODING=UTF8
```

데이터베이스 운영을 위한 postgres 계정의 ulimit 값을 수정해 준다.
```
root 계정으로 수행
# vi /etc/security/limits.conf
#postgreSQL
postgres        soft   nproc   65536
postgres        hard   nproc   65536
postgres        soft   nofile  65536
postgres        hard   nofile  65536
```

작업 후 postgres 계정 로그아웃 후 다시 로그인해 준다.


## initdb

초기 initdb를 생성 후 실행한다
```shell
$ initdb -D /home/postgres/11/data --encoding='UTF8' --lc-collate='C' --lc-ctype='C'
$ mkdir -p /home/postgres/11//log
$ pg_ctl -D /home/postgres/11/data -l /home/postgres/11/log/postgresql.log start
```


실제 사용할 데이터베이스를 생성한다.
```shell
$ createdb --encoding=UTF-8 --username=root --owner=root --port=5432 {db name}
```

데이터베이스 접속 테스트를 한다.
```shell
$ psql --host=127.0.0.1 --port=5432 --username={user name} --dbname={database name} --password
```

데이터베이스 종료 명령어는 다음과 같다.
```shell
$ pg_ctl stop --pgdata=/home/postgres/11/data -m fast
```

## 원격 접속 설정
접속하고자 하는 계정의 패스워드를 먼저 설정해주어야 한다.
```shell
$ psql
postgres=# alter user postgres with password '<new password>';
```

다음으로는 `postgre.sqlconf` 파일을 수정해야 한다. 해당 파일에서 listen_addresses를 주석 해제 후 *를 적용한다.
```shell
$ vi ${PGDATA}/postgresql.conf
listen_addresses = '*'
```

마지막으로 `pg_hba.conf` 파일을 수정한다.
```shell
$ vi ${PGDATA}/pg_hba.conf
# TYPE    DATABASE        USER        ADDRESS        METHOD
host      all             all         0.0.0.0/0      md5
```

`pg_hba.conf`파일에 대한 자세한 설명은 구글을 통해 검색하자.


## 관리 툴(pgadmin) 다운로드
[공식 다운로드 홈페이지](https://www.postgresql.org/ftp/pgadmin/)