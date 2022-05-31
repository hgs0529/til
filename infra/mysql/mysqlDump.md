## admin 업로드 다운로드 로직 디버깅

파일 다운로드시 파일명을 content-disposition헤더에 담아 보내는데 이부분 cors 설정이 누락되어 프론트에서 해당 헤더에 접근이 안됨.

- 어드민 백 webSecurity 에 있는 exposedHeaders 옵션에 Content-disposition 추가

```java
// Content-dispostion 추가
        configuration.setExposedHeaders(Arrays.asList("Content-dispostion"));
```

## cron과 mysqlDump 를 이용한 자동백업 시스템 구축

저번주 금요일부터 자동백업을 구축하려고 했었는데 계속 cron이 안되거나 쉘스크립트가 안되는 문제가 있었다.

안됬던 이유는 바로 도커컨테이너 내부에서 쉘스크립트가 실행이 되서 디비를 덤프떠야 하는데 crontab 에서 설정할때 root권한이 명령 한번만 적용이 되어 쉘 스크립트 파일 안에있는 sudo명령이 안먹혔던것이다.

이 문제를 crontab 설정 내부에서 root 권한을 가진 한줄 명령에서 바로 도커컨테이너 내부에 접근하여 내부에서 바로 쉘스크립트를 실행하도록 설정하여 문제를 해결하였다.

crontab 설정

```bash
# 매일 0시 0분마다 동작
# exec 명령으로 db라는 컨테이너에 접속하여 뒤에 명령어 실행
# sh -c 명령에서 -c 옵션을 주면 뒤에 나오는 따음표 사이에 세미콜론으로 나눠진 명령들을 한줄로 실행할수있다.
0 0 * * * root docker exec db sh -c "cd db_backup; sh db_auto_backup.sh"
```

db_backup/db_auto_backup.sh

```bash
# 오늘날짜를 변수에 할당
current=$(date +"%Y%m%d")

# 컨태이너 내부에서 mysql접근 후 mysql_[오늘날짜].sql 이라는 이름으로 덤프
exec mysqldump -u root --password="MYSQL_PASSWORD" ssr_lc > /db_backup/mysql_$current.sql
```
