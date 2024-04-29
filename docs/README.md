[![Gem Version](https://badge.fury.io/rb/test-prof.svg)](https://rubygems.org/gems/test-prof) [![Build](https://github.com/test-prof/test-prof/workflows/Build/badge.svg)](https://github.com/test-prof/test-prof/actions)
[![JRuby Build](https://github.com/test-prof/test-prof/workflows/JRuby%20Build/badge.svg)](https://github.com/test-prof/test-prof/actions)

# TestProf

> Ruby テストのプロファイリングと最適化のためのツールボックス

<img align="right" height="150" width="129"
     title="TestProf logo" class="home-logo" src="/assets/images/logo.svg">

TestProf はテストスイートの性能を分析するための様々なツールの詰め合わせです。

どうしてテストスイートの性能が重要なのでしょうか？  
第一に、テストは開発者のためのフィードバックループの一部です。(参考: [@searls](https://github.com/searls) [talk](https://vimeo.com/145917204))  
そして第二に、テストはデプロイサイクルの一部でもあります。  

はっきりと言うと、遅いテストは時間の無駄であり、あなたの活動を非生産的なものにします。

TestProf ツールボックスは以下のようなツールを備えており、テストスイートに含まれるボトルネックを特定するのに役立つでしょう。

- 一般的な Ruby プロファイラのための プラグ・アンド・プレイ ([`ruby-prof`](https://github.com/ruby-prof/ruby-prof), [`stackprof`](https://github.com/tmm1/stackprof))

- Factory の使用状況についての分析器とプロファイラ

- ActiveSupport を活用したプロファイラ

- より速いテストを書くための、RSpec と minitest のための[ヘルパー](#recipes)

- RuboCop で使える Cop (検査ルール)

- その他色々

📑 [ドキュメント](https://test-prof.evilmartians.io)

<p align="center">
  <a href="http://bit.ly/test-prof-map-v1">
    <img src="/assets/images/coggle.png" alt="TestProf map" width="738">
  </a>
</p>

<p align="center">
  <a href="https://evilmartians.com/?utm_source=test-prof">
    <img src="https://evilmartians.com/badges/sponsored-by-evil-martians.svg"
         alt="Sponsored by Evil Martians" width="236" height="54">
  </a>
</p>

## TestProf を使用するチーム

- [Discourse](https://github.com/discourse/discourse) は [~27% 程度テストスイートの実行時間を削減した](https://twitter.com/samsaffron/status/1125602558024699904)
- [Gitlab](https://gitlab.com/gitlab-org/gitlab-ce) は [39% API テストの実行時間を削減した](https://gitlab.com/gitlab-org/gitlab-ce/merge_requests/14370)
- [CodeTriage](https://github.com/codetriage/codetriage)
- [Dev.to](https://github.com/thepracticaldev/dev.to)
- [Open Project](https://github.com/opf/openproject)
- [その他色々...](https://github.com/test-prof/test-prof/issues/73)

## 情報リソース

- [TestProf: a good doctor for slow Ruby tests](https://evilmartians.com/chronicles/testprof-a-good-doctor-for-slow-ruby-tests)

- [TestProf II: factory therapy for your Ruby tests](https://evilmartians.com/chronicles/testprof-2-factory-therapy-for-your-ruby-tests-rspec-minitest)

- [TestProf III: guided and automated Ruby test profiling](https://evilmartians.com/chronicles/test-prof-3-guided-and-automated-ruby-test-profiling)

- [Rails Testing on Rocket Fuel: How we made our tests 5x faster](https://www.zerogravity.co.uk/blog/ruby-on-rails-slow-tests)

- Paris.rb, 2018, "99 Problems of Slow Tests" talk [[ビデオ](https://www.youtube.com/watch?v=eDMZS_fkRtk), [スライド](https://speakerdeck.com/palkan/paris-dot-rb-2018-99-problems-of-slow-tests)]

- BalkanRuby, 2018, "Take your slow tests to the doctor" talk [[ビデオ](https://www.youtube.com/watch?v=rOcrme82vC8), [スライド](https://speakerdeck.com/palkan/balkanruby-2018-take-your-slow-tests-to-the-doctor)]

- RailsClub, Moscow, 2017, "Faster Tests" talk [[ビデオ](https://www.youtube.com/watch?v=8S7oHjEiVzs) (ロシア語), [スライド](https://speakerdeck.com/palkan/railsclub-moscow-2017-faster-tests)]

- RubyConfBy, 2017, "Run Test Run" talk [[ビデオ](https://www.youtube.com/watch?v=q52n4p0wkIs), [スライド](https://speakerdeck.com/palkan/rubyconfby-minsk-2017-run-test-run)]

- [Tips to improve speed of your test suite](https://medium.com/appaloosa-store-engineering/tips-to-improve-speed-of-your-test-suite-8418b485205c) by [Benoit Tigeot](https://github.com/benoittgt)

## インストール

`test-prof` gem をアプリケーションに追加してください。

```ruby
group :test do
  gem "test-prof", "~> 1.0"
end
```

これだけで完了です。

サポートされるRuby バージョン:

- Ruby (MRI) >= 2.5.0 (**注意:** Ruby 2.2 では TestProf < 0.7.0, Ruby 2.3 では TestProf ~> 0.7.0, Ruby 2.4 では TestProf <0.12.0 を使用してください)

- JRuby >= 9.1.0.0 (**注意:** refinements に依存する機能は 9.2.7+ を必要とする場合があります)

サポートされる RSpec のバージョン (RSpec に関する機能のみ): >= 3.5.0 (より古いバージョンの RSpec に対しては TestProf < 0.8.0 を使用してください)

サポートされる Rails のバージョン (Ralis に関する機能のみ): >= 5.2.0 (より古いバージョンの Rails に対しては TestProf < 1.0 を使用してください)

### RuboCop RSpec によるリント

rubocop-rspec を用いて RSpecファイル を静的解析すると、TestProfが定義している`let_it_be`と`before_all`とった RSpec 用のコンストラクトを正しく検出できないことがあります。

バージョン2.0 以降の `rubocop-rspec` が使用されていることを確認の上、`.rubocop.yml`に以下の記述を追加してください。

```yaml
inherit_gem:
  test-prof: config/rubocop-rspec.yml
```

## プロファイラ

- [RubyProf Integration](./profilers/ruby_prof.md)

- [StackProf Integration](./profilers/stack_prof.md)

- [Event Profiler](./profilers/event_prof.md) (ActiveSupport notifications など)

- [Tag Profiler](./profilers/tag_prof.md)

- [Factory Doctor](./profilers/factory_doctor.md)

- [Factory Profiler](./profilers/factory_prof.md)

- [RSpecDissect Profiler](./profilers/rspec_dissect.md)

## レシピ

テストスイートの性能と効率を向上させるのに役立つ、ちょっとしたコードトリックを紹介します

- [`before_all` Hook](./recipes/before_all.md)

- [`let_it_be` Helper](./recipes/let_it_be.md)

- [AnyFixture](./recipes/any_fixture.md)

- [FactoryDefault](./recipes/factory_default.md)

- [FactoryAllStub](./recipes/factory_all_stub.md)

- [RSpec Stamp](./recipes/rspec_stamp.md)

- [Tests Sampling](./recipes/tests_sampling.md)

- [Active Record Shared Connection](./recipes/active_record_shared_connection.md)

- [Rails Logging](./recipes/logging.md)

## 他のツール

- [RuboCop cops](./misc/rubocop.md)

## 次は何？

良いアイデアあれば、 [このページ](https://github.com/test-prof/test-prof/discussions) から新しい機能を提案してください！

それとも、既に TestProf を使用しているなら、 [あなたの体験をシェアしてください！](https://github.com/test-prof/test-prof/discussions/73)

## ライセンス

この gem は [MIT License](http://opensource.org/licenses/MIT) のもと、オープンソースとして使用することができます。
