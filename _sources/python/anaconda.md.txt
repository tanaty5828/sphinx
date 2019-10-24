# Anaconda

pythonのパッケージマネージャー。



## pipと混ぜない

pythonのパッケージマネージャーは`pip`と`conda(anaconda)`の2種類があるが、

両方を混ぜて使用すると予期しないバグが発生する。

どちらかに統一するべき（筆者はconda統一）



## installしたいパッケージがcondaになかったら？

解決策を列挙する。上からためそう

### よく探す



### conda用のパッケージを自分でビルドする。

例）sphinx_markdown_tablesについて

`conda install sphinx_markdown_tables`としてもパッケージがなく、

[PyPiのサイトにはあるとき](<https://pypi.org/project/sphinx-markdown-tables/>)、

`conda skeleton`を利用する。

```bash
$ conda skeleton pypi <パッケージ名>
$ conda build <パッケージ名>
$ conda install --use-local <パッケージ名>
```

