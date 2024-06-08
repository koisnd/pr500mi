# Print PR-500MI Home router status

* Print status for PR-500MI Home router. PR-500MI の情報を取得するモジュールです

* Hardware Version ファームウェアバージョン
 * 08.00.0004

* Install and example インストール方法と使用例(一部結果抜粋)

```
~$ git clone https://github.com/koisnd/pr500mi.git

~$ cd pr500mi

~/pr500mi$ ./__init__py -h
usage: __init__py [-h] -u USER -p PASSWORD [-U URL]

Get gateway status.

options:
  -h, --help            show this help message and exit
  -u USER, --user USER  Provide username for gateway
  -p PASSWORD, --password PASSWORD
                        Provide password for gateway
  -U URL, --URL URL     Provide url like "http://192.168.x.y"

~/pr500mi$ ./__init__py  -u basic_user_name -p basic_password
current_status:
  hw_status: 正常
  mac_lan: 58:52:8A:**:**:**
  mac_wan: 58:52:8A:**:**:**
  wan_4_dns:
  - 202.***.***.***
  - 202.***.***.***
  wan_4_ip: 218.***.***.***
  wan_4_peer: 110.***.***.***
  wan_4_status: 接続中
  wan_6_dns:
  - 2001:****::****:****
  - 2001:****::****:****
...
ip_masq_4:
- enabled: true
  fwd_ip: 10.***.***.***
  fwd_pt: 80
  fwd_pt_str: 'http'
  name: enableFlag1
  number: 1
  port: 80
  port_str: 'http'
  protocol: TCP＆UDP
- enabled: true
...
static_route_4:
...
- enabled: false
  gateway: ''
  name: enableFlag150
  number: 150
  prefix: ''
telephone_histories:
- - '1'
  - 2024/06/07 20:45:58
  - '-'
  - 2024/06/07 20:45:58
  - フォールバック応答(488)
  - '-'
  - IPv6
  - 外線着信
  - 0xxxxxxxxx
  - ユーザ拒否(P)
  - '-'
  - '-'
...
```
