このガイドでは、Ember 使って、シンプルなアプリケーションを0から構築する方法を紹介します。

次の手順について、説明しています。

  1. Ember のインストール
  2. 新しいアプリケーションの作成。
  3. ルートの定義。
  4. UI コンポーネントの記述
  5. あなたのアプリケーションをプロダクションにデプロイするためのビルド。

## Ember のインストール

Emberは Node.jsのパッケージマネージャー npmを使えば、コマンドを一つ実行するだけでのインストールすることができます。次のコマンドをターミナルで実行してください。

```sh
npm install -g ember-cli
```

npm がないですか?[このリンク先から、Node.js とnpmをインストールできます](https://docs.npmjs.com/getting-started/installing-node)。.

## 新しいアプリケーションの作成

npm で Ember をインストールしたら、ターミナルで実行可能な新しい`ember` コマンドが利用できます。`ember new` コマンドを実行すると、新しいEmber アプリケーションが作成されます。

```sh
ember new ember-quickstart
```

このコマンドで新しい、 `ember-quickstart`というディレクトリが作成され、内部に新しい Ember アプリケーションが準備されます。あなたのアプリケーションにはすでに、次のものが含まれています。

* 開発用サーバー
* テンプレートの編集
* JavaScript と CSS の圧縮
* Babelを介したES2015 の機能

Emberは、あなたのWebアプリケーションがプロダクションに必要なものを全て統合されたパッケージにすることで、新規のプロジェクトを始めるのを爽やかなものにします。

すべてが正常に動作することを確認しましょう。ターミナルで`cd` を実行してにアプリケーション のディレクトリ `ember-quickstart` に移動して、開発用サーバーを起動するために、次のコードを実行します。

```sh
cd ember-quickstart
ember server
```

数秒すると、次のような出力が表示されます。

```text
Livereload server on http://localhost:49152
Serving on http://localhost:4200/
```

(いつでもサーバーを停止するには、ターミナルで Ctrl-C と入力してください)。

好みのブラウザーで [`http://localhost:4200`](http://localhost:4200) を開けてください。 Ember welcome page が見えるはずです。 おめでとうございます ！ あなたは初めての Ember アプリケーションを作成して、起動したのです。

`ember generate` コマンドを利用して新しいtemplate (テンプレート)作成しましょう。

```sh
ember generate template application
```

アプリケーションを読み込むと、スクリーンにはいつも、`application` template (テンプレート)があります。エディターで`app/templates/application.hbs` を開いて、次のコードを追加してください:

```app/templates/application.hbs 

## PeopleTracker

{{outlet}}

    <br />Ember は新しいファイルを検知して、自動的にバックグラウンドでページを再読み込んだはずです。 welcome pageが"PeopleTracker"に置き換わったのが確認できるはずです。
    
    ## Route (ルータ)の定義
    
    科学者のリストを表示するアプリケーションを作っていきましょう。 そのためには、まず、route (ルート)を作成する必要があります。 当面は、ルートはアプリケーションを構成する別のページとしての考えるといいでしょう。
    
    Ember はボイラープレートコードを自動生成する、_generators_ (ジェネレータ)があります。 To generate a route, type this in your terminal:
    
    ```sh
    ember generate route scientists
    

次のような出力が表示されるはずです。

```text
installing route
  create app/routes/scientists.js
  create app/templates/scientists.hbs
updating router
  add route scientists
installing route-test
  create tests/unit/routes/scientists-test.js
```

Ember が次のことを行ったことを意味しています。

  1. ユーザーが`/scientists`を訪れた時に表示されるテンプレート.
  2. テンプレートがモデルを取得する際に利用する`ルート`
  3. アプリケーションが利用するルーターのエントリ(`App/router.js` に存在する).
  4. このルートのためのユニットテスト

`app/templates/scientists.hbs` を開いて、以下のHTMLを追加してください。

```app/templates/scientists.hbs 

## List of Scientists

    <br />ブラウザで[`http://localhost:4200/scientists`](http://localhost:4200/scientists)を開けてください。 `application.hbs`の`<h2>`直下に、`scientists.hbs`テンプレートに追加した、`<h2>`が確認できるはずです。
    
    `scientists`テンプテートのレンダリングが出来たので、描画するためのデータを与えましょう。 そのために、`app/routes/scientists.js`を編集してルートのための_model_を特定します。
    
    ジェネレーターによって、生成されたコードに `model()` メソッドを `Route` に追加します。
    
    ```app/routes/scientists.js{+4,+5,+6}
    import Ember from 'ember';
    
    export default Ember.Route.extend({
      model() {
        return ['Marie Curie', 'Mae Jemison', 'Albert Hofmann'];
      }
    });
    
    
    

(このサンプルでは、最新のJavaScriptの機能を使っているので、そしかすると馴染みがないかもしれません、 [JavaScript の最新の機能の概要](https://ponyfoo.com/articles/es6) からもっと情報を得ることができます。.)

ルートの `model()` メソッドでは、テンプレートで利用する あらゆるデータを返すことができます。 非同期でデータを取得したい場合`model()` メソッドは、[JavaScript Promises](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)をサポートするあらゆるライブラリーを利用することが可能です。.

次は Ember に文字列の配列をどのようにHTMLに変換できか、`scientists`テンプレートを開いて、Handlebarsコードを追加することで、配列をを出力できるよう指示しましょう。

```app/templates/scientists.hbs{+3,+4,+5,+6,+7} 

## List of Scientists

{{#each model as |scientist|}} 

* {{scientist}} {{/each}} 

    <br />ここでは、`model()` から取り出した、配列の各要素を`<li>`内のエレメントとして、出力するために`each`ヘルバーを使っています。
    
    ## UI コンポーネントの記述
    
    アプリケーションが大きくなるにつれ、同じUIエレメントを複数のパージで利用していることに気がつくでしょう ( あるいは、同一のページで複数回使っているということもあるでしょう)、Ember は再利用可能なコンポーネントにリファクタリングすることを、簡単にしています。
    
    人のリストを複数の場所で利用できる、コンポーネント`people-list`を作成してみましょう。
    
    いつものように、私たちにはこの作業を簡単にしてくれる、ジェネレーターがあります。 次のように入力して、新しいコンポーネントを作ります。
    
    ```sh 
    ember generate component people-list
    

`scientists` テンプレートをコピーして `people-list`にペーストし次のように、編集します。

```app/templates/components/people-list.hbs 

## {{title}}

{{#each people as |person|}} 

* {{person}} {{/each}} 

    <br />ハードコーディングされた、 ("List of Scientists") から動的プロパティ (`{{title}}`) に変更していることに注意してください。 また、それまで利用していた`scientist`をもっと一般化された、`person`に名称を変更しています。
    
    このテンプレートを保存したら、`scientists`テンプレートに戻ります。 古いコードを全て、新しくコンポーネント化したコードと置き換えます。 コンポーネントはHTMLタグのように見えるが、ブラケット(`<tag>`) の代わりに、(`{{component}}`)のように二重の中括弧-ダブルカリーブレースを利用している。 コンポーネントに次の指示を与える。
    
    1. titleには'title' 属性の値を使う。
    2. どの人々の配列を`people`属性として利用するか。 このルート`model`を人々のリストとして提供します。
    
    ```app/templates/scientists.hbs{-1,-2,-3,-4,-5,-6,-7,+8} 
    <h2>List of Scientists</h2> 
    
    <ul>
       {{#each model as |scientist|}}
         <li>{{scientist}}</li>
       {{/each}} 
    </ul> 
    {{people-list title="List of Scientists" people=model}}
    

ブラウザーでページをリロードしても、UI が変わっていないことが確認できるはずです。唯一の違いは、コンポーネント化リストにしたことにより、より再利用が可能で保守性の高いやバージョン利用していることです。

この挙動は、違う人々のリストを利用する、ルートを作成することで、確認が可能です。 例題として、著名なプログラマを表示する`programmers`ルートを作成することができます。 `people-list`コンポーネントを利用することで、例題をほぼコードを書かずに解くことができるでしょう。

## プロダクションへビルド

それでは、アプリケーションが開発環境で機能することが確認できたので、いよいよユーザーに向けて、デプロイする時がきました。そのためには次のコマンドを実行してください。

```sh
ember build --env production
```

`build` コマンドは、すべてのあなたのアプリケーションを構成する多くの&mdash;JavaScript、テンプレート、 CSS、 web fontsイメージなどすべての資産をパッケージにします。

今回は Ember に`--env`フラグの元、プロダクション環境を構築するように指示しました。 こうすることで、Webホストにアップロードするために最適化された、バンドルとして作成することが可能です。 ビルドが完了すると連結され、ミニファイドされた、アセットが`dist/`ディレクトリ配下されたアセットが確認できます。

Ember コミュニティはコラボレーションと、すべての人が共通で利用できるツールを作ることを高く評価します。 もし、高速で信頼性の高い方法でアプリケーションをプロダクションに載せたいと持っているなら、[Ember CLI Deploy](http://ember-cli-deploy.com/) addon (アドオン)を確認するといいでしょう。

Apache web server がデプロイ対象の場合は、まずApacheでアプリケーションのためにvirtual host を設定します。 すべてのroutes (ルート)が確実にindex.htmlによって処理されるために、アプリケーションのvirtual host設定に次の設定を追加してください。

    FallbackResource index.html