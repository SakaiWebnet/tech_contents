# Site
[【VSCode×GitHub】使い方から連携手順まで完全解説！ - PENGIN BLOG](https://pengi-n.co.jp/blog/vscode-github/)  
[【Mac】Gitの環境構築をしよう！ | プログラミングの入門なら基礎から学べるProgate[プロゲート]](https://prog-8.com/docs/git-env)  
[Gitの設定をgit configで確認・変更 | note.nkmk.me](https://note.nkmk.me/git-config-setting/)  
[VSCodeでGitソース管理時におすすめのプラグイン3選！ | コードライク](https://codelikes.com/vscode-git-plugin/#toc10)
##
&nbsp;

# 各種インストール
- Git
  - git --version                       ;実行してバージョンが表示されればインストール済み
- VS Code
- VS Code拡張機能
  - Git History
  - Git Lens-Git supercharged
  - Git Graph
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
## GitHubに置く(貼り付ける)公開鍵の内容をコピーする。  
`pbcopy > .ssh/id_rsa.pub`  
## GitHubにSSH接続設定
1. ヘッダー右上のプロフィール画像内の’Settings'をクリック。
2. 左サイドメニュー内の'SSH and GPG keys'をクリック。
3. 'New SSH Key'をクリック。
4. ’Title'欄に任意の名前を入力(ex.接続するPC名)。
5. 'Key Type'は'Authentication Key'のまま。
6. 'Key'欄には先ほどコピーした公開鍵の内容をペーストする。
7. 'Add SSH Key'をクリック。
8. パスワード入力を求められたらGitHubログインパスワードを入力→OK
## PCからGitHubへ接続確認
```
ssh -T git@github.com                                                                                   ;Githubへ接続

出力例(SSH agent使用)
The authenticity of host 'github.com (20.27.177.113)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes                                ;yesを入力→Enter
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
Hi SakaiWebnet! You've successfully authenticated, but GitHub does not provide shell access.            ;ユーザー名が表示されていればOK
```

