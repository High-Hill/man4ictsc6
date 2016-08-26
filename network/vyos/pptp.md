# PPTP

できればあまり使いたくない．L2TP over IPsec がダメなときのみ．  

### vyos1
```
# クライアントに割り当てるIPアドレスのプール
set vpn pptp remote-access client-ip-pool start 192.168.0.111
set vpn pptp remote-access client-ip-pool stop 192.168.0.120
# 内側のDNS
set vpn pptp remote-access dns-servers server-1 192.168.2.80
set vpn pptp remote-access dns-servers server-2 192.168.2.81
#ユーザ登録
set vpn pptp remote-access authentication mode local
set vpn pptp remote-access authentication local-users username *** password ***
```