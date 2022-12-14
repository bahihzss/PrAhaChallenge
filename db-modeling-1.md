# 回答

https://lucid.app/lucidchart/eb53ad40-8d4d-4564-87bc-41c7409d891a/view

## 課題１

![課題１](https://lucid.app/publicSegments/view/f24de0f7-7b9d-41c5-a03e-6c8c115c72e0/image.png)

### 注文表のデータとは？

- セットって言いながら、鉄火丼とか八宝巻がはいってるの気持ち悪い〜
- 注文表を画面に表示するためのデータ
    - 大カテゴリー（セットメニュー or お好みすし）※ 省略
        - 名前
    - 小カテゴリー（セットの種類、皿の種類）※ 省略
        - 名前
        - 単位
    - 商品（セット or 寿司ネタ）
        - 名前
        - 価格
            - 寿司ネタが価格を持っているのか、皿が価格を持っているのか・・・
            - ややこしいので、寿司ネタってことにしとく
- 顧客が送信する注文データ
    - 注文
        - 注文日時
        - お支払い（未・済）
        - お客様情報
            - お名前
            - 電話番号
        - 注文商品
            - 数量
            - オプション
                - わさび抜き  
                
## 課題２

![課題２](https://lucid.app/publicSegments/view/1f476070-d81b-4217-84ea-d941aa81a59b/image.png)

- 元からオプションは自由に追加できるようにしていたので、シャリサイズの対応はスキップ
- 寿司ネタの販売個数カウントのために Product のセット関係を記録するテーブルを追加

## 課題3

### 自分の考えた仕様変更

![課題3-1](https://lucid.app/publicSegments/view/a436218b-9cb6-4afd-bbe8-96243490707d/image.png)

```
コーンのマヨネーズを抜けるようにしたいので、商品ごとに可能なオプションを変えられるようにしたい
```

Product に対して設定可能なオプションを表現するテーブルを追加

### Yamaguchi さんからもらった仕様変更

![課題3-2](https://lucid.app/publicSegments/view/2524e42c-8252-4664-9f07-ef2865d7aa95/image.png)


```
お得意様割引：特定のお得意様のみ20%割引にしたい。
```

元々、都度電話番号を入力するようなシステムを想定していたので、

1. 別でお得意様テーブルを作って、電話番号から参照すればお得意様かどうか判定できるように変更。
2. お得意様と判定された場合は、該当するお得意様レコードから取得した割引率で注文の合計金額から割引額を計算し、注文レコードに記録する

### Furuno さんからもらった仕様変更

![課題3-3](https://lucid.app/publicSegments/view/b12eefbb-a8d0-42d7-b5d7-146fbf8cb103/image.png)

```
品切れになってしまったセットメニューやお好みすしが注文されないようにしたい。
```

Product に在庫があるかどうかの isOutOfStock フラグを追加
