# 初期設定

## vyos1作業ログ
### 各スイッチとの対応
eth3 - インターネット  
eth4 - ictsc6-router_router-0  
eth5 - ictsc6-dmz-1  
eth6 - ictsc6-steptodmz-100  

### ipアドレスの設定
`set interfaces ethernet eth3 description ‘internet’`  
`set interfaces ethernet eth3 address ’10.1.106.1/16’`  
`set interfaces ethernet eth4 description ‘router_router’`  
`set interfaces ethernet eth4 address ’192.168.0.254/24’`  
`set interfaces ethernet eth5 description ‘dmz’`  
`set interfaces ethernet eth5 address ’192.168.1.254/24’`  
`set interfaces ethernet eth6 description ‘steptodmz’`  
`set interfaces ethernet eth6 address ’192.168.100.254/24’`  
### DNS
`set system name-server 10.1.3.21`  
`set system name-server 10.1.3.80`
### gateway
`set system gateway-address 10.1.3.1`
### static routing
#### default gateway
`set protocols static route 0.0.0.0/0 next-hop 10.1.3.1`  
#### 自分がネットワーク内にいるもの
`set protocols static route 192.168.0.0/24 next-hop 192.168.0.1`  
`set protocols static route 192.168.1.0/24 next-hop 192.168.1.1`  
`set protocols static route 192.168.100.0/24 next-hop 192.168.100.1`  
#### もう一つのルータへ
`set protocols static route 192.168.2.0/24 next-hop 192.168.0.1`  
`set protocols static route 192.168.200.0/24 next-hop 192.168.0.1`  
### time zone
`set system time-zone ‘Asia/Tokyo’`
### host name
`set system host-name ‘vyos1’`


## vyos2作業ログ
### 各スイッチとの対応
eth3 - ictsc6-router_router-0  
eth4 - ictsc6-lan-2  
eth5 - ictsc6-steptolan-200
### ipアドレスの設定
`set interfaces ethernet eth3 description ‘router_router’`  
`set interfaces ethernet eth3 address ’192.168.0.1/24’`  
`set interfaces ethernet eth4 description ‘lan’`  
`set interfaces ethernet eth4 address ’192.168.2.254/24’`  
`set interfaces ethernet eth5 description ‘steptolan’`  
`set interfaces ethernet eth5 address ’192.168.200.254/24’`  
### DNS
`set system name-server 10.1.3.21`  
`set system name-server 10.1.3.80`  
### gateway
`set system gateway-address 192.168.0.254`  
### staticrouting
#### default gateway
`set protocols static route 0.0.0.0/0 next-hop 192.168.0.254`  
#### 自分がネットワーク内にいるもの
`set protocols static route 192.168.200.0/24 next-hop 192.168.200.1`  
`set protocols static route 192.168.2.0/24 next-hop 192.168.2.20`  
#### もう一つのルータへ
`set protocols static route 192.168.1.0/24 next-hop 192.168.0.254`  
`set protocols static route 192.168.100.0/24 next-hop 192.168.0.254`  
### time zone
`set system time-zone ‘Asia/Tokyo’`  
### host name
`set system host-name ‘vyos2’`  

##### メモ
デフォルトゲートウェイの設定が二つあることに関して警告が出る．あまりオススメじゃないみたいだ．

### 2016/08/23
デフォルトゲートウェイの設定がうまくいっていない？
以下の設定を追加して，競合していた設定を外す．

#### vyos1
```
set protocols static route 10.1.0.0/16 next-hop 10.1.3.1
delete system gateway-address 10.1.3.1
```
#### vyos2
```
set protocols static route 10.1.0.0/16 next-hop 192.168.0.254
delete system gateway-address 192.168.0.254
```




