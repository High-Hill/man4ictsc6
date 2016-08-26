# IPマスカレード

### vyos2
`set nat source rule 10 description ‘ip_masquerade’`  
`#出て行く方のインタフェース`  
`set nat source rule 10 outbound-interface ‘eth3’`  
`#出て行く前の送信元IPアドレス`  
`set nat source rule 10 source address ‘192.168.2.0/24’`  
`#出て行く時の送信元IPアドレス．masqueradeを指定すると，ルータのIPアドレス+何らかのポート番号になる`  
`set nat source rule 10 translation address ‘masquerade’`  
または，  
`edit nat source rule 10`  
`set description ‘ip_masquerade’`  
`set outbound-interface ‘eth3’`  
`set source address ‘192.168.2.0/24’`  
`set translation address ‘masquerade’`  
`top`  

`edit`というコマンドで，vyosの設定の階層を移動することができる．  
階層の最初に戻りたい時は，`top`コマンドを使う．  
同じ階層に大量の設定をする場合，時間短縮になるかもしれない．  

#### vyos1
`set nat source rule 10 description ‘ip_masquerade’`  
`set nat source rule 10 outbound-interface ‘eth3’`  
`set nat source rule 10 source address ‘192.168.0.0/24’`  
`set nat source rule 10 translation address ‘masquerade’`  
または，  
`edit nat source rule 10`  
`set description ‘ip_masquerade’`  
`set outbound-interface ‘eth3’`  
`set source address ‘192.168.0.0/24’`  
`set translation address ‘masquerade’`  
`top`  

# IPマスカレードの追加
踏み台サーバからインターネットにpingが通らないようなので，今までLAN用にしか設定していなかったが，各セグメント用にIPマスカレードを設定する．

### vyos2
```
set nat source rule 10 description ‘ip_masquerade_lan’
set nat source rule 11 description ‘ip_masquerade_step2’
set nat source rule 11 outbound-interface ‘eth3’
set nat source rule 11 source address ‘192.168.200.0/24’
set nat source rule 11 translation address ‘masquerade’
```

### vyos1
```
set nat source rule 10 description ‘ip_masquerade_vyos2’
set nat source rule 11 description ‘ip_masquerade_step1’
set nat source rule 11 outbound-interface ‘eth3’
set nat source rule 11 source address ‘192.168.100.0/24’
set nat source rule 11 translation address ‘masquerade’
set nat source rule 12 description ‘ip_masquerade_dmz’
set nat source rule 12 outbound-interface ‘eth3’
set nat source rule 12 source address ‘192.168.1.0/24’
set nat source rule 12 translation address ‘masquerade’
```

