- [構築状況](#構築状況)
  - [構築状況](#構築状況-1)
  - [参考サイト(Chrome Bookmark:Remote-Print)](#参考サイトchrome-bookmarkremote-print)
- [cupsのインストール](#cupsのインストール)
  - [○まずはrpのcloneバックアップ](#まずはrpのcloneバックアップ)
    - [手順](#手順)
  - [○インストール作業](#インストール作業)
  - [○基本的なサービスの動作確認](#基本的なサービスの動作確認)
- [/etc/cups/cupsd.confの編集](#etccupscupsdconfの編集)
  - [○まずはcupsd.confのバックアップ](#まずはcupsdconfのバックアップ)
  - [○編集作業](#編集作業)
- [ユーザーpiを印刷制御するCUPSのグループに追加する。](#ユーザーpiを印刷制御するcupsのグループに追加する)
- [サービスをリスタート](#サービスをリスタート)
- [sambaのインストール](#sambaのインストール)
- [/etc/samba/smb.confの編集](#etcsambasmbconfの編集)
- [サービスをリスタート](#サービスをリスタート-1)
- [CUPSにプリンタ登録](#cupsにプリンタ登録)
  - [プリンタの情報取得(ドライバーレス印刷をサポートしているプリンタかどうか判定)](#プリンタの情報取得ドライバーレス印刷をサポートしているプリンタかどうか判定)
  - [管理画面にアクセス](#管理画面にアクセス)
## 
&nbsp;  
&nbsp;  
&nbsp;  
# 構築状況
## 構築状況
CUPSにBrotherの登録は192.168.1.5でできた→CUPS内からのテスト印刷はできた。  
MacからYamahaに有線で繋いで同一NWにしたらMACにプリンタ登録できた。  
もちろんサーバー(cups)にはアクセスできているのだが(VPNでも)、しかしMacから印刷ができない(認証を求められている模様(スクリーンショット参照))。  
また別ネットワーク(192.168.1.0(VPNも))だとプリンタすら認識しない。  
結局、外部ネットワークからはセキュリティポリシーでできない模様・・・  
&nbsp;  
Brother MFC-J6583CDWは無線が192.168.1.5・有線が192.168.2.5に設定している。どちらか片方しか動作しない仕様になっている。
##
&nbsp;  
## 参考サイト(Chrome Bookmark:Remote-Print)
> [16.5. ドライバーレス印刷の設定 Red Hat Enterprise Linux 8 | Red Hat Customer Portal](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/configuring-driverless-printing_configuring-printing)  
> [16.3. CUPS Web UI へのアクセスおよび設定 Red Hat Enterprise Linux 8 | Red Hat Customer Portal](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/starting-cups-web-ui_configuring-printing)  
> [サーマルプリンターをRaspberry Piでプリントサーバーに仕立てる | ラズパイダ](https://raspida.com/printserver-receipt)
##
&nbsp;  
&nbsp;  
&nbsp;  
# cupsのインストール  
## ○まずはrpのcloneバックアップ  
### 手順  
- (2022/07/19現在)/Users/kazuyoshimatsuoka/Documents/システム設定データ/rp3b-01/backup.txt  
&nbsp;
## ○インストール作業  
```
  pi@rp3b-01:~ $  sudo apt-get update
  pi@rp3b-01:~ $  sudo apt-get upgrade
  pi@rp3b-01:~ $  sudo apt install cups
```
&nbsp;
## ○基本的なサービスの動作確認
SSH自動ログオン・スキャナー・カメラ・LED.....  
&nbsp;  
&nbsp;  
&nbsp;  
&nbsp;  
# /etc/cups/cupsd.confの編集
## ○まずはcupsd.confのバックアップ
```
pi@rp3b-01:/etc/cups $ sudo cp cupsd.conf cupsd.conf.org
```
&nbsp;  
## ○編集作業
**cupsd.conf**  
```
#Listen localhost:631
Port 631
~
~
~
# Restrict access to the server...
<Location />
  Allow from 192.168.2.*/24
  Allow from 192.168.1.*/24
  Order allow,deny
</Location>

# Restrict access to the admin pages...
<Location /admin>
  Allow from 192.168.2.*/24
  Allow from 192.168.1.*/24
  Order allow,deny
</Location>

# Restrict access to configuration files...
<Location /admin/conf>
  Allow from 192.168.2.*/24
  Allow from 192.168.1.*/24
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
</Location>

# Restrict access to log files...
<Location /admin/log>
  Allow from 192.168.2.*/24
  Allow from 192.168.1.*/24
  AuthType Default
  Require user @SYSTEM
  Order allow,deny
</Location>
```
&nbsp;  
# ユーザーpiを印刷制御するCUPSのグループに追加する。
```
root@rp3b-01:/etc/cups# sudo adduser pi lpadmin
ユーザ `pi' をグループ `lpadmin' に追加しています...
ユーザ pi をグループ lpadmin に追加
完了。
root@rp3b-01:/etc/cups# 
```
&nbsp;  
# サービスをリスタート
```
root@rp3b-01:/etc/cups# sudo systemctl restart cups
root@rp3b-01:/etc/cups# 
```
&nbsp;  
# sambaのインストール
```
root@rp3b-01:  sudo apt install samba
```
&nbsp;  
# /etc/samba/smb.confの編集
**smb.conf**  
最下部に以下を追記  
```
[MFC-J6583CDW]
    comment=Brother|MFC-J6583CDW
    printer=Brother|MFC-J6583CDW
    path=/var/spool/samba
    printing=cups
    printable=yes
    printer admin=@admin root pi
    user client driver=yes
    guest ok=yes
    writable=no
    write list=@adm root pi
```
&nbsp;  
# サービスをリスタート
```
root@rp3b-01:/etc/samba# sudo smbcontrol all reload-config
root@rp3b-01:/etc/samba# 
```
&nbsp;  
<!--# プリンタードライバのインストール
## メーカーサイトよりユーザーホームディレクトリにダウンロード  
```
root@rp3b-01:/home/pi# wget -P /home/pi/ https://download.brother.com/welcome/dlf104563/mfcj6583cdwpdrv-1.0.0-0.i386.deb
--2022-07-10 14:36:54--  https://download.brother.com/welcome/dlf104563/mfcj6583cdwpdrv-1.0.0-0.i386.deb
download.brother.com (download.brother.com) をDNSに問いあわせています... 153.145.76.251
download.brother.com (download.brother.com)|153.145.76.251|:443 に接続しています... 接続しました。
HTTP による接続要求を送信しました、応答を待っています... 200 OK
長さ: 3196566 (3.0M) [image/x-deb]
`/home/pi/mfcj6583cdwpdrv-1.0.0-0.i386.deb' に保存中

mfcj6583cdwpdrv-1.0.0-0. 100%[================================>]   3.05M  7.72MB/s 時間 0.4s     

2022-07-10 14:36:55 (7.72 MB/s) - `/home/pi/mfcj6583cdwpdrv-1.0.0-0.i386.deb' へ保存完了 [3196566/3196566]
```
&nbsp;  
## ドライバのインストール
```
root@rp3b-01:/home/pi# ls
mfcj6583cdwpdrv-1.0.0-0.i386.deb
root@rp3b-01:/home/pi# 
root@rp3b-01:/home/pi# dpkg -i --force-all mfcj6583cdwpdrv-1.0.0-0.i386.deb
dpkg: 警告: --force が有効なので、問題を無視します:
dpkg: 警告: パッケージアーキテクチャ (i386) がシステム (armhf) と一致しません
以前に未選択のパッケージ mfcj6583cdwpdrv:i386 を選択しています。
(データベースを読み込んでいます ... 現在 55446 個のファイルとディレクトリがインストールされています。)
mfcj6583cdwpdrv-1.0.0-0.i386.deb を展開する準備をしています ...
mfcj6583cdwpdrv:i386 (1.0.0-0) を展開しています...
mfcj6583cdwpdrv:i386 (1.0.0-0) を設定しています ...
[ ok ] Restarting cups (via systemctl): cups.service.
lpadmin -p MFCJ6583CDW -E -v usb://dev/usb/lp0 -P /usr/share/cups/model/Brother/brother_mfcj6583cdw_printer_en.ppd
[ ok ] Restarting cups (via systemctl): cups.service.
root@rp3b-01:/home/pi# 
```
&nbsp;  
## インストールの確認
root@rp3b-01:/home/pi# dpkg -l | grep Brother
ii  mfcj6583cdwpdrv:i386           1.0.0-0                             i386         Brother Inkjet Printer Driver 
root@rp3b-01:/home/pi# 
&nbsp;  
&nbsp;  -->
# CUPSにプリンタ登録
## プリンタの情報取得(ドライバーレス印刷をサポートしているプリンタかどうか判定)  
```
pi@rp3b-01:/etc $ ipptool -tv ipp://192.168.1.5:631/ipp/print get-printer-attributes.test
```
取得した情報の中に、  
- ipp-version-supportedに2.0以降が含まれているか。
- document-format-supportedにApple Raster・PCLm・PWG Raster Formatのいずれかが含まれているか。
###
&nbsp;  
## 管理画面にアクセス  
1. http://192.168.2.211:631/adminにアクセス
  - プライバシー保護の画面が出たら'詳細情報を表示する' → '192.168.2.211にアクセスする(安全ではありません)'
  - 認証を聞かれたらpi/swn20040601(以後も同じ)
2. 管理-プリンター-プリンターの追加
3. その他のネットワークプリンター内の'インターネット印刷プロトコル(ipp)'を選択して'続ける'
4. '接続:'テキストボックスに「ipp://192.168.1.5/ipp/print」を入力して'続ける'
5. 各項目に入力していく。
   - 名前:Brother|MFC-J6583CDW
   - 説明:Brother|MFC-J6583CDW
   - 場所:Kawachibunka
   - 共有:このプリンターを共有するにチェックを入れる
   - '続ける'
6. 'メーカー' 'モデル':「(Brother MFC-J6583CDW-IPP Everywhere」を選択→'プリンターの追加'
7. 'デフォルトオプションの設定'
8. 登録したプリンタの設定内容画面が表示される。
9. 「メンテナンス」プルダウンメニュー内の'テストページの印刷を選択'→印刷されたことを確認する。
