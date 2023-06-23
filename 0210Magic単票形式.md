# 【説明書】単票形式・1:1形式

## Links

- [単票形式・1:1形式](https://ezostyle.esa.io/posts/544)
- [伝票形式・1:N形式](https://ezostyle.esa.io/posts/554)

---

## Target

- 新たに Wec Client を利用しようとしている方
    - 特にスタートアップとして、シンプルで可読性の高いプログラムの基本構造を知りたい方
    - オンプレ / RIA との 命令や定義、動作の差に不安がある方

---
---

## INDEX

※この記事は、**単票形式** についての説明になります。

- 基礎構造 **単票形式** と **伝票形式** について
    - 単票形式 (1 : 1形式)
    - 伝票形式 (1 : N形式)
- サンプルデータ
- 説明概要・全体構成 と 処理フロー
    - 説明概要・全体構成
        - ヘッダー
        - リスト
        - 詳細
        - 詳細リスト
    - フロー
- 説明本編
    - 会員一覧 MembersList
    - 会員登録 MembersEdit


---
---

## 基礎構造 **単票形式** と **伝票形式** について

基本的なテーブル（データ）間の関係は、大きく分けて２つに分けられるかと思います。

1. 1つのデータに対して、**1:1** の関係にある別テーブルの１つのデータ 
2. 1つのデータに対して、**1:N** の関係にある別テーブルの複数のデータ

この関係性をベースに使用する構造を単票形式・伝票形式で使い分けています。

### 単票形式 (1 : 1形式)

特別な名称ではないですが、チームメンバーとのやり取りをスムーズにするため共通認識として、
ベースになる構造の名称を "単票形式" と呼称しています。


`1個 のデータに対して、1:1 の関係にある別テーブルの 1個 のデータ`

ex) 
ユーザー情報テーブル > 郵便番号 --- 郵便番号 < 住所マスタテーブル

こういった単純な、1つのデータを元に１つのデータを取得する(取得しようとする)形式を単票形式
として紹介します。

### 伝票形式 (1 : N形式)

特別な名称ではないですが、チームメンバーとのやり取りをスムーズにするため共通認識として、
ベースになる構造の名称を "伝票形式" と呼称しています。


`1個 のデータに対して、1:N の関係にある別テーブルの N個 のデータ`

ex) 

発注記録 > 発注No --- 発注No ⇒ 発注明細 < 発注明細テーブル

こちらは、1つのデータを元に複数のデータを取得する(取得しようとする)形式で伝票形式
として紹介します。

---

※この記事は、**単票形式** についての説明になります。

---

### サンプルデータ
【テーブル間の関係性】
- M会員 (ベースになるテーブル)
- M商品 (T購入履歴の商品Codeに対して、1:1)
- T会員管理 (M会員の会員Noに対して、1:1)
- T購入履歴 (M会員の会員Noに対して、1:N)
- T購入履歴明細 (T購入履歴の注文Noに対して、1:N)
- W購入履歴明細(T購入履歴の注文Noに対して、1:N ※入力・編集用のﾒﾓﾘﾃｰﾌﾞﾙ)

【M会員】主ｷｰ(PrimaryKey) 会員No を ｲﾝﾃﾞｯｸｽに登録
| 会員No (N=数値:4) |氏名 (A=文字:10)|
|:-|:-|
| 0001 | 札幌太郎 |
| 0002 | 函館二郎 |
| 0003 | 旭川三郎 |

【M商品】主ｷｰ(PrimaryKey) 商品Code を ｲﾝﾃﾞｯｸｽに登録
| 商品Code (N=数値:4) |商品名 (A=文字:100)|価格 (N=数値:7)|
|:-|:-|:-|
| 0001  | いくら |3000|
| 0002 | ゆめぴりか |2550|
| 0003 | じゃがいも |420|
| 0004 | ジンギスカンキャラメル |680|

【T会員管理】主ｷｰ(PrimaryKey) 会員No を ｲﾝﾃﾞｯｸｽに登録
| 会員No (N=数値:4) | ランク (N=数値:1) | カテゴリ (N=数値:1) | 通知設定 (L=論理:5) |
|:-|:-|:-|:-|
| 0001 | 1 | 3 | True |
| 0002 | 2 | 3 | True |
| 0003 | 3 | 3 | False |

【T購入履歴】主ｷｰ(PrimaryKey) 購入No を ｲﾝﾃﾞｯｸｽに登録
| 購入No (N=数値:5) | 会員No (N=数値:4) | 購入日 (D=日付:####/##/##)|
|:-|:-|:-|
| 00001 | 0001 | 2023/01/02 |
| 00002 | 0001 | 2023/01/15 |
| 00003 | 0002 | 2023/01/02 |
| 00004 | 0003 | 2023/01/05 |

【T購入履歴明細】複合主ｷｰ([Compound]PrimaryKey) 購入No_明細No を ｲﾝﾃﾞｯｸｽに登録
| 購入No (N=数値:5) | 明細No (N=数値:3) | 商品Code (N=数値:4) | 数量 (N=数値:3)|
|:-|:-|:-|:-|
| 00001  | 001 | 0001 | 3 |
| 00001 | 002 | 0002 | 1 |
| 00002 | 001 | 0004 | 2 |
| 00002 | 002 | 0002 | 3 |
| 00003 | 001 | 0001 | 1 |
| 00003 |002 | 0003 | 2 |
| 00004 | 001 | 0001 | 1 |
| 00004 | 002 | 0002 | 1 |
| 00004 | 003 | 0003 | 1 |
| 00004 | 004 | 0004 | 1 |

【W購入履歴明細】複合主ｷｰ([Compound]PrimaryKey) 購入No_明細No を ｲﾝﾃﾞｯｸｽに登録
| 購入No (N=数値:5) | 明細No (N=数値:3) | 商品Code (N=数値:4) | 数量 (N=数値:3)|
|:-|:-|:-|:-|

実際の画面で見たい場合は、[ﾃﾞｰﾀﾘﾎﾟｼﾞﾄﾘ_ｲﾒｰｼﾞ.zip (642.0 kB)](https://esa-storage-tokyo.s3-ap-northeast-1.amazonaws.com/uploads/production/attachments/18698/2023/02/28/116166/b42548ab-f56a-4935-a539-7f92556140fb.zip)

---
---

## 説明概要・全体構成 と フロー

### 説明概要・全体構成

今回作成する紹介するプログラムは4つ(※伝票形式は別記事)

| プログラム名 | 公開名 | 形式 | 役割と構成 |
| :- | :- | :- | :- |
| MembersList | 会員一覧 | リスト(一覧・照会) | ヘッダー  + リスト |
| MembersEdit | 会員登録 | 単票形式 | 詳細(CURD)  |
| PurchaseRecordsList | 購入履歴一覧 | リスト(一覧・照会) | ヘッダー + リスト |
| PurchaseRecordsEdit | 購入履歴登録 |伝票形式 | ヘッダー + 詳細リスト(CURD) |

※ Web Client の 場合、**`プログラム名およびバッチタスク以外のタスクは、英語で表記する必要があります。`**
ここでは説明しませんが、Angularとの兼ね合いによるものです。

---

1つのデータを元に関連する別テーブルのデータに対して、どのようにCURDの処理を定義するか
をタスク構造とその役割、着目点を踏まえながらご紹介していきます。

---

【役割】※ 私たちが暫定的に定めている特徴や機能的なもので、絶対的なものではないです。

- **ヘッダー**
    - 親タスク(最上部のタスク)
    - ページタイトル
    - 遷移元からのパラメータの設定
    - ページ遷移ボタン配置 ex) "戻る"
    - リスト表示へのフィルター機能
    - 新規作成ボタンの配置
- **リスト**
    - ヘッダーの子タスク
    - 一覧表示するデータ (テーブル形式)
    - 修正ボタンの配置
- **詳細**
    - 親タスク ※リストからコールされる別プログラム
    - **単一** の 1:1形式で取得した明細データを表示
    - 登録(更新)ボタンの配置
    - 削除ボタンの配置
- **詳細リスト**
    - 親タスク ※リストからコールされる別プログラム 、ヘッダーに明細行(複数データ)のリストがぶら下がる形
    - **複数** の 1:N形式で取得した明細データを表示
    - 登録(更新)ボタンの配置
    - 削除ボタンの配置

※ 今回は、コールされる側の詳細・詳細リスト側のプログラムは、**`ウィンドウタイプ: O=ｵｰﾊﾞｰﾚｲ`** で
モーダル画面の形をとります。

---

### フロー

今回紹介する構造は、Wec Client との兼ね合いを含めとてもスマートで簡素な構造になっています。

ざっくりというと、
**一覧から選択(or新規作成を実行) ⇒ 詳細(詳細リスト)のプログラムをコール ⇒ そこで新規登録や更新を行う ⇒ 一覧に戻る**
となっております。

こだわれば、1つのプログラムにまとめることも可能ですが、その場合、開発者への依存度が高くなりやすいので、
今回は、常にどの場面でも利用できる可読性の高い構造をご紹介いたします。

+a Web Client 上で確実に動くよう注意すべき点にも触れながら進めてまいりますので、
Web Client へのハードルが少しでも下がると幸いです。

---
---

ここでは、実際に操作・修正する箇所を主に各4つの画面から紹介していきます。

1. タスク特性
1. データビュー
1. ロジック
1. フォーム

---

### 説明本編

**`該当プログラム: MembersList 会員一覧`**
**`該当プログラム: MembersEdit 会員登録`**

---

#### 会員一覧: MembersList

【基本機能・ポイント】
このプログラムは **`表示する`** 役割で、**ユーザーが任意のデータ一覧から探し、次の処理を行うのに選択することができるリスト**です。

【Points】
- 任意の必要なデータ一覧を表示
- 基本的には **`照会モード`** で このプログラム上で修正・削除をすることはない
    - ※ 今回は紹介しませんが、`表示データにフィルターをかける場合は変数を配置してリストにて範囲・位置付を行うので、
        その場合は、修正モードにする必要があります。配置した変数は入力(修正)する為です`

【タスク概要】

- 【親タスク】MembersList ⇒ MembersEdit を 新規作成ﾎﾞﾀﾝからｺｰﾙ
    - 【子タスク】List ⇒ MembersEdit を 編集ﾎﾞﾀﾝからｺｰﾙ
- 【親タスク】MembersEdit ⇒ 登録(更新)・削除ﾎﾞﾀﾝから、その処理を行うﾊﾞｯﾁをｺｰﾙし、処理後にMembersListへ戻る
    - 【子タスク(ﾊﾞｯﾁ)】更新ﾊﾞｯﾁ
    - 【子タスク(ﾊﾞｯﾁ)】削除ﾊﾞｯﾁ

【処理の大まかな流れ】

1. **List** (選択元になるテーブルに一覧表示・選択) ⇒ Editへ
2. **Edit** 選択されたテーブルのキーを元に対象テーブルを範囲指定、詳細となる単一行を取得
3. 入力(編集)を行うために変数項目を設置 ⇒ 取得したデータを代入して初期値として表示 ※新規時には空
4. ボタンを配置し、クリックをトリガーにバッチ処理を実行、更新・削除を行う

---

まず一度全てのタスクを作成し、

1. 全てのタスク特性
1. それぞれのタスクのデータビュー⇒フォーム⇒ロジック

の順でご説明していきます。

---
---

<img width="150" alt="image.png (2.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/d199cb17-671e-4dd5-8831-de8b0391b4d6.png">

**【ﾀｽｸ特性】** MembersList

<img width="538.5" alt="001001.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/b2301529-d3a4-42f5-a4d9-f69a41093253.png">

- 今回は、フィルターなど入力を設置としないので、初期ﾓｰﾄﾞ: Q=照会

<img width="538.5" alt="001002.png (12.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/b9dc9e9b-c18d-4e23-80a8-df7c865a3f1d.png">

- 更新する操作を取らない為、更新確認: No

<img width="538.5" alt="001003.png (13.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/3bea2bb8-8a02-4979-89ba-faa7cf259689.png">

- データを編集するわけではないので、トランザクションは不要
- 対象テーブルのデータが空(リストに表示するものがない)の場合、ページ表示がされないので
   その時には空でも表示するように空のデータビューを許可

<img width="538.5" alt="001004.png (12.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/c51c861a-162a-49f5-99ba-9e56d33f2682.png">

前記通り、照会以外は不要とするので他のﾓｰﾄﾞはオフにする

---

**【ﾀｽｸ特性】** MembersList > List

同様に

<img width="538.5" alt="002001.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/c9824458-a329-445e-8648-f4cad6ee5627.png">
<img width="538.5" alt="002002.png (12.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/90e80ce4-86f0-411e-9a6c-5905734fa4b1.png">
<img width="538.5" alt="002003.png (12.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/c324b331-ef7a-4811-bfbf-487ec120ddd2.png">
<img width="538.5" alt="002004.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/41fc09a1-d602-46db-b9bb-916862c39bc2.png">

---

**【データビュー】** MembersList

<img width="1440" alt="003001.png (54.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/2bde0668-4f42-48df-b2bd-43d5f476edd4.png">

新規作成ﾎﾞﾀﾝだけ配置します。
- 特性: Web Client形式: P=ﾌﾟｯｼｭﾎﾞﾀﾝ で ﾌｫｰﾑ作成時にボタンになるよう定義
- 代入に任意のボタンテキストを入れておきます

---

**【フォーム】** MembersList

<img width="1446" alt="004001.png (99.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/1d727023-1664-4316-b8d5-77d5432a054c.png">

- Web Client では、**タスク名(※バッチを除く)・フォーム名・コントロール名を 英数字 に制限されるので、任意の打ち換えを行います**
    - ※ 命名は自由です。日本語のままだとF8でエラーが表示されます。
- ここで、サブタスクにデータ一覧を表示する予定の子タスク **List** を定義しておきます。

**余談**
デザイン・配置・大きさに関する定義は、全く反映されません。アプリケーション作成時に上下左右の順序は反映されますが、そこからのデザイン・配置・大きさの調整は HTML / CSS の領分になるので、基本的にがざっくりと並べるだけになります。

---

**【ロジック】** MembersList

<img width="1440" alt="005001.png (43.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/943558d3-fde7-418a-88eb-7c0bd8d18349.png">

作成した新規作成ﾎﾞﾀﾝにｸﾘｯｸからの動作を定義します。
- 後程作成する、詳細のﾌﾟﾛｸﾞﾗﾑをｺｰﾙ ※今回、パラメータ不要なので、**ｽｷｯﾌﾟ** を 定義
- 戻ってきたときに、サブフォーム再表示で登録したデータが表示に反映されるように
    - 再表示する対象のサブフォームを定義する(今回では **List** )必要があるのでお忘れなく

---

**【データビュー】** MembersList > List

<img width="1440" alt="006001.png (55.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/c8b4c540-8d00-41c8-8625-e001c6dad827.png">

- 修正ﾎﾞﾀﾝを配置
- 今回一覧表示する M会員 を ﾒｲﾝｿｰｽに定義

---

**【フォーム】** MembersList > List

<img width="1446" alt="007001.png (90.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/b5735f25-f530-42aa-b9dd-57b616fb5b43.png">

- コントロール名を英語に
- テーブルは任意ですが今回は、Webスタイル: HTMLテーブル / スクロールバー:Ture とします

---

**【ロジック】** MembersList > List

<img width="1440" alt="008001.png (88.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/ef493138-11e7-4f5f-8503-1896e0d7f904.png">

作成した編集ﾎﾞﾀﾝにｸﾘｯｸからの動作を定義します。
- 後程作成する、詳細のﾌﾟﾛｸﾞﾗﾑをｺｰﾙ ※ データを選択するのでPrimaryKeyである 会員No を渡すようパラメータ定義
- 戻ってきたときに、ビュー再表示で登録したデータが表示に反映されるようにリ

---

以上で、会員一覧ﾌﾟﾛｸﾞﾗﾑは完了です。
F8でエラー確認の後、
アプリケーション作成から一度作成し、確認してみて下さい。

<img width="429" alt="image.png (30.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/841f222b-4769-441f-9e5b-3fcc6f47df7b.png">

<img width="297.75" alt="image.png (22.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/e10a4ab5-daa9-499e-84fe-03413fd98430.png">

※ Angular で ng serve から ローカルサーバ起動

<img width="609" alt="image.png (14.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/bc9694b1-866a-4c93-9e94-396ab626f7b1.png">

このような画面になっていると思います。

---
---

#### 会員登録: MembersEdit

<img width="153" alt="image.png (3.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/3b7e5db1-fe50-455b-a4cf-5640e4f5c0e0.png">

※  ﾊﾞｯﾁﾀｽｸは、ｻｰﾊﾞｻｲﾄﾞ(Magic内部)での処理のため、英名化は不要です

**【タスク特性】** MembersEdit

<img width="538.5" alt="101001.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/6b871888-c06b-46dc-a7d6-302898afa4ef.png">

- 任意の英名ﾌﾟﾛｸﾞﾗﾑ名
- ﾀｽｸﾀｲﾌﾟ: W=WebClient

<img width="538.5" alt="101002.png (12.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/685610fb-ee07-4290-96de-351c69220a26.png">

- 更新確認は、Yesでも構いません。 ⇒ 更新処理時に確認のダイアログ(ボックス)が出ます
    - 今回は、ロジックにてエラー(条件式による)を定義するので不要と判断して進めます
    - +a Yesにしてエラーを組むと何度もダイアログ(ボックス)が出るのが億劫なため

<img width="538.5" alt="101003.png (12.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/098bf387-f3cf-4642-bf9b-fa7776ab138d.png">

- 登録(更新)・削除の実質の処理はﾊﾞｯﾁで行うので、ﾄﾗﾝｻﾞｸｼｮﾝは不要

<img width="538.5" alt="101004.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/fef0a382-23d9-494d-beec-062fc55115b1.png">

- 入力または閲覧のみ(他は実質の処理がﾊﾞｯﾁ)なので、修正のみ Yes

---

**【タスク特性】** MembersEdit > 更新(ﾊﾞｯﾁ)

<img width="538.5" alt="102001.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/f6382573-8420-4894-a23c-e4307c4b2027.png">

**単一**データを更新するだけなので、終了条件・ﾁｪｯｸ時期を調整します。

<img width="538.5" alt="102002.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/e1923d20-3552-490b-b213-659c7f0ef34a.png">

ﾊﾞｯﾁでは無用なのでNo

<img width="538.5" alt="102003.png (12.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/0cfa260d-e26f-4b54-8398-91f627d4c48a.png">

実際の書き込み処理となるので、ﾄﾗﾝｻﾞｸｼｮﾝを任意のタイミングで定めますが、
ﾊﾞｯﾁに関しては、ﾀｽｸ前の前を選択することがほとんどです。

<img width="538.5" alt="103004.png (12.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/1a056a08-d34a-4815-b6fa-6fbae85be417.png">

削除は行わないので、念のため切っておきます。

---

**【タスク特性】** MembersEdit > 更新(ﾊﾞｯﾁ)

<img width="538.5" alt="103001.png (13.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/d9a1fdab-ff05-4674-8ee6-af8edb8bcb3f.png">

削除ﾊﾞｯﾁなので、初期モード: D=削除 を選択

<img width="538.5" alt="103002.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/0e78a187-1b86-4fc7-831b-a1292c545d88.png">
<img width="538.5" alt="103003.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/2cbe37f7-9fbc-40d4-956b-0e153dbee75c.png">
<img width="538.5" alt="103004.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/ac8c212d-82e9-41b7-be8f-83a21e875b7b.png">

削除のみ行うので、削除をYes

---

**【データビュー】** MembersEdit

<img width="1440" alt="104001.png (58.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/7dc610e7-836e-42bc-a26c-64d376f329a2.png">
<img width="750" alt="image.png (75.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/c6045175-f4e9-4139-8eee-d41a038f1423.png">

- ｺｰﾙされるときのﾊﾟﾗﾒｰﾀ設置・値取得
- 登録(更新)ﾎﾞﾀﾝ・削除ﾎﾞﾀﾝ配置
- パラメータを元に位置付けして照会ﾘﾝｸで登録されている既存データ取得
- 取得したデータを変数に代入して、表示+編集できる項目を定義

※ 下記のテクニカルとなっている部分ですが、
照会ﾘﾝｸをNoでかけることで、項目の型だけ利用することができます
それにより、0から全ての変数項目を打つ手間を省いています。
見分けがつくよう --L.XXX --- であったり変数接頭時に vXXX を採用しています

<img width="1440" alt="104003.png (69.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/027ded17-6b1f-4b9d-9064-600de77c1465.png">

- 新規登録時に会員Noが、重複しないようﾃﾞｰﾀ有無のﾌﾗｸﾞを設置しておきます

---

**【フォーム】** MembersEdit

<img width="569.25" alt="image.png (10.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/e1c5e3bd-ffc6-4316-b2a0-55f47382756f.png">

ｳｨﾝﾄﾞｳﾀｲﾌﾟ: O=ｵｰﾊﾞｰﾚｲ

<img width="1440" alt="105001.png (169.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/6cc34d2c-e63a-47bb-b4da-dbc781b20a11.png">

- 英名化
- ※ Web Client 可視・不可視の表示切替可能です
    - ※ 可視 ⇒ 領分は css、 visible: visible / hidden
- Web Client 有効無効の表示切替可能です
    - ※ 有効 ⇒ 領分は html、 inputﾀｸﾞ属性 : disable

---

**【ロジック】** MembersEdit

<img width="1440" alt="image.png (51.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/456eabfd-1392-497c-a021-522aa4f5626b.png">

---

**【データビュー】** MembersEdit > 更新(ﾊﾞｯﾁ)

<img width="1440" alt="image.png (56.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/a0ca4f9b-13a5-447c-93a4-c60a76b3dea9.png">

<img width="721.5" alt="image.png (67.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/3e0c05f2-5f4f-44e6-98c9-1fb47af42f04.png">

---

**【データビュー】** MembersEdit > 削除(ﾊﾞｯﾁ)

<img width="1440" alt="image.png (48.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/07/116166/9460686f-983c-477e-82d6-faf4ff71ceea.png">

F8でエラー確認の後、
アプリケーション作成から一度作成し、確認してみて下さい。

<img width="403.5" alt="image.png (10.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/78d94aa9-ef33-4b53-a0c4-4374ab1a8a8f.png">

デザインに関しては、html / CSS の 領分となりますのでここでは説明致しません。

---

以上となります。