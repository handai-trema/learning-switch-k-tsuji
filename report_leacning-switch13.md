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
    <td>宛先MACアドレスが`01:00:00:00:00:00/ff:00:00:00:00:00`であればdropする．</td>
  </tr>
  <tr>
    <td>2</td>
    <td>add_multicast_mac_drop_flow_entry</td>
    <td>宛先MACアドレスが`33:33:00:00:00:00/ff:ff:00:00:00:00`であればdropする．</td>
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
    <td>宛先MACアドレスが`ff:ff:ff:ff:ff:ff`であればフラッディングする．</td>
  </tr>
  <tr>
    <td>1</td>
    <td>add_default_flooding_flow_entry</td>
    <td>その他はコントローラに出力ポートを問い合わせる．</td>
  </tr>
</table>






##関連リンク
* [lib/learning_switch13.rb](lib/learning_switch13.rb)
