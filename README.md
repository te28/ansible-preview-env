# ansible + wp-cliを使用した環境構築

## Ansible

* python製のサーバーの構成管理ツール

<http://www.ansible.com/home>

* 日本語のチュートリアル

<http://yteraoka.github.io/ansible-tutorial/>

## wp-cli

wordpressのコマンドラインインターフェース

<http://wp-cli.org/>

## 構成ファイル

* Inventry:どのサーバーを管理するかを記述する(/.hosts)
* ansible.cfg:ansibleの設定を記述(./ansible.cfg)
* Playbook:どういった構成を作るかを記載(./site.yaml)

## 環境

* python： 2.7.6
* ansible： 1.9.4

## 導入方法

`homebrew`や`pip`で`ansible`をインストールして下さい。

macであればpythonはデフォルトでインストールされているかと思います。
必要であればpyenv等を使用してインストールして下さい。

### homebrewからインストール

```
$ brew install ansible
```

### pipからインストール

```
$ pip install ansible
```

## 使用方法

### リポジトリからテンプレートを取得

任意のディレクトリに移動して、このテンプレートをリポジトリから取得してください。

```
$ cd ~/hoge
$ git clone https://github.com/te28/ansible-preview-env
```

### Ansibleの実行

3パターンのplaybookを用意しています。
設定したい内容によってplaybookのパスを変更して下さい。

#### コマンド実行時の注意点

* ansible から ssh 接続する際、`~/.ssh/id_dsa` をデフォルト鍵として使用します。該当のサーバの鍵を異なる名前で保存している場合は、以下２つの方法で鍵ファイルの場所を指定して実行して下さい。

  * コマンドラインの引数として設定

     ```
     $ ansible-playbook <ymlfile> --private-key="~/.ssh/secret_key"
     ```

  * `ansible_cfg`のキーファイルパスを変更

     ```
	  private_key_file = ~/.ssh/secret_key
     ```

#### １．サブドメインとGit Bareリポジトリのみ作成

```
$ ansible-playbook site.yml
```

上記を実行するとアカウントのパスワードを聞かれるので入力して下さい。

#### 実施時の設定

サブドメイン名とベーシック認証のパスワードを設定するために、
`site.yml` の以下の値を変更した上で実行してください。

```yaml
# 実行時に設定する変数
vars:
  common_subdomain: <サブドメイン名>
  common_authpasswd: <ベーシック認証のパスワード>
```

#### 実施する内容

* xxxx.xxxx.xxxxのサブドメイン用ディレクトリ作成
* xxxx.xxxx.xxxxにベーシック認証を設定
* gitのbareリポジトリ設定(repository.git)
* リポジトリのcloneを作成し、bareリポジトリに push されることで
該当のブランチをデプロイするGitHookを設置

#### ２. サブドメインとGit Bareリポジトリの作成 + wordpressの環境構築実施

```
$ ansible-playbook wordpress.yml
```

上記を実行するとアカウントのパスワードを聞かれるので入力して下さい。

#### 実施時の設定

サブドメイン名とベーシック認証のパスワード、
WordPress の設定を行うために、`wordpress.yml` の以下の値を変更した上で実行してください。

```yaml
# 実行時に設定する変数
vars:
  common_subdomain: <サブドメイン名>
  common_authpasswd: <ベーシック認証のパスワード>

  # wordpressサイト名
  wordpress_site_name:

  # wordpressディレクトリ
  # コアファイルをサブディレクトリにインストールする場合は
  # /wpのような形式で指定する
  wordpress_directory:

  # wordpressで使用するDBの設定
  wordpress_db_name:
  wordpress_db_user:
  wordpress_db_password:
  wordpress_db_prefix:

  # wordpressの管理ユーザーの設定
  wordpress_site_admin_user:
  wordpress_site_admin_password:
  wordpress_site_admin_email:
```

### 実施する内容と設定される項目

#### 実施する内容

* xxxx.xxxx.xxxxのサブドメイン用ディレクトリ作成
* xxxx.xxxx.xxxxにベーシック認証を設定
* gitのbareリポジトリ設定(repository.git)
* wordpress用DB、ユーザーの作成
* wordpressのコアファイルを取得、展開
* wordpressのインストール

#### 設定項目

|項目|値|
|:--|:--|
|DB名|`wordpress_db_name`|
|DBユーザー|`wordpress_db_user`|
|DBパスワード|`wordpress_db_password`|
|DB prefix|`wordpress_db_prefix`|
|公開URL|`subdomain`.xxxx.xxxx|
|wordpressURL|`subdomain`.xxxx.xxxx/`wordpress_directory`※|
|wordpress管理者ID|`wordpress_site_admin_user`|
|wordpress管理者パスワード|`wordpress_site_admin_password`|
|wordpress管理者メールアドレス|`wordpress_site_admin_email`|
|wordpress debugモード|有効|

* ※wordpress_directoryを空欄（''）で実行すると、ドキュメントルート直下に
wordressのコアファイルが展開されます。
