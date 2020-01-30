---
layout: post
title: 2020-1-30 Docker에서 MySQL 띄우고 설정하기
---

### 배경
- Docker에 MySQL서버를 띄우고 CRUD를 했었다. 제한된 자원으로 하기도 했었지만, 이 과정에서 아쉬움은 my.cnf파일을 어떻게 설정을 해야되며. logging은 어떻게 해야되는지 등이 궁금했다. 뿐만 아니라 이전에 설정했던 1cpu 128mb로 돌리게 되면 조금만 과부하가 걸려도 컨테이너가 종료됬다.

```shell
// docker setting
$docker run --name containerName -p 33060:3306 --cpus="1" --memory=256m -d mysql:5.7 -e MYSQL_ROOT_PASSWORD=pwd
$docker exec -it <container> bash
$apt-get update
$apt-get install vim
$vim /etc/mysql/my.cnf
$docker restart <container>
$docker stop <container>
$docker start <container>
```
- 이전에는 -p 3306:3306으로 컨테이너를 띄었었는데, 이제는 33060 포트로 들어오면 3306으로 접속을 하게끔 설정을 바꿨다.
- docker exec -it <container> bash를 하게 되면 컨테이너 내부의 bash shell로 접속을 하게된다. 우분투 환경이라서 apt-get update를 통해 리눅스 모듈?패키지들을 업데이트하고 vim을 설치했다.
- vim을 이용해서 my.cnf를 만들고 service restart mysqld를 하려고 했으나 안됬고, 컨테이너를 재시작해야됬다. **중요한건 여기서 꼭 docker logs <container>를 해서 로그를 봐야된다 !!!** 나의 경우 my.cnf파일에 있는 설정들이 오류가 나서 docker container가 재시작이 안됬다!!

```shell
// my.cnf
[client]
default-character-set=utf8
[mysqldump]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
character-set-filesystem=utf8
#bind-address=0.0.0.0

# set error log
log-error=/var/log/mysql/error.log
# slow query log
slow_query_log_file=/var/log/mysql/slow.log
slow_query_log=1
long_query_time=5
# general log
general_log = ON
general_log_file=/var/log/mysql/general.log

explicit_defaults_for_timestamp = 1
default-time-zone='+9:00'
innodb_buffer_pool_size=64M

sort_buffer_size = 512K
join_buffer_size = 6K
read_buffer_size = 64K
read_rnd_buffer_size = 256K
```
- my.cnf파일을 보면 log-error, general_log, default_time_zone 등의 값들을 재설정했다. 여기서 중요한건 버전이다 !! 나의경우 mysql 5.7버전을 사용중에 있는데, 많은 블로그들에서 5.7버전 전후로 my.cnf파일들을 올려줬다. 이전 버전의 설정값들이라 계속 에러가 발생했던것이다 !

### 오늘의 느낀점

- db에 100만 row를 넣어서 성능테스트를 했었다. 성능이 계속 안나와서 결국 도커를 다시 세팅을 하게 되었고, 로깅을 하기 위해서 며칠전에 안됬던 my.cnf파일을 재설정하고 container bash안으로 들어가는것도 배우게 되었다. 이 과정에서 my.cnf파일은 어떤 역할을 하며, 버전별로 설정값이나 어떤것들을 내가 추출할 수 있는지 알게되었다. 점점 mysql에 깊숙히 파고들고 있다. 속도에 대한 이슈를 해결하기 위해 찾아보니 결국은 db튜닝에 대한 부분이였고, db튜닝을 하기 위해서는 db engine이나 내부적인 동작 원리들에 대한 이해가 필요했다. 오래걸리지만 이 과정에서 많이 배웠다. 내일은 어떤것들을 알게될지 기대가 된다.
