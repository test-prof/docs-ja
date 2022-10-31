# 入門

## 前提条件

対応しているRubyのバージョン

* Ruby (MRI) >= 2.5.0
   * Ruby 2.2の場合は、TestProf < 0.7.0、
   * Ruby 2.3の場合は、TestProf ~> 0.7.0、
   * Ruby 2.4の場合は、TestProf < 0.12.0 を使用してください。

* JRuby >= 9.1.0.0（一部のツールはバージョン 9.2.7+ が必要）

RSpecの場合は、対応のバージョンは >=3.5.0 です。もっと古いRSpecには TestProf < 0.8.0 が必要です。

## インストール

ジェム「`test-prof`」を追加してください。

```ruby
group :test do
  gem "test-prof"
end
```

これでインストールが終わりです!

## 設定

TestProfは、全ツールで使用されるいくつかのグローバル設定があります。

```ruby
TestProf.configure do |config|
  #　レポートなどを保存するフォルダー (デフォルトは'tmp/test_prof')
  config.output_dir = "tmp/test_prof"

  # レポートファイルの名前ににタイムスタンプを付ける
  config.timestamps = true

  # 出力をハイライトする
  config.color = true

  # ログ出力の宛先（ファイルまたはSTDOUT）
  config.output = $stdout

  # カスタムのロガーインスタンスを指定することもできます
  config.logger = MyLogger.new
end
```

### レポート区別用の識別子

また、「`TEST_PROF_REPORT`」という環境変数を使用して、レポート名に識別子を追加することができます。これは、異なるセットアップでのレポートを比較したい場合に便利です。

**例：** `bootsnap`を使う場合と使わない場合のロード時間を[`stackprof`](./profilers/stack_prof.md)で比較してみましょう。

```sh
# 一番目のレポートに、「-with-bootsnap」を付けます
$ TEST_STACK_PROF=boot TEST_PROF_REPORT=with-bootsnap bundle exec rake
$ #=> StackProf report generated: tmp/test_prof/stack-prof-report-wall-raw-boot-with-bootsnap.dump

# 二番目のレポートは、bootnapを無効にし、名前に「-no-bootsnap」を付けて作成します
$ TEST_STACK_PROF=boot TEST_PROF_REPORT=no-bootsnap bundle exec rake
$ #=> StackProf report generated: tmp/test_prof/stack-prof-report-wall-raw-boot-no-bootsnap.dump
```

これで、分かりやすい名前の2つのレポートができました。
