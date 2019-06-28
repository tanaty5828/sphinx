# FFTW

![](./fftw.png)

高速フーリエ変換ができるライブラリ？詳しいことはわかんないけど様々なMDソフトのインストールに必要になるためインストールしておくべし



## インストール方法

[FFTW3のサイト](http://www.fftw.org/)にアクセス、Downloadingから`.tar.gz`ファイルをダウンロードする。

以下、`~/App/fftw3`にインストールするものとする。

ダウンロードしたディレクトリに行って

```shell
mkdir ~/App/fftw3
cd fftw-3.3.8 # 2019現在。数字は異なる可能性あり。
./configure --prefix=$HOME/App/fftw3 --enable-threads --enable-float --enable-sse2
make -j 8 # 8はパソコンのコア数。スパコンでやるときは使わないほうがいい。

# エラーが出ていなければ、
make install
```

これで単精度版がインストールできるが、倍精度版も同時にインストールしよう。

```shell
./configure --prefix=$HOME/App/fftw3 --enable-threads --enable-sse2
make -j 8 # 8はパソコンのコア数。スパコンでやるときは使わないほうがいい。

# エラーが出ていなければ、
make install
```

少し時間はかかるがインストールできるはずだ。

