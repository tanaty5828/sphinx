

# How to edit this homepages

このページは、git及びgithubを使って管理されているため、誰でも編集することができます。

gitはオリジナルを編集せずに自分だけで編集ができ、後で統合することができるためとても便利です。

ここではgitの使い方及び編集の仕方を解説します。

## アカウント作成

**git**のアカウントと**github**のアカウントは別物です。

まず、[git入門](https://backlog.com/ja/git-tutorial/intro/06/)を参考に、gitのアカウントを作成します。

次に、[githubアカウント](https://qiita.com/kooohei/items/361da3c9dbb6e0c7946b)を参考に、アカウントを作ります。

更に[github ssh](https://qiita.com/shizuma/items/2b2f873a0034839e47ce#%E5%85%AC%E9%96%8B%E9%8D%B5%E3%82%92github%E3%81%AB%E3%82%A2%E3%83%83%E3%83%97%E3%81%99%E3%82%8B)を参考にアカウントに公開鍵を登録しておくと便利です。（既存のものでいい）



## 編集の流れ

図で示すと、

![](https://backlog.com/ja/git-tutorial/assets/img/pull-request/pull_request3_1.png)

上記のようになっており、皆さんには左の開発者の1~5を行ってもらいます。



### リモートリポジトリをclone（上図１）

わかりやすく言うと、ネットワーク上にあるファイルを持ってくるだけです。

今回は、`~/Document/web`で作業しますが、実際はどこでも構いません。

```shell
cd ~/Document/web/ #ディレクトリの移動
git clone https://github.com/tanaty5828/siege_source #データのダウンロード
```



### ブランチを分ける（上図２）

このままではオリジナルが変更されてしまうので、ブランチを分けます。

わかりやすく言うと、変更するからオリジナルを残してね〜という意味になります。

```shell
cd siege_source #ディレクトリの移動
git branch ishikawa #ブランチの作成、名前は自由です。
git checkout ishikawa #ブランチの移動
```



### 編集をする（上図３）

`siege_source/`の中身は、内容ごとにディレクトリがわけられています。

編集のテスト用に`make_homepages/sandbox.md`というファイルがあるのでここに入り、

中にある`sandbox.md`を編集してみましょう。

```shell
cd make_homepages
vi sandbox.md
#適当に編集…
```



### pushする（上図４）

ここまでの編集をネットに送ります。

```shell
cd ~/Document/web/siege_source/ #siege_source/まで戻る。
git add . #変更履歴の保存
git commit -m "test" #どのような変更を行ったかのメモ
git push https://github.com/tanaty5828/siege_source hishikawa #送る。"hishikawa"はブランチを分けたときの名前にする。
## githubのUsernameとpasswordを入力
```



### pullリクエストの作成（上図５）

このままだと変更は管理者に伝わってないので、プルリクエストを送ります。

[プルリクエストの作成](https://backlog.com/ja/git-tutorial/pull-request/06/)を参考に、送っていただければすぐに反映します。

### 新しい編集

自分の更新から時間が経っていると、リモートの更新がローカルに反映されてないことがあります。

その時は、`fetch`と`pull`を利用しリモートの更新をダウンロードします。

```shell
cd ~/Document/web/siege_source/ #siege_source/まで戻る。
git fetch -p # 更新を確認。リモートの更新情報が表示される。
git pull origin master # リモートで更新されたデータをダウンロード
```

こうして、また新しいブランチを作る->編集する の流れを繰り返します。


## テキストの記載法

ソースコードの書き方には、2種類あり、

- Markdown(`.md`)
- reStructuredText(`.rst`)

があります。Markdownのほうが書きやすいけどできることは少なく、reStructuredTextのほうが書きにくく高機能です。

詳しい書き方は、

- [Markdown](https://qiita.com/Minalinsky_1911/items/b684cfabe0f2fde0c67b)

- [reStructuredText](https://planset-study-sphinx.readthedocs.io/ja/latest/04.html)

を参考にしてください。

エディターは`vim`でも`emacs`でも`vscode`でも何でもいいですが、Markdownを使うならMacでもWindowsでも使える[Typora](https://typora.io/) がおすすめです。

## ディレクトリの構成

```shell
.
├── device
│   └── main.md
├── Gnuplot
│   └── Gnuplot_01.md
├── intro
│   └── tanaka.md
├── linux
│   ├── linux1.md
│   ├── linuxcommand.md
│   ├── linuxq1.md
│   ├── ssh.md
│   └── vim_0.md
├── make_homepages
│   ├── how_to_use_git.md
│   └── sandbox.md
├── MD
│   ├── Discovery_studio.md
│   ├── fftw.md
│   ├── gromacs.md
│   ├── lammps.md
│   ├── mpdyn.md
│   ├── namd.md
│   ├── packmol.md
│   ├── plumed.md
│   └── vmd.md
├── python
│   ├── anaconda.md
│   ├── matplotlib.md
│   ├── MDAnalysis.md
│   └── MDAnalysis_tutorial.md
├── README.md
├── slack
│   └── insto.md
├── _static
│   └── css
├── tanaka
│   ├── home_build_pc.md
│   ├── images
│   └── Readable Code.md
├── trip
│   └── trip.md
└── welcome
```

となっています（現在）。内容ごとに分かれているので、例えば`gromacs`のページを作成したければ、

まず`gromacs`のディレクトリを作り、その中で`~~~.md`のファイルを作り書き込んで行きます。



## Git の便利なコマンドまとめ

### 現在の状況を表示する

```shell
# 現在の状況を表示
git status

# output

# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   make_homepages/how_to_use_git.md
#
```

これは、今`master`ブランチにいて`make_homepages/how_to_use_git.md`というファイルが編集されているが、

まだコミットされていないという意味である。



### これまでのコミット履歴をみる

```shell
# 履歴をみる
git log

# output

commit f65397b64d315d24ef8a586d0d0a2cb4e69c7b20
Merge: efd639e 1540efa
Author: Hiroki tanaka <tanaka@tanaka.og.apchem.nagoya-u.ac.jp>
Date:   Thu Jun 13 15:19:47 2019 +0900

    web

commit efd639e1454a379bb189322d49176872500b02ce
Author: Tsurumaki <tsurumaki.shuhei@d.mbox.nagoya-u.ac.jp>
Date:   Thu Jun 13 14:48:59 2019 +0900

    aaa
```

コミット履歴やマージ履歴が確認できる。

ただし,`git log`は少々細かいので、`git reflog`もおすすめである。

```shell
git reflog

# output

b9411a7 HEAD@{0}: commit: ren
8b3fd63 HEAD@{1}: commit: note
904d45d HEAD@{2}: commit: namd
24699f5 HEAD@{3}: commit: mdpages2
982e5ae HEAD@{4}: commit: mdpages
cb8d492 HEAD@{5}: commit: namechenge
3177df3 HEAD@{6}: commit: namechenge
f65397b HEAD@{7}: commit (merge): web
3a00161 HEAD@{8}: pull origin hishikawa: Merge made by the 'recursive' strategy.
708049f HEAD@{9}: commit: web
82bce41 HEAD@{10}: commit: unknown
d7f1070 HEAD@{11}: commit: git
ae755eb HEAD@{12}: commit: image
2570c4a HEAD@{13}: commit: git
c1490e7 HEAD@{14}: commit: md-pages
f3bb236 HEAD@{15}: checkout: moving from hishikawa to master
f3bb236 HEAD@{16}: checkout: moving from master to hishikawa
f3bb236 HEAD@{17}: commit (initial): first-commit
```

と、ログが並びわかりやすいと思う。



### ファイルを編集したが、やっぱり`git clone`したときの状態に戻したい(まだ`git add .`していない。)

```shell
# myfile.txt を元の状態に戻したい。
git checkout HEAD myfile.txt

# ブランチ全体を戻す。
git checkout HEAD
```

上のコマンドを推奨する。



### ファイル内の変更箇所を見たい

```shell
# myfile.txtの差分を表示
git diff myfile.txt

# output

-# output
+# output_data
```

削除した行は`-`で、追加した行は`+`で表示される。



###  ブランチの表示

```shell
#すべてのブランチ（リモート・ローカル問わず）を表示
git branch -a

# output
  origin/Gnuplot
  origin/master
```



### ブランチの作成、移動をまとめて

```shell
# ブランチの作成、移動を１コマンドで
git checkout -b ishikawa

# こうするより楽
git branch ishikawa
git checkout ishikawa
```

