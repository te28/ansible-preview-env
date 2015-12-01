# ansibleを使用したプレビュー環境構築

## Ansible

python製のサーバーの構成管理ツール

<http://www.ansible.com/home>

* 日本語のチュートリアル
<http://yteraoka.github.io/ansible-tutorial/>

## wp-cli

wordpressのコマンドラインインターフェース

<http://wp-cli.org/>

## 構成ファイル

* Inventry:どのサーバーを管理するかを記述する(/.hosts)
* ansible.cfg:ansibleの設定を記述(./ansible.cfg)
* Playbook:どういった構成を作るかを記載(./playbook.yaml)

## 環境

* python： 2.7.6
* ansible： 1.8.4

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
$ git clone https://192.168.1.201/git/house-projects.ansible-sakuravps
```

### Ansibleの実行

２パターンのplaybookを用意しています。
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
$ ansible-playbook playbook.yml
```

上記を実行すると質問が表示されますので
入力して下さい。

```
sudo password:  // ingsアカウントのパスワードを入力
Enter Subdomain Name: : // 作成したいサブドメインを入力
Enter Basic Authentication Password: : // ベーシック認証のパスワードを設定
```

#### 実施する内容

* xxxx.ingsnet.infoのサブドメイン用ディレクトリ作成
* xxxx.ingsnet.infoにベーシック認証を設定
* gitのbareリポジトリ設定(repository.git)

#### ２．サブドメインとGit Bareリポジトリの作成 + wordpressの環境構築実施

```
$ ansible-playbook playbook_wp.yml
```

上記を実行すると幾つか質問されますのでそれぞれについて入力をして下さい。
記入した内容で設定が実施されます。

```
sudo password:  // ssh ingsアカウントのパスワードを入力
Enter Database Root Password:  // MySQLのrootアカウントのパスワードを入力
Enter Prefix : // wordpressのDBのprefixやwordpressのコアファイル格納ディレクトリに使用される文字を入力
Enter Subdomain Name: : // 作成したいサブドメインを入力
Enter Basic Authentication Password: : // ベーシック認証のパスワードを設定
Enter Wordpress Database Password: : // wordpress用のデータベースパスワードを入力
Enter Your Name => {{ name }}@ingsnet.co.jp: : // wordpressの管理者用メールアドレス
```

### 実施する内容と設定される項目

#### 実施する内容

* xxxx.ingsnet.infoのサブドメイン用ディレクトリ作成
* xxxx.ingsnet.infoにベーシック認証を設定
* gitのbareリポジトリ設定(repository.git)
* wordpress用DB、ユーザーの作成
* wordpressのコアファイルを取得し、サブディレクトリ({prefix})に展開
* wordpressのインストール

#### 設定項目

|項目|値|
|:--|:--|
|DB名|`<サブドメイン名>`|
|DBユーザー|`<サブドメイン名>`|
|DBパスワード|`<DBパスワード>`|
|DB prefix|`<prefix>`_|
|公開URL|`<サブドメイン名>`.ingsnet.info|
|wordpressURL|`<サブドメイン名>`.ingsnet.info/`<prefix>`|
|wordpress管理者ID|`<prefix>`_admin|
|wordpress管理者パスワード|`<prefix>`_pass|
|wordpress管理者メールアドレス|`<name>`@ingsnet.co.jp|
|wordpress debugモード|有効|

