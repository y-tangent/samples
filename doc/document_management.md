# document_management

## DB

OSSで実績と信頼があるpostgresが候補

## postgres

https://qiita.com/ibara1454/items/40ce2d82926f48cf02bc

```
sudo apt-get install postgresql
sudo -i -u postgres
psql
CREATE ROLE role_user WITH LOGIN PASSWORD 'user';
CREATE DATABASE test OWNER role_user;
```

### クライアント認証設定

```
sudo cp /etc/postgresql/9.5/main/pg_hba.conf /etc/postgresql/9.5/main/pg_hba.conf_org
sudo vim /etc/postgresql/9.5/main/pg_hba.conf
```

```:/etc/postgresql/9.5/main/pg_hba.conf
host all all 0.0.0.0/0 trust
```

### リモート設定

```
sudo cp /etc/postgresql/9.5/main/postgresql.conf /etc/postgresql/9.5/main/postgresql.conf_org
sudo vim /etc/postgresql/9.5/main/postgresql.conf
```

```:/etc/postgresql/9.5/main/postgresql.conf
listen_addresses='*'
```

### 再起動

```
/etc/init.d/postgresql restart
```

## remarks

- VirtualBoxでDBを立ち上げ、WindowsからDBクライアントでアクセスする場合
	- VirtualBoxのネットワーク設定でホストオンリーアダプター追加

## a5m2

### テーブル作成

- ファイル->新規->ER図
- エンティティとリレーションシップを追加する
- ER図->DDLを作成する
	- 以下を有効にして生成する
		- リレーションシップから外部キー制約を作成する
- SQL実行

## フロントエンド

- excel db
