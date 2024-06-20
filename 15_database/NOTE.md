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

# 第15章 データベース

### `created_at`, `updated_at` が必要なテーブルの判断基準

- 日々更新するトランザクション、マスタデータ
- レコードが頻繁に更新されるデータ

逆に不要な場合は？

- バッチで丸ごと洗い替えするようなテーブル
  - 全レコード同じ `created_at` `updated_at` になる
  - Excelから取り込むテーブル（L-Order）
  - WebAPI で取り込むテーブル（AL us_stocks）
  - レコード単位個別に `created_at` を足すより、個別に取り込み実行日時を管理するようなアプローチも検討すべき。
- 親子テーブル（1:N）の子
  - 親側の `updated_at` だけみておけば十分。
  - 子テーブルの各レコードが洗い替え更新の場合はなおさら。
  - 個別更新の場合で、子レコードの `updated_at` が業務上必要になる場合は、致し方ない。
- レコードをUPDATEすることがないテーブル
  - `created_at` だけあればよい。近い将来、更新が起こりうるなら最初から `updated_at` を入れておくのも良い。

- 「分からないけどとりあえず足しておけ」というDB設計方針の会社もよくある。
  - 追加してなくて困るより、不要でもとりあえず追加しておけば困ることはない。
- ただし一度運用が始まったら、後からカラムを消すのも一苦労。多分もう消さない。
- 「いつ？」だけでなく「誰が？」を持たせる場合もある。

マスタデータはさらに世代管理が要件として必要になる場合もある。
４月から価格改定などの場合、商品マスタに予約レコードを 追加しておく。
価格改定後も、３月中の注文データを参照すると、古い価格で画面に表示される。


### ポリモーフィック関連

無闇に使うべきでない。原則としては、それでいいと思う。
使い所を見極めるべき。
高度なオブジェクト指向アーキテクチャになってくると、E/R - O/R のミスマッチを埋めるために必要な機能であると思う。


### CHECK制約

Webアプリなら、画面側の要件にしたがって、Rails のバリデーション使うべき。
- CHECK制約はDB側での制御要件しか充足できない。
- DB側の都合だけしか考慮できない。
- Railsのバリデーションと両方管理とかやりだすと、工数増えてしんどくなるだけ。
- バッチ処理メインのシステムとかなら、アリかも。

### モデル層

Sequel 使ったことない。
EntityFramework の式木展開みたいなアプローチ。
最終的にはSQL生で書くのが一番早いので、どっちも同じ。
生SQLができない or 遅いフレームワークは致命的。

Rails でやるなら ActiveRecord しか選択しはない。

## まとめ

情報が偏ってて、ふわっとしてる感じ。基礎編を主観的にまとめた感じ。
ストアドや歴史背景、非正規化とマテビュー、システムのアーキテクチャごとの考え方など、応用編としての説明が欲しかった。
個別のライブラリについて、ピックアップして追加学習が必要。
