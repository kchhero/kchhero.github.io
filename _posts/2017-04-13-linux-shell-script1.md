---
title: 'Shell Script 1'
layout: post
tags:
  - linux
category: Uncategoried
---
##### IFS
shell script에서 string 을 split 하는 방법이다.
주의 할 점은 마지막에 IFS 를 다시 원상복구 해주어야한다는 것이다.
```shell?line_number=false
IFS='/' array=($RESULT_DIR)
local temp="${array[-1]}"

MACHINE_NAME=${temp#*-}
BOARD_SOCNAME=${MACHINE_NAME%-*-*-*}

IFS=''
```

---

##### list copy
list를 copy 하는 방법은 아래와 같다.
```shell
declare -a mem_2G_addrs=( $MEM_2G_LOAD_ADDR \
                          $MEM_2G_JUMP_ADDR \
                          $MEM_2G_SECURE_LOAD_ADDR \
                          $MEM_2G_SECURE_JUMP_ADDR \
                          $MEM_2G_NON_SECURE_LOAD_ADDR \
                          $MEM_2G_NON_SECURE_JUMP_ADDR \
                        )

mem_addrs=("${mem_2G_addrs[@]}")

function mem_addr_setup()
{
    for i in ${mem_addrs[@]}
    do
        echo "$i"
    done
    MEM_LOAD_ADDR=${mem_addrs[0]}
    MEM_JUMP_ADDR=${mem_addrs[1]}
    MEM_SECURE_LOAD_ADDR=${mem_addrs[2]}
    MEM_SECURE_JUMP_ADDR=${mem_addrs[3]}
    MEM_NON_SECURE_LOAD_ADDR=${mem_addrs[4]}
    MEM_NON_SECURE_JUMP_ADDR=${mem_addrs[5]}
}
```
