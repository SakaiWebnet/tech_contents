# Site
[【VSCode×GitHub】使い方から連携手順まで完全解説！ - PENGIN BLOG](https://pengi-n.co.jp/blog/vscode-github/)  
[【Mac】Gitの環境構築をしよう！ | プログラミングの入門なら基礎から学べるProgate[プロゲート]](https://prog-8.com/docs/git-env)  
[Gitの設定をgit configで確認・変更 | note.nkmk.me](https://note.nkmk.me/git-config-setting/)  
[VSCodeでGitソース管理時におすすめのプラグイン3選！ | コードライク](https://codelikes.com/vscode-git-plugin/#toc10)  
[VSCodeでGit・GitHubを使う方法を解説する【初心者向き】](https://miya-system-works.com/blog/detail/vscode-github/)  
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
- 表示されたコマンドをコピペしておく
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

# VS Codeから直接プッシュできるようにする
- ローカルリポジトリがあるディレクトリに移動。
- ローカルリポジトリがあるディレクトリにて下記コマンドを順番に実行。
```
GitHubにてリモートリポジトリを作成した直後にコピペした"…or push an existing repository from the command line"内のコマンド

git remote add origin git@github.com:SakaiWebnet/tech_contents.git          ;GitHubと紐付け
git remote -v                                                               ;接続確認
    以下のように出力されればOK
        origin  git@github.com:SakaiWebnet/tech_contents.git (fetch)
        origin  git@github.com:SakaiWebnet/tech_contents.git (push)

git branch -M main                                                          ;mainブランチ作成
git push -u origin main                                                     ;mainブランチにプッシュ
    以下のように出力されればOK
        Enumerating objects: 49, done.
        Counting objects: 100% (49/49), done.
        Delta compression using up to 8 threads
        Compressing objects: 100% (43/43), done.
        Writing objects: 100% (49/49), 585.06 KiB | 3.63 MiB/s, done.
        Total 49 (delta 5), reused 0 (delta 0), pack-reused 0
        remote: Resolving deltas: 100% (5/5), done.
        To github.com:SakaiWebnet/tech_contents.git
        * [new branch]      main -> main
        Branch 'main' set up to track remote branch 'main' from 'origin'.
```
- GitHubのリモートリポジトリの'<>Code'タブ内にプッシュしたディレクトリやファイルが表示されているかを確認する。
##  
&nbsp;

# VS Codeにてコミット→GitHubへプッシュする
- ローカルリポジトリにてステージング→コミット&プッシュを行う。
    1. 左サイドメニューの'Source Control'ボタンを押下
    2. 'Change'内にある内容変更されたファイルがあるので、右端の'+'をクリックしてステージングを行う。
    3. そのファイルが'Staged Changes'に移行するので、’✔️Commit’ボタンの上にあるMessage欄に任意のコメントを入力する(ex.yyyymmddhhmm)。
    4. ’✔️Commit’ボタンプルダウンメニュー内の'Commit&Push'をクリックする。
- GitHubにて更新されているか確認する。