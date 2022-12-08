# 参考サイト
- [VS Code Serverの使い方](https://zenn.dev/kato_k/articles/6301d35b3d8d3c)
- [VS コード サーバー](https://code.visualstudio.com/blogs/2022/07/07/vscode-server)
- [RaspberryPiにcode-serverをインストールして、ブラウザ上からVisual Studio Codeを使ってみる - uepon日々の備忘録](https://uepon.hatenadiary.com/entry/2021/06/01/003837)

# 確認
## インストールできるかの確認
```
sudo apt-get update
sudo apt-get upgrade

curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
```
### 出力結果
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
### node.jsとnpmをインストールする。
```
sudo apt-get install node.js npm

npm --version
5.8.0

nodejs --version
v10.24.0
```
### node.jsをv12にする
#### NVMのインストール
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```
#### 最新のNode.jsのインストール
```
nvm install stable
```
### インストールできるか再度確認
```
curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
```
#### 出力結果
```
Raspbian GNU/Linux 10 (buster)
No standalone releases for armv7l.
Falling back to installation from npm.
Installing latest from npm.

Installing with npm.

+ npm install -g code-server --unsafe-perm

npm package has been installed.

Extend your path to use code-server:
  PATH="$(npm bin -g):$PATH"
Then run with:
  code-server
(
  Raspbian GNU/Linux 10 (バスター)
  armv7l のスタンドアロン リリースはありません。
  npm からのインストールにフォールバックします。
  npm から最新のものをインストールします。

  npmでインストール。

  + npm install -g code-server --unsafe-perm

  npm パッケージがインストールされました。

  code-server を使用するようにパスを拡張します。
    PATH="$(npm bin -g):$PATH"
  次に、次のように実行します。
    code-server
)
```
# インストール
`wget -O- https://aka.ms/install-vscode-server/setup.sh | sh`  
インストール先  
/usr/local/bin/code-server

