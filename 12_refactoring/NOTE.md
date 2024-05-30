**方針**
> - 読んでないところ、わからないところは「TODO」残す
> - 質問が来そうなところは、先回りして説明コードを書いてみる
> - 読み方
>   - 各節の末尾に まとめが書いてあるので、そこを読んでいく。
>   - 詳しく知りたい内容だったら、本文を読む。
>   - 上から順に読まない。
> - アウトプット
>   - 知らなかったことをまとめて書き出す。
>   - 不明点（調査項目）
>   - みんなに質問したいこと
>   - 自分で追加調査したいこと


# 第12章 変更に対処する

リファクタリングに取りかかる前に、テストを整備しておくことが重要

### メソッドの抽出
サンプルコードはシュークリームパターン

### クラスの抽出
上流設計が提示されていないので、なんとも評価できない。

クラス設計の難しさの好例
- 必須引数でないものをコンストラクタ引数にしてしまうのは良くない
- Entityモデルをイミュータブルにすることはできない
- Client, Shipment というモデリング単位の異なるクラスを並列に比較している
  - Client は顧客なので、マスタ情報
  - Shipment が出荷なので、トランザクション情報。ShippingAddress ならマスタになり得た
- format 責務をエンティティも持たせていること自体、すぐに限界が来る

クラスの抽出、モデリング、DDDにつながる考え方。
リファクタリングというより設計理論。

### 機能追加
**カウボーイアプローチ**
- 無計画にやっちゃうこと？
- 実装しながら、リファクタリングしながら、一緒くたになってしまう。

**系統的アプローチ**
- 計画的なやつ？
- まずリファクタリングして、テストで検証。
- 問題なくリファクタリング出来たことを確認してから、新機能の実装を行う。

計画的にやりましょう。

### 機能の削除
```rb
def method_to_be_removed
  warn("#{__callee__} is deprecated", uplevel: 1, category: :deprecated)
  # ...
end
```

`warn` の定石として使えそう。
TIPSとして社内で共有しておきたい。
- [Warnの公式ドキュメント](https://docs.ruby-lang.org/ja/latest/method/Kernel/m/warn.html)

**引数の意味の調査**

`uplevel:` ドキュメントによると「いくつ前の呼び出し元のファイル名と行番号を表示するかを0以上の数値で指定」とのこと

irb で試してみた
```
$ irb
irb(main):001:0> warn 'test', uplevel: 1, category: :deprecated
=> nil
irb(main):002:0> warn 'test'
test
=> nil
irb(main):003:0> warn 'test', category: :deprecated
=> nil
irb(main):004:0> warn 'test', uplevel: 1
/Users/lobin/.rbenv/versions/3.2.2/lib/ruby/3.2.0/irb/workspace.rb:119: warning: test
=> nil
irb(main):005:0>
```
メッセージの行頭に、バックトレースの１つ上のファイルパスが表示されてる感じ。

`category:` は使ったことなかった。
- [categoryに使える値](https://docs.ruby-lang.org/ja/latest/method/Warning/s/=5b=5d.html)
  現状、以下の２つしかない
  - `:deprecated`
    非推奨の警告。例: nil ではない値を $, や $; に設定する、キーワード引数、ブロックなしで proc / lambda を呼び出す、等
  - `:experimental`
    実験的な機能。
    例: パターンマッチング

#### 引数の削除

```rb
def arg_to_be_removed(arg=(arg_not_given=true; nil))
  unless arg_not_given
    warn("Passing deprecated argument to #{__callee__}", uplevel: 1, category: :deprecated)
  end
  # ...
end
```

パズル的で面白い。みたことない。
これもイディオム（定石）にできそう。

#### 定数の削除

```rb
class Foo BAR = 1
  BAR_ = BAR
  private_constant :BAR_
  deprecate_constant :BAR
end
```

`private_constant` 知らなかった。
`deprecate_constant` は、定数の場合だけ専用のメソッドが用意されてる？
[公式ドキュメント](https://docs.ruby-lang.org/ja/latest/method/Module/i/deprecate_constant.html)

**掲載されている例を以下に転記**
```
FOO = 123
Object.deprecate_constant(:FOO) # => Object

FOO
# warning: constant ::FOO is deprecated
# => 123

Object.deprecate_constant(:BAR)
# NameError: constant Object::BAR not defined
```

こんな感じで自動的に `warn` を出してくれる。

## まとめ

章の後半はみたことないイディオム多めで、とても勉強になった。

