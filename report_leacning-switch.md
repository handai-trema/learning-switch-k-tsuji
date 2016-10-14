#Report: learning-switch
Submission: Oct./12/2016  
Branch:     develop  



##提出者
辻　健太  
33E16012  
長谷川研究室 所属  



## SDNの構造
`trema.multi.conf`より，  
SDNの構造はFig.1の通りになった．  

|<img src="img/NetworkStructure.png" width="500px">|  
|:------------------------------------------------:|  
|                     Fig.1                        |  



##実施内容
それぞれのスイッチ（lsw1 ~ lsw4）は独立しており，  
かつどのスイッチも同じ構造をしているため，  
１つのスイッチ（今回はlsw1）に対して下記２つのことを行った．


###１．プログラムの解読
`lib/multi_learnig_switch.rb`および`lib/fdb.rb`より，  
到着したパケットに対して下記の順序で動作すると考察した．  

####① packet_in

####② flow_mod_and_packet_out


###２．確認
lsw1において，下表のようにでパケット送り合う．  

| 実行順 |  送信者  |   受信者    |                      イメージ                    |直後のフローテーブル|  
|:-----:|:-------:|:----------:|:-----------------------------------------------:|:---------------|  
|   1   | host1-1 |  host1-1   |<img src="img/host1-1_host1-2.png" width="320px">|cookie=0x0, duration=7.658s, table=0, n_packets=0, n_bytes=0, idle_age=7, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1|  
|   2   | host1-2 |  host1-2   |<img src="img/host1-1_host1-2.png" width="320px">|<P>cookie=0x0, duration=18.921s, table=0, n_packets=0, n_bytes=0, idle_age=18, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P>
<P>cookie=0x0, duration=38.093s, table=0, n_packets=0, n_bytes=0, idle_age=38, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  

--------------
|   3   | host1-1 |  host1-1   |<img src="img/host1-2_host1-1.png" width="320px">|<P>cookie=0x0, duration=5.949s, table=0, n_packets=0, n_bytes=0, idle_age=5, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.1,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2<P>
<P>cookie=0x0, duration=54.431s, table=0, n_packets=0, n_bytes=0, idle_age=54, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P>
<P>cookie=0x0, duration=73.603s, table=0, n_packets=0, n_bytes=0, idle_age=73, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  
|   4   | host1-2 |  host1-1   |<img src="img/host1-1_host1-2.png" width="320px">|<P>cookie=0x0, duration=6.857s, table=0, n_packets=0, n_bytes=0, idle_age=6, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.2,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>
<P>cookie=0x0, duration=31.096s, table=0, n_packets=0, n_bytes=0, idle_age=31, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.1,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P>
<P>cookie=0x0, duration=79.578s, table=0, n_packets=0, n_bytes=0, idle_age=79, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P>
<P>cookie=0x0, duration=98.75s, table=0, n_packets=0, n_bytes=0, idle_age=98, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  



##関連リンク
* [trema.multi.conf] (trema.multi.conf)
* [lib] (lib)
* [images] (img)
