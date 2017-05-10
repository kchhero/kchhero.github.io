---
title: 'LAVA develop TroubleShooting'
layout: post
tags:
  - CI
category: CI
---
#### TroubleShooting

1. 아래와 같이 물리 device와 lava-dispatcher 간에 연결이 안되는 경우는 다른 shell에서 minicom이나 kermit 과 같은 녀석들로
serial connection을 잡아놓고 있는지 확인 해야한다.
```shell
...
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
ser2net port 4001 device /dev/ttyUSB0 [115200 N81] (Debian GNU/Linux)
ShellCommand command timed out.: Sending # in case of corruption. connection timeout 120.0, retry in 60.0
pattern: ['-+\\[ cut here \\]-+\\s+(.*\\s+-+\\[ end trace (\\w*) \\]-+)', '(Unhandled fault.*)\r\n', 'Kernel panic - (.*) end Kernel panic', 'ALERT! .* does not exist.\\s+Dropping to a shell!', 'lava-test: # ', 'root@s5p4418-avn-ref:~#']
#
#
ShellCommand command timed out.: Sending # in case of corruption. connection timeout 120.0, retry in 12.0
pattern: ['-+\\[ cut here \\]-+\\s+(.*\\s+-+\\[ end trace (\\w*) \\]-+)', '(Unhandled fault.*)\r\n', 'Kernel panic - (.*) end Kernel panic', 'ALERT! .* does not exist.\\s+Dropping to a shell!', 'lava-test: # ', 'root@s5p4418-avn-ref:~#']
#
#
Connection closed by foreign host.
Connection closed
start: 4.1 power_off (max 5s)
power_off duration: 0.00
...
```
