# 第2章 役に立つ独自クラスを設計する

> オブジェクト指向設計, 関数型設計, 手続き型設計
> メンテナンスしやすい設計の多くは、この 3 つを取り混ぜたもの

認知負荷について

> 独自クラスを新たに 定義すると、コードを扱う全員が、そのクラスを正しく使って仕事を進めていけるように、
> そのクラスの仕組みを学ぶ必要があります。

独自クラスの定義が適切かどうかの判断基準
- 情報の隠蔽が必要か
- 機能の追加が必要か
- どれぐらい広く使われるか

## SOLID原則
復習の意味を込めて自分の理解を書き出す。

- 単一責任
  クラスの責務は一つだけにする
- オープン、クローズド
  拡張に対して開いていて、変更に対して閉じていること
- 置換原則
  あるクラスを、そのサブクラスで置き換えても問題なく動くこと
- インタフェース分離
  クラスを使うとき、その内部実装は気にせず、インタフェースに対してプログラミングする
- 依存関係逆転
  共通クラスから業務固有のクラスに依存が発生する時は、インタフェースを抽出して、それを共通クラスに置いておき、そのインタフェースに対して共通クラスを書く

### 単一責任の法則

> この原則はもっぱら、「複数の目的を担って いる既存の単一のクラスを複数のクラスに分割する」ことの正当化に使われています

なるほど。そういった解釈の仕方をすれば全部そうなる。
Ruby の標準ライブラリはファットモデルと呼ばれる理念。

> 「新しく分割したクラスには、アプリケーションやライブラリでほかにも使いどころはあるだろうか?」

これはとてもいい質問。
まさに こういうこと。

> 一般原則としては、クラス設計に複雑性を付け加えるのはできる だけ後にしましょう。その複雑性が本当に必要になってから追加すべきです。

バランス感覚。

### オープン・クローズドの原則

オープンクラスなので、いくらでも拡張可能。
クローズドの方は、筆者が勘違いしているような感じがする。
変更する際に、変更対象のクラスを変更するだけで、そのクラスを使用している箇所は変更しなくて済む、というのがこの原則。
それなのに、難しいテクニカルな内容になっているので、論点がずれている。

### 置換原則

ダックタイピングだし、`method_missing` あるし、置換原則もくそもない。
いくらでもインタフェースさえ合っていれば置換可能。
と思ったらかのように書かれていた。
> Ruby が採用しているダックタイピングでは、使っ ているオブジェクトが具体的にどのクラスなのかは基本的には気にしません。
> ですから、リスコフの置換原則をそれほどは気にしません。

スーパータイプと挙動が異なる場合について記述があった。
こういった観点で書かれている本は見たことない。

そもそも、スーパータイプと挙動が異なるのはあたりまえ。
インタフェースが異なるのはダメ。
「第二引数を必須にしたい」と書かれている。これはインタフェースの変更。だから置換原則違反。
だからどうするかというと、リファクタリングしろと。
置換原則関係なく、当たり前の話に聞こえる。

`instance_of?` で分岐すると台無し。この指摘はとても良い。
なんでもできてしまう Ruby で特に注意すべきところ。

### インターフェイス分離の原則

これもダックタイピングなので、あまり関係ない。
というか気にしなくてもなんとかなる。
逆に、経験の浅いプログラマだと、頭の中にインタフェースが見えないので、設計意図が伝わらずに困る。

### 依存関係逆転の原則

キーワード引数でオプションを追加する拡張方法。
自分もよく使う。実践的でとても良い例。

## 復習問題

1. TODO
2. インタフェース分離。インタフェースがないため。ダックタイピングだし、そもそも気にしなくて良い。
3. 有用ではない
4. TODO

