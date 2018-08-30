---
title: 'Shell Script 2'
layout: post
tags:
  - linux
category: Uncategoried
---
자주 사용하지만 잘 잊어버리는 shell script 모음 2
#### LOOP
```shell?line_number=false
#!/bin/sh

FREQ_VAL=400000
TEST_CNT="1"
while true; do
    #echo $FREQ_VAL > min_freq
    echo "test count = $TEST_CNT"
    echo "min_freq $FREQ_VAL"
    echo " "
    sleep 2
    if [ $FREQ_VAL == 400000 ];then
        FREQ_VAL=150000
    else
        FREQ_VAL=400000
    fi
    TEST_CNT=$(($TEST_CNT+1))
done
```

