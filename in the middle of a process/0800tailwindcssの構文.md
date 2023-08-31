# 大枠シリーズ

![](imgs/2023-06-22-17-36-14.png)

```
 <div
   [magic]="mgc.Login"
   class="h-screen w-screen flex justify-center items-center"
 >
```

## h-screen：ビューポートの高さ

h-screen 要素をビューポートの高さ全体に広げるために使用します。

## w-screen：ビューポートの幅

w-screen 要素をビューポートの幅全体に広げるために使用します。

## flex：要素を並べる指定

以下の’justify-center’と’items-center’を書く前に入れることで記述を動かします。

## justify-center：中心

justify-center コンテナの主軸の中心に沿って項目を位置揃えするために使用します。

## items-center：中心

items-center コンテナの交差軸の中心に沿ってアイテムを整列させるために使用します。

# 間隔シリーズ

![](imgs/2023-06-22-18-08-03.png)

```
   <div class="px-8 pt-6 pb-8 mb-4">
```

## px-{size}：水平方向のパディングを追加する

ユーティリティを使用して要素の水平方向のパディングを制御します px-{size}。

### p-{size}：すべての側面にパディングを追加する

ユーティリティを使用して、要素のすべての側面のパディングを制御します p-{size}。

### py-{size}：すべての側面にパディングを追加する

ユーティリティを使用して要素の垂直方向のパディングを制御します py-{size}。

## pt-{size}/pb-{size}：上下方向のパディングを追加する

## mt-{size}/mb-{size}：上下左右にパディングを追加する

ユーティリティを使用して要素の上下左右のパディングを制御します px-{size}。

### パディングの使い方と同じ

# 位置シリーズ

![](imgs/2023-06-22-18-53-24.png)

```
     <div class="flex items-center justify-between">
       <button mat-raised-button color="primary" [magic]="mgc.BLogin">
         {{ mg.getValue(mgc.BLogin) }}
       </button>
     </div>
```

## justify-between：間のスペース

justify-between 各アイテム間に等しい量のスペースができるように、コンテナの主軸に沿ってアイテムを位置揃えするために使用します。

# 色

## color="カラー名"：色の名前を付ける

Tailwind は、デフォルトでリテラルの色名(赤、緑など)と数値スケール(50 が明るく、900 が暗い)を使用します。私たちは、これがほとんどのプロジェクトにとって最良の選択であると考えており、primary や danger のような抽象名を使用するよりも保守が簡単であることがわかりました。
