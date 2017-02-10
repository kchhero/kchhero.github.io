---
title: crontab
layout: post
tags:
  - linux
category: linux
---
####cron list 확인

$ crontab -l

1 : 분
2 : 시간
3 : 1~31 날짜
4 : 1~12 월
5 : 0~6 요일
* : every

#### crontab 등록

$ crontab -e
$ service cron restart

30 0 * * 6 /home/jenkins/scripts/opengrok_src_init.sh
30 5 * * * /home/jenkins/scripts/opengrok_src_sync.sh

---

#### [리눅스] 우분투 14.04 crontab 사용법

우분투 14.04 에서 /etc/crontab 파일 사용법입니다.
/etc/crontab 파일의 내용을 보면 아래와 같이 되어 있습니다.

```shell?line_number=false
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
13 *    * * *  root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```

시간마다 (/etc/cron.hourly), 일별로 (/etc/cron.daily), 주별로 (/etc/cron.weekly), 월별로 (/etc/cron.monthly)
해당 디렉토리 아래의 cron 작업파일들을 실행하도록 설정되어 있습니다.
아래는 rdate 로 시간을 동기화하는 작업을 매시간마다 실행하도록 설정합니다.
/etc/cron.hourly/rdate 라는 파일을 생성합니다.

```shell?line_number=false
#!/bin/sh

/usr/bin/rdate -s time.bora.net
```

퍼미션을 줍니다.
```shell?line_number=false
# chmod 755 /etc/cron.hourly/rdate
```

이렇게 작업을 실행할 스크립트를 만들어서 해당 디렉토리에 저장해 두면 정해진 시간에 실행이 됩니다.
만약 hourly 로 실행될 시간을 바꾸고 싶다면 /etc/crontab 파일을 편집해서
```shell?line_number=false
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
00 *    * * *  root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *  root        test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
```

저장 후 cron 서비스를 reload 해 줍니다.
```shell?line_number=false
# service cron reload
```