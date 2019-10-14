# ansible-jenkins

## 概要

以下を行うAnsibleスクリプトである。

- Jenkins(Dockerコンテナ)を利用するための実行環境構築（jenkins.yml）  
   ※Slave構築も含む
- Jenkinsのメンテナンス設定（maintenance.yml）
  - Jenkins設定のバックアップ
- コンテナ監視設定（monitoring.yml）

## 利用手順 ～共通～

### 前提条件

- Ansibleがインストールされていること
  - jenkins-setuptool/maintenance/ansible-serverspecにDockerでの構築用ファイルあり
- 構築対象環境がProxy配下の場合、各種Proxy設定が行われていること

### 制限事項

- 構築対象サーバでは、rootユーザと同じ権限を持つユーザで実行できる前提のスクリプト

### 設定
1. hosts/dev/group_vars/all.ymlを設定する。
   ```sh
   [jenkins-master] # JenkinsのMaster Node
   10.0.0.1 
   
   [jenkins-slave] # JenkinsのSlave Node
   10.0.0.2
   10.0.0.3
   
   [jenkins-master:vars]
   # Jenkinsコンテナの/car/jenkins_homeとのホスト側の共有ディレクトリ
   #  ＝ Jenkinsコンテナ内でのdocker実行ユーザのホームディレクトリ
   docker_user_home=/var/lib/jenkins
   jenkins_home=/var/lib/jenkins
   
   [jenkins-slave:vars]
   # docker実行ユーザディレクトリ
   docker_user_home=/root
   # SlaveのJenkinsディレクトリ
   jenkins_home=/var/lib/jenkins
   ```

2. hosts/dev/group_vars/all.ymlを設定する。
   ```yaml
   proxy: false # Proxy配下の場合は、trueにする。
   proxy_ip: localhost # Proxy配下の場合に、プロキシのIPアドレスを設定
   proxy_port: 8080 # Proxy配下の場合に、プロキシのポートを設定
   ```

## 利用手順 ～Jenkins(Dockerコンテナ)を利用するための実行環境構築～

### ツール詳細

JenkinsのMaster/slaveとなるサーバのセットアップを行う。

MasterノードはDockerコンテナで起動する。
そのツールは以下を利用する。利用方法は下記のREADME.md参照。
　jenkins-setuptool/docker-jenkins

### 実行方法

```
# ansible-playbook -i hosts/dev/inventory jenkins.yml  -k
```

## 利用手順 ～Jenkinsのメンテナンス設定～

### ツール詳細
Jenkinsの設定ファイルのバックアップをcronで実行するようにセットアップする。

バックアップ対象は以下。
```
${JENKINS_DIR}/*.xml
${JENKINS_DIR}/*.key*
${JENKINS_DIR}/secrets/.*
${JENKINS_DIR}/updates/.*
${JENKINS_DIR}/users/.*
${JENKINS_DIR}/plugins/.*.jpi
${JENKINS_DIR}/nodes/.*/config.xml
${JENKINS_DIR}/jobs/.*/config.xml
```

### 制限事項

- ★Jenkinsサーバと同一サーバに出力するところまでなので、バックアップ用サーバに移すのは、別途対応すること。

### 設定

1. hosts/dev/group_vars/maintenance.ymlを設定する。
   ```yaml
   backup_tool_dir: /root/tools/backup # バックアップツールディレクトリ
   backup_dir: /root/backup/jenkins # バックアップ先ディレクトリ
   backup_limit_day: 31 # バックアップファイルの保存期間
   cron_minute: "0"
   cron_hour: "0"
   cron_month: "*"
   cron_weekday: "*"
   ```

### 実行方法

```
# ansible-playbook -i hosts/dev/inventory maintenance.yml  -k
```

## コンテナ監視設定～

### 前提条件

- Elasticsearch、Kibanaが構築済みであること。
  - jenkins-setuptool/maintenance/elasticstackにDockerでの構築用ファイルあり

### 設定

1. hosts/dev/group_vars/maintenance.ymlを設定する。
   ```yaml
   kibana_ip: 10.0.1.1 # KibanaのIPアドレス
   kibana_port: 5601 # Kibanaのポート
   elasticsearch_ip: 10.0.1.1 # ElasticsearchのIPアドレス
   elasticsearch_port: 9200 # Elasticsearchのポート
   ```

### 実行方法
```
# ansible-playbook -i hosts/dev/inventory monitoring.yml  -k
```
