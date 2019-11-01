# Sandbox

このページは初心者編集用＋markdownの記法確認用です。

ここいろいろ編集してみてね:star:





**テストテスト**





Qiitaのマークダウン記法を一覧表にしました。

| やりたいこと                                                 | 書き方                                                    |      | やりたいこと                                                 | 書き方                                             |
| :----------------------------------------------------------- | :-------------------------------------------------------- | :--: | :----------------------------------------------------------- | :------------------------------------------------- |
| [見出し](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E8%A6%8B%E5%87%BA%E3%81%97) | # 見出しh1 <br> ## 見出しh2                               |      | [リスト](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%83%AA%E3%82%B9%E3%83%88) | - 箇条書き<br>&nbsp;&nbsp;&nbsp;- インデントはタブ |
| [番号付きリスト](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E7%95%AA%E5%8F%B7%E4%BB%98%E3%81%8D%E3%83%AA%E3%82%B9%E3%83%88) | 1. テキスト                                               |      | [空行・改行](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E7%A9%BA%E8%A1%8C%E6%94%B9%E8%A1%8C) | 半角スペース2つ、\<br>など                         |
| [インライン表示](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%82%A4%E3%83%B3%E3%83%A9%E3%82%A4%E3%83%B3%E8%A1%A8%E7%A4%BA) | \`テキスト`                                               |      | [コードの挿入](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AE%E6%8C%BF%E5%85%A5) | \`\`\`java:title<br>code<br>\`\`\`                 |
| [リンクの挿入](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%83%AA%E3%83%B3%E3%82%AF%E3%81%AE%E6%8C%BF%E5%85%A5) | \[タイトル](URL)                                          |      | [引用](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E5%BC%95%E7%94%A8) | \> テキスト<br> \>> テキスト                       |
| [画像の挿入](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E7%94%BB%E5%83%8F%E3%81%AE%E6%8C%BF%E5%85%A5) | ドラッグ＆ドロップ <br> \!\[代替テキスト](URL "タイトル") |      | [画像の挿入（サイズ指定）](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E7%94%BB%E5%83%8F%E3%81%AE%E6%8C%BF%E5%85%A5%E3%82%B5%E3%82%A4%E3%82%BA%E6%8C%87%E5%AE%9A) | \<img width="数値" alt="代替テキスト" src="URL">   |
| [テーブルの挿入](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E6%8C%BF%E5%85%A5) | 列数x行数                                                 |      | [文字色](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E6%96%87%E5%AD%97%E8%89%B2) | \<font color="Red">テキスト\</font>                |
| [太字](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E5%A4%AA%E5%AD%97) | \*\*テキスト**                                            |      | [斜体](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E6%96%9C%E4%BD%93) | \*テキスト*                                        |
| [打ち消し線](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E6%89%93%E3%81%A1%E6%B6%88%E3%81%97%E7%B7%9A) | \~\~テキスト~~                                            |      | [水平線](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E6%B0%B4%E5%B9%B3%E7%B7%9A) | ***                                                |
| [注釈](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E6%B3%A8%E9%87%88) | テキスト\[^1]<br>\[^1]: 注釈内容                          |      | [マークダウンのエスケープ](http://qiita.com/kamo_qiita/items/6f342da395ad57468ae3#%E3%83%9E%E3%83%BC%E3%82%AF%E3%83%80%E3%82%A6%E3%83%B3%E3%81%AE%E3%82%A8%E3%82%B9%E3%82%B1%E3%83%BC%E3%83%97) | \                                                  |

見出し

* 書き方

>
```
# 見出しh1
## 見出しh2
```

* **結果**

>
## 見出しh1
### 見出しh2

リスト

* 書き方

>
```
* テキスト
    * テキスト
    * テキスト
```

* **結果**

>
* テキスト
    * テキスト
    * テキスト

* 補足
    * 「-」でも可。
    * 箇条書き中はタブでインデントを下げられます

## 番号付きリスト
* 書き方

>
```
1. テキスト
2. テキスト
    3. テキスト
```

* **結果**

>
1. テキスト
2. テキスト
    3. テキスト

空行、改行

* 書き方

>
```
1行目__（←半角スペース２つ）
2行目
<br>
<br>
3行目
```

* **結果**

>
>1行目  
>2行目
><br>
><br>
>3行目

## インライン表示
* 書き方

>
```
`テキストやコード`
```
>>
```
例： `int i = 0`
```

* **結果**

>
>このように`int i = 0`がインライン表示されます

* 補足
    * バッククォート 「 shift + @ 」

## コードの挿入
* 書き方

>
>\`\`\`言語:タイトル
>コード
>\`\`\`
>>
>>例：
>>\`\`\`java
>>int i = 0; //コード
>>\`\`\`

* **結果**

>
>例：
>
```java
int i = 0;
```

>

* 補足
    * バッククォート 「 shift + @ 」
    * 言語を指定すればシンタックスハイライトが使えます。


## リンクの挿入
* 書き方

>
```
[タイトル](URL)
```
>>
```
例：[Qiita](http://qiita.com/)
```

* **結果**

>
>例：[Qiita](http://qiita.com/)


## 引用
* 書き方

>
```
>> テキスト
>>> テキスト
```

* **結果**

>
>> テキスト
>>
>> > テキスト


## 画像の挿入
* 書き方

>
```
ドラッグ＆ドロップ
```
```
![代替テキスト](URL "タイトル")
```
>>
```
例： ![qiita-square.png](https://qiita-image-store.s3.amazonaws.com/0/126861/90386757-fd96-8ba6-3477-485669713c55.png "qiita-square")
```

* **結果**

>
>例：![qiita-square.png](https://qiita-image-store.s3.amazonaws.com/0/126861/90386757-fd96-8ba6-3477-485669713c55.png "qiita-square")

* 補足
    * 代替テキストは空、タイトルは無しでもOK


## 画像の挿入（サイズ指定）
* 書き方

>
```
<img width="数値" alt="代替テキスト" src="URL">
```
>>
```
例： <img width="200" alt="qiita-square" src="https://qiita-image-store.s3.amazonaws.com/0/126861/90386757-fd96-8ba6-3477-485669713c55.png">
```

* **結果**

>
>例：<img width="200" alt="qiita-square" src="https://qiita-image-store.s3.amazonaws.com/0/126861/90386757-fd96-8ba6-3477-485669713c55.png">

* 補足
    * htmlの\<img>タグで指定できます

## テーブルの挿入
* 書き方

>
```
列数x行数 （）
```

* **結果**

>
>![table-d8f2751a111f693b2e8ad0700598ff97-1.gif](https://qiita-image-store.s3.amazonaws.com/0/126861/0359df9f-67db-724d-047b-9ed5ed97c47f.gif)


* 補足
    * 左寄せ 「:--」
    * 右寄せ 「--:」
    * 中央寄せ 「:-:」


## 文字色
* 書き方

>
```
<font color="Red">テキスト</font>
```

* **結果**

>
><font color="Red">テキスト</font>


## 太字
* 書き方

>
```
**テキスト**
```

* **結果**

>
>**テキスト**


## 斜体
* 書き方

>
```
*テキスト*
```

* **結果**

>
>*テキスト*


## 打ち消し線
* 書き方

>
```
~~テキスト~~
```

* **結果**

>
>~~テキスト~~

水平線

* 書き方

>
```
***
```

* **結果**

>
***

* 補足
    * 「---」（ハイフン）でも化。



## マークダウンのエスケープ
* 書き方

>
```
\マークダウン記号
```
>>
```
例： \`インライン表示されなくなる`
```

* **結果**

>
>例： \`インライン表示されなくなる`

* 補足
    * バックスラッシュ
        * macは 「 alt + ¥ 」
        * windowsは？？？



## 注釈、警告(rst記法)

- 書き方

>
> ```
> ​```eval_rst
> 
> .. note::
> 
>     これは注釈です。
>     
> .. warning::
>     
>     これは警告です。
>     
> ​````
> ```

- 結果

```eval_rst

.. note::

    これは注釈です。
    
.. warning::
    
    これは警告です。
    
````

- 補足
  - インデントと空行が大事です。

## その他

* htmlタグが色々使えるそうなのでマークダウンでできそうにないときはhtmlタグを使ってみるのもいいかもしれません。
* 公開されているQiita記事のURLの末尾に「.md」をつけるとマークダウンが見れます。


## 参考にした、参考になるサイト
* [Markdown記法について](http://kobito.qiita.com/ja/docs/about-markdown)
* [Markdown記法 チートシート](http://qiita.com/Qiita/items/c686397e4a0f4f11683d#%E6%B3%A8%E9%87%88)
* [Qiita Markdown 書き方 まとめ](http://qiita.com/shizuma/items/8616bbe3ebe8ab0b6ca1#%E5%8F%82%E8%80%83)
* [Markdown記法一覧](http://qiita.com/oreo/items/82183bfbaac69971917f)
* [Markdown記法 サンプル集](http://qiita.com/tbpgr/items/989c6badefff69377da7)
* [Qiitaのマークダウンで色をつける方法[140色]](http://qiita.com/twipg/items/d8043cd4681a2780c160)
* [Qiitaで色々なフォントを使う](http://qiita.com/yyu/items/894de7fc3a83e675b06b)