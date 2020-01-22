# PLUMED

![](./plumed.png)



[PLUMED](<https://www.plumed.org/>)は分子動力学シミュレーションのための拡張サンプリングアルゴリズム、様々な自由エネルギー手法、および解析ツールを実装するオープンソースライブラリである。ACEMD、AMBER、DL_POLY、GROMACS、LAMMPS、NAMD、OpenMM、ABIN、CP2K、i-PI、PINY-MD、およびQuantum ESPRESSOと一緒に使うために設計されているが、解析および可視化ツールのVMD、HTMD、OpenPathSamplingと一緒に使うこともできる。

加えて、PLUMEDは分子動力学トラジェクトリの分析のためのスタンドアローンツールとして使うこともできる。METAGUIと名付けられたグラフィカルユーザインタフェースも入手可能である。



## インストール方法

本研究室では、gromacsなどでレプリカ交換などの計算をするためにPLUMEDを必要とする場合がほとんどだろう。手順としては、

1. plumed本体のインストール
2. gromacsなどのMDソフトウェアにパッチを当てる

という流れが一般的になるだろう。

### 注意

現在(2020/01/22)、plumedとgromacsの依存関係は以下のとおりである。

| plumed | MD software                                                  |
| :----: | ------------------------------------------------------------ |
|  2.5   | gromacs-2016-6<br />gromacs-2018-8<br />gromacs-2019-4<br />gromacs-4-5-7<br />namd-2-12<br />namd-2-13 |
|  2.4   | gromacs-2018-6<br />gromacs-4-5-7<br />gromacs-5-1-4<br />lammps-6Apr13<br />namd-2-12<br />namd-2-13 |



これより古いplumed(2.3以前)はサポート対象外のため推奨しない。自分の利用したいMDソフトウェアに合わせたplumedをインストールすること。

### plumedのインストール

ソースコードは[公式サイトのDownload](<https://www.plumed.org//download.html>)から入手できる。plumed-?.?.?.tgzをダウンロードし、適当な場所(`~/App`)で展開する。

その後以下のコマンドを入力

```bash
cd ~/App/plumed-?.?.?
./configure --prefix=/usr/local/
make -j 4
make doc
sudo make install  # sudo is needed if the prefix is root
```

`which plumed`とコマンド入力し、`/usr/local/bin/plumed`と出力されればインストールに成功している。

### パッチを当てる

[gromacsのページ](<https://tanaty5828.github.io/sphinx/MD/gromacs.html>)でも書いてあるが、gromacsのルートディレクトリで

```bash
plumed patch -p
```

のコマンドを実行、対話形式でMDソフトウェアのバージョンを選択するだけである。

もしスパコン上でパッチを当てる場合は、

```bash
plumed patch --no-mpi -p
```

--no-mpiを追加する。