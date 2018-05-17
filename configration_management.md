# Why Drupal
Configuration Management

金子 智嗣
[@snize](https://twitter.com/snize)<br>
kaneko@zerobase.jp<br>


## Intro

- どちらかと言うとビジネス向けの話をします
- WEB系のエンジニアで、プロトタイピングから本番までのフロントエンドからバックエンドまで
- 案件によってはDrupalを採用
- エンジニア向けのデモも最後に共有します


### CMS変遷（なんとなく年代順）

Movable type, Wordpress, Nucleus, Geeklog, MODx, eZ Publish, concrete5, Drupal 7, Drupal 8

最近CMSはもっぱら **Drupal 8**



## 今日の本題

## **Configuration Management** at Drupal


Drupalでの構成管理？


### ソフトウェア構成管理

> ソフトウェア構成管理（ソフトウェアこうせいかんり、英: software configuration management、SCM）とはソフトウェア開発プロジェクトをその成果物を通して制御・管理する方法論である。**ソースコード**や文書**などの成果物の変更履歴を管理し**、製品のバージョンやリビジョンに個々の成果物のどのバージョンが対応しているかを識別し、**任意のバージョンの製品を再現可能とする。**

[ソフトウェア構成管理 - Wikipedia](https://ja.wikipedia.org/wiki/%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E6%A7%8B%E6%88%90%E7%AE%A1%E7%90%86)


> ソースコードなどの成果物の変更履歴を管理し、任意のバージョンの製品を再現可能とする。

**CMS周りでは聞かない...** なぜでしょう？


一般的にCMSの設定はDBへ格納されているから<br>**バージョン管理しづらい**からでは？


Drupal 8からは設定の<br>**インポート/エクスポートが標準でサポート**


他のソースコードと共に構成管理がしやすくなった

> ソースコードなどの成果物の変更履歴を管理し、任意のバージョンの製品を再現可能とする。


### そもそもConfiguration Managementすると<br>何が嬉しいのか

Configuration Managementしない場合に比べて...


##### ビジネス側の変更や要求に対して柔軟に対応できる

- gitのブランチごとに**平行開発**してマージも簡単
- **特定の時点に戻せる**からプロトタイピングしやすい
- 人間でも読めるから何が変更されたか**理解しやすい**


#### ビルド・デプロイが容易

- CIサーバやgit hookを利用して半自動化/完全自動化
- リリースに問題があった場合ロールバックしやすい


つまり、開発側が**本質的な作業に集中**できるので<br>
ビジネス的には**プロダクトの価値**を<br>ユーザに届けるまでの**距離が短くなる**


フルスクラッチでの開発に比べて<br>
**CMSを利用する理由**はなんですか？



### Drupal 8でのConfiguration Management


#### Drupal 8では何を管理するのか

- モジュールの有効・無効
- コンテンツタイプ
- ブロックのレイアウト
- 管理画面の`/admin/config`以下の設定とかいろいろ

**コンテンツ**は管理しない


#### どうやって管理するのか

データベースに格納された設定を**YAML**という形式の<br />ファイルに書き出してgitなどでバージョン管理する


サムネイル生成の設定サンプル

```
langcode: ja
status: true
dependencies: {  }
name: 1200x640
label: 1200x640
effects:
    id: image_scale
    weight: 1
    data:
      width: 1200
      height: 640
      upscale: false
```
普通に読める


### どんな時に始めるか

- もちろんどんなプロジェクトでも今すぐ始められる（本番運用しています）
- 納品したら終わり**では無く**自社案件やクライアントワークでも継続的にプロジェクトに関わる時（メリット大）



### 実例（本番運用している話）

- メディアサイト
- 1日数本の記事が更新される
- チーム構成：**オーナー / 開発・デザイン / 編集部**
- 週1回の定例会で開発やコンテンツの方針決め
- ローンチ後1年で本番環境へのデプロイ回数：**約60回**
  - 1デプロイ/週


### 定例会でよくある会話

オーナーOR編集部：新しい機能を追加してほしい<br>
開発：わかりました、このモジュールで対応しましょう


#### DrupalでConfiguration Managementしてたら

1. 開発環境でインストールと設定、設定の書き出し
2. 本番環境からステージング環境へ<br>コンテンツ（DB）のコピー
3. ステージング環境で**1.**で書き出した設定のインポート
4. テスト/確認
5. 本番環境へデプロイ


#### Configuration Managementしてない<br>または他のCMSだったら

1. 開発環境でインストールと設定
2. コンテンツの更新を停止<!-- .element: class="fragment highlight-red" data-fragment-index="1" -->
2. 本番環境からステージング環境へDBのコピー
3. ステージング環境で開発環境と同じ設定を手作業で設定<!-- .element: class="fragment highlight-red" data-fragment-index="1" -->
（開発環境のデバッグ設定などをコピーしないため）
5. 問題なければ、DBを本番環境へコピー
6. コンテンツの更新を再開<!-- .element: class="fragment highlight-red" data-fragment-index="1" -->


#### なにが問題か

- 手間が多く、手作業によるミスの発生の可能性が大きい
  - 不安を抱えながらのデプロイ?
- デプロイ作業のため更新を止める必要がある
  - いつデプロイする？夜中？
- これらの作業中に割り込みのタスクが発生したら、対応可能？

開発にとって、本質的で価値のある作業の阻害要因になる<br>
<!-- .element: class="fragment" data-fragment-index="1" -->
**ビジネス的には足枷になりうる**
<!-- .element: class="fragment" data-fragment-index="2" -->


## ということで、本日のWhy Drupalは
Configuration Management!



## 以上です、ありがとうございました<br /> (｡･ω･｡)ﾉ


#### 本日のスライド

- [Why Drupal - Configuration Management](https://github.com/snize/why_drupal_Configration_Management)

#### 過去のスライドとデモ

- [Configuration Split Demo](https://github.com/snize/demo-config-split): Core CMで足りないところの話とデモ
- [Talking with Drupal](https://github.com/snize/demo_drupal-psysh): psyshでdrupalと対話的に

#### コンタクト

- [@snize](https://twitter.com/snize)
- kaneko@zerobase.jp