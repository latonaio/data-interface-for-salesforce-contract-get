# data-interface-for-salesforce-contract-get
data-interface-for-salesforce-contract-get は、salesforce の契約オブジェクトを取得するために必要なデータの整形、および作成時に salesforce から返ってきた response の MySQL への格納を行うマイクロサービスです。  

## 動作環境  
data-interface-for-salesforce-contract-get は、aion-coreのプラットフォーム上での動作を前提としています。  
使用する際は、事前に下記の通りAIONの動作環境を用意してください。     
   
* OS: Linux OS   
* CPU: ARM/AMD/Intel   
* Kubernetes     
* [AION](https://github.com/latonaio/aion-core)のリソース      

## セットアップ
1. 以下のコマンドを実行して、docker imageを作成してください。
```
$ cd /path/to/data-interface-for-salesforce-contract-get
$ make docker-build
```

2. 本マイクロサービスは DB に MySQL を使用します。MySQL に関する設定を、 `data-interface-for-salesforce-contract-get.yaml` の環境変数に記述してください。

| env_name | description |
| --- | --- |
| MYSQL_HOST | ホスト名 |
| MYSQL_PORT | ポート番号 |
| MYSQL_USER | ユーザー名 |
| MYSQL_PASSWORD | パスワード |
| MYSQL_DBNAME | データベース名 |
| MAX_OPEN_CONNECTION | 最大コネクション数 |
| MAX_IDLE_CONNECTION | アイドル状態の最大コネクション数 |
| KANBANADDR: | kanban のアドレス |
| TZ | タイムゾーン |

## 起動方法
以下のコマンドを実行して、podを立ち上げてください。
```
$ cd /path/to/data-interface-for-salesforce-contract-get
$ kubectl apply -f data-interface-for-salesforce-contract-get.yaml
```

## kanban との通信
### kanban(ui-backend) から受信するデータ
kanban から受信する metadata に下記の情報を含む必要があります。

| key | value |
| --- | --- |
| method | get |
| object | Contract |
| id | 契約ID |
| connection_type | request |

具体例 : 
```example
# metadata (map[string]interface{}) の中身

"method": "get"
"object": "Contract"
"id": "contract_id_xxxxxxxxx"
"connection_type": "request"
```

### kanban に送信するデータ
kanban に送信する metadata は下記の情報を含みます。

| key | type | description |
| --- | --- | --- |
| method | string | get |
| object | string | Contract |
| connection_key | string | contract_get |

具体例: 
```example
# metadata (map[string]interface{}) の中身

"method": "get"
"object": "Contract"
"connection_key": "contract_get"
```

## kanban(salesforce-api-kube) から受信するデータ
kanban からの受信可能データは下記の形式です

| key | value |
| --- | --- |
| key | 文字列 "Contract" を指定 |
| content | Contract の詳細情報を含む JSON |
| connection_type | response |

具体例:
```example
# metadata (map[string]interface{}) の中身

"key": "Contract"
"content": "{xxxxxxxxxxxxxx}"
"connection_type": "response"
```

