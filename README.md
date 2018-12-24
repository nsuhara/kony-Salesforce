# はじめに

*(Mac環境の記事ですが、Windows環境も同じ手順になります。環境依存の部分は読み替えてお試しください。)*

この記事を最後まで読むと、次のことができるようになります。

- iOS/AndroidアプリからSalesforceのレコードを作成する
- SalesforceのレコードをiOS/Androidアプリで表示する

`Salesforceのイメージ画像`

<img width="500" alt="スクリーンショット 2018-12-20 17.47.39.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/00a63f26-603e-fe4a-a79d-82e985b0de2e.png">

`アプリのイメージ画像`

<img width="200" alt="IMG_0409.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/21d9b8ee-3247-f9f2-ff24-602038bf951f.png">&nbsp;&nbsp;<img width="200" alt="IMG_0406.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/83862ca9-723f-b915-117f-24b9ff00e3ba.png">&nbsp;&nbsp;<img width="200" alt="IMG_0411.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/2b22d10c-64d7-6158-d7aa-54af7dc24e12.png">

# 関連する記事

- [Kony AppPlatformを使ってiOS/Androidアプリを作成する](https://qiita.com/nsuhara/items/c28d838492512850520c)
- [Kony AppPlatformで作成したiOS/AndroidアプリのAuto Layoutについて学ぶ](https://qiita.com/nsuhara/items/a52abd9861c51823ecec)
- [Kony AppPlatformで作成したiOS/Androidアプリのコーディングについて学ぶ](https://qiita.com/nsuhara/items/bf0e8884a7efc3c55176)

# 実行環境

- macOS Mojave 10.14.1
- Kony Visualizer 8.3.10
- Salesforce Winter '19

# ソースコード

実際に実装内容やソースコードを追いながら読むとより理解が深まるかと思います。是非ご活用ください。

[GitHub](https://github.com/nsuhara/kony-Salesforce.git)

# 事前準備

- シナリオ
    1. iOS/AndroidアプリからSalesforce(Sampleアプリ)のレコード(First Name, Last Name)を登録する。
    1. 登録したレコードをiOS/Androidアプリで表示する。

### Salesforce

1. Kony AppPlatformと繋ぐオブジェクトを作成する

    |   項目名   |   API参照名   |
    |------------|---------------|
    | Sample     | Sample__c     |
    | First Name | First_Name__c |
    | Last Name  | First_Name__c |

    <img width="500" alt="スクリーンショット 2018-12-20 17.47.39.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/00a63f26-603e-fe4a-a79d-82e985b0de2e.png">

1. 接続アプリケーションを設定する

    設定 -> 作成 -> アプリケーション -> 接続アプリケーション -> `新規`をクリックする

    |            項目名            |                 設定値                  |
    |------------------------------|-----------------------------------------|
    | 接続アプリケーション名       | Sample                                  |
    | API 参照名                   | Sample                                  |
    | 取引先責任者 メール          | sample@gmail.com                        |
    | OAuth 設定の有効化           | TRUE                                    |
    | コールバック URL             | https://sample.auth0.com/login/callback |
    | 選択した OAuth 範囲          | 基本情報へのアクセス                    |
    | Web サーバフローの秘密が必要 | TRUE                                    |

    <img width="500" alt="スクリーンショット 2018-12-19 23.42.27.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/32b14593-0691-bd48-531e-ed7ac6281e80.png">

    `接続アプリケーションを使用する前に、サーバ上で変更が有効になるまで 2 ～ 10 分お待ちください。`

### Kony AppPlatform

1. メニュー画面を作成する

    | 項目名  | オブジェクト |          説明          |
    |---------|--------------|------------------------|
    | lblMenu | ラベル       | タイトル               |
    | btnAdd  | ボタン       | Salesforceレコード登録 |
    | btnView | ボタン       | Salesforceレコード表示 |
    | btnExit | ボタン       | アプリ終了             |

    <img width="500" alt="スクリーンショット 2018-12-19 11.32.15.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/bf747264-462e-6e2a-1a71-f10140ca017e.png">

1. 登録画面を作成する

    |    項目名    |   オブジェクト   |          説明          |
    |--------------|------------------|------------------------|
    | lblAdd       | ラベル           | タイトル               |
    | lblFirstName | ラベル           | First Nameラベル       |
    | txtFirstName | テキストボックス | First Name入力         |
    | lblLastName  | ラベル           | Last Nameラベル        |
    | txtLastName  | テキストボックス | Last Name入力          |
    | btnAdd       | ボタン           | Salesforceレコード登録 |
    | btnBack      | ボタン           | Menuへ戻る             |

    <img width="500" alt="スクリーンショット 2018-12-19 11.33.13.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/6744b910-1da5-09ac-844e-8e4c621c7ae4.png">

1. 表示画面を作成する

    | 項目名  | オブジェクト |         説明         |
    |---------|--------------|----------------------|
    | lblView | ラベル       | タイトル             |
    | segView | セグメント   | Salesforceリスト表示 |
    | btnBack | ボタン       | Menuへ戻る           |

    <img width="500" alt="スクリーンショット 2018-12-19 11.33.40.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/06caa43c-5176-7894-59a1-f7937b1e5a41.png">

# Salesforce(Sample)の登録

### 設定手順

1. OAuth設定

    Data -> `Configure`をクリック

    <img width="400" alt="スクリーンショット 2018-12-20 0.17.00.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/cb8aa29c-0438-78e4-a76c-5f27db9a790a.png">

    GET DATAのSalesforceをクリック

    |     項目名     |                             設定値                              |
    |----------------|-----------------------------------------------------------------|
    | Name           | Sample                                                          |
    | Authentication | Specify Login Endpoint                                          |
    | Endpoint URL   | Salesforce接続アプリケーションのコンシューマ鍵 (参照1)     |
    | Client ID      | Salesforce接続アプリケーションのコンシューマの秘密 (参照1) |
    | User ID        | SalesforceユーザID                                              |
    | Password       | Salesforceパスワード + セキュリティトークン (参照2)        |

    <img width="500" alt="スクリーンショット 2018-12-20 0.32.31.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/43115a16-1769-1c4a-e22a-5b0b73ce2108.png">

    最後に`SAVE & GENERATE`をクリックします。

    **(参照1)**

    <img width="500" alt="スクリーンショット 2018-12-20 0.29.33.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/997ae843-b774-b32e-a576-bbe8fad75c8e.png">

    **(参照2)**

    セキュリティトークンは、私の設定 -> 個人用 -> `私のセキュリティトークのリセット`から再発行ができます。

    <img width="400" alt="スクリーンショット 2018-12-20 0.43.24.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/1d6a4c55-d9ba-535e-4777-ef43ce8eeebe.png">

1. Salesforce(Sample)の接続

    `SAVE & GENERATE`の続きです。`Sample__c`を検索しチェックボックにチェックを入れます。最後に`Next` -> `GENERATE`の順番にクリックします。

    <img width="500" alt="スクリーンショット 2018-12-20 0.49.56.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/9ecf4270-8811-7e68-1ef0-ed49319e7cf6.png">

    Data Modelに`Sample__c`追加されたら接続完了です。

    <img width="400" alt="スクリーンショット 2018-12-20 0.54.33.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/047c093e-06cc-34a8-1e91-d67184490de0.png">

# レコード登録の実装

### 設定手順

1. データマッピング

    Projectタブ -> Mobile -> Forms -> frmAdd -> flxAdd -> `btnAdd`をクリックします。

    Propertiesタブ -> ActionたタブのonClickの`Edit`をクリックします。

    <img width="400" alt="スクリーンショット 2018-12-20 16.11.33.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/2349c054-ef10-1595-0683-c0382cd53dc9.png">

    Network APIsの中の`Invoke Object Service`をクリックします。

    Select Serviceに`Sample$Sample__c`、Select Methodに`create`を設定します。

    <img width="500" alt="スクリーンショット 2018-12-20 16.15.39.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/1601b265-c817-0ae1-08ef-f9853d7c472e.png">

    Call Object Serviceをクリックしてから`Open Mapping Editor`をクリックします。

    iOS/AndroidアプリとSalesforce(Sample)のデータをクリックしてマッピングします。

    | アプリ             | Mapping     | Salesforce           |
    | --------------     | :---------: | -------------------- |
    | txtFirstNameのtext | ->          | FirstName__c         |
    | txtLastNameのtext  | ->          | LastName__c          |

    <img width="500" alt="スクリーンショット 2018-12-20 16.18.13.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/b208d37c-dcf0-2732-32c8-78e54365caa4.png">

1. テキストボックスのクリア

    CallbackをクリックしてFunctionsの中から`Add Snippet`をクリックします。

    データ送信後にテキストボックスをクリアするコードを記述します。

    ```CodeSnippet.js
    self.view.txtFirstName.text = null;
    self.view.txtLastName.text = null;
    ```

    <img width="500" alt="スクリーンショット 2018-12-20 17.02.33.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/c3a1798f-b920-3843-4737-ab4a352e693e.png">

1. Infoポップアップ表示

    CallbackをクリックしてExternalの中から`Show Alert`をクリックします。

    データ送信後のInfoポップアップ表示を設定します。

    |    項目名     |   設定値   |
    |---------------|------------|
    | Alert Type    | Info       |
    | Alert Title   | Result     |
    | Yes Label     | OK         |
    | Alert Message | Sent Data. |

    <img width="500" alt="スクリーンショット 2018-12-20 17.09.28.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/eabc6e60-29ab-7325-7428-6b915113be97.png">

### 動作確認

1. 登録画面の確認

    Menu -> `Addボタン`をタップしてSalesforceのレコードを登録します。

    <img width="200" alt="IMG_0406.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/83862ca9-723f-b915-117f-24b9ff00e3ba.png">

    Salesforceに登録されたことを確認します。

    <img width="500" alt="スクリーンショット 2018-12-20 17.23.06.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/e76bc3a3-8d6f-67ec-7c4e-f7eb197051e7.png">

1. Infoポップアップ表示の確認

    レコードを登録した後にInfoポップアップが表示されることを確認します。

    <img width="200" alt="IMG_0407.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/b06feeed-7f47-c86e-ebd2-d9ba04f0bcb3.png">

1. テキストボックスクリアの確認

    レコードを登録した後にテキストボックスがクリアされることを確認します。

    <img width="200" alt="IMG_0408.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/cd9ded07-f515-a6bd-f461-618373b7c1ee.png">

# レコード表示の実装

### 設定手順

1. 表示項目テンプレートの作成

    Templatesタブ -> Segments -> 右クリック -> `New Template`をクリックします。

    |    項目名    | オブジェクト |            説明           |
    |--------------|--------------|---------------------------|
    | lblFirstName | ラベル       | First Nameラベル          |
    | txtFirstName | ラベル       | Salesforce First Name表示 |
    | lblLastName  | ラベル       | Last Nameラベル           |
    | txtLastName  | ラベル       | Salesforce Last Name表示  |

    <img width="500" alt="スクリーンショット 2018-12-20 9.58.25.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/296de426-a734-fc7a-9f3e-2622bafb52cf.png">

    <font color="red">*注) flxViewDetailsの中でFlexContainerを使うとNetwork APIsのMappingができない(正確にはMappingはできるがデータ連携ができない)。セグメントの中ではFlexContainerを使わない方が良さそうです。*</font>

    *例) lblFirstNameとtxtFirstNameをFlexContainerでグループ化するなど*

1. テンプレートの割り当て

    Projectタブ -> Mobile -> Forms -> frmView -> flxView -> `segView`をクリックします。

    Propertiesタブ -> SegmentたタブのRow Templateに`segViewDetails`を設定します。 

    <img width="400" alt="スクリーンショット 2018-12-20 8.32.21.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/eb5c70c2-4b65-bfbc-b083-92913fc71925.png">

    テンプレートが設定されます。

    <img width="500" alt="スクリーンショット 2018-12-20 8.38.04.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/c29bca74-56d6-9f88-3891-d577c0a7931a.png">

1. データマッピング

    Projectタブ -> Mobile -> Forms -> `frmView`をクリックします。

    Propertiesタブ -> ActionたタブのonMappingの`Edit`をクリックします。

    <img width="400" alt="スクリーンショット 2018-12-20 8.42.52.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/88a22982-ad10-cdaf-14d9-e6f0a12c721a.png">

    Network APIsの中の`Invoke Object Service`をクリックします。

    Select Serviceに`Sample$Sample__c`、Select Methodに`get`を設定します。

    <img width="500" alt="スクリーンショット 2018-12-20 8.54.59.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/b2e8a742-8539-3c2b-90bd-c2e2bb314a8b.png">

    CallbackをクリックしてからMappingの中の`Add Mapping`をクリックします。

    <img width="500" alt="スクリーンショット 2018-12-20 9.07.14.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/540c7d2a-c374-5a08-ced2-5ebe838b8fea.png">

    Salesforce(Sample)とiOS/Androidアプリのデータをクリックしてマッピングします。

    | Salesforce     | Mapping     | アプリ               |
    | -------------- | :---------: | -------------------- |
    | records        | ->          | masterdatamap        |
    | FirstName__c   | ->          | txtFirstNameのtext   |
    | LastName__c    | ->          | txtLastNameのtext    |

    <img width="500" alt="スクリーンショット 2018-12-20 9.10.49.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/937a603e-557a-9eaf-b05e-c5b540b34cc9.png">

### 動作確認

1. リスト表示の確認

    Salesforce側の登録状況を確認します。

    <img width="500" alt="スクリーンショット 2018-12-20 17.47.39.png" src="https://qiita-image-store.s3.amazonaws.com/0/326996/00a63f26-603e-fe4a-a79d-82e985b0de2e.png">

    Menu -> `Viewボタン`をタップしてSalesforceのレコードを確認します。

    <img width="200" alt="IMG_0410.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/1b59d6cc-1c63-1f8c-4dc9-b52d15c1fbbc.png">&nbsp;->&nbsp;<img width="200" alt="IMG_0411.PNG" src="https://qiita-image-store.s3.amazonaws.com/0/326996/2b22d10c-64d7-6158-d7aa-54af7dc24e12.png">
