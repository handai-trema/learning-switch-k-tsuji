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

####① FDB（Forwarding DB）への登録
fdbへ送信車のMACアドレス，入力ポート番号（Entry）を登録する．    
このとき，FDBに入力Entryがあれば，ポート番号（現時刻も）を更新する．  
そして，FDBに入力Entryがなければ，新しくEntryを作り，FDBに格納する．
ちなみに，FDB.age()を呼び出せば，最大時間（age_max: 300）を超えたエントリーはFDBから排除される．  
>参照：
> * [MultiLearningSwitch.packet_in()](lib/multi_learning_switch.rb)  
> * [FDB.learn()](lib/fdb.rb)  

####② フローテーブルの規則を生成
FDBから宛先のMACアドレスおよび宛先につながるポート番号（Entry）を取得する．  
このとき，Entryが存在すれば，入力パケット（の宛先）に対しては取得したEntryに含まれるポート番号へ出力する規則をつくる．  
そして，FDBにEntryがなければ，そのまま③に進む．

###③ 入力パケットの出力
②において決まった出力ポートに入力パケットを出力する．  
しかし，②において出力ポートが定まっていないならば，入力パケットを全てのポートへ出力する．  


###２．確認
`１．`を実証するため，  
lsw1において，下表の通りにパケットを送り合わせて見た．  

| 実行順 |  送信者  |   受信者    |                      イメージ                    |解釈|直後のフローテーブル|  
|:-----:|:-------:|:----------:|:-----------------------------------------------:|:------|:---------------|  
|   1   | host1-1 |  host1-1   |<img src="img/host1-1_host1-2.png" width="320px">|cookie=0x0, duration=7.658s, table=0, n_packets=0, n_bytes=0, idle_age=7, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1|  
|   2   | host1-2 |  host1-2   |<img src="img/host1-1_host1-2.png" width="320px">|<P>cookie=0x0, duration=18.921s, table=0, n_packets=0, n_bytes=0, idle_age=18, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P><P>cookie=0x0, duration=38.093s, table=0, n_packets=0, n_bytes=0, idle_age=38, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  
|   3   | host1-1 |  host1-1   |<img src="img/host1-2_host1-1.png" width="320px">|<P>cookie=0x0, duration=5.949s, table=0, n_packets=0, n_bytes=0, idle_age=5, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.1,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2<P><P>cookie=0x0, duration=54.431s, table=0, n_packets=0, n_bytes=0, idle_age=54, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P><P>cookie=0x0, duration=73.603s, table=0, n_packets=0, n_bytes=0, idle_age=73, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  
|   4   | host1-2 |  host1-1   |<img src="img/host1-1_host1-2.png" width="320px">|<P>cookie=0x0, duration=6.857s, table=0, n_packets=0, n_bytes=0, idle_age=6, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.2,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P><P>cookie=0x0, duration=31.096s, table=0, n_packets=0, n_bytes=0, idle_age=31, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.1,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P><P>cookie=0x0, duration=79.578s, table=0, n_packets=0, n_bytes=0, idle_age=79, priority=65535,udp,in_port=2,vlan_tci=0x0000,dl_src=cb:95:96:e6:9d:03,dl_dst=cb:95:96:e6:9d:03,nw_src=192.168.0.2,nw_dst=192.168.0.2,nw_tos=0,tp_src=0,tp_dst=0 actions=output:2</P><P>cookie=0x0, duration=98.75s, table=0, n_packets=0, n_bytes=0, idle_age=98, priority=65535,udp,in_port=1,vlan_tci=0x0000,dl_src=dd:36:82:ff:45:88,dl_dst=dd:36:82:ff:45:88,nw_src=192.168.0.1,nw_dst=192.168.0.1,nw_tos=0,tp_src=0,tp_dst=0 actions=output:1</P>|  



##関連リンク
* [trema.multi.conf] (trema.multi.conf)
* [lib] (lib)
* [images] (img)
