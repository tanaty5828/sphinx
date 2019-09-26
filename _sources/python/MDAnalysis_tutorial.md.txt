# MDAnalysis tutorial

MD（分子動力学）の解析ツール（Pythonライブラリ）は**MDTraj**なども存在するが、
チュートリアルがありしっかりしていそうだったためこちらも紹介する。
また、チュートリアルが英語しかなくMDを始めたての学生は苦労すると思ったので、
日本語訳と解説をつけていく。
ここでは1~6章を解説する.

### 環境

 - Python 3.6.6 :: Anaconda, Inc
 - MDAnalysis 0.19.0

### 注意
最近筆者の環境ではnumpyとMDAnalysisの相性が悪く、こちらのインポート時にエラーがでるようになってしまった。
（インポート時のエラーでプログラム自体は正常に動いた）
一応numpyのバージョンを下げることでエラーは出なくなった。
Anacondaの利用者は以下を実行。

```bash
conda install numpy=1.15.0
```

## 1.How to use this tutorial

### 1.1.Have you installed MDAnalysis

##### チュートリアルのはじめの章であり、MDAnalysisがインストールされているかを確かめる

適当なPythonのファイルに以下の内容を書き込み、実行してみる

```python
import MDAnalysis
from MDAnalysis.tests.datafiles import PSF, DCD

print(MDAnalysis.Universe(PSF, DCD))
print(MDAnalysis.__version__)
```

```bash
<Universe with 3341 atoms>
0.19.0
```

上のように表示されていればMDAnalysisはインストール済みであり、次の章に進める。

何も表示されないorエラーが出る場合には、MDAnalysisはインストールされていないため
[Installing MDAnalysis](https://www.mdanalysis.org/MDAnalysisTutorial/installation.html#chapter-installing-mdanalysis)
を参考にインストールする。
このインストールセクションが2章に相当するため、2章は省略。

## 3.Preparation

### 3.1.Python interpreter : ipython

##### ipythonを使うとtabキー補完が便利になるよっていう話

Pythonを使うときは、多くの人がプログラムを.pyファイルに書き込み、

```bash
$ python test.py
```

として実行している人が多いと思うが、実はPythonには対話型で実行できる機能も備えており、

```bash
$ python

>>>print('Hello')
Hello
```

というふうにも実行できる。
この対話型シェルを大幅に拡張したものが`ipython`であり、特にtabキーでの補完がすごい。

###### コマンドについての説明がほしいときはipython上で`command?`とする。

```bash
$ ipython
In [1]: print?
```

```
Docstring:
print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

Prints the values to a stream, or to sys.stdout by default.
Optional keyword arguments:
file:  a file-like object (stream); defaults to the current sys.stdout.
sep:   string inserted between values, default a space.
end:   string appended after the last value, default a newline.
flush: whether to forcibly flush the stream.
Type:      builtin_function_or_method
```

と、かなり詳しい説明が出る。

###### tabキー補完が優秀

```python
import MD <tabキー入力>
import MDAnalysis

MDAnalysis.Universe.<tabキー入力>
###使えるもの一覧が出る
```

###### 数値計算、グラフの即時プロットのために`numpy`,`matplotlib`をインポートしておくと良い

ipythonを

```bash
$ ipython --matplotlib
```

として起動するか、ipythonの中で、

```python
import numpy as np
```

とする。（npは任意の名前で良い）

### 3.2.Loading MDAnalysis

###### ライブラリ（モジュール）のインポート

.pyファイルに書く場合でも、対話型の場合でもMDAnalysisのインポートは

```python
import MDAnalysis
```

のみでいい。
（ただしMDAnalysis.analysis.rmsのようなサブモジュールは自動でインポートされないため、明示的にインポートする必要がある。）

###### サンプルファイル（のインポート）

またMDAnalysisをインポートすると、サンプルファイルであるアデニル酸キナーゼのコンフォメーション変化のトラジェクトリも同時に読み込まれる。以降のチュートリアルではこのアデニル酸キナーゼを使って解析を進める。
ここではトラジェクトリファイル（CHARMM　DCD　format）とトポロジーファイル（CHARMM　PSF　format）が読み込まれる。
以下のように読み込むといい

```python
from MDAnalysis.tests.datafiles import PSF, DCD
```

## 4.Basic

### 4.1.Universe and AtomGroup

MDAnalysisはオブジェクト指向であり、系は`Atom`オブジェクトを形成する。これはトポロジーとトラジェクトリを読み込むことで作ることができる。

#### `Universe`object
ファイルの読み込み時に用いる。

```python

u = MDAnalysis.Universe(PSF, DCD)
print (u)

##output
<Unuverse with 3341 atoms>
```
また、自分のトラジェクトリファイルを複数読み込む場合などは、

```python
u = MDAnalysis.Universe("test.psf", "../test1.dcd", "../test2.dcd")
```

などとすればいい。

#### `Atom`object

こうして読み込むと原子の情報はAtom属性に格納される。

```python
print (u.atoms)
##output
<AtomGroup with 3341 atoms>

print(list (u.atoms[:5]))
##output
[<Atom 1: N of type 56 of resname MET, resid 1 and segid 4AKE>,
 <Atom 2: HT1 of type 2 of resname MET, resid 1 and segid 4AKE>,
 <Atom 3: HT2 of type 2 of resname MET, resid 1 and segid 4AKE>,
 <Atom 4: HT3 of type 2 of resname MET, resid 1 and segid 4AKE>,
 <Atom 5: CA of type 22 of resname MET, resid 1 and segid 4AKE>]
##全原子リストのうちの上から5個が出力される。
```

#### `residue`object

もちろん原子はいずれかの分子に属するため、atomは`ResidueGruop`の中にある。

```python
print(u.atoms[100:130].residues)
##output
<ResidueGroup with 3 residues>

print(list(u.atoms.residues))
##output
[<Residue LEU, 6>, <Residue GLY, 7>, <Residue ALA, 8>]
```

#### `segment`object
更に大きなグループとして`segmentGroup`があり、これは例えばタンパク質や高分子のモノマーに対応している。
さらにこれらは「Segment ID(segids)」をあわせて持っている。

```python
print(u.atoms.segments)
##output
<SegmentGroup with 1 segment>

print(list(u.atoms.segments))
##output
[<Segment 4AKE>]
```

## 5.Working with AtomGroup

先ほど説明した３つのグループはルールをもとに自動的に生成されるobjectであるが、
自分が自由に作成できるobjectとして`AtomGroup`がある。

またここでの出力結果はnumpy.ndarrayで出力されることがほとんどなので、前もってインポートしておくと良い。

使い方の例として、

```python
CA = u.select_atoms("protein and name CA")
r = CA.positions
print (r.shape)
##output
(214.3)
#214原子の座標（x:0, y:1, z:2 に対応）があるよって情報が得られた。
```
などとできる。

### 5.1.Important methods and attributes of AtomGroup

上の`positions`のように、出力できる重要な例として。

 - center_of_mass( )  :　系の重心
 - centor_of_geometry( ) : 重心
 - total_mass( ) : 質量
 - total_chargre( ) : 電荷
 - radius_of_gyration( ) : 慣性半径
 - principal_axis( ) : 使ったこと無いのでわかりません

などがある。

### 5.2.Exersice

省略

### 5.3.Processing AtomGroup

特定のatomgroupを別ファイルに書き出す方法が述べられているが、 ~~使えない~~ 使わないので割愛。

### 参考
次回は6章以降を解説する予定である。

（原文）[MDAnalysis Tutorial](https://www.mdanalysis.org/MDAnalysisTutorial/index.html)

## 6.Trajectory analysis
`MDAnalysis.Universe`はpdbのような座標情報と原子の結合のようなトポロジーを結びつけるものであり、ここから様々な解析ができる。
ここでは簡易的な解析法について説明する。

トラジェクトリーのフレーム数は

```python
print(len(u.trajectory))
```

で出力できる。

また、基本的にある物理量の各タイムステップでの値を取り出したい場合は、以下のように`Universe.trajectory`をイテレーション（反復）する。

```python
result_rg = []
protein = u.select_atoms("protein")
for ts in u.trajectory:
    result.append(protein.radius_of_gyration())
```

こうすることで`result_rg`のリストの中にタンパク質の慣性半径の値が保存される。
また、pythonの復習だが、以下のように内包表記を用いることでコードは短くなる。

```python
protein = u.select_atoms("protein")
result_rg = [i.radius_of_gyration() for i in u.trajectory]
```

さらに、以下のようにすれば、タイムステップと慣性半径の値をまとめたリストができるため、
gnuplotなどでプロットできるデータが生まれる。

```python
result_rg = []
protein = u.select_atoms("protein")
for ts in u.trajectory:
    result_rg.append((u.trajectory.time, ,protein.radius_of_gyration()))
```