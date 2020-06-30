# MPDyn

篠田の個人的な汎用プログラムであり、粗視化モデルの開発などもこちらを用いて最初行っていた。並列化効率は高くないので一般的なMDには扱いがよくないが、新しいモデルの開発には扱いが非常に良い。膜系の解析ツールが充実しているため、現在では主にMDのトラジェクトリーの解析プログラムとして利用している。ソースコードはfortran90で書かれており、比較的解読が容易と思われる。



## インストール

[MPDynのダウンロードページ](http://www.chembio.nagoya-u.ac.jp/labhp/physchem1/member/wshinoda/MPDyn2/index.html)からMPDynをダウンロードし、適当な場所で展開しておく。
また、バーションによっては`bin`ディレクトリが作成されない場合もあるので、その場合は手動で作成する。

```bash
cd ~/App
tar xvf MPDyn2.tar.gz
cd MPDyn2/
mkdir -p bin
cd source
```

### MPDynS(1コア)

基本的には、makeファイルを選んでmakeするだけでインストールできる。

gnuコンパイラ(gfortran)を利用する場合は`Makefile.gnu`を、intelコンパイラを利用する場合は`Makefile.intel`を利用しmakeする。

#### Gnuコンパイラの場合

```bash
make -f Makefile.gnu -j 4
make -f Makefile.gnu -j 4 TOOL # tool類の生成(CONV_CRDなど)
```

#### Intelコンパイラを利用する場合

```bash
make -f Makefile.intel -j 4
make -f Makefile.intel -j 4 TOOL # tool類の生成(CONV_CRDなど)
```

これで、`MPDyn2/bin/`の中に

- `MPDynS`
- `MPDynS_MOLFILE`
- `CONV_PDB_CRD`
- `PSFCG`
- `GenCGconf`

が、生成される。パスを通しておこう。

```bash
vi ~/.bashrc #.bashrcの編集

export PATH=???????　と書いてある行の末尾に
:$HOME/App/MPDyn2/
を追加する。
```

`source ~/.bashrc`して`MPDynS`と実行したときに

```
Fortran runtime error: File 'input.data' does not exist
At line 291 of file Read_Condition.f90 (unit = 2, file = '��=��')
```

と表示されればインストールには成功している。



### MPI版(並列計算版)

先程の続きでも問題はない。

sourceディレクトリでmakeするときに、MPIの文字を追加する。

```bash
make -j 4 -f Makefile.intel MPI
```

こうすることで、`./bin/MPDynP`が生成される。これはMPI計算に利用できる実行バイナリである。

```eval_rst

.. warning::

    gnuコンパイラを利用しても並列版はコンパイルできるはずであるが、自分は何度試してもできなかった。(gnu-4.8.4, 4.9.4, 8.3.1で検証した。)

```

##### dds4でのインストール例

```bash
tar xvf MPDyn.tar.gz
cd MPDyn
mkdir -p bin
cd source
mpi-selector-menu
# set openmpi-2.0.2a1 as a current user default.
# If you set the default openmpi, please relogin the machine.
module purge
module load openmpi/2.1.1-intel2017u4 intel/2017u4
make -j 4 -f Makefile.intel
make -j 4 -f Makefile.intel MPI
```

## 計算

具体的なオプション等は公式ホームページを参考。

```bash
mpirun -np $NSLOTS MPDynP
```

などで並列計算ができると思う。



## 解析

大まかな手順は以下の通りである。

0. MD計算をし、トラジェクトリを書き出す(.xtcでも.dcdでもいい。)

1. `./Analy`を作成

2. crdファイルを作る

3. `top_CG.prm`に分子の情報を追加

4. `par_CG.prm`にパラメーターを追加

5. `input.data`に解析方法を書く。





### MD計算

`NAMD`や`lammps`で計算する事が多いか。



### `./Analy`

MPDynで解析したアウトプットは`実行ディレクトリ/Analy`に保存されていくために先に`mkdir Analy`しておこう。



### CRDファイル

#### 手順

1. トラジェクトリからpdb生成
2. 分子ごとにソート
3. 原子のインデックスを揃える
4. `CONV_PDB_CRD`する



MPDyn用の構造ファイルで、0フレーム目(何フレーム目でもいいが)の座標ファイル(pdb)を使って作ることが多い。

vmdでトラジェクトリを読み込んで、TKconsoleで

`animate write pdb initial.pdb beg 0 end 0`

とすると、0フレーム目のinitial.pdbが生成される。

しかし、この生成されたpdbは分子種でソートされていないため、ソートする。

(ソート前)

```
ATOM      1  1N   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      2  1P   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      3  N    POPI    2      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      4  P    POPI    2      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      5  1N   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      6  1P   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
```

(ソート後:こうしたい)

```
ATOM      1  1N   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      2  1P   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      5  1N   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      6  1P   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      3  N    POPI    2      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      4  P    POPI    2      -4.198   1.022  19.272  1.00  0.00      MEMB
```

 これには`grep`と`>(リダイレクト)`が便利である。

grepは特定のパターンを含む文字列を抽出するコマンドで、

```bash
cat initial.pdb | grep POPC

ATOM      1  1N   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      2  1P   POPC    1      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      5  1N   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
ATOM      6  1P   POPC    3      -4.198   1.022  19.272  1.00  0.00      MEMB
```

と行にPOPCのみが含まれる行のみを抜き出すことができる。これをリダイレクトする(`>`)とファイルに書き込む事ができるので、

```bash
cat initial.pdb | grep POPC > ordered_initial.pdb
```

とすると`ordered_initial.pdb`に書き込まれる。

```eval_rst

.. note::
    注意点として、 ``>`` は上書き、 ``>>`` はファイルがあれば書き足すと動作が違う。

    ２種類目の分子からは ``>>`` を使う。

```

つまり、

```bash
cat initial.pdb | grep POPC > ordered_initial.pdb
cat initial.pdb | grep POPI >> ordered_initial.pdb
cat initial.pdb | grep TIP3 >> ordered_initial.pdb
...(分子種の数だけ繰り返す)
```



これで、分子ごとに並んだpdbファイルが生成された。

しかし、これだと原子番号が並んでいない。これはvmdで読み込んで書き出すことで修正できる。

```
vmd ordered_initial.pdb

(TK console)
animate write pdb mod.pdb
```

これで`mod.pdb`は分子ごとに並び、かつインデックスも揃ったモノができている。

長くなったが、これでやっとcrdファイルが作れる。

これは`CONV_PDB_CRD`というコマンドで作れる。

対話形式でいろいろ質問される。



-  Name of PDB file ?
   -  変換するpdbファイルの名前
-  number of molecular species ?
   -  分子の種類が何個あるか（整数）
-  name of molecules ? write all in one line with space inbetween
   -  分子の名前をスペース区切りで
-  number of molecules ? write all in one line
   -  各分子の分子数。
-  number of atoms per molecule ? write all in one line.
   -  １分子あたりの原子数。

これらに答えて行けばいい。

例）

```
 Name of PDB file ?
mod.pdb
 number of molecular species ?
3
 name of molecules ? write all in one line with space inbetween
POPC POPI TIP3
 number of molecules ? write all in one line.
64 64 1707
 number of atoms per molecule ? write all in one line.
16 18 1
```

下3つの質問は先ほど並び変えた分子の順番通りにすること。



### top_CG.prm

分子のトポロジー（結合順序などを書く）

フォーマットは中を見てくれればわかると思うが、

```
>> POPC  ! palmitoyl oleyl PC
NUMATOM= 16
NUMBOND= 15
NUMIMPR= 0
ATOM       1      NC          NC        87.1647   +0.1118033989 0.0
ATOM       2      PH          PH        94.9716   -0.1118033989 0.0
ATOM       3      GL          GL        41.0725    0.00   0.0
ATOM       4      EST1        EST1      58.0358    0.00   0.0
ATOM       5      C11         CM        42.0804    0.00   0.0
ATOM       6      C12         CM        42.0804    0.00   0.0
ATOM       7      C13         CMD2      26.0378    0.00   0.0
ATOM       8      C14         CM        42.0804    0.00   0.0
ATOM       9      C15         CM        42.0804    0.00   0.0
ATOM      10      C16         CT2       29.0615    0.00   0.0
ATOM      11      EST2        EST2      58.0358    0.00   0.0
ATOM      12      C21         CM        42.0804    0.00   0.0
ATOM      13      C22         CM        42.0804    0.00   0.0
ATOM      14      C23         CM        42.0804    0.00   0.0
ATOM      15      C24         CM        42.0804    0.00   0.0
ATOM      16      C25         CT2       29.0615    0.00   0.0
BOND 1 2  2 3  3 4  4 5  5 6  6 7  7 8  8 9  9 10
BOND 3 11  11 12  12 13  13 14  14 15  15 16
<<
```

を分子ごとに追加する。



```eval_rst

.. note::

    解析で使う場合、bondやangleの情報は必要としないので、NUMBOND = 0とし、

    BONDの記述をすべて消してもいい。（というか、下手にbond, angleを入れるとそのパラメーターが必要になりめんどくさい）


```

例

```
>> POPC  ! palmitoyl oleyl PC
NUMATOM= 16
NUMBOND= 0
NUMIMPR= 0
ATOM       1      NC          NC        87.1647   +0.1118033989 0.0
ATOM       2      PH          PH        94.9716   -0.1118033989 0.0
ATOM       3      GL          GL        41.0725    0.00   0.0
ATOM       4      EST1        EST1      58.0358    0.00   0.0
ATOM       5      C11         CM        42.0804    0.00   0.0
ATOM       6      C12         CM        42.0804    0.00   0.0
ATOM       7      C13         CMD2      26.0378    0.00   0.0
ATOM       8      C14         CM        42.0804    0.00   0.0
ATOM       9      C15         CM        42.0804    0.00   0.0
ATOM      10      C16         CT2       29.0615    0.00   0.0
ATOM      11      EST2        EST2      58.0358    0.00   0.0
ATOM      12      C21         CM        42.0804    0.00   0.0
ATOM      13      C22         CM        42.0804    0.00   0.0
ATOM      14      C23         CM        42.0804    0.00   0.0
ATOM      15      C24         CM        42.0804    0.00   0.0
ATOM      16      C25         CT2       29.0615    0.00   0.0
<<
```

としてもいい。

### par_CG.prm

ここに、相互作用パラメーターを追加する必要がある。

上で書いたように、bondなどを追加しなければ必要なのはnonbond (lj)のパラメーターだけのはず。

フォーマットは

```
>> NONBOND
UNIT= kcal_per_mol # [ kcal_per_mol / K / kJ_per_mol
# water
W    W    LJ12-4   0.895   4.371     2.0   15.0
# Alkane
CT   CT   LJ9-6    0.469   4.585     2.0   15.0
CT   CM   LJ9-6    0.444   4.5455    2.0   15.0
```

である。

これは系に含まれる原子の種類分必要である。

ex)

系にCT, CM, Wが含まれるなら、

CT-CT, CT-CM, CT-W, CM-CM, CM-W, W-Wの6個のパラメーターが必要。

```eval_rst

.. note::
    これも解析で使う場合、パラメーターの値は正しくなくていいため

    CT   CT   LJ9-6    0.0   0.0     2.0   15.0

    CT   CM   LJ9-6    0.0   0.0    2.0   15.0

    というふうに追加するのが楽。

```



### input.data

やっと解析ができる。MPDynでの解析は解析をするディレクトリに`input.data`を作成し、そこにいろいろ書き込んでいく。

以下インプットの例

```
>> JOB_NAME
  SZZ
<<
>> STDOUT
  ON
<<
>> METHOD
  Analysis
<<
>> PBC
  RC=    60.
  RSKIN=  2.
  RRESPA= 6.
  RHEAL=  4.
  MAXPAIR= 40000000
  TBOOK=  8
  VDWSWITCH=  OFF
  VDWCORRECT= OFF
<<
>> STATUS
  Initial
  PDB= ON
<<
>> FORCEFIELD
 FF= CG
 PARFILE= "par_CG.prm"
 TOPFILE= "top_CG.prm"
# PARFILE= "./new3.prm"
# PARFILE="./par_c36_sm_chol.prm"
# TOPFILE="./SDKparam/top_CG.prm"
# PSF= "./dmpc_autopsf.psf"
<<
>> SYSTEM
  SYSMOL=4
  MOLNAME=POPC POPI TIP3 SOD
  MOLNUM=64 64 1707 64
  ATMNUM=16 18 1 1
<<
>> DATA_FORM_TRJ
  FORM=DCD
<<
>> ANALYZ_FILE
         1                ! number of files
./3_200ns.cg ! ## file name & step number & interval
         1000    1
<<
>> ANALYZ_NAME
  METHOD= SZZ
<<
<end>
```



基本的には[JOB_SCRIPT](http://simulo.apchem.nagoya-u.ac.jp/personal/wshinoda/MPDyn2/)のページを参考にしてほしい。

どんな解析ができるかはANALYZ_NAMEを参照のこと。

ここでは、簡単にしか説明しない。言及のないものは上のexampleと同じにしておけばいい。

- JOB_NAME

  - おそらく任意だが解析法を書くといいと思う

- METHOD

  - 解析をする場合はAnalysis

- STATUS

  - Initial   PDB=ONにすると先ほど作ったinitial.crdを読みとってくれるみたいだ。

- FORCEFIELD

  - FFは力場。粗視化ならCG、CHARMM36ならCHARMM

  - 全原子の場合はPARFILEとPSFを指定する必要がある。PARFILEは計算に使ったパラメーターファイルでいいが複数指定ができないためあらかじめ1つのファイルにマージしておく必要がある。

  - 粗視化の場合は PARFILE= "par_CG.prm"、TOPFILE= "top_CG.prm"と先ほど編集した2つのファイルを指定する。

    ``` eval_rst

    .. warning::

        筆者の環境では何故か ``~/CGparam/par_CG.prm`` とか ``/home/tanaka/CGparam/par_CG.prm`` のようなパス付き指定ができず、毎回解析実行ディレクトリに2つのファイルを持ってくる必要があったが、これはシンボリックリンクを貼ることで誤魔化している。ただし、シンボリックリンクを削除すると元ファイルも消える恐れがある。取り扱い注意。

        シンボリックリンクの貼り方 ``ln -s ~/CGparam/par_CG.prm``
    ```



- SYSTEM

  - `CONV_PDB_CRD`した時のような系の情報が必要となる。
- DATA_FORM_TRJ

  - トラジェクトリの形式を指定する。基本的にはDCDかXTCだろう。
- ANALYZ_FILE

  - 解析するトラジェクトリを指定する。
    上から、解析するファイル数、解析ファイル名（拡張子は除く）、フレーム数とstride数である。
    もし、トラジェクトリが複数になっている場合は

```
    >> ANALYZ_FILE
             6                ! number of files
    ./centering_pr100 ! ## file name & step number & interval
             10000    1
    ./centering_pr200 ! ## file name & step number & interval
             10000    1
    ./centering_pr300 ! ## file name & step number & interval
             10000    1
    ./centering_pr400 ! ## file name & step number & interval
             10000    1
    ./centering_pr500 ! ## file name & step number & interval
             10000    1
    ./centering_pr600 ! ## file name & step number & interval
             10000    1
    <<
```

のように記述する。

- ANALYZ_NAME

  - 解析方法を選択。ここでは粗視化脂質膜のオーダーパラメーターを解析するSZZを選択した。



### 解析完了

大体1回目は失敗するので出てきたエラーメッセージを元に修正していけばいい。



## 解析の種類

ここでは、筆者がよく使った解析について書いていく。とはいえ、usageは公式を見ればわかるので注意点を書いていこうと思う。



### RDF / RDFG

動径分布関数 / 重心の動径分布関数をもとめるメソッドである。NOINTRA= ONにすることで同分子内の原子をカウントしなくなるため、便利である。

```eval_rst

.. warning::

    RC / GRID　が割り切れる値じゃないとMPDynがsegmentation faultする気がする。

```

膜などを扱うグループでは、2次元の動径分布関数が必要になる事が多いが、MPDynでは解析できない。

MDAnalysisを使おう！



### RZ

各原子の膜垂直軸に沿ってのプロフィールが計算できるメソッドである。
```eval_rst

.. warning::

    RANGE / GRID　が割り切れる値じゃないとMPDynがsegmentation faultする気がする。

```



### SCD（全原子限定）

脂質分子のオーダーパラメーターが計算できる。混ぜものの脂質も可能。

対応脂質がDPPC/DPhPC/DMPC/TEPCとなるため、それ以外の脂質についてはソースコードを書き換える必要がある。



### SZZ（粗視化限定）

そもそも隠し解析コマンドなので気づかない。

粗視化MDにおいて脂質分子のオーダーパラメーターが計算できる。混ぜものの脂質も可能。

対応脂質が少ない。それ以外の脂質についてはソースコードを書き換える必要がある。

