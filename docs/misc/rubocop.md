# RuboCop Copsのカスタム

TestProfにはより高いパフォーマンスのテストを書くことを支援する[RuboCop](https://github.com/bbatsov/rubocop) Copsが付属しています。

これらを有効化するにはRuboCopの設定に`test_prof/rubocop`を追加してください。

```yml
# .rubocop.yml
require:
 - 'test_prof/rubocop'
```

自分のニーズに合わせて設定する場合は以下のようにしてください。

```yml
RSpec/AggregateExamples:
  AddAggregateFailuresMetadata: false
```

もしくは、動的に追加することも可能です。

```sh
bundle exec rubocop -r 'test_prof/rubocop' --only RSpec/AggregateExamples
```

## RSpec/AggregateExamples

このCopは最近のRSpecの強力な機能の一つである「失敗の集約（aggregate_failures）」をexampleの中で使用することを推奨しています。

アサーションごとにexampleを書くのではなく、 _独立した_ アサーションを一つのexampleにまとめることで、セットアップのフックを一度だけ実行するようにできます。
これによりテストのパフォーマンスが劇的に向上します。(exampleの総数の減少によって

以下の例を考えます。

```ruby
# bad
it { is_expected.to be_success }
it { is_expected.to have_header("X-TOTAL-PAGES", 10) }
it { is_expected.to have_header("X-NEXT-PAGE", 2) }
its(:status) { is_expected.to eq(200) }

# good
it "returns the second page", :aggregate_failures do
  is_expected.to be_success
  is_expected.to have_header("X-TOTAL-PAGES", 10)
  is_expected.to have_header("X-NEXT-PAGE", 2)
  expect(subject.status).to eq(200)
end
```

自動修正(auto-correct)時は通常、exampleに`:aggregate_failures`を自動的に追加します。
しかし、プロジェクト全体でそれがグローバルに有効になっている場合や、例えばファイルの場所などからメタデータを導出するように選択的に有効にしている場合は、 `AddAggregateFailuresMetadata`設定オプションを使用しているものの追加はしない可能性があります。

このCopは自動修正をサポートしているため、古いテストを自動的にリファクタリングすることが可能です！

**補足**: ここで使用されている`its`exampleはRSpec3以降では非推奨になっていますが、[rspec-its gem](https://github.com/rspec/rspec-its)を使用しているユーザーは、このCopを活用してその依存を削減できます。

**補足**: `change`などのブロックマッチャーを使ったexampleの自動修正は、意図的にサポートされていません。
