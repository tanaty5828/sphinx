# LAMMPS

篠田グループの粗視化モデルがインストールされているため、粗視化MD計算はこちらで行うことが多い。

## インストール

### CMake

lammpsもGromacs同様、cmakeでインストールする方法が最近主流になってきている。CMakeのほうがかなり簡単にインストールできるので、頑張ってみよう。

基本的には、ダウンロードしたディレクトリに入り、`build`ディレクトリを作成し`build`のなかに入り、cmakeを実行するだけである。

```bash
git clone -b stable https://github.com/lammps/lammps.git
cd lammps
mkdir build && cd build
cmake ../cmake
make -j 8
```

ただし、これでは必要なパッケージ等々がインストールできていないのでcmakeの際にオプションを指定する必要がある。

#### 並列オプション

これらがないとスパコン等での並列計算ができない

| Options        | Description |
| -------------- | ----------- |
| -DBUILD_MPI=on | MPI並列     |
| -DBUILD_OMP=on | OpenMP並列  |

#### コンパイラオプション

lammpsは、c++のコンパイラにデフォルトで`mpicxx`を使用する。理由がなければ、インテルコンパイラに変更したほうがいい。

| Options                           | Description                       |
| --------------------------------- | --------------------------------- |
| -DCMAKE_C_COMPILER=mpiicc         | Cコンパイラにmpiiccを指定         |
| -DCMAKE_CXX_COMPILER=mpiicpc      | C++コンパイラにmpiicpcを指定      |
| -DCMAKE_Fortran_COMPILER=mpiifort | Fortranコンパイラにmpiifortを指定 |

#### FFTオプション

PPPM(Particle–Particle–Particle–Mesh)などの静電相互作用の取り扱いのために必要になる。

| Options        | Description      |
| -------------- | ---------------- |
| -DFFT=FFTW3    | FFTにFFTW3を指定 |
| (or) -DFFT=MKL | FFTにMKLを指定   |

FFTにFFTW3を指定しても、CMakeがFFTW3を見つけてくれないときがある。その時はFFTW3のincludeとlibディレクトリを指定する必要がある。

| Options                        | Description                      |
| ------------------------------ | -------------------------------- |
| -DFFTW3_INCLUDE_DIRS=/home/... | FFTW3のincludeディレクトリを指定 |
| -DFFTW3_LIBRARIES=/home/...    | FFTW3のlibディレクトリを指定     |

#### パッケージオプション

SPICA粗視化力場などを使用するために必要

| Options                  | Description                            |
| ------------------------ | -------------------------------------- |
| -DPKG_MOLECULE=on        | MOLECULEパッケージ                     |
| -DPKG_OPT=on             | OPTパッケージ                          |
| -DPKG_RIGID=on           | Rigid bodyをあつかうパッケージ         |
| -DPKG_MISC=on            | xtcなどトラジェクトリ関連のパッケージ  |
| -DPKG_KSPACE=on          | PPPMなどの静電相互作用関連のパッケージ |
| -DPKG_USER-MISC=on       | xtcなどトラジェクトリ関連のパッケージ  |
| -DPKG_USER-MOLFILE=on    | PDBの出力ができるようになる            |
| -DPKG_USER-CGSDK=on      | SPICA力場のパッケージ                  |
| -DPKG_USER-COLVARS=on    | ABFなどcolvarsのパッケージ             |

これらは全て必要になると思うので、入れておきましょう。

#### GPUパッケージ

GPUのあるスパコンで利用できる。

| Options          | Description                                             |
| ---------------- | ------------------------------------------------------- |
| -DPKG_GPU=on     | GPUパッケージ本体                                       |
| -DGPU_API=cuda   | 利用するAPIの指定。指定しないと**OpenCL**が指定される。 |
| -DGPU_ARCH=sm_?? | GPUアーキテクチャの指定(以下参照)                       |

GPUのアーキテクチャはcudaのバージョンやGPU本体によって変わる。大まかに、

- sm_12 or sm_13 for GT200 (supported by CUDA 3.2 until CUDA 6.5)
- sm_20 or sm_21 for Fermi (supported by CUDA 3.2 until CUDA 7.5)
- sm_30 or sm_35 or sm_37 for Kepler (supported since CUDA 5)
- sm_50 or sm_52 for Maxwell (supported since CUDA 6)
- sm_60 or sm_61 for Pascal (supported since CUDA 8)
- sm_70 for Volta (supported since CUDA 9)
- sm_75 for Turing (supported since CUDA 10)

という区分け。これを参考に、`-D GPU_ARCH=`を指定する。詳しくは[CUDA - Wikipedia(English)](https://en.wikipedia.org/wiki/CUDA#GPUs_supported)を参照してほしい。

### TL;DL

名大cxにインストールするときは、こんな感じになった。

```bash
# cmakeやmpiicpcなどモジュールのロード
module load intel/2019.5.281
module load impi/2019.5.281
module load mpi-fftw/3.3.8
module load fftw/3.3.8
module load cuda/10.2.89_440.33.01
module load cmake/3.17.3

# lammpsソースのダウンロード
git clone stable https://github.com/lammps/lammps.git

# ディレクトリの管理
cd lammps
mkdir build && cd build

# cmake
cmake ../cmake -DBUILD_MPI=on -DBUILD_OMP=on -DCMAKE_C_COMPILER=mpiicc -DCMAKE_CXX_COMPILER=mpiicpc -DCMAKE_Fortran_COMPILER=mpiifort -DPKG_USER-COLVARS=yes -DPKG_MOLECULE=on -DPKG_OPT=on -DPKG_RIGID=on -DPKG_MISC=on -DPKG_USER-MISC=on -DPKG_USER-CGSDK=on -DPKG_KSPACE=on -DFFT=MKL -DPKG_GPU=on -DGPU_API=cuda -DGPU_ARCH=sm_70

# インストール
make -j 8
```

これで、`build`の中に`lmp`という実行バイナリが生成される。



### Traditional make(古い方法)

かなり長くて大変だが、説明しよう。

以下は田中が東大物性研にインストールした際の手順だが、ほとんど同じはずだ。

[LAMMPSのサイト](https://lammps.sandia.gov/)に行き、ダウンロードのページからstableと書いてあるものをダウンロード。

以下は`~/App/`にインストールするものとする。

ダウンロードした`.tar.gz`ファイルを`~/App/`に移動しておく。

```shell
tar xvf lammps-11Aug17.tar.gz #2019現在。数字は異なる可能性あり。
cd lammps-11Aug17/lib/colvars
make -f Makefile.g++
cd ../../src
make yes-molecule yes-kspace yes-opt yes-rigid yes-misc yes-user-colvars yes-user-misc yes-user-cgsdk# 別途必要なモジュールがあれば指定する。
vi MAKE/Makefile.mpi
```

`make yes-...`のコマンドは2-3回やったほうがいいかもしれない。謎の依存関係があるみたい。

```
FFT_INC =
FFT_PATH =
FFT_LIB =
 と、空行になっているところを編集する。fftwが`~/App/fftw3`にインストールしてあるなら、

FFT_INC = -I/home/tanaka/App/fftw3/include -DFFT_FFTW3
FFT_PATH =
FFT_LIB = -L/home/tanaka/App/fftw3/lib -lfftw3
と編集し、保存する。(/home/tanaka/)の部分は環境に合わせてください。
```

ここまでやったら、

```shell
make mpi -j 8
```

とする。

```
   text	   data	    bss	    dec	    hex	filename
19373039	 396960	 290848	20060847	1321aaf	../lmp_mpi
make[1]: Leaving directory '/home/tanaka/App/lammps-11Aug17/src/Obj_mpi'
```

↑のような表示が出ればインストール成功で、(`./lmp_mpi`)という実行バイナリが生成されている。

インストールできなかった場合、`make clean-all`とすると生まれたゴミを一掃してくれて助かる。



### `make`に失敗する場合

また、東大物性研にインストールする際は、`MAKE/makefile.mpi` を

```
CCFLAGS =	-g -O3
を、
CCFLAGS =	-g -O3 -restrict
```

と編集しないとインストールできなかった。とりあえずそのままやってみて`make mpi -j 8`、上手く行かなかったら上のような編集をするといい。



## インストール(gpu)

パソコンにグラフィックボードが刺さってる場合は、gpu版を使ったほうが計算が劇的に早くなることがある。ただし、コンパイルのミスなどで逆に遅くなることもあるので注意して扱うべき？

手元の環境ではgpuを使うとかなり早くなる。

先ほどの続きで、

```shell
cd ~/App/lammps11-Aug17/src
cd ../lib/gpu
vi Makefile.linux
```

```
CUDA_HOME=/usr/local/cuda
を、
CUDA_HOME=/home/app/cuda/cuda-8.0 # 東大物性研の場合。
CUDA_HOME=/usr/local/cuda-10.0 # 研究室PCの場合
に変更する。（デフォルトのcudaバージョンが変わったら数値は変更する必要あり。）
ちなみに、
echo $CUDA_HOME
の出力結果に変更するのと同義である。わからなかったらecho $CUDA_HOMEをやって調べよう。

また、
CUDA_ARCH = -arch=sm_21
の行はコメントアウトしておくほうがいい。ここはGPUのアーキテクチャに依存して変更するのだが、このアーキテクチャを調べる方法がわからないのである。
```

編集が終わったら、

```shell
make -f Makefile.linux
vi Makefile.lammps
```

で確認して

```
CUDA_HOME=/home/app/cuda/cuda-8.0
```

のようになっていればOK。まれにここもデフォルト(`CUDA_HOME=/usr/local/cuda`)のままになっていることがあるので、注意が必要である。

`make -f Makefile.linux`に成功したら、

```shell
cd ../../src
make yes-gpu
# いくつかファイルがないと言われるが、大丈夫である。

make package-status
# GPUがONになっていればOKである。

make mpi -j 8
```

```
   text	   data	    bss	    dec	    hex	filename
19373039	 396960	 290848	20060847	1321aaf	../lmp_mpi
make[1]: Leaving directory '/home/tanaka/App/lammps-11Aug17/src/Obj_mpi'
```



↑のような表示が出ればインストール成功で、./lmp_mpiという実行バイナリが生成されている。

これはGPU計算にも対応しているバイナリである。

インストールできなかった場合、make clean-allとすると生まれたゴミを一掃してくれて助かる。