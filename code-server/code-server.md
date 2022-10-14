# 参考サイト
- [RaspberryPiにcode-serverをインストールして、ブラウザ上からVisual Studio Codeを使ってみる - uepon日々の備忘録](https://uepon.hatenadiary.com/entry/2021/06/01/003837)

# 確認
## インストールできるかの確認
```
sudo update
sudo upgrade

curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
```
## 出力結果
```
Raspbian GNU/Linux 10 (buster)
No standalone releases for armv7l.
Falling back to installation from npm.
Installing latest from npm.

Please install npm to install code-server!
You will need at least node v12 and a few C dependencies.
See the docs https://coder.com/docs/code-server/latest/install#npm
(
    Raspbian GNU/Linux 10 (バスター) 
    armv7l のスタンドアロン リリースはありません。
    npm からのインストールにフォールバックします。
    npm から最新のものをインストールします。
    
    npm をインストールして code-server をインストールしてください!
    少なくともノード v12 といくつかの C 依存関係が必要です。
    ドキュメントを参照してください https://coder.com/docs/code-server/latest/install#npm
)
```
## 出力結果に対する対処
