# はじめに

## インストール

`test-prof` gem をアプリケーションに追加してください。

```ruby
group :test do
  gem "test-prof", "~> 1.0"
end
```

これだけで完了です。TestProfを使用する準備ができました！ [プロファイル](/#profilers).

## 設定

TestProf には、全ツールで使用されるグローバル設定がいくつかあります。

```ruby
TestProf.configure do |config|
  #　レポートなどを保存するフォルダー (デフォルトは'tmp/test_prof')
  config.output_dir = "tmp/test_prof"

  # レポートに対し一意なファイル名を付与する(単に、現在のタイムスタンプを追加する)
  config.timestamps = true

  # 色付きで出力する
  config.color = true

  # ログの出力先 (デフォルト)
  config.output = $stdout

  # あるいは、カスタムのロガーインスタンスを指定することもできます
  config.logger = MyLogger.new
end
```

また、「`TEST_PROF_REPORT`」という環境変数を使用して、レポート名に識別子を追加することができます。  
これは、異なるセットアップ間でレポートを比較したい場合に役立ちます。

**例：** `bootsnap`を使う場合と使わない場合のロード時間を[`stackprof`](./profilers/stack_prof.md)で比較してみましょう。

```sh
# 一番目のレポートに、接頭語「-with-bootsnap」を付けます
$ TEST_STACK_PROF=boot TEST_PROF_REPORT=with-bootsnap bundle exec rake
$ #=> StackProf report generated: tmp/test_prof/stack-prof-report-wall-raw-boot-with-bootsnap.dump


# bootsnapを無効にし、二番目のレポートを作成したい
# Assume that you disabled bootsnap and want to generate a new report
$ TEST_STACK_PROF=boot TEST_PROF_REPORT=no-bootsnap bundle exec rake
$ #=> StackProf report generated: tmp/test_prof/stack-prof-report-wall-raw-boot-no-bootsnap.dump
```

これで、分かりやすい名前のレポートが二つできました。
