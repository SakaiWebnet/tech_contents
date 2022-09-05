#tech
##目次:
###背景
これまで、ひかり電話にかかってきた電話はボイスワープ(即時転送)を使ってスマホで受けるようにしていたが、営業電話が大半を占め、ボイスワープのオプション料金が\550/月でかつ転送からスマホへの通話料金は負担しなければならないというコストの高さが気になっていた。
そこで要件&折り返し先を留守電に吹き込んでもらい、こちらからかけ放題プランのスマホでかけ直す運用に変えることにした。

###ハードウェア
NTT ホームゲートウェイ(ひかり電話契約付)(以下、HGW)
Raspberry Pi 3 Model B(以下、RP3b)

###手順
####1.rp3b-01のLANポートのMACアドレス確認
####2.HGW(NTT/RX-600MI)内の「電話設定」-「内線設定」にrp3b-01を登録する。
#####1.
####3.asteriskのインストール
####4.編集する設定ファイルのバックアップ
####5.編集する設定ファイルの所有者変更





# 参考サイト
>
[バイク野郎のひとりごと: Raspberry Pi 3へAsteriskを構築する -- その１](https://bike8615.blogspot.com/2017/06/raspberry-pi-3asterisk-1.html)  
[簡易インストールマニュアル - VoIP-Info.jp](https://voip-info.jp/index.php/%E7%B0%A1%E6%98%93%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E3%83%9E%E3%83%8B%E3%83%A5%E3%82%A2%E3%83%AB#.E6.97.A5.E6.9C.AC.E8.AA.9E.E9.9F.B3.E5.A3.B0.E3.83.95.E3.82.A1.E3.82.A4.E3.83.AB.E3.81.AE.E3.82.A4.E3.83.B3.E3.82.B9.E3.83.88.E3.83.BC.E3.83.AB_2)  
[majishini - Asteriskで050 Freeに留守電を付ける](https://www.majishini.net/hg/post/2014/20141115-001/)  
[バイク野郎のひとりごと: Asterisk の再セットアップ -- googletts.agi を使ってみる](https://bike8615.blogspot.com/2017/11/asterisk-googlettsagi.html)  
[asterisk14.0.2 freepbx13 extensionで日本語を喋らせる | なんでもDIY](http://www.ckenko25.jp/2018/12/post-12311)  
[Asteriskで留守電作ったメモ | Nacky – Snowland.net](https://snowland.net/wp/2018/03/20/asterisk-voicemail/)  
[Asterisk13の音声案内を日本語にする | ぱぴぃの、人生これから](https://mrhiroshi2010.wordpress.com/2018/03/21/asterisk13%E3%81%AE%E9%9F%B3%E5%A3%B0%E6%A1%88%E5%86%85%E3%82%92%E6%97%A5%E6%9C%AC%E8%AA%9E%E3%81%AB%E3%81%99%E3%82%8B/)  
[Google TTSを利用してAsteriskのIVRで日本語読み上げ - がとらぼ](https://gato.intaa.net/archives/7095)  

&nbsp;  
##

# rp3b-01のLANポートのMACアドレス確認
```
$ ifconfig eth0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.2.211  netmask 255.255.255.0  broadcast 192.168.2.255
        inet6 fe80::ff12:6fa6:8ffe:4871  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:d2:01:2c  txqueuelen 1000  (イーサネット)                # MACアドレス
        RX packets 177300  bytes 243204839 (231.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 96214  bytes 14839416 (14.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

&nbsp;  
##

# HGW(NTT/RX-600MI)にrp3b-01を登録する
「電話設定」-「内線設定」  
今回は”内線設定”の"7"にrp3b-01に割り当てる。  
- ニックネーム:rp3b-01
- MACアドレス:先ほど確認したrp3b-01のeth0のMACアドレス
- "ユーザーID"と"パスワード"を控えておく。
- 電話番号設定:今回はSWNの電話番号のみを通知番号&着信番号を選択。
## 
&nbsp;  

# SDカードにrp3b-01をクローンバックアップ
/Users/kazuyoshimatsuoka/Documents/システム設定データ/rp3b-01/SD-cloneBackup.md
## 
&nbsp;  

# Asteriskインストール
```
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install asterisk
```
## 
&nbsp;  

# 編集する設定ファイルのバックアップ
```
管理者にて
# cd /etc/asterisk/
# cp sip.conf sip.conf.bak
# cp extensions.conf extensions.conf.bak
# cp asterisk.conf asterisk.conf.bak
# cp voicemail.conf voicemail.conf.bak
```
## 
&nbsp;  

# 編集する設定ファイルの所有者変更
管理者にて
```
# chown pi asterisk.conf
# chown pi extensions.conf
# chown pi voicemail.conf
# chown pi sip.conf
```
## 
&nbsp;  

# バックアップと所有者変更の確認
```
$ cd /etc/asterisk
/etc/asterisk $ ls -l
合計 xx
~
-rw-r--r-- 1 pi       asterisk 707  1月  9 11:01 asterisk.conf
-rw-r--r-- 1 root     root     578  1月  5 10:28 asterisk.conf.bak
-rw-r--r-- 1 pi       asterisk 707  1月  9 11:01 extensions.conf
-rw-r--r-- 1 root     root     578  1月  5 10:28 extensions.conf.bak
-rw-r--r-- 1 pi       asterisk 707  1月  9 11:01 sip.conf
-rw-r--r-- 1 root     root     578  1月  5 10:28 sip.conf.bak
-rw-r----- 1 pi       asterisk 26449  1月  3 16:39 voicemail.conf
-rw-r----- 1 root     root     26449  1月  5 10:33 voicemail.conf.bak
~
```
## 
&nbsp;  

# asterisk.confファイルの編集
/etc/asterisk/asterisk.conf
```
languageprefix = yes          ;コメントを外す。
defaultlanguage = ja          ;コメントを外してjaに変更
```
## 
&nbsp;  

# sip.confファイルの編集
/etc/asterisk/sip.conf
```
# デフォルトの内容を全削除して、以下だけにする。
# 「# HGW(NTT/RX-600MI)にrp3b-01を登録する」で控えた内容をもとにする。

[general]
register => 7:nt0wehzi:0007@hikari-denwa_swn/200        ;register => 内線番号:パスワード:ユーザID＠任意の名前/任意の数字

context=default
bindport=5062
bindaddr=0.0.0.0
language=ja
dsiallow=all
allow=ulaw
allow=alaw
allow=gsm
localnet=192.168.2.0/255.255.255.0

[hikari-denwa_swn]
type=friend                     ; 双方向（送受信）可能なこと
secret=nt0wehzi                 ; パスワードはレジスト時のものと同じ
bindport=5060                   ; UDPポート番号
username=0077                   ; ユーザIDはレジスト時のものと同じ
fromuser=7                      ; ひかり電話のHGW上の内線番号
host=192.168.1.1                ; HGWのIPアドレス
fromdomain=192.168.1.1          ; HGWのIPアドレス
context=default                 ; デフォルトのコンテキストであることを示す
insecure=port,invite
dtmfmode=inband
disallow=all
allow=ulaw
allow=gsm
allow=alaw
```
## Asteriskにひかり電話を収容できているかを確認する。
```
sudo service asterisk restart           ;Asteriskの再起動

sudo asterisk -r                        ;Asteriskコマンドでコンソールに入る
Asterisk 16.2.1~dfsg-1+deb10u2, Copyright (C) 1999 - 2018, Digium, Inc. and others.
Created by Mark Spencer <markster@digium.com>
Asterisk comes with ABSOLUTELY NO WARRANTY; type 'core show warranty' for details.
This is free software, with components licensed under the GNU General Public
License version 2 and other licenses; you are welcome to redistribute it under
certain conditions. Type 'core show license' for details.
=========================================================================
Connected to Asterisk 16.2.1~dfsg-1+deb10u2 currently running on rp3b-01 (pid = 15645)
rp3b-01*CLI>                            ;Asteriskコンソールに入った
rp3b-01*CLI> sip show registry          ;HGWよりひかり電話を収容できているか確認(Stateが'Registered'であればOK)。

Host                                    dnsmgr Username       Refresh State                Reg.Time                 
hikari-denwa_swn:5060                   N      7                 3585 Registered           Fri, 31 Dec 2021 15:36:59
1 SIP registrations.
rp3b-01*CLI> quit                       ;Asteriskコンソールから抜ける。
Asterisk cleanly ending (0).
Executing last minute cleanups
root@rp3b-01:/etc/asterisk#             ;抜けた。
```
## HGWにrp3b-01のAsteriskが登録できているかを確認する
HGW(NTT/RX-600MI)内の「電話設定」-「内線設定」-[内線番号一覧]で、"内線番号:7"(ニックネーム:rp3b-01)の「登録状態」が"登録済み"なっていればOK。
## 
&nbsp;  

# 録音されたメッセージファイルをメール送信するためのssmtp mailutilsのインストール
root@rp3b-01:/etc/asterisk# apt-get install ssmtp mailutils
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています                
状態情報を読み取っています... 完了
以下の追加パッケージがインストールされます:
  guile-2.2-libs libgc1c2 libgnutls-openssl27 libgsasl7 libkyotocabinet16v5 liblzo2-2 libmailutils5 libntlm0
  mailutils-common
提案パッケージ:
  mailutils-mh mailutils-doc
以下のパッケージは「削除」されます:
  postfix
以下のパッケージが新たにインストールされます:
  guile-2.2-libs libgc1c2 libgnutls-openssl27 libgsasl7 libkyotocabinet16v5 liblzo2-2 libmailutils5 libntlm0 mailutils
  mailutils-common ssmtp
アップグレード: 0 個、新規インストール: 11 個、削除: 1 個、保留: 0 個。
8,109 kB のアーカイブを取得する必要があります。
この操作後に追加で 45.0 MB のディスク容量が消費されます。
続行しますか? [Y/n] Y
取得:1 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libgc1c2 armhf 1:7.6.4-0.4 [212 kB]
取得:2 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf guile-2.2-libs armhf 2.2.4+1-2+deb10u1 [4,918 kB]
取得:3 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libgnutls-openssl27 armhf 3.6.7-4+deb10u7 [315 kB]
取得:4 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libntlm0 armhf 1.5-1+deb10u1 [21.6 kB]
取得:5 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libgsasl7 armhf 1.8.0-8+b1 [196 kB]
取得:6 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf liblzo2-2 armhf 2.10-0.1 [48.4 kB]
取得:7 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libkyotocabinet16v5 armhf 1.2.76-4.2+rpi1 [280 kB]
取得:8 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf mailutils-common all 1:3.5-4 [689 kB]
取得:9 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf libmailutils5 armhf 1:3.5-4 [809 kB]
取得:10 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf mailutils armhf 1:3.5-4 [566 kB]          
取得:11 http://ftp.tsukuba.wide.ad.jp/Linux/raspbian/raspbian buster/main armhf ssmtp armhf 2.64-8 [54.2 kB]              
8,109 kB を 7秒 で取得しました (1,215 kB/s)                                                                               
パッケージを事前設定しています ...
(データベースを読み込んでいます ... 現在 49646 個のファイルとディレクトリがインストールされています。)
postfix (3.4.14-0+deb10u1) を削除しています ...
以前に未選択のパッケージ libgc1c2:armhf を選択しています。
(データベースを読み込んでいます ... 現在 49471 個のファイルとディレクトリがインストールされています。)
.../00-libgc1c2_1%3a7.6.4-0.4_armhf.deb を展開する準備をしています ...
libgc1c2:armhf (1:7.6.4-0.4) を展開しています...
以前に未選択のパッケージ guile-2.2-libs:armhf を選択しています。
.../01-guile-2.2-libs_2.2.4+1-2+deb10u1_armhf.deb を展開する準備をしています ...
guile-2.2-libs:armhf (2.2.4+1-2+deb10u1) を展開しています...
以前に未選択のパッケージ libgnutls-openssl27:armhf を選択しています。
.../02-libgnutls-openssl27_3.6.7-4+deb10u7_armhf.deb を展開する準備をしています ...
libgnutls-openssl27:armhf (3.6.7-4+deb10u7) を展開しています...
以前に未選択のパッケージ libntlm0:armhf を選択しています。
.../03-libntlm0_1.5-1+deb10u1_armhf.deb を展開する準備をしています ...
libntlm0:armhf (1.5-1+deb10u1) を展開しています...
以前に未選択のパッケージ libgsasl7 を選択しています。
.../04-libgsasl7_1.8.0-8+b1_armhf.deb を展開する準備をしています ...
libgsasl7 (1.8.0-8+b1) を展開しています...
以前に未選択のパッケージ liblzo2-2:armhf を選択しています。
.../05-liblzo2-2_2.10-0.1_armhf.deb を展開する準備をしています ...
liblzo2-2:armhf (2.10-0.1) を展開しています...
以前に未選択のパッケージ libkyotocabinet16v5:armhf を選択しています。
.../06-libkyotocabinet16v5_1.2.76-4.2+rpi1_armhf.deb を展開する準備をしています ...
libkyotocabinet16v5:armhf (1.2.76-4.2+rpi1) を展開しています...
以前に未選択のパッケージ mailutils-common を選択しています。
.../07-mailutils-common_1%3a3.5-4_all.deb を展開する準備をしています ...
mailutils-common (1:3.5-4) を展開しています...
以前に未選択のパッケージ libmailutils5:armhf を選択しています。
.../08-libmailutils5_1%3a3.5-4_armhf.deb を展開する準備をしています ...
libmailutils5:armhf (1:3.5-4) を展開しています...
以前に未選択のパッケージ mailutils を選択しています。
.../09-mailutils_1%3a3.5-4_armhf.deb を展開する準備をしています ...
mailutils (1:3.5-4) を展開しています...
以前に未選択のパッケージ ssmtp を選択しています。
.../10-ssmtp_2.64-8_armhf.deb を展開する準備をしています ...
ssmtp (2.64-8) を展開しています...
libgnutls-openssl27:armhf (3.6.7-4+deb10u7) を設定しています ...
libgc1c2:armhf (1:7.6.4-0.4) を設定しています ...
ssmtp (2.64-8) を設定しています ...
liblzo2-2:armhf (2.10-0.1) を設定しています ...
libntlm0:armhf (1.5-1+deb10u1) を設定しています ...
mailutils-common (1:3.5-4) を設定しています ...
guile-2.2-libs:armhf (2.2.4+1-2+deb10u1) を設定しています ...
libkyotocabinet16v5:armhf (1.2.76-4.2+rpi1) を設定しています ...
libgsasl7 (1.8.0-8+b1) を設定しています ...
libmailutils5:armhf (1:3.5-4) を設定しています ...
mailutils (1:3.5-4) を設定しています ...
update-alternatives: /usr/bin/frm (frm) を提供するために自動モードで /usr/bin/frm.mailutils を使います
update-alternatives: /usr/bin/from (from) を提供するために自動モードで /usr/bin/from.mailutils を使います
update-alternatives: /usr/bin/messages (messages) を提供するために自動モードで /usr/bin/messages.mailutils を使います
update-alternatives: /usr/bin/movemail (movemail) を提供するために自動モードで /usr/bin/movemail.mailutils を使います
update-alternatives: /usr/bin/readmsg (readmsg) を提供するために自動モードで /usr/bin/readmsg.mailutils を使います
update-alternatives: /usr/bin/dotlock (dotlock) を提供するために自動モードで /usr/bin/dotlock.mailutils を使います
update-alternatives: /usr/bin/mailx (mailx) を提供するために自動モードで /usr/bin/mail.mailutils を使います
man-db (2.8.5-2) のトリガを処理しています ...
libc-bin (2.28-10+rpt2+rpi1) のトリガを処理しています ...
root@rp3b-01:/etc/asterisk#



#ssmtp.confファイルのバックアップ&VScodeで編集するための所有者変更とVScodeでファイル編集
root@rp3b-01:/etc/ssmtp# ls
revaliases  ssmtp.conf
root@rp3b-01:/etc/ssmtp# cp ssmtp.conf ssmtp.conf.bak
root@rp3b-01:/etc/ssmtp# chown pi ssmtp.conf
pi@rp3b-01:/etc/ssmtp $ ls -l
合計 12
-rw-r--r-- 1 root root 200  7月 20  2014 revaliases
-rw-r--r-- 1 pi   root 707  1月  9 11:01 ssmtp.conf
-rw-r--r-- 1 root root 578  1月  5 10:28 ssmtp.conf.bak

##ssmtp.confを編集する(全削除して以下の通りだけにする)
root=kmtok@violet.plala.or.jp       ;メールアドレス（例えば、abcdefgh@gmail.com など）
Mailhub=secure.plala.or.jp:587      ;送信用ドメインのアドレス/ポート
AuthUser=kmtok@violet.plala.or.jp   ;メールアカウント
AuthPass=mailpassword               ;メールパスワード
AuthMethod=LOGIN
UseSTARTTLS=Yes                     ;メールサーバーの仕様に基づいて設定
UseTLS=Yes                          ;メールサーバーの仕様に基づいて設定
hostname=secure.plala.or.jp         ;メールサーバー名(‘localhost’ では hylafax で DNS エラーになる)

##メール送信テスト
root@rp3b-01:~# mail guest@sakaiwebnet.com
Cc: 
Subject: test
test
###Ctrl+Dで送信
root@rp3b-01:~#
###メールが届いたかを確認する。

##所有者を戻す
root@rp3b-01:/etc/ssmtp# chown root ssmtp.conf
＃#所有者が元に戻ったか確認する
root@rp3b-01:/etc/ssmtp# ls -l
合計 12
-rw-r--r-- 1 root root 200  7月 20  2014 revaliases
-rw-r--r-- 1 root root 707  1月  9 11:01 ssmtp.conf
-rw-r--r-- 1 root root 578  1月  5 10:28 ssmtp.conf.bak



#asterisk.confの内容を一部編集する(内容を全削除しないこと)
languageprefix = yes
defaultlanguage = ja



#voicemail.confの編集(内容を全削除して以下のみにする)
[general]
format=wav49                            ;wav形式で録音
serveremail=SWN_rp3b-01_asterisk        ;メール送信者名
attach=yes
maxmsg=100                              ;最大保存メッセージ
maxsecs=180                             ;最大メッセージ長（録音時間：秒）
skipms=3000                             ;デフォルトのままにしておく
maxsilence=30                           ;最大無音許容時間(この時間内(秒)に録音されなければ切断）
silencethreshold=128                    ;無音検出のスレッショルド
maxlogins=3                             ;ボックスへの最大ログイン数
charset=UTF-8                           ;日本語メールのための文字セットをUTF-8にする
emailsubject=SWN電話に留守電が吹き込まれました。 ${VM_MSGNUM} 番目 (${VM_MAILBOX} 番ボックス)の新しいメッセージです。    ;メールタイトル
emailbody=電話番号: ${VM_CALLERID}\n メッセージ長: ${VM_MSGNUM}秒\n ${VM_DATE}\n\n by SWN_rp3b-01_asterisk     ;メール本文
emaildateformat=着信日時: %Y/%m/%d, %r
mailcmd=/usr/sbin/sendmail -t           ;sendmailで送信
saycid=yes
sendvoicemail=yes

[zonemessages]
japan=Japan|Q PHM `jp-ni' `vm-received'

[default]
500 => 1111,SWN,kazuyoshi.matsuoka@sakaiwebnet.com,,tz=japan    ;ボックス => メッセージを再生するときのパスワード,送信先名前,送信先メールアドレス,,タイムゾーン



#extensions.confファイルをVScodeで編集(全削除して下記のみ記述する)
[general]
static=yes
writeprotect=no

[globals]
MYNUMBER=0722884159
USEVOICEMAIL=YES

[default]
exten => 200,1,Ringing
exten => 200,n,Wait(3)
exten => 200,n,Voicemail(500)
exten => 200,n,Wait(1)
exten => 200,n,Hangup()



#音声日本語化
##あらかじめ作成されている音声ファイルを利用
root@rp3b-01:~# cd /usr/share/asterisk/sounds       ;asterisk.confでastdatadirの場所を確認しておく
root@rp3b-01:/usr/share/asterisk/sounds# wget https://voip-info.jp/downloads/asterisk/sounds/1_6/asterisk-sound-jp_16_pre.tar.gz
--2022-01-14 16:29:53--  https://voip-info.jp/downloads/asterisk/sounds/1_6/asterisk-sound-jp_16_pre.tar.gz
voip-info.jp (voip-info.jp) をDNSに問いあわせています... 140.227.82.19
voip-info.jp (voip-info.jp)|140.227.82.19|:443 に接続しています... 接続しました。
HTTP による接続要求を送信しました、応答を待っています... 200 OK
長さ: 945968 (924K) [application/x-gzip]
`asterisk-sound-jp_16_pre.tar.gz' に保存中

asterisk-sound-jp_16_pre.tar.gz   100%[===========================================================>] 923.80K  4.89MB/s 時間 0.2s     

2022-01-14 16:29:54 (4.89 MB/s) - `asterisk-sound-jp_16_pre.tar.gz' へ保存完了 [945968/945968]

root@rp3b-01:/usr/share/asterisk/sounds# ls
asterisk-sound-jp_16_pre.tar.gz  custom  en  en_US  en_US_f_Allison  priv-callerintros  recordings
root@rp3b-01:/usr/share/asterisk/sounds# tar xzvf asterisk-sound-jp_16_pre.tar.gz
root@rp3b-01:/usr/share/asterisk/sounds# ls
asterisk-sound-jp_16_pre.tar.gz  custom  en  en_US  en_US_f_Allison  priv-callerintros  recordings
root@rp3b-01:/usr/share/asterisk/sounds# rm asterisk-sound-jp_16_pre.tar.gz 
root@rp3b-01:/usr/share/asterisk/sounds# ls
custom  en  en_US  en_US_f_Allison  ja  priv-callerintros  recordings
root@rp3b-01:/usr/share/asterisk/sounds#

##TTSの利用
###Google TTS
####asterisk.confでagiディレクトリの確認
astagidir => /usr/share/asterisk/agi-bin

####Text to speech using Google Translate for Asterisk PBXのインストール
#####公式サイト: http://zaf.github.io/asterisk-googletts/
root@rp3b-01:/usr/share/asterisk/agi-bin# cd /var/lib/asterisk/agi-bin/       ;agi-binディレクトリがない場合は新規作成する。
root@rp3b-01:/usr/share/asterisk/agi-bin# git clone https://github.com/zaf/asterisk-googletts.git
Cloning into 'asterisk-googletts'...
remote: Enumerating objects: 546, done.
remote: Total 546 (delta 0), reused 0 (delta 0), pack-reused 546
Receiving objects: 100% (546/546), 165.59 KiB | 2.33 MiB/s, done.
Resolving deltas: 100% (292/292), done.
root@rp3b-01:/usr/share/asterisk/agi-bin# 
root@rp3b-01:/usr/share/asterisk/agi-bin# cd asterisk-googletts
root@rp3b-01:/usr/share/asterisk/agi-bin/asterisk-googletts# cp -a googletts.agi ..
root@rp3b-01:/usr/share/asterisk/agi-bin/asterisk-googletts# cd ..
root@rp3b-01:/usr/share/asterisk/agi-bin/# chmod 755 googletts.agi

####Text to speech using Google Translate for Asterisk PBXが依存するソフトのインストール
root@rp3b-01:/usr/share/asterisk/agi-bin/# apt-get update
root@rp3b-01:/usr/share/asterisk/agi-bin/# apt-get install libwww-perl libcrypt-ssleay-perl sox mpg123

####動作テストのためのextensions.confの編集
他の設定はコメントアウトしておく
[default]
exten => 200,1,Answer()
exten => 200,n,Wait(2)
exten => 200,n,agi(googletts.agi,"これはテストです",ja)
exten => 200,n,Wait(2)
exten => 200,n,Hangup()
root@rp3b-01:/usr/share/asterisk/agi-bin/# service asterisk restart
架電して「これはテストです」と応答すればOK

####本番稼働のためのextensions.confの編集
前項のテスト用設定はコメントアウト or 削除しておく
[default]
;#Google TTS(変更後は"service asterisk restart"を忘れずに!)
exten => 200,1,Ringing
exten => 200,n,Wait(3)
exten => 200,n,agi(googletts.agi,"はい、さかいウェブネットです。お電話ありがとうございます。 恐れ入りますが、2022年より、留守電に吹き込まれた内容をもとに、090の番号で始まる携帯電話にて折り返しのご連絡をさせていただく対応となりました。 なお当方のホームページ、さかいウェブネットドットコム内のお問い合わせからもご用件を送信いただけます。 今からメッセージを吹き込んでいただく際は、プーーっと発信音の後に、3分以内でご用件と折り返し先電話番号を仰ってください。 またこちらには、かけていただいている電話番号は表示されていませんので、必ず折り返し先電話番号を吹き込んでいただきますようお願いいたします。 こちらからは090で始まる番号で掛けさせていただきますのでよろしくお願いします。",ja,,1.4)        ;agi(googletts.agi,"応答メッセージ(区切りのいいとことで半角スペースを入れないと全文読み上げない)",言語の種類(設定値は公式サイトのREADME.txtを参照),IVR(任意),読み上げ速度)
exten => 200,n,Voicemail(500)
exten => 200,n,Wait(1)
exten => 200,n,Hangup()
root@rp3b-01:/usr/share/asterisk/agi-bin/# service asterisk restart



#所有者を戻す
root@rp3b-01:/etc/asterisk# chown asterisk asterisk.conf
root@rp3b-01:/etc/asterisk# chown asterisk extensions.conf
root@rp3b-01:/etc/asterisk# chown asterisk sip.conf
root@rp3b-01:/etc/asterisk# chown asterisk voicemail.conf



#所有者が元に戻ったか確認する
pi@rp3b-01:/etc/asterisk $ ls -l
合計 xx
~
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 asterisk.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 extensions.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 sip.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 voicemail.conf
~



#最後にAsteriskを再起動
root@rp3b-01:/etc/asterisk# service asterisk restart



#動作確認
留守電を吹き込んでメール転送されているか。



#録音メッセージの削除
##保存先はasterisk.confの[directories](!)astspooldir => のディレクトリ配下の/voicemail/default/受信箱番号/INBOX
root@rp3b-01:/var/spool/asterisk/voicemail/default/500/INBOX# ls
msg0000.WAV  msg0000.txt
root@rp3b-01:/var/spool/asterisk/voicemail/default/500/INBOX# rm *      ;メッセージ全削除
root@rp3b-01:/var/spool/asterisk/voicemail/default/500/INBOX# ls
root@rp3b-01:/var/spool/asterisk/voicemail/default/500/INBOX# 



#tech
