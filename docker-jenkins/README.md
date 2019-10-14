# docker-jenkins

## 概要

Docker、Docker Composeを利用できるJenkinsコンテナを起動する。

## 利用手順

### 前提条件

- ホスト側にDockerをインストールしておくこと。
- コンテナと共有するJenkins用ディレクトリを作成しておくこと。（例：/var/lib/jenkins）

### 設定

1. .envファイルにDockerのグループIDを設定する。
2. .envファイルにコンテナと共有するJenkins用ディレクトリを設定する。

### 実行方法

```
# docker-compose up -d
```
