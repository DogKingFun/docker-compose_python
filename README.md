# ディレクトリ構成

```
　
docker-compose.yml
python/
 　├ app.py
 　├ Dockerfile
 　└ requirements.txt
```

# docker-compose

```docker-compose.yml
version: '3'

services:
   python:
     build: ./python
     container_name: PYTHON
```

　重要なのはbuildの項だけでしょう。
　これはpythonディレクトリにDockerfileがあるので、それを参考にコンテナを作ってね、ということです。docker-composeでは他にも環境変数をいじったり出来る用ですが、pythonコンテナをとりあえず建てたいというだけであれば、問題はありません。

# Dockerfile

```Dockerfile
FROM python:3

# アプリケーションディレクトリを作成する
WORKDIR /usr/src/python

# pipのアップデート
RUN pip install --upgrade pip

# pipでインストールしたいモジュールをrequirements.txtに記述しておいて、
# コンテナ内でpipにインストールさせる
# requirements.txtの書き方は[pip freeze]コマンドから参考に出来る
COPY requirements.txt ./
RUN pip install -r requirements.txt

# アプリケーションコードをコンテナにコピー
COPY . .

EXPOSE 8000
CMD [ "python", "app.py" ]
```

## FROM python:3
　「Python3.xのバージョンのあるイメージを使う」という意味…だと思います。たぶん。

## WORKDIR /usr/src/python
　コンテナ内では仮想のLinuxOSが起動しています。
　その/usr/srcディレクトリにpythonディレクトリを作成し、どうやらカレントディレクトリにしているようです。

## RUN pip install -rf requirements.txt
　これはrequirements.txtに書かれているインストールしたいモジュールをインストールする処理です。この前にCOPYによって、用意していたrequirements.txtをコンテナ内にコピーしているため使うことが出来ています。インストールしたいモジュールをRUN pip install numpyのようにいくつも書くのはめんどくさいのでこのインストール方法を採用しました。
　requirements.txtの書き方は「pip freeze」コマンドで確認できます。

## COPY . .
　コンテナ外のカレントディレクトリの全ファイルおよびディレクトリを、コンテナ内のカレントディレクトリにコピーしています。これにより、コンテナ外にあるファイルを編集することでプログラムの変更が可能です。
　docker-composeを使ってアプリ開発をするのであれば、必須級のコマンドだと思います。
```Dockerfile
COPY . .
```

# app.py
　コンテナを起動して、ちゃんと動くか確認するためのコード。
　なのでとても短い。

```app.py
print('test')
```

# requirements.txt
　requirements.txtの内容です。参考までにnumpyをインストールしていますが、見ての通り今回は使用していません。

```requirements.txt
numpy
```

# 起動

```
docker-compose up --build
```

　コマンドを実行するとprintのアウトプットが表示されると思われます。

```
Attaching to PYTHON
PYTHON    | test
PYTHON exited with code 0
```
