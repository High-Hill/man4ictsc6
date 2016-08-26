# ロードバランサへのDNAT

- Webサーバへの行きのパケット(DNAT)  
インターネット→vyos1→LB→vyos1→vyos2→Apache or Nginx  
- 戻りのパケット(SNAT)  
Apache or Nginx→vyos2→vyos1→インターネット

vyos1の外向けIPアドレス(10.1.106.1)に80番ポート(http)，または443番ポート(https)にパケットが来た際に，DMZ(192.168.1.0/24)内のロードバランサ(192.168.1.1)にフォワーディングする．  
ロードバランサにパケットが届くと，LAN(192.168.2.0/24)内のApacheサーバ(192.168.2.20)かNginxサーバ(192.168.2.30)にパケットの送信先IPアドレスが書き換えられ，送信される．

### vyos1
```  
# httpのパケットをロードバランサに送る
set nat destination rule 20 description ‘DNAT-for-Load-Balancer-80’
## 送信先IPアドレスとポートを指定する
set nat destination rule 20 destination address ’10.1.106.1’
set nat destination rule 20 destination port 80
## 該当パケットがどのインタフェースから入ってくるか指定する
set nat destination rule 20 inbound-interface eth3
## 送信先IPアドレスとポートを書き換える
set nat destination rule 20 translation address ‘192.168.1.1’
set nat destination rule 20 translation port 80
## 該当パケットがどのプロトコルで送られているか指定する
set nat destination rule 20 protocol tcp

#ポート番号を二つ指定することができなかったため，別ルールでhttpsの場合を設定
set nat destination rule 21 description ‘DNAT-for-Load-Balancer-443’
set nat destination rule 21 destination address ’10.1.106.1’
set nat destination rule 21 destination port 443
set nat destination rule 21 inbound-interface eth3
set nat destination rule 21 translation address ‘192.168.1.1’
set nat destination rule 21 translation port 443
set nat destination rule 21 protocol tcp

# sshの設定を追加
set nat destination rule 22 description ‘DNAT-for-Load-Balancer-22’
set nat destination rule 22 destination address ’10.1.106.1’
set nat destination rule 22 destination port 22
set nat destination rule 22 inbound-interface eth3
set nat destination rule 22 translation address ‘192.168.1.1’
set nat destination rule 22 translation port 22
set nat destination rule 22 protocol tcp
```

LB用のsshへのDNATを踏み台用に変える
```
set nat destination rule 22 description ‘DNAT-for-Step-22’
set nat destination rule 22 translation address ‘192.168.100.1’
```
