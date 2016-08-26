## VPNの設定
### IPsec+L2TPの設定
vyos1に設定していく．リモートアクセスVPNの形で，インターネット内とictsc6ネット内をつなぐ．
#### IPsecについて
IPsecは，IP層で暗号化や認証，カプセル化の処理を行うプロトコルで，IETFで標準化されたプロトコルである．IPsecは，次のようなサブプロトコルで構成される．

- AH(Authentication Header)  
そのパケットが第三者により偽装されたものではないこと(認証)と途中で改ざんされていないこと(完全性)を保証するためのプロトコル
- ESP(Encapsulating Seculity Payload)  
パケットを暗号化することによる機密性の確保と，認証と完全性を確保するためのプロトコル
- IKE(Internet Key Exchange)  
インターネット上で安全に暗号化に用いる共有秘密鍵を交換するためのプロトコル

VyOSでは，ESPとIKEだけが利用可能．また，AHによって提供される機能はほぼESPでも実現可能なため，AHが使えなくてもOKということらしい．  
また，IPsecには2つの動作モードがある．

1. トランスポートモード(主にホスト間でIPsecを適用するため)  
IPパケットのペイロード部のセキュリティを保証する
2. トンネルモード(ルータなどのゲートウェイ間でIPsecを適用するため)  
IPパケット全体をセキュリティ的に保護する

途中の経路にNATやNAPTなどのアドレス変換をするゲートウェイが存在する状況では利用が難しい場合がある．

#### L2TPについて
L2TPはIETFによって標準化されたトンネリングプロトコルで，主にリモートアクセスVPNで利用されるプロトコル．L2TP自体にはデータを暗号化したり認証したりする機能は提供されておらず，通常はIPsecのトランスポートモードと組み合わせることで機密性や安全性を確保する．UDPの1701番ポートを利用する．  
IPsecのトランスポートモードを利用するため，IPsec同様，途中の経路にNATやNAPTなどのアドレス変換をするゲートウェイが存在すると利用が難しい．  

説明文引用：[Vyatta入門(ISBN 978-4-7741-4711-6 C3055)](http://gihyo.jp/book/2011/978-4-7741-4711-6)

#### IPsecの設定

##### vyos1
```
set vpn ipsec ipsec-interfaces interface eth3
# ipsecのNAT Treversalオプションを有効化
set vpn ipsec nat-traversal enable
set vpn ipsec nat-networks allowed-network 192.168.0.0/24
```

#### L2TPの設定

##### vyos1
```
set vpn l2tp remote-access outside-address 10.1.106.1
# インターネット側のデフォルトゲートウェイ
set vpn l2tp remote-access outside-nexthop 10.1.3.1
# クライアントに割り当てるIPアドレスのプール
set vpn l2tp remote-access client-ip-pool start 192.168.0.100
set vpn l2tp remote-access client-ip-pool stop 192.168.0.199
# 内側のDNS
set vpn l2tp remote-access dns-servers server-1 192.168.2.80
set vpn l2tp remote-access dns-servers server-2 192.168.2.81
# 共有シークレット
set vpn l2tp remote-access authentication pre-shared-secret ***
#ユーザ登録
set vpn l2tp remote-access mode local
set vpn l2tp remote-access local-users username *** password ***
```


