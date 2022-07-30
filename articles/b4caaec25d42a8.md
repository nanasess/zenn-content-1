---
title: "MongoDB Atlasの無料クラスタでMongoDBサーバーを使ってみる"
emoji: "💻"
type: "tech"
topics: ["mongodb"]
published: true
published_at: 2020-05-27
---

:::message
この記事は、2020-05-27に別のブログ媒体に投稿した記事のアーカイブです。
:::

# MongoDB Atlasとは

[MongoDB Atlas](https://www.mongodb.com/cloud/atlas) は、MongoDB社が提供しているMongoDBのDBaaSです。

[料金プラン](https://www.mongodb.com/cloud/atlas/pricing) が従量制でパッと分かりづらいですが、 `M0` という種類のクラスタを選択すれば無料で利用することもできます。

以下、無料で使い始めるまでの具体的な手順を解説していきます✋

# 1. 無料のクラスタを作成する

まずは [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) のサイトの `Start free` ボタンからユーザー登録をしてください。

ログインできたら、クラスタ一覧画面で `Build a Cluster` をクリックして最初のクラスタを作成します。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf65qq2blsj315f0u0wo5.jpg)

無料で使い始める場合は、 `Shared Clusters` の `Create a cluster` をクリックします。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf65sgfxg8j315f0u04e9.jpg)

クラウドサービスとリージョンを選択できるので、例えばAWSのシンガポールリージョンを選んでみます。

`Cluster Tier` で選択できる `M0 Sandbox` が無料のクラスタです。（ `M0` クラスタはユーザーあたり1つだけ作成可能です）

`Cluster Name` （クラスタ名）は後から変更ができないので注意してください。

`Create Cluster` をクリックするとクラスタの作成が始まります。（3分ぐらいかかります）

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf65uy8b1xj30u00xawtv.jpg)

無事、クラスタが作成されました！

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf662gv9dnj31330u07wi.jpg)

# 2. DBユーザーを作る

MongoDBを利用するためのDBユーザーを作成します。

サイドメニューの `Database Access` からDBユーザー一覧画面へ移動して、 `Add New Database User` をクリックします。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf665brp6yj316j0u0n6s.jpg)

ユーザー名とパスワードを適切に設定して `Add User` をクリックします。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf67boscxej30u014vjxx.jpg)

以下のようなアラートが表示されてユーザー情報の更新が始まるので、完了してアラートが消えるまで1〜2分待ちます。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66alsh7yj31370u0n6r.jpg)

# 3. IPアドレスホワイトリストを設定する

MongoDB AtlasではIPアドレスのホワイトリストで外部ホストからの接続をコントロールします。

最初はリストが空でどこからも接続できない状態になっているので、まずはホワイトリストを登録しましょう。

サイドバーの `Network Access` でIPホワイトリスト一覧画面を開いて、 `Add IP Address` をクリックします。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66khhvqcj319j0u0gum.jpg)

以下のような画面が開くので、許可したいIPアドレスを記入して `Confirm` をクリックします。

`ADD CURRENT IP ADDRESS` をクリックすると現在ダッシュボードを閲覧しているローカルマシンのIPアドレスが入力されます。今回は後ほどローカルマシンからCLIで接続確認をしますので、これを登録しておきましょう。

> `ALLOW ACCESS FROM ANYWHERE` をクリックすると `0.0.0.0/0` が入力されてどこからでもアクセスできるように設定できますが、安全性を考えるならあまり使わないほうがよいでしょう。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66oth69tj312k0niq5p.jpg)

例によって、青いアラートが消えて `Status` が `Active` に変わるまで待ちます。（1分ぐらい）

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66q4a1g3j319j0u0wnw.jpg)

# 4. DBへの接続情報を確認する

実際に利用するにあたり、MongoDBクラスタに接続するための情報を確認しておきましょう。

ダッシュボードのクラスタ一覧に戻って、クラスタの `CONNECT` ボタンをクリックしてください。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66gxn91tj31370u0amk.jpg)

DBユーザーの作成とIPホワイトリストの設定が済んでいれば、以下のような画面が表示されます。

今回はCLIで接続確認をしようと思いますので、 `Connect with the mongo shell` を選択してください。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66swm8w2j30yh0u0tdc.jpg)

Macから接続している場合は以下のような画面が表示されるかと思います。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gf66wi03r8j30u0113wkj.jpg)

Homebrewで `mongo` コマンドをインストールする方法と、それを使ってクラスタに接続するためのワンライナーが表示されています。

実際に試してみましょう！

# 5. CLIで接続確認

先ほどの画面に表示されていたとおり、Macの場合は以下のようにHomebrewでインストールできます。

```bash
$ brew install mongodb/brew/mongodb-community-shell

# または
$ brew tap mongodb/brew
$ brew install mongodb-community
```

インストールできたら、先ほどの画面に表示されていたワンライナーをコピーして実行してみましょう。

```bash
$ mongo "mongodb+srv://{クラスタのホスト名}.mongodb.net/test" --username {DBユーザーのユーザー名}
```

パスワードの入力を求められるので、DBユーザーのパスワードを入力します。

```
MongoDB Enterprise {クラスタ名-shard-0}:PRIMARY>
```

こんな感じのプロンプトが表示されたら接続成功です！

## CLIからの接続ではパスワードはURLエンコード不要

ちなみに、先ほどの画面に

> You will be prompted for the password for the MongoDB user, {username}. When entering your password, make sure all special characters are URL encoded.

こんな表記がありましたが、実際に使ってみたところ、CLIからの接続の場合はパスワードはURLエンコード不要なようです🤔（Webアプリなどから `mongodb+srv://` で始まるURIを使って接続する場合はユーザー名・パスワードともにURLエンコードが必要）

## MongoDBの中身を一旦全削除したい！というときは

CLIでMongoDBに接続した上で

```bash
> db.dropDatabase();
```

を実行すればDBの中身を全削除できます。（[参考](https://stackoverflow.com/questions/3366397/delete-everything-in-a-mongodb-database)）

# 参考リンク

MongoDB Atlasを使い始める (MongoDB as a Service) - Qiita  
<https://qiita.com/nacam403/items/08c18a8234c82e2b304e>
