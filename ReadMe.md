# このソフトウェアについて

GitHubリポジトリを作成するPythonスクリプトの外部呼出対応版。

[前回](https://github.com/ytyaru/GitHub.Upload.ByPython.201702051553)の改良版。

# 開発環境

* Linux Mint 17.3 MATE 32bit
* [Python 3.4.4](https://www.python.org/downloads/release/python-344/)
    * [requests](http://requests-docs-ja.readthedocs.io/en/latest/)
    * [dataset](https://github.com/pudo/dataset)

## WebService

* [GitHub](https://github.com/)
    * [アカウント](https://github.com/join?source=header-home)
    * [AccessToken](https://github.com/settings/tokens)
    * [Two-Factor認証](https://github.com/settings/two_factor_authentication/intro)
    * [API v3](https://developer.github.com/v3/)

# 準備

1. GitHubの利用準備
2. データベースを作成する
3. データベースに挿入する
4. 今回のスクリプトを配置し設定する

## 1. GitHubの利用準備

* [GitHubアカウント](https://github.com/join?source=header-home)を作成しておく
* [AccessToken](https://github.com/settings/tokens)を作成する
    * [AccessToken](https://github.com/settings/tokens)のscopesに`repo`を付与する
        * [リモートリポジトリ生成にはrepoまたはpublic_repoのscopesが必要](https://developer.github.com/v3/repos/#oauth-scope-requirements)
* [SSH鍵の作成と設定をする](http://ytyaru.hatenablog.com/entry/2016/06/17/082230)
    * `.ssh/config`ファイルの`Host`の値は`github.com.{github_username}`とする
        * ユーザ名が`ytyaru`なら`github.com.ytyaru`とする

## 2. データベースを作成する

SQLite3でDBファイルを作成する。

* [GitHub.Accounts.Database](https://github.com/ytyaru/GitHub.Accounts.Database.20170107081237765)
    * [GiHubApi.Authorizations.Create](https://github.com/ytyaru/GiHubApi.Authorizations.Create.20170113141429500)
* [GitHub.Repositories.Database.Create](https://github.com/ytyaru/GitHub.Repositories.Database.Create.20170114123411296)

## 3. データベースに挿入する

指定アカウントから全リポジトリ情報をDBに挿入する。

* [GiHub.Repo.Insert.20170114155109609](https://github.com/ytyaru/GiHub.Repo.Insert.20170114155109609)

## 4. 今回のスクリプトを配置し設定する

1. 今回のスクリプトをダウンロードし配置する
1. `up.py`のコードにある`path_db_account`,`path_db_repo`にDBファイルパスを指定する
1. `call.sh`の`path_script`に`up.py`のパスを指定する
1. GitHubリポジトリにしたいディレクトリ配下に`call.sh`を配置する

GitHubリポジトリにしたいディレクトリは適当に作っておくこと。

# 実行

1. リポジトリ設定
2. CUIで対話する
    1. リポジトリを作成する
    2. commit,pushする
    3. 集計を表示する

## リポジトリ設定

1. `call.sh`の`user_name`にGitHubユーザ名を指定する
1. `description`にリポジトリの説明文を指定する
1. `homepage`にリポジトリの関連URLを指定する
```sh
user_name=ytyaru
description=説明文。
homepage=http://abc
```

値は任意に変更すること。

## 2. CUIで対話する

1. ターミナルを起動する
1. 以下のコマンドを実行する（今回のスクリプトを実行する）

```sh
bash call.sh
```

### 2-1. リポジトリを作成する

`.git`ディレクトリがない場合、以下のようなメッセージが出る。

```sh
ユーザ名: ytyaru
メアド: {DBにあるメールアドレス}
SSH HOST: github.com.ytyaru
リポジトリ名: {call.shを実行したディレクトリ名}
説明: 説明文。
URL: http://abc
リポジトリ情報は上記のとおりで間違いありませんか？[y/n]
```

問題なければ以下のように`y`を入力してEnterキーを押下する。

```
y
```

* `git init`でGit管理する。
* `ytyaru`というGitHubユーザに`{call.shを実行したディレクトリ名}`という名前のリモートリポジトリを作成する。

これでローカルに`.git`が作成される。なお、すでに`.git`が作成されていたら、この処理は行わない。リモートリポジトリ作成も同じく行われない。リポジトリ作成以降に`bash call.sh`を実行したときは以下のcommit,push画面からはじまる。

### 2-2. commit,pushする

```sh
リポジトリ名： ytyaru/{call.shを実行したディレクトリ名}
説明: 説明文。
URL: http://abc
----------------------------------------
add 'LICENSE.txt'
add 'ReadMe.md'
add 'main.py'
commit,pushするならメッセージを入力してください。Enterかnで終了します。
サブコマンド    n:終了 a:集計 e:編集 d:削除 i:Issue作成
```

* `git add -n .`で今回のcommit対象ファイル一覧を表示する
* Enterのみ,`n`,`a`,`e`,`d`,`i`のみの場合、それぞれのアクションが実行される（ほとんど未実装）
* それ以外のテキストを入力してEnterを押下すると、コミットメッセージとみなされてcommitする（確認メッセージなしのため注意）
* 続けてリモートリポジトリにpushする

エラーが出なければ、これにてGitHubへのアップロードが完了する。

なお、ブランチ操作は一切行わない。

### 2-3. 集計を表示する

最後に、集計結果が表示される。

```sh
開始日: 2016-06-16T13:13:25Z
最終日: 2017-03-08T23:26:45Z
期  間: 265 日間
リポジトリ総数  : 149
リポジトリ平均数: 0.5660377358490566 repo/日
コード平均量    : 3101.433962264151 Byte/日
コード総量      : 821880 Byte
  C++         : 406817 Byte
  C#          : 145420 Byte
  Python      : 128845 Byte
  HTML        :  54746 Byte
  Batchfile   :  42585 Byte
  JavaScript  :  23102 Byte
  Shell       :  12287 Byte
  Makefile    :   3755 Byte
  Visual Basic:   1717 Byte
  C           :   1076 Byte
  PowerShell  :    855 Byte
  CSS         :    675 Byte
```

DBにある全情報から集計した結果。

アップロードするたびに達成感を味わうことで、己のアウトプットを促進する。

# ライセンス #

このソフトウェアはCC0ライセンスである。

[![CC0](http://i.creativecommons.org/p/zero/1.0/88x31.png "CC0")](http://creativecommons.org/publicdomain/zero/1.0/deed.ja)

