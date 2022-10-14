目次
- [参考サイト](#参考サイト)
- [tcpdumpのインストール](#tcpdumpのインストール)
- [YAMAHA配下からHGW直結となるためRasPiのIPアドレス変更](#yamaha配下からhgw直結となるためraspiのipアドレス変更)
- [PPPoE接続](#pppoe接続)
  - [クローンバックアップ](#クローンバックアップ)
  - [pppoeとpppoeconfパッケージのインストール](#pppoeとpppoeconfパッケージのインストール)
  - [pppoeconfの実行&設定](#pppoeconfの実行設定)
    - [デバイスを検査中](#デバイスを検査中)
    - [変更の確認](#変更の確認)
    - [ユーザ名の入力](#ユーザ名の入力)
    - [パスワードの入力](#パスワードの入力)
    - [ピア DNS の利用](#ピア-dns-の利用)
    - [制限MSS問題](#制限mss問題)
    - [完了](#完了)
    - [接続の確立](#接続の確立)
    - [接続の初期化](#接続の初期化)
  - [接続確認](#接続確認)
- [デフォルトルート設定](#デフォルトルート設定)
  - [/etc/ppp/ip-up.d/10dsl-providerの作成](#etcpppip-upd10dsl-providerの作成)
  - [動作確認](#動作確認)
- [ファイアウォール設定](#ファイアウォール設定)
  - [クローンバックアップ](#クローンバックアップ-1)
  - [ufwのインストール](#ufwのインストール)
  - [ufwの設定](#ufwの設定)
- [VPNサーバー構築](#vpnサーバー構築)
  - [クローンバックアップ](#クローンバックアップ-2)
  - [任意に設定値の決定(特に共有キーは20文字以上推奨)](#任意に設定値の決定特に共有キーは20文字以上推奨)
  - [VPNサーバーインストール](#vpnサーバーインストール)
  - [各ノードにアクセスするためのルーティング設定](#各ノードにアクセスするためのルーティング設定)
- [各クライアントからサービス利用確認](#各クライアントからサービス利用確認)
- [PPPoE関連コマンド](#pppoe関連コマンド)
- [ufw関連コマンド](#ufw関連コマンド)
## 
&nbsp;  
# 参考サイト
> [v6プラス自宅LANに外出先からアクセス Raspberry Pi VPNルータ版 | 日記というほどでも](https://denor.jp/v6%E3%83%97%E3%83%A9%E3%82%B9%E8%87%AA%E5%AE%85lan%E3%81%AB%E5%A4%96%E5%87%BA%E5%85%88%E3%81%8B%E3%82%89%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9-raspberry-pi-vpn%E3%83%AB%E3%83%BC%E3%82%BF%E7%89%88#Step_3_IPSecL2TP_VPN)  
> [【パクろう】ラズパイでファイアーウォールを設定する方法 | sukiburo](https://sukiburo.jp/raspberry-pi-firewall/#2-1)  
> [ufwでファイアウォール設定、管理 - Buno Journals](https://bunoacts.hatenablog.com/entry/2016/05/16/173816#%E3%83%AB%E3%83%BC%E3%83%AB%E3%81%AE%E5%84%AA%E5%85%88%E5%BA%A6%E3%81%A8%E6%8C%BF%E5%85%A5)
##
&nbsp;  
# tcpdumpのインストール
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install tcpdump
```
##
&nbsp;  
# YAMAHA配下からHGW直結となるためRasPiのIPアドレス変更
**/etc/dhcpcd.conf**
```
~
~
~
# Example static IP configuration:
#interface eth0
#static ip_address=192.168.2.211/24
static ip_address=192.168.1.211/24                      # IPアドレス
#static ip6_address=fd51:42f8:caae:d92e::ff/64
#static routers=192.168.2.201
static routers=192.168.1.1                              # デフォルトゲートウェイ
#static domain_name_servers=192.168.2.201
static domain_name_servers=192.168.1.1                  # DNSサーバー
~
~
~
```
編集後、再起動。
```
pi@rp3b-01:/etc $ sudo reboot 
```
変更後のIPアドレスでのSSH接続やスキャナー送信確認をする。  
NTT/HGWのルート設定を削除する。
##
&nbsp;  
# PPPoE接続
## クローンバックアップ
(2022/07/19現在)/Users/kazuyoshimatsuoka/Documents/システム設定データ/rp3b-01/backup.txt
## pppoeとpppoeconfパッケージのインストール
```
pi@rp3b-01:~ $  sudo apt-get update
pi@rp3b-01:~ $  sudo apt-get upgrade
pi@rp3b-01:/etc $ sudo apt install pppoe pppoeconf -y
```
## pppoeconfの実行&設定
```
pi@rp3b-01:/etc $ sudo pppoeconf
Plugin rp-pppoe.so loaded.
```
### デバイスを検査中
>eth0でのPPPoEアクセスコンセントレータの検索中...0%→100%  
### 変更の確認
>このプログラムを続けると、設定ファイル /etc/ppp/peers/dsl-provider、/etc/network/interfaces、/etc/ppp/*-secretsは変更されます。「はい」と答える前に、バックアップコピーがあることを確認してください。  
>設定を続けますか?

→<はい>を選択。
### ユーザ名の入力
>以下の入力ボックスに、あなたのプロバイダへのPPPログインに通常必要となるユーザ名を入力してください。もしヘルプ画面を見たいのであれば、ユーザ名を削除して「OK」を押してください。  
>username________________________________________________  

→'usernema'を削除して接続ユーザー名(ID)を入力→<了解>を選択。
### パスワードの入力
>以下の入力ボックスに、あなたのプロバイダへのPPPログインに通常必要となるパスワードを入力してください。  
>注意: パスワードは入力中にプレインテキストで見えます。  
>　　　　________________________________________________________  

接続パスワードを入力→<了解>を選択。
### ピア DNS の利用
>通常のホスト名を解決するためには、少なくとも1つのDNS IPアドレスが必要です。通常、あなたのプロバイダは、接続確立時に利用可能なサーバのアドレスを送ってきているはずです。  
>これらのアドレスを自動的にローカル /etc/resolv.confファイルのネームサーバのリストに追加しますか?(推奨します)  

→<はい>を選択
### 制限MSS問題
>ほとんどのプロバイダは1460以上のMSSのIPパケットをサポートしないルータを持っています。通常、このマシンから外へのパケットは、デフォルトの MTU サイズ (1500)で実イーサネットリンクを通ります。  
>不運なことに、もし別のホストからのパケットを転送(マスカレーディングなど)している場合、MSSはパケットの大きさとクライアントホストへの経路に応じて増加されることになります。このため、あなたのクライアントマシンはいくつかのサイトに接続できません。  
>解決方法はあります:  
>最大MSSをpppoeで制限できます。この問題の詳細については pppoeのドキュメントで参照できます。  >pppoe で MSS を 1452 バイトに制限しますか?  
>確信がなければ「はい」と答えてください。  
>(上記の問題がまだ出るようであれば、dsl-provider ファイルの 1412〜  

→<はい>を選択。
### 完了
>PPPDが設定されました。ブート時に接続を開始するようにしますか?  

→<はい>を選択。
### 接続の確立
>これで、"pon dsl-provider"でDSL接続を行い、"poff"で切断できるようになりました。今接続を開始しますか?  

→<はい>を選択。
### 接続の初期化
>DSL接続が引き起こされました。状況を見るのに"plog"コマンドを使ったり、一般的なインターフェイス情報を見るのに"ip addr show ppp0" を使ったりすることができます。  

→<了解>を選択

## 接続確認
```
pi@rp3b-01:/etc $ ip addr show ppp0 

4: ppp0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UNKNOWN group default qlen 3
    link/ppp 
    inet 180.63.225.74 peer 125.201.244.3/32 scope global ppp0
       valid_lft forever preferred_lft forever
```
```
pi@rp3b-01:/etc $ plog

Aug 15 09:36:28 rp3b-01 pppd[4017]: not replacing default route to eth0 [192.168.1.1]
Aug 15 09:36:28 rp3b-01 pppd[4017]: local  IP address 180.63.225.74
Aug 15 09:36:28 rp3b-01 pppd[4017]: remote IP address 125.201.244.3
Aug 15 09:36:28 rp3b-01 pppd[4017]: primary   DNS address 220.220.248.9
Aug 15 09:36:28 rp3b-01 pppd[4017]: secondary DNS address 220.220.248.1
```
##
&nbsp;  
# デフォルトルート設定
## /etc/ppp/ip-up.d/10dsl-providerの作成
```
#!/bin/sh
/usr/sbin/ip route change default via "$PPP_REMOTE" dev ppp0 proto static
```
実行権限の付与
```
sudo chmod 755 /etc/ppp/ip-up.d/10dsl-provider
```
再起動
```
sudo reboot
```
## 動作確認
PPPoE接続状況('inet'のアドレスがWAN側アドレスとなる)
```
pi@rp3b-01:/etc $ ip addr show ppp0 

4: ppp0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UNKNOWN group default qlen 3
    link/ppp 
    inet 180.63.88.143 peer 125.203.194.161/32 scope global ppp0
       valid_lft forever preferred_lft forever
```
デフォルトルート確認(最上段に’ppp0’があること)
```
pi@rp3b-01:/etc $ ip route

default via 125.203.194.161 dev ppp0 proto static 
default via 192.168.1.1 dev eth0 src 192.168.1.211 metric 202 
125.203.194.161 dev ppp0 proto kernel scope link src 180.63.88.143 
192.168.1.0/24 dev eth0 proto dhcp scope link src 192.168.1.211 metric 202

- PPPoEを切断している時
default via 192.168.1.1 dev eth0 src 192.168.1.211 metric 202 
192.168.1.0/24 dev eth0 proto dhcp scope link src 192.168.1.211 metric 202
```
「myDNS」へIPアドレス手動通知('REMOTE ADDRESS:'がPPP0のアドレスと一致していること)
```
pi@rp3b-01:/etc $ wget -O- 'http://mydns875595:RJbqjsGtQne@ipv4.mydns.jp/login.html'

--2022-08-16 09:29:43--  http://mydns875595:*password*@ipv4.mydns.jp/login.html
ipv4.mydns.jp (ipv4.mydns.jp) をDNSに問いあわせています... 168.235.80.32, 168.235.72.167, 210.197.74.211, ...
~
~
~
<DT>MASTERID :</DT><DD>mydns875595</DD>
<DT>REMOTE ADDRESS:</DT><DD>180.63.88.143</DD>                  # ppp0アドレスと一致しているか
<DT>ACCESS DAYTIME:</DT><DD>2022/08/16 00:29:44 UTC</DD>
<DT>SERVER ADDRESS:</DT><DD>168.235.80.32</DD>
~
~
~
2022-08-16 09:29:44 (6.71 MB/s) - stdout へ出力しました [617]
```
「myDNS(https://www.mydns.jp)」のログにppp0アドレスがアップデートされているか確認  
10分後にnslookupにて'ddns.sakaiwebnet.com'とppp0アドレスが一致しているか確認  
毎時17分に送信されるsendmailにて、最下部の'REMOTE ADDRESS:'がppp0アドレスと一致しているかも確認  
##
&nbsp;  
# ファイアウォール設定
## クローンバックアップ
(2022/07/19現在)/Users/kazuyoshimatsuoka/Documents/システム設定データ/rp3b-01/backup.txt
## ufwのインストール
```
sudo apt-get update
sudo apt-get upgrade
sudo apt install ufw
```
インストールされているかの確認
```
pi@rp3b-01:/etc $ sudo ufw status 

Status: inactive            # このように出力されればOK
```
## ufwの設定
FWを通過させるサービスの登録
```
sudo ufw default deny                                     # まずは一旦全拒否
sudo ufw allow port 22                                    # SSH許可(必須!) これで接続確認してから下記の限定許可設定を行う。これは最終的に削除。
(sudo ufw allow from 192.168.1.0/24 to any port 22        # SSH許可(必須!))
sudo ufw allow from 192.168.1.0/24 to any port 137        # samba許可
sudo ufw allow from 192.168.1.0/24 to any port 138        # samba許可
sudo ufw allow from 192.168.1.0/24 to any port 139        # samba許可
sudo ufw allow from 192.168.1.0/24 to any port 445        # samba許可
sudo ufw allow from 192.168.1.0/24 to any port 5060       # Asterisk許可(/etc/asterisk/sip.conf)
sudo ufw allow from 192.168.1.0/24 to any port 5062       # Asterisk許可(/etc/asterisk/sip.conf)
sudo ufw allow from 192.168.1.0/24 to any port 80        # web(camera/LED)許可
```
ufwの有効化
```
pi@rp3b-01:/etc $ sudo ufw enable

Command may disrupt existing ssh connections. Proceed with operation (y|n)? y   # 'y'を入力→Enter
Firewall is active and enabled on system startup

pi@rp3b-01:/etc $ sudo reboot       # Raspiを再起動
```
SSHログイン確認&ufw状態確認
```
pi@rp3b-01:/var/log $ sudo systemctl status ufw

● ufw.service - Uncomplicated firewall                                                      # '●'が白以外の色になっていること
   Loaded: loaded (/lib/systemd/system/ufw.service; enabled; vendor preset: enabled)
   Active: active (exited) since Tue 2022-08-16 16:04:09 JST; 21min ago                     # 'active (exited)'であること
     Docs: man:ufw(8)
  Process: 118 ExecStart=/lib/ufw/ufw-init start quiet (code=exited, status=0/SUCCESS)
 Main PID: 118 (code=exited, status=0/SUCCESS)

 8月 16 16:04:09 rp3b-01 systemd[1]: Started Uncomplicated firewall.
Warning: Journal has been rotated since unit was started. Log output is incomplete or unavailable.

- 他、設定内容確認等
pi@rp3b-01:/var/log $ sudo ufw status
pi@rp3b-01:/etc $ sudo ufw status numbered
pi@rp3b-01:/etc $ sudo ufw status verbose
```
- samba/asterisk等、各サービスの動作確認。

##
&nbsp;  
# VPNサーバー構築
## クローンバックアップ
(2022/07/19現在)/Users/kazuyoshimatsuoka/Documents/システム設定データ/rp3b-01/backup.txt
## 任意に設定値の決定(特に共有キーは20文字以上推奨)
- 接続ユーザー名
- 接続パスワード
- 共有キー
## VPNサーバーインストール
オプション３でインストール
```
sudo apt-get update
sudo apt-get upgrade

rootに移行
pi@rp3b-01:/etc $ su -
パスワード:
root@rp3b-01:~# 

root@rp3b-01:~# wget https://git.io/vpnsetup -O vpnsetup.sh && sudo \
> VPN_IPSEC_PSK='20040601swn@kawachibunka103' \         # 共有キーを''内に入力
> VPN_USER='Remote_SWN' \                               # 接続ユーザー名を''内に入力
> VPN_PASSWORD='20040601@kawachibunka103' \             # 接続パスワードを''内に入力
> sh vpnsetup.sh \
> sh vpnsetup.sh
--2022-08-18 09:45:43--  https://git.io/vpnsetup
git.io (git.io) をDNSに問いあわせています... 140.82.114.22
git.io (git.io)|140.82.114.22|:443 に接続しています... 接続しました。
HTTP による接続要求を送信しました、応答を待っています... 302 Found
~
~
~ # (sh実行からインストール完了まで約7分)
~
~
## Starting services...

================================================

IPsec VPN server is now ready for use!

Connect to your new VPN with these details:

Server IP: 121.112.78.132                       # PPP0に割り当てられているIPアドレス
IPsec PSK: 20040601swn@kawachibunka103          # 設定された共有キー
Username: Remote_SWN                            # 設定された接続ユーザー名
Password: 20040601@kawachibunka103              # 設定された接続パスワード

Write these down. You'll need them to connect!

VPN client setup: https://vpnsetup.net/clients

================================================

================================================

IKEv2 setup successful. Details for IKEv2 mode:

VPN server address: 121.112.78.132
VPN client name: vpnclient

Client configuration is available at:
/root/vpnclient.p12 (for Windows & Linux)
/root/vpnclient.sswan (for Android)
/root/vpnclient.mobileconfig (for iOS & macOS)

Next steps: Configure IKEv2 clients. See:
https://vpnsetup.net/clients

================================================

root@rp3b-01:~# 
```
サービス起動確認
```
sudo systemctl status ipsec.service

● ipsec.service - Internet Key Exchange (IKE) Protocol Daemon for IPsec
   Loaded: loaded (/lib/systemd/system/ipsec.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2022-08-18 09:51:52 JST; 8min ago                     # 'active'であること
     Docs: man:ipsec(8)
           man:pluto(8)
           man:ipsec.conf(5)
  Process: 16294 ExecStartPre=/usr/local/libexec/ipsec/addconn --config /etc/ipsec.conf --checkconf
  Process: 16295 ExecStartPre=/usr/local/libexec/ipsec/_stackmanager start (code=exited, status=0/S
  Process: 16452 ExecStartPre=/usr/local/sbin/ipsec --checknss (code=exited, status=0/SUCCESS)
  Process: 16453 ExecStartPre=/usr/local/sbin/ipsec --checknflog (code=exited, status=0/SUCCESS)
 Main PID: 16464 (pluto)
   Status: "Startup completed."
    Tasks: 4 (limit: 1935)
   CGroup: /system.slice/ipsec.service
           └─16464 /usr/local/libexec/ipsec/pluto --leak-detective --config /etc/ipsec.conf --nofor

 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface ppp0 121.112.78.132:500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface ppp0 121.112.78.132:4500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface eth0 192.168.1.211:500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface eth0 192.168.1.211:4500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface lo 127.0.0.1:500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface lo 127.0.0.1:4500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface lo [::1]:500
 8月 18 09:51:52 rp3b-01 pluto[16464]: adding UDP interface eth0 [2400:4150:4120:4800:d79a:5837:345
 8月 18 09:51:52 rp3b-01 pluto[16464]: forgetting secrets
 8月 18 09:51:52 rp3b-01 pluto[16464]: loading secrets from "/etc/ipsec.secrets"
lines 13-26/26 (END)
```
```
root@rp3b-01:~# sudo systemctl status xl2tpd.service

● xl2tpd.service - LSB: layer 2 tunelling protocol daemon
   Loaded: loaded (/etc/init.d/xl2tpd; generated)
   Active: active (running) since Thu 2022-08-18 09:51:09 JST; 11min ago                # 'active'であること
     Docs: man:systemd-sysv-generator(8)
  Process: 16037 ExecStart=/etc/init.d/xl2tpd start (code=exited, status=0/SUCCESS)
    Tasks: 1 (limit: 1935)
   CGroup: /system.slice/xl2tpd.service
           └─16041 /usr/sbin/xl2tpd

 8月 18 09:51:09 rp3b-01 xl2tpd[16040]: Not looking for kernel SAref support.
 8月 18 09:51:09 rp3b-01 xl2tpd[16040]: Using l2tp kernel support.
 8月 18 09:51:09 rp3b-01 xl2tpd[16037]: Starting xl2tpd: xl2tpd.
 8月 18 09:51:09 rp3b-01 systemd[1]: Started LSB: layer 2 tunelling protocol daemon.
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: xl2tpd version xl2tpd-1.3.12 started on rp3b-01 PID:16041
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: Written by Mark Spencer, Copyright (C) 1998, Adtran, Inc.
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: Forked by Scott Balmos and David Stipp, (C) 2001
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: Inherited by Jeff McAdams, (C) 2002
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: Forked again by Xelerance (www.xelerance.com) (C) 2006-2016
 8月 18 09:51:09 rp3b-01 xl2tpd[16041]: Listening on IP address 0.0.0.0, port 1701
```
```
root@rp3b-01:~# ip addr

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether b8:27:eb:d2:01:2c brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.211/24 brd 192.168.1.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 2400:4150:4120:4800:d79a:5837:345a:bd2a/64 scope global dynamic mngtmpaddr noprefixroute 
       valid_lft 13818sec preferred_lft 12018sec
    inet6 fe80::ff12:6fa6:8ffe:4871/64 scope link 
       valid_lft forever preferred_lft forever
3: wlan0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether b8:27:eb:87:54:79 brd ff:ff:ff:ff:ff:ff
4: ppp0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1454 qdisc pfifo_fast state UNKNOWN group default qlen 3
    link/ppp 
    inet 121.112.78.132 peer 125.203.196.131/32 scope global ppp0                   # 「myDNS」のアドレスと一致していること
       valid_lft forever preferred_lft forever
5: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
7: ppp1: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1280 qdisc pfifo_fast state UNKNOWN group default qlen 3
    link/ppp 
    inet 192.168.42.1 peer 192.168.42.10/32 scope global ppp1                       # IPアドレスが割り当てられていること
       valid_lft forever preferred_lft forever
```
クライアントより接続確認
- この時点ではL2TP/IPsecの接続とネットブラウジングのみで、LAN側デバイスへの接続(SSH・カメラetc)はできない。
## 各ノードにアクセスするためのルーティング設定
NTT西日本ホームゲートウェイ RX-600MI(192.168.1.1)
  1. 詳細設定→LAN側静的ルーティング設定→「編集」をクリック
       - 宛先IPアドレス/マスク長:192.168.42.0/24       # VPNサーバーの払い出しネットワークセグメント()
       - ゲートウェイ:192.168.1.211                    # rp3b-01のローカルアドレス
       - 「設定」をクリック
  2. 「有効/無効」にチェックを入れ「設定」をクリック  

ufwにルールを追加
```
(/etc/xl2tpd/xl2tpd.conf)
sudo ufw route allow from 192.168.42.0/24 to 192.168.1.0/24
sudo ufw route allow from 192.168.1.0/24 to 192.168.42.0/24
sudo ufw allow from 192.168.42.0/24 to any port 22          # SSH許可
sudo ufw allow from 192.168.42.0/24 to any port 80          # web(camera/LED)許可
sudo ufw allow from 192.168.42.0/24 to any port 5900        # VNC許可
sudo ufw reload
sudo ufw status
sudo ufw status numbered
```
/etc/ufw/before.rulesの最後尾に追記
```
# VPNリモートアクセス設定
*nat
:POSTROUTING - [0:0]
:PREROUTING - [0:0]
-F
-A POSTROUTING -s 192.168.42.0/24 -o eth0 -j MASQUERADE

# don't delete the 'COMMIT' line or these rules won't be processed
COMMIT
```
設定反映
```
sudo ufw reload

’Firewall not enabled (skipping reload)'が出た場合
sudo ufw enable
sudo ufw reload
```

##
&nbsp;  
# 各クライアントからサービス利用確認
できればPCから
- HGW/PRの管理画面
- rp3b-01の共有ディレクトリ
- SSH
- カメラ
- ひかり電話(留守電)

接続できない場合はufwをリロードする。`sudo ufw reload`

##
&nbsp;  
# PPPoE関連コマンド
```
切断
sudo poff

接続(Raspi起動時に自動起動する)(複数回行うとその回数分だけ接続が確立される)
sudo pon

全て切断
sudo poff -a

接続状態
ip addr show ppp0

接続ログ(Raspiを再起動するとクリアされる)
plog

デフォルトルート確認
ip route
```

##
&nbsp;  
# ufw関連コマンド
```bat
ファイアウォール状態確認
sudo ufw status

ファイアウォール設置内容確認
sudo ufw status number

ファイアウォール有効化
sudo ufw enable

ファイアウォール無効化
sudo ufw disable

ポート番号の接続を許可
sudo ufw allow 12345(ポート番号)

ポート番号の接続をローカルからのみ許可
sudo ufw allow from 192.168.0.0/24 to any port 12345(ポート番号)

ポート番号の接続をローカルの特定の端末(ex 192.168.0.10)からのみ許可
sudo ufw allow from 192.168.0.10/24 to any port 12345(ポート番号)

ポート番号の接続を拒否
sudo ufw deny 12345(ポート番号)

接続許可しているポート番号の設定を削除
sudo ufw delete allow 12345(ポート番号)

接続拒否しているポート番号の設定を削除
sudo ufw delete deny 12345(ポート番号)

ポート番号の代わりにサービス名での指定も可能
sudo ufw allow samba
sudo ufw deny samba

サービス名一覧出力
sudo ufw app list

sudo ufw status numberで出力された左端の番号を指定して削除
sudo ufw delete 1(左端の番号)

ルールの優先順位挿入
sudo ufw inseert 1(左端の番号) allow from 192.168.1.0/24 to any port 137

ufwのリロード
sudo ufw reloade
```
ログの保存場所:
`/var/log/ufw.log`  
&nbsp;  