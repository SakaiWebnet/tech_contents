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
[Text to speech using Google Translate for Asterisk PBX](http://zaf.github.io/asterisk-googletts/)
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

# 録音されたメッセージファイルをメール送信するためのssmtp mailutilsの設定
## raspiのcloneバックアップ
```
lsblk
sudo rpi-clone sda -f
```
## ssmtp mailutilsのインストール
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install ssmtp mailutils
```
## ssmtp.confファイルのバックアップ&所有者変更&ファイル編集
```
cd /etc/ssmtp
cp ssmtp.conf ssmtp.conf.bak
sudo pi ssmtp.conf
ls -l
  合計 12
  -rw-r--r-- 1 root root 200  7月 20  2014 revaliases
  -rw-r--r-- 1 pi   root 707  1月  9 11:01 ssmtp.conf
  -rw-r--r-- 1 root root 578  1月  5 10:28 ssmtp.conf.bak
```
ssmtp.confの中身を全削除して以下の通りだけにする。
```
root=kmtok@violet.plala.or.jp       ;メールアドレス（例えば、abcdefgh@gmail.com など）
Mailhub=secure.plala.or.jp:587      ;送信用ドメインのアドレス/ポート
AuthUser=kmtok@violet.plala.or.jp   ;メールアカウント
AuthPass=mailpassword               ;メールパスワード
AuthMethod=LOGIN
UseSTARTTLS=Yes                     ;メールサーバーの仕様に基づいて設定
UseTLS=Yes                          ;メールサーバーの仕様に基づいて設定
hostname=secure.plala.or.jp         ;メールサーバー名(‘localhost’ では hylafax で DNS エラーになる)
```
メール送信テスト
```
mail guest@sakaiwebnet.com          ;任意のメール送信先を入力してEnter
Cc:                                 ;Enter
Subject: test                       ;任意のタイトルを入力してEnter
test                                ;任意の本文を入力
キーボードのCtrl+Dで送信操作。
メールが届いたかを確認する。
```
所有者を戻す
`sudo chown root ssmtp.conf`
所有者が元に戻ったか確認する
```
ls -l
  合計 12
  -rw-r--r-- 1 root root 200  7月 20  2014 revaliases
  -rw-r--r-- 1 root root 707  1月  9 11:01 ssmtp.conf
  -rw-r--r-- 1 root root 578  1月  5 10:28 ssmtp.conf.bak
```
##
&nbsp;  

# 留守電の設定
## /etc/asterisk/voicemail.confの編集(内容を全削除して以下のみにする)
```
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
```
## /etc/asterisk/extensions.confファイルをVScodeで編集(全削除して下記のみ記述する)
```
[general]
static=yes
writeprotect=no

[globals]
MYNUMBER=0722884159                   ;電話番号
USEVOICEMAIL=YES                      ;留守電有効化

[default]                             ;着信した時の挙動を上から順番に設定
exten => 200,1,Ringing                ;着信
exten => 200,n,Wait(3)                ;3秒間呼び出し
exten => 200,n,Voicemail(500)         ;留守電スタート
exten => 200,n,Wait(1)                ;相手が切った後1秒間ポーズ
exten => 200,n,Hangup()               ;切断
```


# 応答音声日本語化
## あらかじめ作成されている音声ファイル(英語)を利用
```
cd /usr/share/asterisk/sounds                                                                 ;asterisk.conf内のastdatadirの場所
wget https://voip-info.jp/downloads/asterisk/sounds/1_6/asterisk-sound-jp_16_pre.tar.gz       ;日本語音声ファイルのダウンロード
ls /usr/share/asterisk/sounds                                                                 ;ダウンロード完了の確認
  asterisk-sound-jp_16_pre.tar.gz  custom  en  en_US  en_US_f_Allison  priv-callerintros  recordings
tar xzvf /usr/share/asterisk/sounds/asterisk-sound-jp_16_pre.tar.gz                           ;解凍
ls /usr/share/asterisk/sounds                                                                 ;解凍完了の確認(jaディレクトリがそれ)
  asterisk-sound-jp_16_pre.tar.gz  custom  en  en_US  en_US_f_Allison  ja  priv-callerintros  recordings
rm /usr/share/asterisk/sounds/asterisk-sound-jp_16_pre.tar.gz                                 ;ダウンロードファイルの削除
ls /usr/share/asterisk/sounds                                                                 ;再度ディレクトリ内の確認
  custom  en  en_US  en_US_f_Allison  ja  priv-callerintros  recordings
```
## Google TTSの利用
### Text to speech using Google Translate for Asterisk PBXのダウンロード
```
cd /usr/share/asterisk/agi-bin                                  ;asterisk.conf内のastagidirの場所
cd /var/lib/asterisk/agi-bin/                                   ;agi-binディレクトリに移動。がない場合は新規作成する。
git clone https://github.com/zaf/asterisk-googletts.git         ;Text to speech using Google Translate for Asterisk PBXのダウンロード。
cd asterisk-googletts                                           ;asterisk-googlettsディレクトリに移動。
cp -a googletts.agi ..                                          ;asterisk-googletts内にあるgoogletts.agiをagi-binディレクトリにコピー。
cd ..                                                           ;一つ上のディレクトリagi-binに移動。
sudo chmod 755 googletts.agi                                    ;実行権限付与。
```
### Text to speech using Google Translate for Asterisk PBXが依存するソフトのインストール
```
sudo apt-get update
sudo apt-get install libwww-perl libcrypt-ssleay-perl sox mpg123
```
### 動作テストのためのextensions.confの編集
他の設定はコメントアウトしておく。
```
[default]
```
exten => 200,1,Answer()
exten => 200,n,Wait(2)
exten => 200,n,agi(googletts.agi,"これはテストです",ja)
exten => 200,n,Wait(2)
exten => 200,n,Hangup()
'''
`sudo service asterisk restart`
架電して「これはテストです」と応答すればOK
### 本番稼働のためのextensions.confの編集
前項のテスト用設定はコメントアウト or 削除しておく
```
[default]
;#Google TTS(変更後は"service asterisk restart"を忘れずに!)
exten => 200,1,Ringing
exten => 200,n,Wait(3)
exten => 200,n,agi(googletts.agi,"はい、さかいウェブネットです。お電話ありがとうございます。 恐れ入りますが、2022年より、留守電に吹き込まれた内容をもとに、090の番号で始まる携帯電話にて折り返しのご連絡をさせていただく対応となりました。 なお当方のホームページ、さかいウェブネットドットコム内のお問い合わせからもご用件を送信いただけます。 今からメッセージを吹き込んでいただく際は、プーーっと発信音の後に、3分以内でご用件と折り返し先電話番号を仰ってください。 またこちらには、かけていただいている電話番号は表示されていませんので、必ず折り返し先電話番号を吹き込んでいただきますようお願いいたします。 こちらからは090で始まる番号で掛けさせていただきますのでよろしくお願いします。",ja,,1.4)
exten => 200,n,Voicemail(500)
exten => 200,n,Wait(1)
exten => 200,n,Hangup()
```
agi(googletts.agi,"応答メッセージ(区切りのいいとことで半角スペースを入れないと全文読み上げない)",言語の種類(設定値は公式サイトのREADME.txtを参照),IVR(任意),読み上げ速度)  
`sudo service asterisk restart`
##
&nbsp;  

# 設定ファイルの所有者を戻す
```
sudo chown asterisk /etc/asterisk/asterisk.conf
sudo chown asterisk /etc/asterisk/extensions.conf
sudo chown asterisk /etc/asterisk/sip.conf
sudo chown asterisk /etc/asterisk/voicemail.conf
```
所有者が元に戻ったか確認する
```
ls -l /etc/asterisk

合計 xx
~
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 asterisk.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 extensions.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 sip.conf
-rw-r--r-- 1 asterisk asterisk 707  1月  9 11:01 voicemail.conf
~
```
**最後にAsteriskを再起動**  
`sudo service asterisk restart`  
##  
&nbsp;  

# 動作確認
**留守電を吹き込んでメール転送されているか。**  
##  
&nbsp;  
##  
&nbsp;  

# 録音メッセージの削除
```
メッセージの保管場所
ls /var/spool/asterisk/voicemail/default/500/INBOX      ;asterisk.conf内のastspooldirのディレクトリ配下の/voicemail/default/受信箱番号/INBOX。
  msg0000.WAV  msg0000.txt                              ;録音されたメッセージの実態ファイル。
sudo rm *                                               ;メッセージ全削除
ls
                                                        ;何も表示されない(空になった)ことを確認する

