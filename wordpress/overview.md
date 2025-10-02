---
title: WordPress メモ
lang: ja
---

# WordPress

## 投稿REST APIの有効化
httpの場合、ユーザー管理画面のアプリケーションパスワードが有効にならない

```php
define( 'WP_ENVIRONMENT_TYPE', 'development' );
```
をwp-config.phpに入れればよいとあるが、有効にならない.  
function.phpに以下を入れる

```php
// api使用のためにアプリケーションパスワードを強制表示
add_filter( 'wp_is_application_passwords_available', '__return_true' );
```

## GETのREST APIが効かない
wp-json/wp/v2/media/のAPIは、パーマリンク設定がデフォルトの`/?p=123`になっていると呼べない？変えると呼び出せた. 


## サイトのリセット
データベースからwp関連のテーブルをごっそり消せばいいらしいが、「WPRESET」プラグインでやるのが楽. 

## ページごとのアクセス数取得
プラグインなしで自作する場合、カスタムフィールドにアクセス数カラムを追加して、記事が表示されるごとにそのカラムをカウントアップする.  
1. function.phpに以下を追加

```php
function set_post_views($post_id) {
    $custom_key = 'post_views_count';
    $view_count = get_post_meta($post_id, $custom_key, true);
    if ($view_count === '') {
        delete_post_meta($post_id, $custom_key);
        add_post_meta($post_id, $custom_key, '0');
    } else {
        $view_count++;
        update_post_meta($post_id, $custom_key, $view_count);
    }
}
```

2. single.phpに以下を追加

```php
<?php
if ((!is_user_logged_in() || !current_user_can('administrator')) && !is_robots()) {
    setPostViews(get_the_ID());
}
?>
```

## 投稿一覧、固定ページ一覧に最終更新日を追加

function.phpに以下を追加

```php
/* 投稿一覧、固定ページ一覧に最終更新日を追加 */
function add_posts_columns_last_modified( $columns ) {
    $columns[ 'modified-last' ] = '最終更新日' ;
    return $columns ;
}
add_filter( 'manage_posts_columns', 'add_posts_columns_last_modified' ) ;
add_filter( 'manage_pages_columns', 'add_posts_columns_last_modified' ) ;

// 最終更新日を表示
function custom_posts_columns_last_modified( $column_name, $post_id ){
 
    if( 'modified-last' != $column_name ){
        return ;
    }
    $modified_date   = the_modified_date( 'Y年Md日 Ag:i' ) ;
    echo $modified_date ;
}
add_action( 'manage_posts_custom_column', 'custom_posts_columns_last_modified', 10, 2 ) ;
add_action( 'manage_pages_custom_column', 'custom_posts_columns_last_modified', 10, 2 );

// ソートできるようにする
function sort_columns_last_modified( $columns ){
    $columns['modified-last'] = 'modified' ;
    return $columns ;
}
add_filter( 'manage_edit-post_sortable_columns', 'sort_columns_last_modified' ) ;
add_filter( 'manage_edit-page_sortable_columns', 'sort_columns_last_modified' ) ;
```

## Migrate

自サイトをラッコM&Aで売却できたときにやったことのメモ

### サイトに残っている個人情報

- 設定 - 一般 - 管理者メールアドレス（https://my-site.com/wp-admin/options.php のadmin_emailも）
- UpdraftプラグインでDropboxにバックアップコピーしていたので、バックアップ先の変更とDropboxの認証情報の削除
- （多分いらない）Site Kit by Googleプラグインでアナリティクスとサーチコンソールの接続をしているので削除
- 問い合わせページにGoogleフォームを使っているので削除
- 日本ブログ村とブログランキングへのリンクを削除（外観 - ウィジェット - デフォルトサイドバー）

### 移行先で必要なこと

- 証明書の設定
- （多分いらない）Googleアナリティクスとサーチコンソール
- 問い合わせページの作成

### データのエクスポート

- WP標準機能で記事・画像をエクスポート（ツール - エクスポート）...この方法だとアイキャッチが引き継がれないというネット記事があるが今回インポートは移行先の方がやるのでうまくいくか不明

### テーマとプラグインのエクスポート

- テーマファイルをプラグイン「Download Plugins and Themes from Dashboard」でエクスポート（プラグインの有効化のところにzip download）したが、実際はUpdraftバックアップで取得したものや、wordPressのプラグインフォルダ、テーマフォルダまるごとでもよいはず。ただし、テーマやプラグインを常に最新にしていない場合は、新側のワードプレスのバージョンと合わないので、結局、新側でポチポチすることになりそう。

### テーマの細かい設定

- 今回のサイトはOceanWPをテーマに使っていたが、テーマの設定（外観 - カスタマイズ）はデータのエクスポートで入らなかったので、新側でポチポチ設定した

 
### Googleアナリティクスとサーチコンソール

- アナリティクスはアナリティクスとしてのアカウントを追加し、そのアカウント権限を移行先の方のメールアドレスを追加して公開する
- 移行するサイトのプロパティをアナリティクス上で[プロパティの移行](https://support.google.com/analytics/answer/6370521?hl=ja&ref_topic=1009620#zippy=%2C%E3%81%93%E3%81%AE%E8%A8%98%E4%BA%8B%E3%81%AE%E5%86%85%E5%AE%B9) を行う。移行先で確認後、移行先で移行元（私）のアカウントを削除してしまえばok
- サーチコンソールも同様に、サーチコンソール上で移行先ユーザをオーナーとして追加し、確認できれば移行先で移行元（私）のアカウントを削除する

### ドメインの移管

- Route53では[ココ](https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/domain-transfer-from-route-53.html)に手順がある
- 今回の相手先はムームードメインだった
- Route53側は↑の手順7で認証コードを発行し伝えたのと、最後にホストゾーンを削除する作業のみ
- 認証コード発行後、相手先で受け入れたら、1週間後にドメインが移管される旨のメールが来るので、それを待つのみ
- ムームー側がどういう作業かわからないが、移管後はRoute53側のホストゾーンが残っていても、ドメインは新しい方のIPをさしていた（`dig`コマンド）
 
### WP Offload Mediaの解除

WP Offload Mediaプラグインで静的コンテンツをCDNしているのでローカルに移した

- `Remove Files From Server`設定をオンにしていなければローカルにコンテンツが残っているのでプラグインの無効化で記事のパスは自動的に戻り、ローカル参照になる
- /opt/bitnami/apps/wordpress/htdocs/wp-config.php にS3への認証情報が入っているので削除する
- モバイルのロゴだけCloudFrontを見に行っていたので貼り直す
- [ココ](https://deliciousbrains.com/wp-offload-media/doc/uninstall/)にデータベースの中の後始末をしろと書いてあるので一応やる

#### mysqlで実行

mysql cliの起動

```bash
mysql -u root -p[password] [DB_NAME]
```
-pのあとにスペースも何も入れずにパスワードを記入する.  
-pでEnterするとパスワードを聞いてきて、そこでパスワードを入れてもaccess deniedとなるクソ仕様...

実行したsql  

```sql
show tables;	//テーブル一覧表示
rename table wp_as3cf_items to bk_20221203_wp_as3cf_items;
create table  bk_20221203_wp_options_delete_rec as select * FROM wp_options WHERE option_name = 'tantan_wordpress_s3' OR option_name LIKE 'as3cf_%';	//3 rows
delete FROM wp_options WHERE option_name = 'tantan_wordpress_s3' OR option_name LIKE 'as3cf_%';
create table bk_20221203_wp_postmeta_delete_rec as  select * FROM wp_postmeta WHERE meta_key LIKE 'amazonS3_%' OR meta_key LIKE 'as3cf_%' OR meta_key LIKE 'wpos3_%';	//37 rows
delete FROM wp_postmeta WHERE meta_key LIKE 'amazonS3_%' OR meta_key LIKE 'as3cf_%' OR meta_key LIKE 'wpos3_%';
create table bk_20221203_wp_options_2_delete_rec as select * FROM wp_options WHERE option_name LIKE '%transient_as3cf%' OR option_name LIKE '%transient_timeout_as3cf%';	//6 rows
delete FROM wp_options WHERE option_name LIKE '%transient_as3cf%' OR option_name LIKE '%transient_timeout_as3cf%';
```
※wp_usermetaに条件に一致するレコードなし

## ウィルス対応

### WordPressでindex.phpと.htaccessが書き換えられるウィルスの対応

#### 事象

[これ](https://wp-doctor.jp/blog/2021/07/28/%E3%83%AF%E3%83%BC%E3%83%89%E3%83%97%E3%83%AC%E3%82%B9%E3%81%A7%E4%B8%80%E7%9E%AC%E3%81%A7%E5%86%8D%E5%BA%A6%E6%94%B9%E3%81%96%E3%82%93%E3%81%95%E3%82%8C%E3%82%8Bhtaccess%E3%83%95%E3%82%A1%E3%82%A4/)
とか
[これ](https://stackoverflow.com/questions/64673952/wordpress-keeps-creating-index-php-and-htaccess-files-and-changes-permission-to)
と同じ事象。

1つ目でそのサイトが提供しているファイルをアップロードすれば対応できる、となっているが、さくらのレンタルサーバの環境では対応できないため、個別に行う。

#### 対処方法

前提として、このウィルスはなにかをトリガーにして、トップディレクトリの.htaccessとindex.phpを書き換える。  
厳密にいうとこの2ファイルを削除して再作成する。  
なので、ファイル属性を読み取り専用にしてもダメ。読み取り専用でも削除はできるから。  
削除されるのを防ぐには、そのファイルが含まれるディレクトリの書き込み属性を剥奪する必要がある。  

また、.htaccessを削除してもすぐに再作成されるので、

なので手順としては、
.htaccessの削除、index.phpの削除、ディレクトリの書き込み権限変更の3行のスクリプトを作って実行する。  

そして、正しい.htaccess、index2.phpを保存する。

- .htaccess

```txt
# BEGIN WordPress
RewriteEngine On
RewriteBase /
RewriteRule ^index2\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index2.php [L]
RewriteCond %{REQUEST_URI} !^/index2.php$
RewriteRule ^$ /index2.php [L]
# END WordPress
```

- index2.php

```php
<?php define( 'WP_USE_THEMES', true );require __DIR__ . '/wp-blog-header.php';
```
index**2**.phpとするのは、index.phpとするとまた書き換えられてしまうため。

このようにすると、https://[ドメイン]/index2.phpでアクセスできるようになり、https://[ドメイン]はindex2.phpにリダイレクトされるから、修正できる。

その後、他の書き換えられたファイルを戻すためにWordFenceプラグインをインストールして、スキャンして、書き換えられたファイルをすべて修復する。  
書き換えられたファイルのせいで、プラグインをインストールするための管理者ログイン自体ができない場合は、画面に表示される異常ファイルを確認しながら最低限管理者ページが開くように個別にファイルを戻す（対象バージョンのWordPressインストーラをダウンロードして正規ファイルと比較する）。  

最後に、.htaccessファイルが大量に生成されているので、タイムスタンプとサイズを確認して、同じファイルはすべて削除する。

## プラグイン

### Wordfence
セキュリティ系プラグイン。

メールアドレスを入力してライセンス登録が必要だが、Freeライセンスがある。

ウィルス汚染されてファイルが書き換えられた場合、スキャン・バージョンごとに書き換えられた内容が確認できる機能が超便利。本来ないファイルも検知されて一括削除が可能。テーマやプラグインのファイルもある程度はチェックしている模様。  

ウィルス感染したらこれでスキャンして一律修復・削除がよい。


