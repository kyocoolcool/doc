# IBM-MQ

安裝IBM MQ
```Bash
# cd /home/user
# mkdir ibmmq
# mv 9.3.0-IBM-MQ-LinuxX64-FP0011.tar IBM_MQ_9.3.0.2_LUNUX_X86-64.tar ./bimmq
# cd ibmmq
# mkdir mq93
# mkdir fix11
mv IBM_MQ_9.3.0.2_LUNUX_X86-64.tar ./mq93/
mv 9.3.0-IBM-MQ-LinuxX64-FP0011.tar ./fix11/
# cd /home/user/mq93
# tar vxf IBM_MQ_9.3.0.2_LUNUX_X86-64.tar
# cd /home/user/fix11
# tar vxf 9.3.0-IBM-MQ-LinuxX64-FP0011.tar
# cd /home/user/mq93/MQServer/
# ./mqlicense.sh
# rpm -ivh *.rpm
# cd /home/user/fix11
# rpm -ivh *.rpm
```

增加 `/etc/security/limits.conf`
```Bash
mqm soft nofile 524288
```

切換 mqm帳號
```Bash
# su - mqm
# mkdir /MQHA_PROD ; mkdir /MQHA_TEST
```

更改切換腳本權限
```Bash
$ chmod 777 HQMA_ST*
$ chown mqm:mqm HQMA_ST*
mv ./HQMA_START.sh /var/mqm/HQMA_START.sh
mv ./HQMA_STOP.sh /var/mqm/HQMA_STOP.sh
```

創建QM
```Bash
$ cd /opt/mqm/bin
$ ./crtmqm -lc -lp 30 -ls 10 -lf 32768 0md /MQHA_TEST/data -ld /MQHA_TEST/log PNREXTT
```

啟動QM
```Bash
$ ./strmqm PNREXTT
$ ./runmqm PNREXTT
```



