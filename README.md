#  Flaskで作る画像判定

## venv環境作成

バーチャル環境にvenvを使います。

### 今回作成アプリの仮想環境設定

flask_vgg16という環境を作るには、以下を実行

```
python -m venv  flask_vgg16
```



環境に入るのは以下`cd flask_vgg16g`で移動した後に次のコマンドを実行

ここはよく失敗するので注意。

作成した環境名のフォルダ内にbinフォルダが作成されます。binフォルダ内にactivateファイルがあるのでそれを実行すれば良いのです。

```python
source bin/activate
```

現在の仮想環境から出る。

```python
deactivate
```

## アプリケーションの環境設定

Flaskの導入

```
pip install Flask
```

### Flaskバージョン確認

Flaskのバージョン確認は次のようにPythonから確認します。

```
$ python
>> import flask
>> flask.__version__
```

'1.1.2'のようにバージョンが確認できます。

今回のFlaskバージョンは 1.1.1 での例です。

### tensorflowインストール

```
pip install tensorflow==2.0.0
```

### werkzeug

```
pip install Jinja2 redis Werkzeug
```

### gunicorn のインストール

```
pip install gunicorn
```

### psycopg2 のインストール

```
pip install psycopg2-binary
```

以下numpyまでは導入すみ

### jinja2のインストール

```
pip install jinja2
```

### h5pyのインストール

```
pip install h5py
```

### numpyのインストール

```
pip install numpy
```

### pillowインストール

```
pip install pillow
```

###matplotlibインストール
```
pip install matplotlib
```



### Flaskドキュメント

[Flask1.1系のドキュメントはこちら](https://flask.palletsprojects.com/en/1.1.x/)

## アプリケーション本体の作成

コードの解説は割愛します。

ポイントは別に作成したColaboファイルでModelを学習します。

今回はcifar-10の画像とVGG16で転移学習しています。その結果をHDF5ファイルに保存しておいて、今度はこのアプリ内で保存しておいたHDF5ファイルで予測を行うものです。

あくまで学習用のサンプルですから、内容は単純ですが、応用すると人物などの判定にも使えるものです。

## ローカル環境でのサーバー起動

guess.pyのファイルがある場所にcdで移動後以下コマンド

```
python guess.py
```

* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)と表示されますのでこのURLでブラウザを開きます。

サーバーを止めるには、表示に記述されている通り、CTRL+C です。



cd コマンドでclassifierフォルダに移動しておきます。

## アプリの実行

コマンドで以下を実行

```
python guess.py
```

\* Serving Flask app "guess" (lazy loading)

 \* Environment: production

  WARNING: This is a development server. Do not use it in a production deployment.

  Use a production WSGI server instead.

 \* Debug mode: off

 \* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)

以上の表示でブラウザで確認します。

## デプロイ

### Procfile の準備

heroku 上で実行するコマンドを記載します。あるいは新規でファイルを作成して、Procfileというファイル名にします。

```
vim Procfile
```

vimで以下内容を記述　適当なエディッタで記述してもOK

```
web: gunicorn guess:app --log-file -
```

ここは、vimの使い方が苦手な場合、現在作業を行なっているフォルダ内にProcfileを作成して、その中に記述をしてもよい。

### requirements.txt の準備

`pip freeze` でインストール済パッケージ一覧を表示することが出来ます。
`heroku` は`requirements.txt`を頼りに必要なライブラリをインストールするので準備します。

```
pip freeze > requirements.txt
```

`requirements.txt`が出来上がっているのがわかります。

```
Flask==1.1.2
gunicorn==20.0.4
h5py==2.10.0
Jinja2==2.11.2
numpy==1.18.3
Pillow==7.1.2
tensorflow==2.3.0
Werkzeug==1.0.1
```

## Heroku CLIの導入

Heroku-CLIはターミナル上でHerokuアプリをより簡単に使えるツールです。

[ダウンロード](https://devcenter.heroku.com/articles/heroku-cli)こちらのページから自分のPC環境に合ったものでインストールします。

### ログイン

Herokuにログインは次のコマンドです。ログインはブラウザが立ち上がりますので、そこでログイン操作します。一度Herokuの管理画面を知っておくと良いでしょう。

```
heroku login
```

もし全てコマンドで実行したいなら` -i`オプションをつけてログインします。

```
heroku login -i
```

### Herokuのアプリ名を決めます。

アプリの作成コマンド

```
heroku create flask-vgg16
```

エラーが出たら名前がすでに使われているので、
違う名前でアプリを作成してください。

次の内容が表示されるとOK

Creating ⬢ flask-vgg16... done

https://flask-vgg16.herokuapp.com/ | https://git.heroku.com/flask-vgg16.git



## GitでHeroku コミット

まずはgitにコミットします。

**staticフォルダ内のimagesフォルダにはローカルでテストした画像を入れたままaddします。空の状態だとフォルダがgitに反映されずにHerokuでフォルダが作成されません**

git初期化コマンド（既にgit管理している場合は必要ありません）

```
git init
```

herokuのremotを教えるコマンド

```
heroku git:remote -a flask-vgg16
```

ステージにaddするコマンド

```
git add .
```

コミットするコマンド

```
git commit
```

herokuにpushするコマンド

```
git push heroku master
```

Herokuにプッシュが終わると、requirements.txtに従ってインストールが始まります。

一番のネックはTensorflowのインストールです。2.1以上のバージョンだとコケます。

原因はSlug Sizeの問題です。

無事にインストールできれば、Webアプリは以下のアドレスで開きます。

https://flask-vgg16.herokuapp.com/ 

## インストールがうまくいかない場合

Herokuで作成したアプリには、Slug Sizeというものがあります。

Slug SizeはブラウザからHerokuにログイン後、アプリ名をクリック→Settingsをクリック→Infoから判ります。

Slug Sizeが大きいと、次のような問題が起こります。

特にTensorlowの最新版では500MB制限にかかりインストール出来ません。

Tensorlow2.0.0ではインストールに成功しています。

- アプリの起動が遅くなる
- 500MB制限に引っかかる

## 判定結果でエラーが出る場合

ColaboのTensorlowのバージョンやKerasの使い方次第でうまくHDF5ファイルが読み込めないことがあります。その場合ColaboのTensorlowのバージョンをHerokuにインストールするバージョンに合わせるなどの対策が必要になります。

ColaboのTensorflowをアンインストール

```
pip uninstall -y tensorflow
```

バージョン2.0.0のインストール

```
pip install tensorflow-gpu==2.0.0
```

