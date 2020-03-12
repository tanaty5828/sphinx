# Packmol

[Packmol](<http://m3g.iqm.unicamp.br/packmol/home.shtml>)は、シミュレーションの初期構造を作成する際に便利なソフトウェア。

例えば、水(TIP3P)とイオン(Na+)のランダムな配置の水溶液を作成したい時などに有用である。



## インストール方法

適当なディレクトリ(`~/App/`)で、

ダウンロードして、

```bash
git clone https://github.com/m3g/packmol.git
cd packmol
make
```

で、使用できるようになるはず。

あとはパスなりエイリアスなり通せばいいと思う。

```bash
# 以下を~/.bashrcに追記
alias packmol='~/App/packmol/packmol'
```



## 使い方

### 完全にランダムな配置

ここでは、水1000分子の中に尿素400分子を入れた水溶液を作成する。

必要なものは、

- [水のpdb](http://m3g.iqm.unicamp.br/packmol/examples/water.pdb)
- [尿素のpdb](http://m3g.iqm.unicamp.br/packmol/examples/urea.pdb)
- [packmol用のインプットファイル](http://m3g.iqm.unicamp.br/packmol/examples/mixture-comment.inp)

それぞれダウンロードしておいて欲しい。

インプットファイルは、以下のようになっている。

```bash
#
# A mixture of water and urea
#

# All the atoms from diferent molecules will be separated at least 2.0
# Anstroms at the solution.

tolerance 2.0

# The file type of input and output files is PDB

filetype pdb

# The name of the output file

output mixture.pdb

# 1000 water molecules and 400 urea molecules will be put in a box
# defined by the minimum coordinates x, y and z = 0. 0. 0. and maximum
# coordinates 40. 40. 40. That is, they will be put in a cube of side
# 40. (the keyword "inside cube 0. 0. 0. 40.") could be used as well.

structure water.pdb 
  number 1000 
  inside box 0. 0. 0. 40. 40. 40. 
end structure

structure urea.pdb
  number 400
  inside box 0. 0. 0. 40. 40. 40. 
end structure

```

#### tolerance

各分子をどのくらい離して配置するかの値[Å]。3.0くらいでいいと思う。

#### filetype

pdbにしておいて。

#### output

出力ファイル名。自由にどうぞ。

#### structure~

structureから、end structureまでを一つの分子として認識する。

##### structure

利用する分子のpdb名。

##### number

何分子入れるか。

##### inside box

0. 0. 0. 40. 40. 40. だと0<x<40, 0<y<40, 0<z<40の箱の中に配置することになる。



実行は、

```bash
packmol < mixture-comment.inp
```

とインプットファイルを読み込ませて実行する。数秒でmixture.pdbが作成されているはず。vmdで確認しよう。



### 方向を固定して配置

#### constrain_rotation

structure...end structureの間に`constrain_rotation 軸 角度 誤差`を入れる。

例えば、z軸に並行に配置したい場合、

`constrain_rotation x 0. 5.`
`constrain_rotation y 0. 5.`

とする。



## tips

### seed

packmolは同じ粒子数で実行した場合、配置が完全に同じ系ができてしまう。

粒子数が同じで異なる初期配置を何個か作成したい場合、ランダムシード値を指定することで解決する。

この時、-1を指定すると毎回異なる配置が作成されるため便利。

使い方) インプットの最後に`seed -1`