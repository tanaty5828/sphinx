# Linux Basic Questions

練習問題一覧

基礎編

## 問題：次の用語を説明せよ

- Linux
- ディレクトリ

### 解答

Linux : 簡単に言えば、"Windows"や"Mac"のような種類のこと。OS(オペレーティング・システム)ともいう

ディレクトリ : フォルダのこと。コンピュータではさまざまなデータを扱いますが、これらは基本的に全て「ファイル」として HDD などの記憶媒体に保存されています。そして、これらのファイルがどこにあるかを整理したものが 「ディレクトリ ( directory ) になります。



## 問題：端末を開いて、カレントディレクトリを表示せよ



### 解答

![](./img1484.jpg)

([Ctrl] + [shift] + [T]でも端末が開くかもしれません)

```shell
[study@localhost ~]$ pwd
/home/study
```

現在のカレントディレクトリは`/home/study`(ホームディレクトリである。)



## 問題：`Pictures`ディレクトリに入り、ホームディレクトリにもどる。その後`Documents`ディレクトリに移動しよう。



### 解答

```shell
[study@localhost ~]$ cd Pictures
[study@localhost Pictures]$ cd ../ #(cd ~)も正解
[study@localhost ~]$ cd Documents
[study@localhost Documents]$
```



## 問題：まずは`Pictures`ディレクトリに入る。その後１行のコマンドで`Documents`ディレクトリに移動しよう。(上の解答の2~3行目を１行で行う)



### 解答

```shell
[study@localhost ~]$ cd Pictures
[study@localhost Pictures]$ cd ../Documents #(cd ~/Documents)も正解
[study@localhost Documents]$
```



## 問題：ホームディレクトリに`new_dir`というディレクトリを作成し、その中へ移動せよ



### 解答

```shell
[study@localhost ~]$ mkdir new_dir
[study@localhost ~]$ cd new_dir
```



## 問題：