# PaceMaker

兩個node heart beat命名
```Bash
$ vim /etc/hosts
```
```
168.202.26.42 s-i-pras-mq1-heartbeats
168.202.26.43 s-i-pras-mq2-heartbeats
````

解壓縮pacemaker壓縮檔,須先將`pcs_package.tar.gz`上傳至`/home/user/pcs_package.tar.gz`
```Bash
$ tar -xzvf /home/user/pcs_package.tar.gz
```

安裝pacemaker
```Bash
# cd /home/user
# yum localinstall * -y
```
開通防火牆
```Bash
# firewall-cmd --permanent --add-service=high-availability
# firewall-cmd --add-service=high-availability
# firewall-cmd reload
```

更改`hacluster`密碼
```Bash
# passwd hacluster
```
啟用pcsd.service
```Bash
# systemctl start pcsd.service
# systemctl enable pcsd.service
```
創建高可用集群
先進行節點驗證
```Bash
# pcs host auth s-i-pras-mq1-heartbeat s-i-pras-mq1-heartbeat
hacluster #帳號
redhat  #先前設定的密碼
```
以上步驟在HA各節點都需執行

加入集群(單一節執行)
```Bash
pcs cluster setup external_mqcluster --start s-i-pras-mq1-heartbeat s-i-pras-mq1-heartbeat
```
note: external_mqcluster 為自定義群組名稱

啟用集群(單一節執行)
```Bash
pcs cluster enable --all
```
確認集群執行狀態
```Bash
pcs cluster status
```

創建share storage資源
與iSCSI設備並建立連結
```Bash
# iscsiadm -m discovery -t st -p 168.202.20.45
```
磁碟切割後創建LVN,先將LVM系統ID設置為uname標示符值
將`/etc/lvm/lvm.conf`配置文件中的system_id_source更改為uname
```Bash
# Configuration option global/system_id_source.
system_id_source = "uname"
```
驗證節點上的LVM系統ID是否與節點的uname匹配
```Bash
# lvm systemid
  system ID: s-i-pras-mq1  (s:共構, i:外, d:內)
# uname -n
  s-i-pras-mq1
```
在磁碟分區創建LVM
```Bash
# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created
```
創建vg
```Bash
vgcreate --setautoactivation n external_vg /dev/sdb1
```
確認新卷組帶有要運行的節點系統ID
```Bash
# vgs -o+systemid
  VG    #PV #LV #SN Attr   VSize  VFree  System ID
  my_vg   1   0   0 wz--n- <499.00g 0 s-i-pras-mq2
```
創建lv
```Bash
# lvcreate -l +100%FREE -n external_lv external_vg
```

顯示lv
```Bash
# lvs
```

external_lv上創建XFS文件系統
```Bash
# mkfs.xfs /dev/external_vg/external_lv
```

創建mount point
```Bash
# mkdir -p /MQHATest
```

創建資源和資源群組(VIP)
```Bash
# pcs resource create external_test_vip IPaddr2 ip=172.30.20.46 cidr_netmask=24 --group TEST-mqgroup
# pcs resource create internal_test_vip IPaddr2 ip=168.202.20.46 cidr_netmask=24 --group TEST-mqgroup
```

確認資源狀態
```Bash
# pcs resource status
```

關閉fence-在共構外是關閉的,共構內有開啟,因為只有共構外無法連到vCenter
```Bash
# pcs property set stonith-enabled=false
```

創建資源和資源群組(Share storage)
```Bash
# pcs resource create test_lvm ocf:heartbeat:LVM-activate vgname=external_vg vg_access_mode=system_id --group TEST-mqgroup
# pcs resource create test_fs Filesystem device="/dev/external_vg/external_lv" directory="/MQHA_TEST" fstype="xfs" --group TEST-mqgroup
```

設定resource權重
```Bash
# pcs resource defaults resoure-stickiness=50
# pcs constraint location TEST-mqgroup prefers s-i-pras-mq1-heartbeat 50
# pcs constraint location TEST-mqgroup prefers s-i-pras-mq2-heartbeat 200
```

[官方文件-參考CH4-5](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/9/html-single/configuring_and_managing_high_availability_clusters/index#assembly_creating-high-availability-cluster-configuring-and-managing-high-availability-clusters)

