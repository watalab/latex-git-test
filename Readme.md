# GitでLaTeXの文書のバージョン管理する＋latexdiff-vcで差分を出す

## GitでLaTeXのソースコードのバージョン管理
Gitの使い方やその詳細はこの文章の後半を確認すること。

### バージョン管理されてない状態からバージョン管理する流れ
ソースのあるフォルダで

```bash
git init
```

を実行。これでgitの実行の準備ができる。

ソースファイル（全部のTeXファイル）を管理の対象にする。

```bash
git add *.tex
```

履歴をコミットする。

```bash 
git commit
```

コミットログを書く。

このあと，変更履歴をgit add, git commitで追加する。履歴はgit logで確認できる。

### このリポジトリで練習する

git cloneコマンドでリポジトリをコピーする。

```bash
git@github.com:watalab/latex-git-test.git
```

コピーしたリポジトリ内のTeXファイルをどれでもいいので編集する。

```bash
git diff
```

これで差分がでる。

編集したファイルがsolution.texだとすると，

```bash
git add solution.tex
git commit
```

で履歴をコミットできる。


## latexdiff-vc
- Gitでバージョン管理しているLaTeXのソースがあったとする。
- ソースファイルは分割されており，mainのソースファイルがmain.texだとする。

latexdiff-vcの使い方例：現在のバージョンとリビジョンfebxxのファイルとの差分を生成。

``` bash
latexdiff-vc  -e utf8  --flatten -t CFONT --force --git -r febxx main.tex
```

+ -eはエンコードの指定
+ --flattenはソースファイルをinclude等で分割している場合のオプション。 
+ -t CFONTオプションは差分を色分けとフォントの変更で表現するオプション。なくてもいい。ただし，つけないと１つの変更履歴が複数行にわたるようだと，ちゃんと表示されない。
+ --forceは差分用のTeXファイルを強制的に上書き
+ --gitはバージョン管理がgit
+ -rはリビジョンの指定。


# Gitの使い方

## （序論）バージョン管理でなにができるか
+ コードの変更履歴・差分が見れる
+ コードの変更理由を残せる
+ コードの内容を以前の内容に戻す
+ 他人の変更点を取り込める
+ 似たような名前のファイルが増えるのを阻止できる
+ コード以外のドキュメントの管理

## Gitの概要

+ Gitは分散型のバージョン管理ソフト。変更等の一式を保管する「リポジトリ」を各ユーザーがもつ。自分が行った変更はローカルリポジトリが管理する。特に共有リポジトリは使わなくてもバージョン管理は可能。
+ コードなどの作業を行うディレクトリを「ワーキングツリー」と呼ぶ。   
+ コードを変更したら，変更を「コミット」することで，リポジトリに変更点が格納される。Gitではコミットの前にステージングエリア（indexともいう）という段階があり，コミットする変更点を調節することができる。
+ 既存のプログラムがあり，そこから開発するときはリポジトリのクローンを作成する。このとき，既存のリポジトリからプログラム一式がコピーされる。
+ コード開発がまとまったら共有リポジトリに反映する（この操作を「プッシュ」と呼ぶ）。変更を各ユーザーが「プル」することで，変更を共有することが可能になる。
+ プログラムの開発中に「ブランチ」を作成し，試験的な開発部分とメイン部分を分岐させることができる。またブランチを後からマージすることも可能。ブランチを変更する操作を「チェックアウト」と呼ぶ。
+ Gitはプログラムの内容を追跡している。つまりソースコードの中の関数や変数の内容から変更点を認識する。
+ プログラムの内容に競合が衝突した場合はメッセージが出て，人間が修正することになる。

## 初期設定
gitをインストールして，使いはじめるときにユーザ情報の初期設定を行う。初期設定にはgit configコマンドを使う。

### git config

git configで設定できる項目はたくさんある。まずはじめにユーザ名とメールアドレスを設定する。

``` bash
   $ git config --global user.name "Hoge Hoge" 
   $ git config --global user.email "hoge@g.u-fukui.ac.jp"
```   

Gitログをカラー表示したほうが見やすいので，color.uiを設定。

``` bash
   $ git config --global color.ui "auto"
```   

コミットメッセージを編集するエディタはデフォルトではViとなっている。Vi以外のエディタにする場合にはcore.editorを編集。（GUIのGitソフトを使う場合は関係ない）

``` bash
   $ git config --global core.editor emacs (エディタ名)
```   

改行コードの自動変換に関するオプションは--core.autocrlfで指定。

+ false 改行コードを変換しない
+ true チェックアウトしたときに改行コードをCRLFに変換。コミットしたときに改行コードをLFに変換。
+ input コミットするときのみ改行コードをLFへ変換する。

Windowsの改行コードはCRLFで，UnixがLF。

## 基本操作： git init, git add, git commit
Gitで最も基本的で重要な操作がgit add, git commit。git initでリポジトリを初期化して，git add, git commitを使って，履歴を残していく。

### git init
新しいGitのリポジトリを作成するときに使用。Gitで管理されていないフォルダ内でgit initと入力することで，Gitの設定ファイル等を作成してくれる。

管理したいコードがあるフォルダもしくはこれからコードを置くフォルダ内で

``` bash
   $ git init
```

git initを実行したフォルダ内では.gitというフォルダが作成される。この中に履歴のデータが残る。

ちなみに共有リポジトリを作成するときは--bareオプションをつけて初期化。（最初はお作法だと思って下さい）

``` bash
   $ git init --bare
```

### git add

変更内容をindexに登録して，コミットの候補にするためのコマンド。git addでindexに登録しただけではリポジトリに変更履歴は反映されない。git addでindexに登録した後，git commitでリポジトリに変更履歴を反映する。indexは反映すべき変更履歴の取捨選択や整理を行うためにある。

フォルダ内の全てのファイルをindexに登録する。

``` bash
   $ git add *
```

indexへの登録をやめるときはgit reset。git resetの詳細についてはまた後で。

``` bash
   $ git reset a.out main.o
```

変更箇所単位でindexに登録する。git diffしたときの差分のブロックごとにindexに登録するかどうかを決定できる。

``` bash
   $ git add -p
```

### git commit
リポジトリにindexに登録した変更履歴をコミットするためのコマンド。

コミットログをオプションで指定してコミットする。コミットログが短いときに使われる。

``` bash
   $ git commit -m "fix bug about xx"
```

コミットログをエディタで編集してコミットする。コマンド入力した後にエディタが起動するので，コミットログを書く。

``` bash
   $ git commit 
```

直前のコミットを修正するには，--amendオプションをつける。ファイルの登録漏れやコミットログの修正に使う。

``` bash
   $ git commit -m "fix bug aBOut xx" (コミットログを修正したい)
   $ git commit --amend
```

直前のコミットに追加しなかったファイルを追加するとき。直前のコミットにhoge.f90の変更履歴が追加される。

``` bash
   $ git add hoge.f90
   $ git commit --amend
```

## commitできるようになったあと（その他のコマンド）
git add, git commitで変更履歴をリポジトリに反映できるようになった後，現状・差分を確認したり，ファイル名を変更したりするときのコマンドを紹介。

### 現状の確認 git status
ワーキングツリー内のファイルの状態を表示。バージョン管理対象のファイル，indexに登録済みファイルなどが確認できる。

``` bash
   $ git status
```

ファイルを指定して表示

``` bash
   $ git satus main.f90
```
   
### コミットメッセージの確認 git log
現在のブランチのコミットメッセージの表示。

この文章を作ってる最中のログ。

``` bash
   $ git log
   commit 464265f54ede2f013be75ada97202aed20c3be0a
   Author: Ishigaki <Ishigaki>
   Date:   Tue Jan 5 17:02:18 2016 +0900

		初回の内容を修正。
		資料などを追加。

   commit cf9b8102532aca20a74f4d1cacb897652b2b2a5b
   Author: Ishigaki <Ishigaki>
   Date:   Wed Dec 2 13:38:25 2015 +0900

		分散管理のイメージ図を追加
```

コミットメッセージの1行目だけ表示。

``` bash
   $ git log --oneline
   464265f 初回の内容を修正。 資料などを追加。
   cf9b810 分散管理のイメージ図を追加
   450e229 アンケート結果追加，その他文章修正
   ce2dd1b イントロとGitの概要について記載
   ac4605d ファイル初期作成
```		

グラフを表示。ブランチがあるときは便利。

``` bash
   $ git log --graph
   * commit 464265f54ede2f013be75ada97202aed20c3be0a
   | Author: Ishigaki <Ishigaki>
   | Date:   Tue Jan 5 17:02:18 2016 +0900
   | 
   |     初回の内容を修正。
   |     資料などを追加。
   |  
   * commit cf9b8102532aca20a74f4d1cacb897652b2b2a5b
   | Author: Ishigaki <Ishigaki>
   | Date:   Wed Dec 2 13:38:25 2015 +0900
   | 
   |     分散管理のイメージ図を追加
   |  
```

指定ファイルのコミットメッセージを表示。

``` bash
   $ git log -- main.f90 （ファイル名）
```

各種条件に合うログだけ表示。

``` bash
   $ git log origin/master（ブランチ名） ←origin/masterブランチのコミットを表示
   $ git log --since='2015-12-01' --until '2015-12-31' ←2015年12月のコミットを表示
   $ git log --author='hoge' ←hogeさんが行ったコミットを表示
   $ git log -E --grep='refs #123' ←コミットメッセージがrefs #123にマッチするコミットを表示
```

### Gitで管理しているファイルの表示 git ls-files
Gitで管理対象となっているファイルの表示。

``` bash
   $ git ls-files
```   

### 変更履歴の比較 git diff
コミット-コミット，コミット-ワーキングツリー間の差分を表示。

indexとワーキングツリーの差分を表示。

``` bash
   $ git diff
   diff --git a/Fig/Git-OpenFOAM.png b/Fig/Git-OpenFOAM.png
   old mode 100755
   new mode 100644
   diff --git a/howtoGit1.rst b/howtoGit1.rst
   index 5eed68c..9827e54 100644
   --- a/howtoGit1.rst
   +++ b/howtoGit1.rst
   @@ -65,3 +65,8 @@ Gitの概要
   single: checkout
   single: clone
   
   +
   +.. raw:: latex
   +
   +   \clearpage
   +   
```

HEADとindexの差分（次のコミットでリポジトリに反映される内容）を表示。


``` bash
   $ git diff --cached
   diff --git a/howtoGit2.rst b/howtoGit2.rst
   new file mode 100644
   index 0000000..63b7e83
   --- /dev/null
   +++ b/howtoGit2.rst
   @@ -0,0 +1,261 @@
   +================
   +Gitの使い方2
   +================
   +
   +Gitのコマンドは100種類以上。基本的なコマンドを紹介。他のは困ったときに調べてみるのがいいかと。
   +
   +
```

コミットとワーキングツリーとの差分を表示。


``` bash
   $ git diff <commit>
```

コミット間の差分を表示。


``` bash
   $ git diff <commit1> <commit2>
```
   
### ファイル内容表示 git show
コミットの差分もしくはそのコミットでのファイルの内容を表示する。

git showの後にコミットIDを指定すると，コミットメッセージが表示されたあと，コミットによる変更がdiff形式で表示される。

git showの後にコミットID:ファイル名を指定するとそのコミットの時点でのファイルの内容が表示される。

``` bash
   $ git show cf9b81:howtoGit1.rst 
   ================
   Gitの使い方1
   ================

   バージョン管理でなにができるか
   ===========================
   + コードの変更履歴・差分が見れる
   + コードの変更理由を残せる
   + コードの内容を以前の内容に戻す
   + 他人の変更点を取り込める
   + 似たような名前のファイルが増えるのを阻止できる
   + コード以外のドキュメントの管理
```

### 各行の変更履歴を表示 git blame
ファイルの特定の場所の変更履歴を参照する。

どのコミットでだれが変更したかを一覧で表示する。

``` bash
   $ git blame No1.rst 
   464265f5 (Ishigaki 2016-01-05 17:02:18 +0900   1) .. raw:: latex
   464265f5 (Ishigaki 2016-01-05 17:02:18 +0900   2) 
   464265f5 (Ishigaki 2016-01-05 17:02:18 +0900   3)    \clearpage
   464265f5 (Ishigaki 2016-01-05 17:02:18 +0900   4) 
   464265f5 (Ishigaki 2016-01-05 17:02:18 +0900   5) 
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900   6) ================
   ce2dd1b2 (Ishigaki 2015-12-01 19:41:46 +0900   7) イントロ
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900   8) ================
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900   9) 
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900  10) 本勉強会の概要
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900  11) ====================
   ^ac4605d (Ishigaki 2015-11-30 14:30:07 +0900  12) 
```

履歴を表示する行を指定するには-L <start>,<end>を使う。

### リポジトリを検索 git grep
リポジトリで管理されているファイルを検索する。

### ファイルの削除 git rm
管理対象になっているファイルを削除する。

``` bash
   $ git rm hoge.f90
```

ファイルを残したまま，バージョン管理対象からはずす。

``` bash
   $ git rm --cached hoge.f90
```

### ファイルの移動 git mv
管理対象のファイルをリネームしたり，移動する。

``` bash
   $ git mv hoge.f90 hogehoge.f90
```   

## 参考資料

### Gitのインストール
+ Windows https://git-scm.com/book/ja/v2/使い始める-Gitのインストール
+ Windowsでは日本語設定が必要 msysgit で日本語を使いたい http://qiita.com/kumazo@github/items/2169e1ee7be278f82b94
+ GUIソフトを使うならSourceTreeがいい感じ https://ja.atlassian.com/software/sourcetree/overview/
+ 初心者のためのGitとSourceTree　〜サルでもわかるGit入門を試してみた〜 http://qiita.com/Simayichutatsu/items/cfaab4cbd340455aa255  

+ Visual Studioで使う

  - http://codezine.jp/article/detail/8436?p=3
  - https://git-scm.com/book/ja/v2/その他の環境でのGit-Visual-StudioでGitを使う
  - マイクロソフトがオープンソースとして出しているVisual Studio CodeならWin, Mac, Linuxで使えて，Gitのバージョン管理までできる。

+ Eclipseなど各IDEに対してGitを導入する方法はググれば出てきます。Vim, Emacs上で使えるGitもあります。

### Git使い方
+ こわくないGit http://www.slideshare.net/kotas/git-15276118
+ Git チュートリアル https://www.atlassian.com/ja/git/tutorial/git-basics
+ いつやるの？Git入門 http://www.slideshare.net/matsukaz/git-17499005?related=1
+ サルでも分かるGit入門 http://www.backlog.jp/git-guide/
+ Pro Git（日本語版） https://git-scm.com/book/ja/v2
+ ［初心者向け］こんなときどうする　GitのTips26選！ http://qiita.com/Keisuke69/items/35d60e4e375fc525ccbd
+ バージョン管理されていないシステムをバージョン管理していく http://qiita.com/genzouw/items/ce999466123c8c04286a
+ デザイナーがこうやってGit覚えて大好きになったよ http://qiita.com/yunico-jp/items/87bdd13971e82833f6bb
+ もっと早く知りたかった！ Gitが鬼のようにわかるスライド厳選7選 http://www.find-job.net/startup/7-git-slides
+ Git入門：Git初学習者のための効率的な学習方法を考えてみた https://takanabelab.wordpress.com/2014/12/13/git入門：git初学習者のための効率的な学習方法を考/
