# Gromacs

## 概要

GROMACS (Groningen Machine for Chemical Simulations)は、フリーウェアの分子動力学シミュレーションパッケージの一つである。オランダ・フローニンゲン大学で開発され、その後世界中の開発者によって更新され続けている。



## インストール方法

[Download GROMACS](http://manual.gromacs.org/documentation/)にアクセスし、適当なバージョンの項目の「Download」をクリックし、"Source code" の項目からダウンロードする。

ダウンロードするファイル名は、gromacs-XXX.tar.gz のような名前になっているはず。"XXX"にはバージョンが入る。

------

どのバージョンをダウンロードすればいいかわからないときは、とりあえず最新のものにしておけば良い。
自分の研究テーマの前任者とバージョンを合わせた方が良い場合もある。

------

```shell

## "gromacs-XXX.tar.gz"が、ディレクトリ"~/App"内にあるとします。

cd  ~/App/
tar -xzvf gromacs-XXX.tar.gz
cd  ./gromacs-XXX/
```

上のようにコマンドを打つと、圧縮ファイルである"gromacs-XXX.tar.gz"を解凍して、新たに"gromacs-XXX"というディレクトリが作成されます。
("圧縮","解凍"などが意味不明でキレそうな時は、[参考ページ](https://wa3.i-3-i.info/word1477.html)を読んで落ち着いてください。)

この状態で`ls`コマンドを打つと、"gromacs-XXX"の中身が表示されます。
その中に、"README"や"INSTALL"のようなファイルがあり、それを読み、その指示通りにすればインストールができるはずです。
ただ、全て英語でわかりにくいと思うので、ここに日本語で書いておきます。

このまま、つまりディレクトリ"gromacs-XXX"にいる状態で、次のようにコマンドを打ってください。

```shell
mkdir gmx_install
mkdir build
cd ./build
cmake ../ -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON -DCMAKE_INSTALL_PREFIX=${HOME}/App/gromacs-XXX/gmx_install
make -j4
make check
sudo make install
```

```eval_rst
.. note::

    スパコン等を使用してMPI並列計算をしたい場合は、 ``-DGMX_MPI=on`` のオプションを追加する必要がある。

```

"cmake"や"make"のコマンドではある程度時間がかかります。

ここまででエラーがでなければ、さらにVim等で"~/.bashrc"を開き(`vim ~/.bashrc`),次のように書き加える。

```bash
最後の行に

export PATH=${HOME}/App/gromacs-XXX/gmx_install/bin:${PATH}

を追加する。
```

確認のため、

```shell
source ~/.bashrc
gmx --version
```

と打ち、gromacsのバージョンが正しく表示されていれば、インストール完了です。



### GPU版のインストール

gromacsはGPUによる高速計算に対応している。グラフィックボードが刺さっているスパコンではこれを利用すると計算が早くなるため(できるなら)ばちこり利用しよう。

```eval_rst

.. warning::

    数は少ないが、AMDのグラッフィックボード(RADEON)を使う場合、2018.8を使用することをおすすめする。

    これは2018.7までは力場の制御にバグがあることが修正されていなかった（近年になってようやく発見された）ことと、2019.1〜2019.4まではAMD製のGPUがうまく検出されず動作しないバグ、2020.1ではそれは直ったものの動作が非常に不安定になっていることがあるためです。

```

#### tgpu7の場合

tgpu7のCUDAバージョンは8.0であるため、gromacs2018-8がインストールできる最新バージョンである。またデフォルトで入っているcmakeもバージョンが古いため、cmakeも新しいものをインストールする必要がある。
以下にcmakeのオプションを示す。

```bash
/home/tanaka/App/cmake-3.10.2/build/bin/cmake ../ \
-DCMAKE_C_COMPILER=/usr/local/gcc-4.9.4/build/bin/gcc \ 
-DCMAKE_CXX_COMPILER=/usr/local/gcc-4.9.4/build/bin/g++ \ 
-DGMX_BUILD_OWN_FFTW=on \
-DGMX_SIMD=AVX_256 \
-DCMAKE_INSTALL_PREFIX=/home/tanaka/App/gromacs-2018.8/gmx_install/
```

とした。(cmake3.10.2は上記の場所にインストールした。)

```eval_rst

.. warning::

    SIMD最適化に関して、gromacsは自動で利用できる最適化を探してくれるはずが、手動で指定する必要があった(`-DGMX_SIMD=AVX_256`)。このままインストールを続けると、このあとのmakeでfftwのビルドに失敗する。
    これを修正するためには、**cmake後にできる`build/src/contrib/fftw/CMakeFiles/fftwBuild.dir/build.make`のファイルの109行目から(`;--enable-avx2;--enable-avx512`)を消去する。**

```

```eval_rst

.. warning::

    上記のfftwビルド失敗は、gromacs-5.1.5では発生せず、

    gromacs2020では、**cmake後にできる`src/external/build-fftw/CMakeFiles/fftwBuild.dir/build.make`のファイルから(`;--enable-avx2;--enable-avx512`)を消去する必要がある。** gromacs-2019とはファイルの場所が変わっているので注意。

```

#### 東大物性研の場合

GPU版gromacsのインストールでは、

- gromacs
- C, C++コンパイラ(gnu)
- CUDA
- cmake
- intelコンパイラ

これらすべての依存関係を満たす必要がある。

田中が、gromacs2018-8を東大物性研にインストール成功したときのそれぞれのバージョンは、

| module |  version   |
| :----: | :--------: |
|  mpt   |    2.16    |
|  cuda  |    8.0     |
| cmake  |   3.10.2   |
|  gnu   |   4.9.4    |
| intel  | 16.0.8.266 |

で成功した。

実際にインストールするときは一度すべてのモジュールを消して、ロードしたほうがいいだろう。

```bash
module purge  # moduleの初期化
module load mpt/2.16
module load cuda/8.0
module load cmake/3.10.2
module load gnu/4.9.4
module load intel/16.0.8.266
```

あとは上と同様にインストールをすればいいが、cmakeのオプションは

```bash
cmake ../ -DGMX_BUILD_OWN_FFTW=on \
-DREGRESSIONTEST_DOWNLOAD=on \
-DCMAKE_INSTALL_PREFIX=${HOME}/App/gromacs-2018.8/gmx_install/ \
-DGMX_MPI=on \
-DGMX_GPU=on \
-DCMAKE_C_COMPILER=/home/app/gcc/4.9.4/bin/gcc \
-DCMAKE_CXX_COMPILER=/home/app/gcc/4.9.4/bin/g++
```

と、GPUを有効化(`-DGMX_GPU=on`)し、CとC++のコンパイラの場所を指定(`-DCMAKE_C_COMPILER=/home/app/gcc/4.9.4/bin/gcc \
-DCMAKE_CXX_COMPILER=/home/app/gcc/4.9.4/bin/g++`)した。

ちなみに、**ジョブスクリプトでもモジュールをロードする必要がある**ため注意。
東大物性研ではジョブスクリプト中で`. /etc/profile.d/modules.sh `の記述がないとmoduleコマンドが使えない。



### CG(Spica)+plumed版

粗視化の計算をgromacsで行う必要があり、またレプリカ交換のMDを行う必要があった。

レプリカ交換はPlumed[^1]というインターフェイスを介して実装されているため、そちらをインストールしてある必要がある。[このページ](<https://tanaty5828.github.io/sphinx/MD/plumed.html>)を参考にすること。

#### 東大物性研の場合

plumedとgromacsはコンパイラのバージョンを揃える必要があるため、

```bash
module purge  # moduleの初期化
module load mpt/2.16
module load cmake/3.10.2
module load gnu/4.9.4
module load intel/16.0.8.266
```

としておこう。(MD計算時も同じモジュールを読み込む必要がある。)



#### Plumedのパッチ処理

```eval_rst
.. note::

    また粗視化の計算をgromacsで行うにはgromacs- **5.1.4** or  gromacs- **5.1.5** が必要であり、このバージョンに対応しているplumedのバージョンは2.4である。

    つまり現状(2020/01/22)ではgromacs- **5.1.4** + plumed- **2.4.6** が動く組み合わせとなる。

```

Plumedをインストールしたら、

```bash
tar xvf gromacs-5.1.4
cd gromacs-5.1.4
plumed patch -p
```

とすると対話形式でソフトウェア、バージョンを聞かれるため適切なものを選ぶ。

もしスパコンでこの作業をする場合は、

```bash
plumed --no-mpi -p
```

としてplumed-patchを実行する必要があるかもしれない。

#### `bonded.cpp`の置換

粗視化の計算をgromacsで行う場合は、ソースコードを置き換える必要がある。

`bonded.cpp`のファイルを`gromacs-5.1.4/src/gromacs/listed_forces/bonded.cpp`と置き換える。

#### gromacsのコンパイル

上記と同じだが、

- cコンパイラの場所を指定し(`-DCMAKE_C_COMPILER=/home/app/gcc/4.9.4/bin/gcc -DCMAKE_CXX_COMPILER=/home/app/gcc/4.9.4/bin/g++`)
- 固定ライブラリを利用(`-DBUILD_SHARED_LIBS=OFF -DGMX_PREFER_STATIC_LIBS=ON`)

すると上手くいく。

```eval_rst
.. note::

    粗視化ではテーブルポテンシャルを利用する影響でGPUは使用できない。

    このため、明示的にGPUをOFFにしてコンパイルするのがベスト。( `-DGMX_GPU=off` )

```

つまりまとめると、

```bash
mkdir gmx_install
mkdir build
cd ./build
cmake ../ -DGMX_BUILD_OWN_FFTW=on -DREGRESSIONTEST_DOWNLOAD=on -DCMAKE_INSTALL_PREFIX=/home/k0386/k038615/App/gromacs-5.1.4/gmx_install/ -DGMX_MPI=on -DGMX_GPU=off -DCMAKE_C_COMPILER=/home/app/gcc/4.9.4/bin/gcc -DCMAKE_CXX_COMPILER=/home/app/gcc/4.9.4/bin/g++ -DBUILD_SHARED_LIBS=OFF -DGMX_PREFER_STATIC_LIBS=ON
make -j4
make check
sudo make install
```

となる。エラーがでなければ成功である。



### Minimum supported compiler versions

各バージョンのGromacsをコンパイルするために必要なコンパイラーの(最小)バージョン

ただし、intelコンパイラを使用したとしてもあるバージョンのg++が必要なことがある(libstdc++を使用する)ため、注意が必要。

| Gromacs version | CMAKE | GNU(gcc)                    | Intel(icc) + libstdc++      | Note    |
| --------------- | ----- | --------------------------- | --------------------------- | ------- |
| 2021-rc1        | 3.13  | 7                           | 19.1<br />libstdc++ 7.1     |         |
| 2020.5          | 3.9.6 | 5.1                         | 17.0.1<br />libstdc++ 5.1   | CentOS8 |
| 2019.6          | 3.4.3 | 4.8.1                       | 17.0.1<br />libstdc++ 4.8.1 |         |
| 2018.8          | 3.4.3 | 4.8.1                       | 15.0<br />libstdc++ 4.8.1   |         |
| 2016.6          | 2.8.8 | 4.6                         | 14<br />libstdc++ 4.6.1     | CentOS7 |
| 5.1.5           | 2.8.8 | 4.4? <br />(4.7↑ is better) | 12?<br />libstdc++ 4.4      | CentOS6 |

また、GPU有効でコンパイルするためには、更にCUDAのバージョンを合わせる必要がある。CUDAは新しいものほど良いというわけではなく、丁度いいものを選ぶ必要がある。

 ```eval_rst
.. note::

    CentOS7のgcc系は4.8.xがデフォルトで入っているため、cmakeのバージョンを上げれば、2019.6をコンパイルすることも可能なはず。

 ```



## Command-line reference

gromacsの実行バイナリ`gmx`には様々な実行コマンドがあり、それぞれで必要なオプションが覚えられなかったのでここにまとめる。



### `gmx mdrun`

実際にMD計算を実行するコマンドである。

|  Options  |                    Description                    |
| :-------: | :-----------------------------------------------: |
| `-deffnm` |           デフォルトのファイル名。[str]           |
| `-nb gpu` | GPUで計算する。<br />(GPU=onでのコンパイルが必要) |
|   `-v`    |              標準出力へログを出す。               |

ex)`gmx mdrun -deffnm 1_100ns`



### `gmx grompp`

MDのインプットなどを一つのバイナリ(.tpr)としてまとめるコマンド。(the gromacs preprocessor)

| Options |               Description               |
| :-----: | :-------------------------------------: |
|  `-f`   |       MDのインプットを指定[.mdp]        |
|  `-c`   | 作成した/前回の結果の構造ファイル[.gro] |
|  `-t`   |       MD計算を引き継ぐ場合[.cpt]        |
|  `-p`   |        トポロジーファイル[.top]         |
|  `-n`   |       インデックスファイル[.ndx]        |
|  `-o`   |    アウトプットのtprファイル名[.tpr]    |

ex) `gmx grompp -f DNA_npt.mdp -c 1_npt_equil.gro -t 1_npt_equil.cpt -p topol.top -n index.ndx -o 2_100ns.tpr`



### `gmx solvate`

1. 周期境界の箱を作成し、(`gmx editconf`でもできる。)
2. 系を水などで満たす。

コマンドである。

| Options |                         Description                          |
| :-----: | :----------------------------------------------------------: |
|  `-cp`  |            水で満たす系(溶質)の構造ファイル[.gro]            |
|  `-cs`  | 溶媒の構造ファイル<br />TIP3Pは3点モデルなのでspc216.groでOK[.gro] |
|  `-p`   |                   トポロジーファイル[.top]                   |
| `-box`  |             ボックスサイズ(nm) [real real real]              |
|  `-o`   |              アウトプットのgroファイル名[.tpr]               |

ex) `gmx solvate -cp 1bna.aa.newbox.gro -cs tip3p.gro -o 1bna.aa.solvate.gro -p topol.top`



### `gmx genion`

系の水とイオンを交換し、指定した濃度にするコマンド。

実際は`ions.mdp`から`ions.tpr`を作成し、これを用いて`gmx genion`する。

```eval_rst

:download:`ions.mdp <./ions.mdp>`

```



|  Options   |                   Description                    |
| :--------: | :----------------------------------------------: |
|    `-s`    |          イオンを入れるインプット[.tpr]          |
|    `-p`    |             トポロジーファイル[.top]             |
|  `-pname`  |               陽イオンの名前[str]                |
|  `-nname`  |               陰イオンの名前[str]                |
| `-neutral` | 系が中性になるように<br />カウンターイオンを追加 |
|  `-conc`   |            系の濃度(mol/liter)[real]             |
|    `-o`    |        アウトプットのgroファイル名[.tpr]         |

```eval_rst

.. warning::
    カウンターイオン＋特定の濃度になるようにイオンを入れる場合は、

    neutralとconcの両方を指定する必要がある。

```

ex)

```bash
gmx grompp -f ions.mdp -c 1AKI_solv.gro -p topol.top -o ions.tpr
gmx genion -s ions.tpr -o 1AKI_solv_ions.gro -p topol.top -pname NA -nname CL -neutral
```



### `gmx pdb2gmx`

構造のpdbファイルをgroファイルに変換するコマンド

| Options  |            Description            |
| :------: | :-------------------------------: |
|   `-f`   |    変換したいpdbファイル[.pdb]    |
|  `-ff`   |            力場名[str]            |
| `-water` |           水の名前[str]           |
|   `-o`   | アウトプットのgroファイル名[.tpr] |

```eval_rst

.. note::
    使用できる力場は ``/gmx-install/share/gromacs/top/`` に[力場.ff]というディレクトリを追加すると使えるようになる。

    charmm36なども自分で追加する必要がある。（探すのが難しかった）

```

`-ff`や`-water`は指定しなくても、その後対話形式で聞かれる。

ex) `gmx pdb2gmx -f 1AKI_clean.pdb -o 1AKI_processed.gro`



### `gmx energy`

計算のアウトプットのedrファイル(バイナリ)をgnuplotなどで表示できるようにするコマンド。

| Options |                  Description                   |
| :-----: | :--------------------------------------------: |
|  `-f`   |      インプットのエネルギーファイル[.edr]      |
| `-xvg`  | 出力フォーマット(noneにするとデータのみが並ぶ) |
|  `-o`   |       アウトプットのxvgファイル名[.xvg]        |

ex) `gmx energy -f 1_npt_equil.edr -o 1_npt_equil.cone -xvg none`

コマンド実行後に出力する物理量を選択する。



### `gmx trjconv`

書き出されたトラジェクトリに様々な処理をするコマンド。

| Options |                         Description                          |
| :-----: | :----------------------------------------------------------: |
|  `-f`   |           インプットのトラジェクトリファイル[.trr]           |
|  `-s`   | 初期構造ファイル[.gro, .tpr]<br />-pbc molを使用する場合は.tprが必須 |
|  `-n`   |                  インデックスファイル[.ndx]                  |
|  `-b`   |                読み込むファイルの開始時間(ps)                |
|  `-e`   |                読み込むファイルの終了時間(ps)                |
| `-pbc`  | 周期境界の取り扱い<br />[mol, res, atom, nojump, cluster, whole] |
|  `-o`   |             アウトプットのファイル名[.gro, .trr]             |

`-o`では.groも.trrも指定できる。

コマンドの実行後にサブセットを指定できる。これにより、系から水を除いたトラジェクトリなどが書き出せる。

#### -pbc

田中が色々試した結果、トラジェクトリの変換操作は、

1.  PBCによる途切れを全体的に修正する（whole）
2.  さらにそのトラジェクトリをclusterごとに集める
3.  続いてタンパク質（溶質）の重心の位置をセンタリングする
4.  必要に応じて回転操作を行う

の順に変換を行うと、最も普遍的に問題なく処理を行うことができるようだ。これらの変換操作を一度に行うことはできない様子。`gmx trjconv`によるトラジェクトリ変換にはリファレンス構造を必要とし、`gmx trjconv`の`-s`に続くオプションで指定する。通常`tpr`形式が最も都合が良い。このとき、**途切れた状態で保存されている`tpr`形式を`-s`入力に使うと表示がおかしくなるので十分に注意する**。したがって、`-s`に入れるリファレンス構造は、<u>トポロジーを作ってエネルギー最小化を始める前の`tpr`ファイル</u>が最も都合が良い。

ex)

```bash
gmx trjconv -f input.trr -o output1.trr -s minimize.tpr -n in.ndx -pbc whole
gmx trjconv -f output1.trr -o output2.trr -s minimize.tpr -n in.ndx -pbc cluster
gmx trjconv -f output2.trr -o output3.trr -s minimize.tpr -n in.ndx -pbc mol -ur compact -center
```

`-pbc cluster`するとクラスタリングしたいグループを選ばされる。各自溶質を選ぶといいだろう。

## Reference

[^1]: Bonomi, M. et al. PLUMED: A portable plugin for free-energy calculations with molecular dynamics. Comput. Phys. Commun. 180, 1961–1972 (2009).