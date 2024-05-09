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

# 第10章 DSL

DSLの章があること自体、とても嬉しい。
内部DSLを簡単に実現できるのがRubyのいいところ。得意分野。

DSL向けに有利な点として、文法上の特徴をあげてみた。
- 文末のセミコロンが省略可能
- 関数呼び出しのカッコを省略可能
- `do 〜 end` や `{ 〜 }` がブロックになり、クロージャとして扱える
- `method_missing` があるので、未定義のメソッドでもエラーにせずに動かせる

## 設定用DSL

いきなり初めて聞いた言葉。
Rubyに特化した内容ではない。
他の言語でもこのようなことは普通に行われる。DSLでもない。

**コマンドオブジェクト方式**
```rb
Foo.process_bars do |c|
  c.bar(:bar1, :baz2, 3, quux: 1)
  c.bar(:bar2, :baz4, 5)
  
  # ...
  
  c.skip_check{|bar| bar.number == 5}
  c.generate_names = true
end
```
コマンドパターンとシュークリームパターンの合わせ技とも取れる。

ExcelImporter の設定とかに応用できそう。

**instance_exec**
ブロックを別のコンテキストで評価する方法。
黒魔術の基本。

ブロックパラメータがあると、`instance_exec` をしないような分岐を入れるのは、いいアイデア。

> 特にライブラリに慣れていない ユーザーにとっては混乱のもと

確かにそうですが、そもそもDSL自体そういうものでは？

複雑で大規模な設定項目を取り扱おうとしているユーザは、DSL経由であろうが別のインタフェースであろうが、
少なからず学習コストがかかる。


## DSLが提供されるライブラリ

`minitest/spec`
`sinatra`

Rails でもたくさん使われている。
- マイグレーションファイル
- `routes.rb`
- モデルアソシエーション
- バリデーション
- コントローラやモデルのコールバック
- など


## 独自のDSL

シンプルな雛形コード
```rb
def RSpec.configure
  yield RSpec::Core::Configuration.new
end
```

`method_missing` をつかう裏技が紹介されていた。

> Sinatra では、トップレベルのスコープでモジュールを指定して extend を呼び出すことによ り、これを解決しています。

なるほど、面白い。


> minitest/spec は DSL であることのメリットがとても大きい事例

DSLとしては、出来がいいのかもしれないが、ライブラリの存在意義に疑問。
素直に🪹`RSpec` を使えば良い。
`minitest` を使うなら、素の `minitest` を使えば良い。
無駄に学習コストが増えるだけのライブラリ。

## EzGate はどういうパターンになっているのか？

コンセプト
- Ruby言語であると意識しなくても書ける。
- 実際には Ruby インタプリタで実行している。
- 関数定義したり、ループ処理したりと、プログラミングできる

[EzGate のDSL](https://qiita.com/lobin-z0x50/items/2d91050fa2e7bffa692a#%E8%A8%AD%E5%AE%9A%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E6%9B%B8%E3%81%8D%E6%96%B9)
```rb
domain('www.example.com') {
  proxy_to "www"                # 中継先サーバリスト。名前解決できればIPでなくとも良い
  cert_email 'your@email.com'   # SSL証明書取得に使用するメールアドレス
}

domain('redmine.example.com') {
  proxy_to "redmine"
  cert_email 'your@email.com'
}
```

[ソースコード](https://github.com/neogenia-jp/EzGate/blob/master/docker/resources/scripts/lib/parser.rb)
ブロック引数は使っていない。
Sinatraのアプローチ？


