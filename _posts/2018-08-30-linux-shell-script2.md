---
category: Uncategoried
layout: post
tags:
  - linux
title: 'Shell Script 2'
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

<br>

#### file list read and save
```
function update_support_target_list()
{
    configs=$(ls -trh ${META_NEXELL_DISTRO_PATH}/tools/configs/board/)
    for entry in ${configs}
    do
         filename="${entry%.*}"
         targets+=($filename)
    done
}

```