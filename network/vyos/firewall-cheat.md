# VyOS Firewall Cheat Sheet

1. ルールを使ったファイアウォール  
2. 全体へのファイアウォール

## 1.ルールを使ったファイアウォール

基本的に，  
```
set firewall name <firewall-name> rule <rule-number>
```
まで打ち終わっていて，そのさきに打つものを表にした．  

また，それぞれのルール番号ごとに  
```
action <action>
```
があると決まっている.  

それぞれのルールごとに  
```
set firewall name <firewall-name> default-action <action>
```
があるのも決まっている．

そして，ルールが出来上がったら，何かインタフェースのin/out/localにルールを適用する．  
```
set interfaces ethernet <interface-name> firewall <in/out/local> name <firewall-name>
```

### Cheat Sheet

```
set firewall name <firewall-name> rule <rule-number>
```

|階層1|階層2|階層3|意味|
|:-|:-|:-|:-|
|destination|address|1.1.1.1|送信先IPアドレスが1.1.1.1の場合|
|||1.1.1.1/24|送信先IPアドレスが1.1.1.1/24のセグメント内の場合|
|||!1.1.1.1|送信先IPアドレスが1.1.1.1以外の場合|
|||!1.1.1.1/24|送信先IPアドレスが1.1.1.1/24のセグメント外の場合|
||port|80|送信先ポートが80番の場合|
||mac-address|00:30:48:96:f0:31|送信先MACアドレスが00:30:48:96:f0:31の場合|
|source|address|2.1.1.1|送信元IPアドレスが2.1.1.1の場合|
|||2.1.1.1/24|送信元IPアドレスが2.1.1.1/24のセグメント内の場合|
|||!2.1.1.1|送信元IPアドレスが2.1.1.1以外の場合|
|||!2.1.1.1/24|送信元IPアドレスが2.1.1.1/24のセグメント外の場合|
||port|22|送信元ポートが22番の場合|
||mac-address|00:30:48:96:f0:31|送信元MACアドレスが00:30:48:96:f0:31の場合|
|protocol|tcp||プロトコルがTCPの場合|
|recent|time|30|(下のcountとあわせて)30秒の間に|
||count|3|(上のtimeとあわせて)3回ほかのルールに当てはまるものがあれば|
|state|new|enable|通信が新しい接続であった場合|
||established|enable|通信が確立された接続であった場合|
||related|enable|通信が確立されている別の通信と関係のある接続であった場合|
||invalid|enable|接続がnew,established,relatedのどれでもない場合|
|tcp|flags|SYN,!ACK,!FIN,!RST|TCPフラグがSYNの場合|
|icmp|type-name|echo-request|ICMPのタイプがエコーリクエストの場合|
|time|starttime|09:00:00|(stoptimeと合わせて)9時から|
||stoptime|17:00:00|(starttimeと合わせて)17時までのパケットの場合|
||weekdays|Mon,Tue,Wed,Thu,Fri|平日の場合|

## 2.全体へのファイアウォール

VyOSルータ全体に設定をしたい場合は，
```
set firewall
```
から続くルールを設定する．


### Cheat Sheet

```
set firewall 
```


|階層1|階層2|意味|
|:-|:-|:-|
|all-ping|enable|すべてのICMP Echo Requestに返答を可能にする|
|broadcast-ping|enable|ICMP Echo,TimeStamp Request に返答を可能にする|
|ip-src-route|enable|IPv4 Source Routingを許可する|
|receive-redirects|enable|ICMPリダイレクトを受信できるようにする|
|send-redirects|enable|ICMPリダイレクトを送信できるようにする|
|source-validation|strict|送信元アドレス検証(RFC3704)を厳格に行う|
|syn-cookies|enable|TCP SYN cookie を有効にする|
|ipv6-receive-redirects|enable|IPv6リダイレクトを受信できるようにする|
|ipv6-src-route|enable|IPv6 Source Routing を許可する|
|conntrack-expect-table-size|500000000|コネクション追跡モジュール(セッション保持情報テーブルサイズを設定する)|
|conntrack-table-size|500000000|コネクション追跡モジュール(セッション保持情報テーブルサイズを設定する)|
|conntrack-hash-size|16384|コネクション追跡モジュール(セッション保持情報ハッシュサイズを設定する)|