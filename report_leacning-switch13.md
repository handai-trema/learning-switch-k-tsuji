#Report: learning-switch13
Submission: &nbsp; Oct./20/2016<br>
Branch: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; develop<br>






##提出者
<B>辻　健太</B><br>
33E16012<br>
長谷川研究室 所属<br>




##ソースコードの解読
 <p>[lib/learning_switch13.rb](lib/learning_switch13.rb)
より，INGRESS_FILTERING_TABLEおよびFORWARDING_TABLEという下記２つのフローテーブルが設定されていることがわかった．</p>
<p>そして，INGRESS_FILTERING_TABLEがフィルタリングの役割をし，INGRESS_FILTERING_TABLEからFORWARDING_TABLEへ遷移する流れとなる．</p>

<table>
  <caption>INGRESS_FILTERING_TABLE</caption>
  <tr>
    <td>優先度</td>
    <td>メソッド名</td>
    <td>ルール</td>
  </tr>
  <tr>
    <td>2</td>
    <td>add_ipv6_multicast_mac_drop_flow_entry</td>
    <td>宛先MACアドレスが01:00:00:00:00:00/ff:00:00:00:00:00であればdropする．</td>
  </tr>
  <tr>
    <td>2</td>
    <td>add_multicast_mac_drop_flow_entry</td>
    <td>宛先MACアドレスが33:33:00:00:00:00/ff:ff:00:00:00:00であればdropする．</td>
  </tr>
  <tr>
    <td>1</td>
    <td>add_default_forwarding_flow_entry</td>
    <td>その他はFORWARDING_TABLEへ．</td>
  </tr>
</table>

<table>
  <caption>FORWARDING_TABLE</caption>
  <tr>
    <td>優先度</td>
    <td>メソッド名</td>
    <td>ルール</td>
  </tr>
  <tr>
    <td>3</td>
    <td>add_default_broadcast_flow_entry</td>
    <td>宛先MACアドレスがff:ff:ff:ff:ff:ffであればフラッディングする．</td>
  </tr>
  <tr>
    <td>1</td>
    <td>add_default_flooding_flow_entry</td>
    <td>その他はコントローラへパケットを送る．</td>
  </tr>
</table>


##構造
<p>今回は，独自の構造を
[learning_switch13.conf](learning_switch13.conf)
に定義した．</p>
<p>概略的に説明すると，送信機（`sender`）が１台とフローテーブルのそれぞれの条件を満たす受信機（`recever`）が５台が１台のスイッチ`lsw`に接続されている．</p>



##フローテーブル
<p>フローテーブルを確認し，下表の通りに解釈した．</p>
<p>やはり，想定通りであった．</p>

```
cookie=0x0, duration=43.75s, table=0, n_packets=1, n_bytes=42, priority=2,dl_dst=01:00:00:00:00:00/ff:00:00:00:00:00 actions=drop
cookie=0x0, duration=43.713s, table=0, n_packets=198, n_bytes=34346, priority=2,dl_dst=33:33:00:00:00:00/ff:ff:00:00:00:00 actions=drop
cookie=0x0, duration=43.713s, table=0, n_packets=32, n_bytes=10344, priority=1 actions=goto_table:1
cookie=0x0, duration=43.713s, table=1, n_packets=31, n_bytes=10302, priority=3,dl_dst=ff:ff:ff:ff:ff:ff actions=FLOOD
cookie=0x0, duration=43.713s, table=1, n_packets=1, n_bytes=42, priority=1 actions=CONTROLLER:65535
```

##関連リンク
* [lib/learning_switch13.rb](lib/learning_switch13.rb)
