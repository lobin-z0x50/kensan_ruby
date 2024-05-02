> **読み方**
> - 読んでないところ、わからないところは「TODO」残す
> - 質問が来そうなところは、先回りして説明コードを書いてみる
> - **読み方**
>   - 各節の末尾に まとめが書いてあるので、そこを読んでいく。
>   - 詳しく知りたい内容だったら、本文を読む。
>   - 上から順に読まない。
> - アウトプット
>   - 知らなかったことをまとめて書き出す。
> - 不明点（調査項目）
>   - みんなに質問したいこと
>   - 自分で追加調査したいこと


# 第9章 メタプログラミング

```rb
class Bar
  extend HashAccessor
end

# ミックスインを使うなら普通 include して、
# モジュール側の `included do` でやればよいのでは？
```

> extend は singleton_class.include の短縮記法

ややこしいですね。
オブジェクト指向の言葉では、'extend' = 「継承」なので、
`class Bar < HashAccessor` 相当と捉えることもできる。

`hash_accessor` がクラスメソッドなんだけど、DSLになっている。
呼ぶと、内部で `define_method` してくれる。


**特異クラスの特異クラス**
わかりやすい説明がないな〜
TODO: Qiitaとかに良い説明がないか探してみたい。


### evalベースのメタプログラミング
Perl時代はこれでやってました。
Rubyにきてからも、最初の1,2年はevalベースでやってた。


### method_missing

```rb
Class.new(BasicObject) do
  define_method(:method_missing) do |meth, *|
    array << meth
  end
end.new.instance_exec(&block)
```

`words{def}` とかやると、予約語なのでエラーにならないのかな？


試してみた
```
irb(main):015:1* def words(&block) array = []
irb(main):016:3* Class.new(BasicObject) do define_method(:method_missing) do |meth, *|
irb(main):017:2* array << meth end
irb(main):018:1* end.new.instance_exec(&block); array.reverse
irb(main):019:0> end
=> :words
irb(main):020:0> words{this is a pen}
=> [:this, :is, :a, :pen]
irb(main):021:1> words{def}
irb(main):022:1*   }
irb(main):023:0> end
/Users/lobin/.rbenv/versions/3.2.2/lib/ruby/3.2.0/irb/workspace.rb:119:in `eval': (irb):21: syntax error, unexpected '}' (SyntaxError)
words{def}
         ^

        from /Users/lobin/.rbenv/versions/3.2.2/lib/ruby/gems/3.2.0/gems/irb-1.6.2/exe/irb:11:in `<top (required)>'
        from /Users/lobin/.rbenv/versions/3.2.2/bin/irb:25:in `load'
        from /Users/lobin/.rbenv/versions/3.2.2/bin/irb:25:in `<main>'
irb(main):024:0>
```
