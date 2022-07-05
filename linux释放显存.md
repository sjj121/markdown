#### Linux 释放显存

```
fuser -v /dev/nvidia*
kill -9 [PID]
```

有时候会遇到中断程序后Linux系统中显存没有被及时释放的情况，需要使用以上的命令，kill掉占用显存的进程。