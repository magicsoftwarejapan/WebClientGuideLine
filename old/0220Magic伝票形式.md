# 【説明書】伝票形式・1:N形式

## Links

- [単票形式・1:1形式](https://ezostyle.esa.io/posts/544)
- [伝票形式・1:N形式](https://ezostyle.esa.io/posts/554)

---

## Target

- Magicはすでに知っている方
- 新たに Wec Client を利用しようとしている方
    - 特にスタートアップとして、シンプルで可読性の高いプログラムの基本構造を知りたい方
    - ｵﾝﾌﾟﾚ / RIA との 命令や定義、動作の差に不安がある方

---
---

## INDEX

※この記事は、**伝票形式** についての説明になります。

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
    - 購入履歴一覧 PurchaseRecordList
    - 購入履歴登録 PurchaseRecordEdit
- **【Angular(HTML)】** テーブル行を修正可能にする 


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

※この記事は、**伝票形式** についての説明になります。

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
| PurchaseRecordList | 購入履歴一覧 | リスト(一覧・照会) | ヘッダー + リスト |
| PurchaseRecordEdit | 購入履歴登録 |伝票形式 | ヘッダー + 詳細リスト(CURD) |

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

**`該当プログラム: PurchaseRecordList 購入履歴一覧`**
**`該当プログラム: PurchaseRecordEdit 購入履歴登録`**

---

#### 購入履歴一覧: PurchaseRecordList

【基本機能・ポイント】
このプログラムは **`表示する`** 役割で、**ユーザーが任意のデータ一覧から探し、次の処理を行うのに選択することができるリスト**です。

【Points】
- 任意の必要なデータ一覧を表示
- 基本的には **`照会モード`** で このプログラム上で修正・削除をすることはない
    - ※ 今回は紹介しませんが、`表示データにフィルターをかける場合は変数を配置してリストにて範囲・位置付を行うので、
        その場合は、修正モードにする必要があります。配置した変数は入力(修正)する為です`



【タスク概要】

- 【親タスク】PurchaseRecordList ⇒ PurchaseRecordEdit を 新規作成ﾎﾞﾀﾝからｺｰﾙ
    - 【子タスク】List ⇒ PurchaseRecordEdit を 編集ﾎﾞﾀﾝからｺｰﾙ
- 【親タスク】PurchaseRecordEdit ⇒ 登録(更新)・削除ﾎﾞﾀﾝから、その処理を行うﾊﾞｯﾁをｺｰﾙし、処理後にPurchaseRecordListへ戻る
    - 【子タスク(ﾊﾞｯﾁ)】ﾜｰｸ作成
        - 【ﾊﾞｯﾁ】明細ﾜｰｸ削除
        - 【ﾊﾞｯﾁ】明細ﾜｰｸ作成
    - 【子タスク(ﾊﾞｯﾁ)】更新処理
        - 【ﾊﾞｯﾁ】ﾍｯﾀﾞ削除
        - 【ﾊﾞｯﾁ】明細削除
        - 【ﾊﾞｯﾁ】ﾍｯﾀﾞ更新
        - 【ﾊﾞｯﾁ】明細更新
    - 【子タスク(ﾊﾞｯﾁ)】行追加

【処理の大まかな流れ】

1. **List** (選択元になるテーブルに一覧表示・選択) ⇒ Editへ
2. **Edit** 選択されたテーブルのキーを元に対象テーブルを範囲指定 **しますが、**
3. 単一と異なり複数行分の変数項目が必要なので、バッチを利用し複数行取得
4. メモリ―テーブルを利用し、その取得データをそのまま初期値として書む
5. メモリ―テーブルをメインソースに選択し、そのまま入力(修正)できるようにする
6. ボタンを配置し、クリックをトリガーにバッチ処理を実行、更新・削除を行う

---

まず一度全てのタスクを作成し、

1. 全てのタスク特性
1. それぞれのタスクのデータビュー⇒フォーム⇒ロジック

の順でご説明していきます。

---
---

<img width="152.25" alt="image.png (2.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/512f6489-1005-4b58-8c7b-bbb414f3aa59.png">


**【ﾀｽｸ特性】** PurchaseRecordList

<img width="538.5" alt="1001.png (13.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/5d9ae313-8785-43c0-9f81-c05be18cc2f9.png">

- 今回は、フィルターなど入力を設置としないので、初期ﾓｰﾄﾞ: Q=照会

<img width="538.5" alt="1002.png (13.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/d54513a7-b153-4be7-bfe6-1c5f68d76f18.png">

- 更新する操作を取らない為、更新確認: No

<img width="538.5" alt="1003.png (13.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/46675e5d-0e43-4afc-918a-86781b65a23c.png">

- データを編集するわけではないので、トランザクションは不要
- 対象テーブルのデータが空(リストに表示するものがない)の場合、ページ表示がされないので
   その時には空でも表示するように空のデータビューを許可

<img width="538.5" alt="1004.png (13.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/6397f993-13f2-4df2-aae2-7de8b0a25833.png">

前記通り、照会以外は不要とするので他のﾓｰﾄﾞはオフにする

---

**【ﾀｽｸ特性】** PurchaseRecordList > List

同様に

<img width="538.5" alt="1101.png (13.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/e84a8c34-9128-4095-98bd-30e53996784a.png">
<img width="538.5" alt="1102.png (13.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/8b9a3f35-ab30-434c-9fcd-2a153d7e92fc.png">
<img width="538.5" alt="1103.png (13.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/bf7c9788-7d64-490d-a1ed-96e7fd107eaa.png">
<img width="538.5" alt="1104.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/c3a6768d-810a-49b0-9ce2-bb53e4b2927b.png">


---

**【データビュー】** PurchaseRocordList

<img width="1440" alt="1201.png (55.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/d1d71b83-c90a-4dd4-896e-2451d19a889c.png">

新規作成ﾎﾞﾀﾝだけ配置します。
- 特性: Web Client形式: P=ﾌﾟｯｼｭﾎﾞﾀﾝ で ﾌｫｰﾑ作成時にボタンになるよう定義
- 代入に任意のボタンテキストを入れておきます

---

**【フォーム】** PurchaseRocordList

<img width="1446" alt="1301.png (82.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/20cef80a-1168-40b5-b9c3-18fe2e7eb5fb.png">

- Web Client では、**タスク名(※バッチを除く)・フォーム名・コントロール名を 英数字 に制限されるので、任意の打ち換えを行います**
    - ※ 命名は自由です。日本語のままだとF8でエラーが表示されます。
- ここで、サブタスクにデータ一覧を表示する予定の子タスク **List** を定義しておきます。

**余談**
デザイン・配置・大きさに関する定義は、全く反映されません。アプリケーション作成時に上下左右の順序は反映されますが、そこからのデザイン・配置・大きさの調整は HTML / CSS の領分になるので、基本的にがざっくりと並べるだけになります。

---

**【ロジック】** PurchaseRecordList

<img width="1440" alt="image.png (51.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/f322e163-ac54-4bd8-a424-396968e8328b.png">

作成した新規作成ﾎﾞﾀﾝにｸﾘｯｸからの動作を定義します。
- 後程作成する、詳細リストのﾌﾟﾛｸﾞﾗﾑをｺｰﾙ ※今回、パラメータ不要なので、**ｽｷｯﾌﾟ** を 定義
- 戻ってきたときに、サブフォーム再表示で登録したデータが表示に反映されるように
    - 再表示する対象のサブフォームを定義する(今回では **List** )必要があるのでお忘れなく

---

**【データビュー】** PurchaseRecordList > List

<img width="1440" alt="1401.png (60.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/0ce2e444-e3d9-41b2-a670-20b1655b828e.png">

- 修正ﾎﾞﾀﾝを配置
- 今回一覧表示する T購入履歴 を ﾒｲﾝｿｰｽに定義
- 氏名が欲しいので、1:1 で M会員 から照会ﾘﾝｸ を定義して取得

---

**【フォーム】** PurchaseRecordList > List

<img width="1446" alt="1501.png (99.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/d80963d3-a49d-4208-bb92-bc28629735f8.png">

- コントロール名を英語に
- テーブルは任意ですが今回は、Webスタイル: HTMLテーブル / スクロールバー:Ture とします

---

**【ロジック】** PurchaseRecordList > List

<img width="1440" alt="1601.png (98.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/874b9a7f-bb1a-4af2-bb2e-40b167e2b137.png">

作成した編集ﾎﾞﾀﾝにｸﾘｯｸからの動作を定義します。
- 後程作成する、詳細リストのﾌﾟﾛｸﾞﾗﾑをｺｰﾙ ※ データを選択するのでPrimaryKeyである 購入No を渡すようパラメータ定義
- 戻ってきたときに、ビュー再表示で登録したデータが表示に反映されるように

---

以上で、購入履歴一覧ﾌﾟﾛｸﾞﾗﾑは完了です。
F8でエラー確認の後、
アプリケーション作成から一度作成し、確認してみて下さい。

<img width="377.25" alt="image.png (33.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/b5d5db86-5529-4820-8927-e1555e760315.png">

<img width="297.75" alt="image.png (23.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/629b7713-f037-468d-8066-d0a08d067ff3.png">

※ Angular で ng serve から ローカルサーバ起動

<img width="263.25" alt="image.png (17.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/02/28/116166/0922051c-ed55-476d-b5c6-d34aefbe22fd.png">

このような画面になっていると思います。

---
---

#### 購入履歴登録: PurchaseRecordEdit

<img width="152.25" alt="image.png (8.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/5a1c8bb2-6621-41dd-a5a3-e029548a4669.png">

※  ﾊﾞｯﾁﾀｽｸは、ｻｰﾊﾞｻｲﾄﾞ(Magic内部)での処理のため、英名化は不要です

**【タスク特性】** PurchaseRecordEdit

<img width="538.5" alt="2001.png (13.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/c35863fa-e378-4220-a05b-d8b88aaa0773.png">

- 任意の英名ﾌﾟﾛｸﾞﾗﾑ名
- ﾀｽｸﾀｲﾌﾟ: W=WebClient

<img width="538.5" alt="2002.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/b27a7cc4-f630-40b2-8f4a-7dc933a3d9b1.png">

- 更新確認は、Yesでも構いません。 ⇒ 更新処理時に確認のダイアログ(ボックス)が出ます
    - 今回は、ロジックにてエラー(条件式による)を定義するので不要と判断して進めます
    - +a Yesにしてエラーを組むと何度もダイアログ(ボックス)が出るのが億劫なため

<img width="538.5" alt="2003.png (13.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/562397e6-2177-4fd1-aa9e-ca7a793889d1.png">

- 登録(更新)・削除の実質の処理はﾊﾞｯﾁで行うので、ﾄﾗﾝｻﾞｸｼｮﾝは不要
- 何のため表示データが空の時でも表示されるように...

<img width="538.5" alt="2004.png (13.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/135a6f6c-693b-4908-81fc-33757b9e3941.png">

- 入力または閲覧のみ(他は実質の処理がﾊﾞｯﾁ)なので、修正のみ Yes

---

**【タスク特性】** PurchaseRecordEdit > List

<img width="538.5" alt="2101.png (13.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/992b62bb-00d9-4b68-96ef-87ee7ebe307b.png">

- Concept として、メモリテーブルに本テーブルをコピした上で、それを修正
更新時にそのメモリ―テーブルから本テーブルに書き込む流れです
よって、ここではメモリデータをメインソースに据えて、直接編集します。

<img width="538.5" alt="2102.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/dff112ee-6c3e-48e2-8c4a-904b95fb67eb.png">

- 更新実行時にエラーで対応するので、更新確認不要

<img width="538.5" alt="2103.png (14.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/122c5262-1fd5-4045-a6c2-ebd5d780f683.png">

- 形として、メインソースを修正しているのでトランザクションをオープン
- レコードごとに実装する 行削除ﾎﾞﾀﾝ を適正に動かすために、レコード前の前

<img width="538.5" alt="2104.png (12.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/01/116166/51688c20-69c8-426b-999e-82864bca2e08.png">

---

**【データビュー】** PurchaseRecordEdit

<img width="1440" alt="2201.png (82.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/687fd10e-b277-468b-b78b-e7ef9faab384.png">

<img width="750" alt="image.png (78.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/30d0dc6a-02fb-499e-8d79-3c716781cc6e.png">

- ｺｰﾙされるときのﾊﾟﾗﾒｰﾀ 設置・値取得
- 登録(更新)ﾎﾞﾀﾝ・削除ﾎﾞﾀﾝ・削除ﾎﾞﾀﾝ配置
- パラメータを元に位置付けして照会ﾘﾝｸで登録されている既存データ取得
- 取得したデータを変数に代入して、表示+編集できる項目を定義
- ﾊﾟﾗﾒｰﾀが0の時、すなわち新規作成時には購入Noを自動的に既存データの最大値+1を設定するように今回は定義

---

**【フォーム】** PurchaseRecordEdit

<img width="540.75" alt="2300.png (11.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/7feb8a53-6bcc-4ae8-a856-87f7755d3a42.png">

ｳｨﾝﾄﾞｳﾀｲﾌﾟ: O=ｵｰﾊﾞｰﾚｲ

<img width="1446" alt="2301.png (123.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/1b983b9e-0ab4-407b-aa7c-5dc8d6250cf0.png">

- 英名化
- 今回、購入Noは編集できないようにしている作りなので、修正許可を切っておきます
- ※ Web Client 可視・不可視の表示切替可能です
    - ※ 可視 ⇒ 領分は css、 visible: visible / hidden
- Web Client 有効無効の表示切替可能です
    - ※ 有効 ⇒ 領分は html、 inputﾀｸﾞ属性 : disable

---

**【ロジック】** PurchaseRecordEdit

<img width="1440" alt="image.png (74.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/cb17399e-eea4-4e06-aae5-fd6c9f2cb763.png">

<img width="750" alt="image.png (78.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/30d0dc6a-02fb-499e-8d79-3c716781cc6e.png">

- 更新時に
    - 会員Noが埋まっていなければエラー
    - 既存ﾃﾞｰﾀから会員Noが変更されている時は確認

---

**【データビュー】** PurchaseRecordEdit  >List

<img width="1440" alt="image.png (70.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/88422145-d57f-4c84-af3d-176d486c7784.png">

<img width="750" alt="image.png (81.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/d82ba596-b11f-49e7-b7b9-9e37ece9c6d6.png">

- 行削除ﾎﾞﾀﾝを設置
- 商品名・値段を表示しておきたいので照会ﾘﾝｸ
- 不明な商品Codeを入力・登録できないようにデータをチェックするフラグを建てておく

---

**【フォーム】** PurchaseRecordEdit  >List

<img width="1446" alt="2401.png (113.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/b3d9940f-d01c-41d2-8edc-0a02962017ff.png">

- 商品名・値段は修正不要なので修正許可:false
    照会ﾘﾝｸなので、別にしなくてもいいのですが...

---

**【ロジック】** PurchaseRecordEdit > List

<img width="1440" alt="image.png (58.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/67e9a4b6-ef4c-441e-b87c-e137d27dea41.png">

---

**【タスク特性】** ﾜｰｸ作成

<img width="152.25" alt="image.png (2.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/b116149c-124a-4109-b973-37a60c6aa2d7.png">

**ﾜｰｸ作成** 自体には大きな役割はありません、が、変数を利用したﾙｰﾌﾟ処理の実装など
バッチタスク内でも親タスク子タスクの関係を設け、子タスクにそれぞれの処理を采配する方が
構成しやすいかと思われます

<img width="538.5" alt="3001.png (13.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/3f3a5abb-1727-4d25-b9ac-2bc9be02f655.png">

- このタスクで特に何かをするわけではないので、一度しか起動しないようにします

<img width="538.5" alt="3002.png (13.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/307d6f82-8d4f-44db-91cf-71f13483e4b6.png">

<img width="538.5" alt="3003.png (13.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/cc62fecb-8f03-4cd0-9de4-0b220b9018a8.png">

- このﾊﾞｯﾁ自体ではレコードへの処理は無いので、トランザクションは不要

<img width="538.5" alt="3004.png (12.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/76647320-557b-47eb-87f9-9d63a9213e69.png">

---

**【データビュー】** ﾜｰｸ作成

<img width="1440" alt="image.png (66.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/9c3429dc-dd46-4e53-bc2e-99a58141f051.png">

- レコードに今回は触れることがないので、何もないです
- 余談: ループ数やN:M関係でのループ等を行う時には、ﾒｲﾝｿｰｽや項目を定義し次のロジック部に繋ぐイメージです

---

**【ロジック】** ﾜｰｸ作成

<img width="1440" alt="image.png (55.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/0ef6c80c-1db6-4a33-8b93-98131b47b2e0.png">

- それぞれの処理をコールします
    今回だと、１度しか処理しないようにタスク特性を設定しているので、それぞれのバッチが一回ずつ実行されます

---

**【タスク特性】** ﾜｰｸ作成 > ﾜｰｸ削除

<img width="538.5" alt="3101.png (14.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/04ac94a3-252a-4867-a78b-d19c45ac2f31.png">

- ﾒﾓﾘﾃｰﾌﾞﾙを空 ⇒ 既存ﾃﾞｰﾀがあれば書き込んで編集用のﾒﾓﾘﾃｰﾌﾞﾙを作成していきます
    なので、このﾊﾞｯﾁでは削除を行うので D=削除 を選択

<img width="538.5" alt="3102.png (13.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/ef3a188b-3780-42ac-8ee6-4fbb00cacd4b.png">

<img width="538.5" alt="3103.png (14.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/664b8693-c29e-4692-8d34-e1f1450a4efa.png">

<img width="538.5" alt="3104.png (13.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/9b0d8c1f-d9ae-4ff9-a597-592a69176ad0.png">

---

**【データビュー】** ﾜｰｸ作成 > ﾜｰｸ削除

<img width="1440" alt="3201.png (57.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/2514cc9a-3132-4015-b224-33d4eb2ea6bc.png">

- 削除するﾒﾓﾘﾃｰﾌﾞﾙをメインソースに指定

このﾊﾞｯﾁは以上です。漠然と削除するだけなのでロジックも今回はありません

---

**【タスク特性】** ﾜｰｸ作成 > ﾜｰｸ作成

<img width="538.5" alt="3301.png (13.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/f90f0b2d-b780-495e-89da-79fb2c5c7ed3.png">
<img width="538.5" alt="3302.png (13.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/fcd795ca-dd0b-47f0-8102-8ed3b1bd6cad.png">
<img width="538.5" alt="3303.png (14.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/8ea13a52-2759-4456-9164-704419355cab.png">

---

**【データビュー】** ﾜｰｸ作成 > ﾜｰｸ作成

<img width="1440" alt="3401.png (61.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/c0fb5d7b-e228-4618-a9e0-fb9979d0c1fc.png">

<img width="750" alt="3501.png (78.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/4fe6ad16-43d4-45b4-846b-10f81eab48e8.png">

- 既存データが入っているT購入履歴明細 を 範囲付けしてレコードを回し、ﾒﾓﾘﾃｰﾌﾞﾙに書き込みます
- 新規作成時(pi購入No=0)は、既存ﾃﾞｰﾀが無いので、if文で対応します

ﾊﾞｯﾁﾀｽｸ: ﾜｰｸ作成 以上

---

**【タスク特性】** 更新処理

<img width="538.5" alt="3601.png (14.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/d7702609-4620-49d1-a9df-2d9953ad26c6.png">

- ﾜｰｸ作成と同じイメージです。なので、一度だけ実行します
    1つで統括して、それぞれの処理をぶら下げる形にしてきます

<img width="538.5" alt="3602.png (13.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/f611a616-a2fe-4cba-b54f-971f5b1a5384.png">

今回は、ﾚｺｰﾄﾞ削除・更新に関わるので、ﾄﾗﾝｻﾞｸｼｮﾝ開始を指定します

<img width="538.5" alt="3603.png (13.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/190597af-0f6e-437c-ae39-05db55dd2fe7.png">

---

**【データビュー】** 更新処理

<img width="1440" alt="3604.png (52.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/c323b8d4-68b3-4b25-9277-022243c50660.png">

<img width="750" alt="3606.png (73.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/8a2b8b75-9dd8-40d2-a446-583e0762c8ac.png">

- 展開スピード・トランザクションの兼ね合いで、対象テーブルに対してD=宣言を行います
- 削除のみ / 削除 ⇒ 更新(書込) の 2ﾊﾟﾀｰﾝがあるので、コール時に分岐の判定をするﾊﾟﾗﾒｰﾀを設定

---

**【ロジック】** 更新処理

<img width="1440" alt="3605.png (53.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/02/116166/92c4f659-ebd9-4ff9-9d3d-cd72a90a6c7a.png">

- データビューで述べていた、分岐(削除 / 削除 ⇒ 更新) を定義します

---

**【タスク特性】** 更新処理 > ﾍｯﾀﾞ削除

1:N の 1 のﾃﾞｰﾀを範囲付け、削除します

<img width="538.5" alt="image.png (15.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/78550cc8-e279-4d4f-98c5-a17d3b860589.png">

<img width="538.5" alt="image.png (14.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/98a52ce1-5aa3-48e4-8f19-5d43aa91ff27.png">

<img width="538.5" alt="image.png (14.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/f5ed4930-e17e-4740-8bad-4fe53197d824.png">

<img width="538.5" alt="image.png (14.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/ad887793-6414-4e58-9604-b86cb16f6cdf.png">

---

**【データビュー】** 更新処理 > ﾍｯﾀﾞ削除

<img width="1440" alt="image.png (88.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/171e3dea-3384-4714-917b-8acb0806e2c8.png">

・ロジックは必要ないです

---

**【タスク特性】** 更新処理 > 明細削除

原理は同じ、1:N の N のﾃﾞｰﾀを範囲付け、削除します

<img width="538.5" alt="image.png (14.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/6f1e6f73-8d90-420f-8b6e-ae27038dbbd8.png">

<img width="538.5" alt="image.png (15.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/21f8adc7-e6af-4b9e-9ecd-3625a86d2a29.png">

<img width="538.5" alt="image.png (15.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/ddf5308c-d8b8-4b27-9354-384e43250b3f.png">

<img width="538.5" alt="image.png (14.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/1190fd67-3437-4bfe-a24d-82c02cb682c8.png">

---

**【データビュー】** 更新処理 > ﾍｯﾀﾞ削除

<img width="1440" alt="image.png (88.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/20dda533-1147-454a-832c-3fbdbea8c2ae.png">

・ロジックは組んでいません

---

**【タスク特性】** 更新処理 > ﾍｯﾀﾞ更新

PurchaseRecordList で 変数化に差し込んでいる登録する値をここで実際に登録します

<img width="538.5" alt="image.png (14.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/80063bf0-6a4f-47bd-a19d-717e1143e070.png">

<img width="538.5" alt="image.png (14.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/f7113bb9-ad17-41b2-804b-0af99d7d4226.png">

<img width="538.5" alt="image.png (14.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/7b7e13b4-4fae-4b80-b430-0fedae9ef069.png">

<img width="538.5" alt="image.png (14.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/0d246ac7-5801-45f5-8006-9254c7e30dbd.png">

---

**【データビュー】** 更新処理 > ﾍｯﾀﾞ更新

<img width="1440" alt="image.png (180.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/bfc84e6f-56d8-416f-81d8-0d6c81bdb9d6.png">

・削除ﾊﾞｯﾁで消して、登録しているので厳密にいうと更新処理ではありませんが、
新規登録を含め、一律して登録処理のため多少処理速度も速く採用しています
・ロジックは組んでいません

---

**【タスク特性】** 更新処理 > 明細更新

W購入履歴明細を回して、そのまま登録していく形になります

<img width="538.5" alt="image.png (14.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/b2187e3f-2ed8-41be-a932-bfe65e4b9b97.png">

<img width="538.5" alt="image.png (14.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/2fef3b3a-6910-421a-8487-53c42885c1c0.png">

<img width="538.5" alt="image.png (14.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/3624f966-63dc-4299-9b4e-7b7ea9b93818.png">

<img width="538.5" alt="image.png (14.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/dee233a5-e7af-47dc-b7f6-07088c1ebbb0.png">

---

**【データビュー】** 更新処理 > 明細更新

<img width="1440" alt="image.png (154.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/b4bd71b2-5bcf-479d-8703-4772e5b48d03.png">

・位置付けの兼ね合いもあるので、書出を利用しています
・ロジックは組んでいません

---

**【タスク特性】** 更新処理 > 明細更新

ﾜｰｸに行追加を施します。これにより、PrimaryKeyを調整すれば複数行を連続して追加することが可能になります。
※いない場合には、ｲﾝﾃﾞｯｸｽ重複ｴﾗｰをおこします

<img width="538.5" alt="image.png (13.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/a55355a6-f5c2-4d3f-a90c-9e91655ab4f5.png">

<img width="538.5" alt="image.png (14.0 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/05e92c01-f19d-488f-bd30-99e8a3abcec4.png">

<img width="538.5" alt="image.png (13.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/3cc20ffe-1161-446a-a59b-28b4d99829e5.png">

<img width="538.5" alt="image.png (13.7 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/4885bf7a-e0ad-4708-8a2f-1e2f06a99214.png">

---

**【データビュー】** 更新処理 > 明細更新

<img width="1440" alt="image.png (166.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/5b9694a6-6901-40bb-8c03-c08223767612.png">

・インデックスが明細Noとなっているので、現状の最大値+1をすることで重複を回避するようにしています
・ロジックは組んでいません

以上で作成は終わりです。

---

F8でエラー確認の後、
アプリケーション作成から一度作成し、確認してみて下さい。

PurchaseRecordList 新規作成 or 更新ﾎﾞﾀﾝより

<img width="238.5" alt="image.png (15.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/a24fe90c-0bc2-43b2-a719-b48cd2c55ce8.png">

この用に表示されると思います。

デザインに関しては、html / CSS の 領分となりますのでここでは説明致しません。

**が、このままではリスト部分入力できない！っとなりますよね**
その部分について説明します

---

## **【Angular(HTML)】** テーブル行を修正可能にする

作成したWebアプリケーションをVSCodeから開きます

<img width="387" alt="image.png (32.1 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/1d22d7fa-3e96-4cbe-aa6e-374449aeccf4.png">

自分で設定したMagic上の任意のフォルダ階層に合わせて、プログラムのファイルが生成されています

<img width="186.75" alt="image.png (8.4 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/4fab9a82-4c48-4910-988c-a5d5625f9c67.png">

今回修正を可能にしたいテーブルは、PurchaseRecordEdit の List なので
PurchaseRecordEdit > List の List.component.html を展開します

<img width="192.75" alt="image.png (6.5 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/8c8d75d3-063a-4a8d-a91a-6030bf530ae0.png">

<img width="1440" alt="image.png (232.3 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/916e733d-cf36-4184-8616-da94034b1339.png">

今回修正をかける場所は、商品Code(ProductCode)・数量(Amount)

<img width="1440" alt="image.png (208.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/2f4e1aa2-44f5-40e1-b6e7-feb38af7249d.png">

・*ngIf=... ⇒ 取ります
<img width="1440" alt="image.png (194.9 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/8a447d9e-de5a-46c6-becc-a8fab3be68ec.png">
・`<mgerror><label>` ⇒ 取ります
<img width="1440" alt="image.png (198.8 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/07bdcf2c-f2f8-4b29-a369-3dd72cfbec7f.png">
これで制限のない単純に入力ができる `<input>` となりました
<img width="1440" alt="image.png (193.6 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/3de5546f-283c-4a4d-abc7-26c663160737.png">
Amount も 同様に
<img width="1440" alt="image.png (184.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/fe87a6e9-4b04-4443-9f4f-a07d28b29b17.png">

※ 余談
O=ｵｰﾊﾞｰﾚｲのﾌﾟﾛｸﾞﾗﾑは初期値だと小さくて見づらいので、端的なところだと tsﾌｧｲﾙからサイズ調整が行えます...
※より洗練したものにしたい場合には、やはりhtml / css による調整が必要となります

300px ⇒ 1000px に打ち換えてみました

<img width="1440" alt="image.png (199.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/b5adec2a-1994-4f19-ae1e-06291433576f.png">

表示してみましょう。

<img width="1440" alt="image.png (73.2 kB)" src="https://img.esa.io/uploads/production/attachments/18698/2023/03/06/116166/9553d517-17e1-4918-b657-ba83b258bba7.png">

修正可能となり、任意の入力から登録できるようになりました

---

以上となります。