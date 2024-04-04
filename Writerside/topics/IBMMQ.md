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

Mount iscsi 空間提供IBM MQ儲存 data及log
```Bash
# dnf install iscsi-initiator-utils
# systemctl restart iscsi-init.service
# cat /etc/iscsi/initiatorname.iscsi
# iscsiadm -m discovery -t st -p  10.11.121.15 10.11.121.15:3260,1 iqn.2024-01.io.openshift:test
```

先將ACL登記在iscsi上
```Bash
# targetcli
# ls /iscsi
# cd /iscsi/iqn.2024-01.io.openshift:test/tpg1/acls
# create iqn.1994-05.com.redhat:1bc7f033dc17
# exit
```

```Bash
# iscsiadm -m node -T iqn.2024-01.io.openshift:test -p 10.11.121.15 --login
# lsblk
# mkdir /MQHA_PROD
# mkfs.ext4 /dev/sdf
需確認mount硬碟是否已被掛載
# mount /dev/sdf /MQHA_PROD/
# df -h
# mkdir -p /MQHA_PROD/data
# mkdir -p /MQHA_PROD/log
# cd /MQHA_PROD
# chown -R mqm:mqm data
# chown -R mqm:mqm log
```

此段暫時不需要
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

設定環境參數
```Bash
# vim .bash_profile
PATH=$PATH:$HOME/.local/bin:$HOME/bin:/opt/mqm/bin
```


創建QM
```Bash
$ cd /opt/mqm/bin
$ ./crtmqm -lc -lp 30 -ls 10 -lf 32768 0md /MQHA_TEST/data -ld /MQHA_TEST/log PNREXTT
```

記得開防火牆

啟動QM
```Bash
$ ./strmqm PNREXTT
$ ./runmqm PNREXTT
```



