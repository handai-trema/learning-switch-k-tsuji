#Report: learning-switch
Submission: Oct./12/2016  


##提出者
辻　健太  
33E16012  
長谷川研究室 所属  


## SDNの構造
`trema.multi.conf`より，SDNの構造はFig.1の通りになった．  

|<img src="img/NetworkStructure.png" width="500px">|  
|:------------------------------------------------:|  
|                     Fig.1                        |  


##実施内容

下記２つのことを行った．

###１．互いにパケットを送り合う．
まず，それぞれのスイッチ（lsw1 ~ lsw4）において，  
ホスト同士でパケット送り合う．  
（下記はlsw1のみ）  

|  送信者  |   受信者    |                      イメージ                    |  
|:-------:|:----------:|:-----------------------------------------------:|  
| host1-1 |  host1-2   |<img src="img/host1-1_host1-2.png" width="320px">|  
| host1-2 |  host1-1   |<img src="img/host1-2_host1-1.png" width="320px">|  


##関連リンク
* [images] (img)
