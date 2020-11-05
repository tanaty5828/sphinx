# MPI environment

MPIとは、Message Passing Interfaceのことで、並列コンピューティングを利用するための標準化された規格のこと。

## MPI vs OpenMP

並列計算には、共有メモリ型と分散メモリ型の2通りの処理形態がある。MPIは分散メモリ型の並列処理を行い、OpenMPは共有メモリ型の並列処理を行う。

(単純に、並列処理にはMPIとOpenMPの2種類があると覚えておけば大丈夫です。)

### 実装

これがややこしい。MPIは、MPICHやOpenMPI(OpenMPじゃないよ)などの実装を通して利用できる。

MPIには更に2種類あると考えればよい。

- MPI
    - MPICH
    - OpenMPI
- OpenMP

## インストール

### 手元のマシン

手元のPCには、比較的簡単にインストールできる。おそらく、`sudo dnf install openmpi openmpi-devel`コマンドでインストール可能である。

```eval_rst

.. warning::

    ただし、実行バイナリへパスが通らないので、 **/usr/lib64/openmpi/binへパスを通す** 必要がある。

    ex) export PATH=${PATH}:/usr/lib64/openmpi/bin を~/.bashrcに追記。

```

これで、`mpicc`や`mpirun`のようなコマンドが利用できるようになる。

### スパコン

スパコンには、MPI環境が整備されているはずなので、自分でインストールせず、`module`コマンドで提供されているものを使おう。

## Reference

[MPIの環境構築や基本コマンドのまとめ](https://qiita.com/kkk627/items/49c9c35301465f6780fa)
