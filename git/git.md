# Site
[【VSCode×GitHub】使い方から連携手順まで完全解説！ - PENGIN BLOG](https://pengi-n.co.jp/blog/vscode-github/)  
[【Mac】Gitの環境構築をしよう！ | プログラミングの入門なら基礎から学べるProgate[プロゲート]](https://prog-8.com/docs/git-env)  
[Gitの設定をgit configで確認・変更 | note.nkmk.me](https://note.nkmk.me/git-config-setting/)
##
&nbsp;

# GitHubにリモートリポジトリ作成
- GitHubにログイン
- 右上の"+" → ”New repository” → "Create s new repository"となる
- "Repository name"に任意のリポジトリ名(プロジェクト名)を入力
- 公開範囲は基本"Private"(非公開)とする
- "Create repository"を押下して作成
##  
&nbsp;

# ローカル(PC)リポジトリ作成
## 初期設定
```
git config --global user.name 'km_swn'
git config --global user.email 'sakaiwebnet@gmail.com'
git config --global core.editor 'code --wait'
git config --global merge.tool 'code --wait "$MERGED"'
git config --global push.default simple 
```
## 確認
```
git config user.name
git config user.email
.....
```
## 任意のディレクトリにリポジトリ作成(Git適用)
適用するディレクトリに移動  
`cd *ディレクトリ*`  
リポジトリ作成(Git適用)  
`git init`  
ブランチ名を”main”に変更  
`git branch -M main`  
ディレクトリ内に.gitディレクトリが作成されていることを確認する。  
##  
&nbsp;

# GitHubにSSH接続設定
秘密鍵の内容をコピーする。
