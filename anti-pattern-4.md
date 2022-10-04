# 課題１

https://teitei-tk.hatenablog.com/entry/2020/11/30/130000

* 「隣接リスト」のパターンになる
* 一番の問題点は階層が深い場合、単一のSQLで取得することが難しいこと

# 課題２

このままでも、 WITH RECURSIVE が使える DB ならいいんじゃないかと思ったりしている。
(MySQL 8+,PostgreSQL 8.4+が対応)

```sql
WITH RECURSIVE TreeItems AS (
  SELECT
    id,
    parentMessageId,
    text,
    CONVERT(id, CHAR) AS path,
    1 as depth
  FROM Message WHERE id = 1
  UNION ALL
  SELECT
    Child.id,
    Child.parentMessageId,
    Child.text,
    CONCAT(Parent.path, '/', CONVERT(Child.id, CHAR)) AS path,
    Parent.depth + 1 AS depth
  FROM TreeItems AS Parent
    JOIN Message AS Child ON Parent.id = Child.parentMessageId AND Parent.depth < 3
)
SELECT * FROM TreeItems;
```

| id | parentMessageId | text               | path  | depth |
|----|-----------------|--------------------|-------|-------|
|  1 | NULL            | Root Message       |     1 |     1 |
|  2 |               1 | Message on Layer 2 |   1/2 |     2 |
|  3 |               1 | Message on Layer 2 |   1/3 |     2 |
|  4 |               2 | Message on Layer 3 | 1/2/4 |     3 |
|  5 |               2 | Message on Layer 3 | 1/2/5 |     3 |
|  6 |               3 | Message on Layer 3 | 1/3/6 |     3 |
|  7 |               3 | Message on Layer 3 | 1/3/7 |     3 |

使えないのであれば閉包テーブルかなと思います。

WITH RECURSIVE の方が閉包テーブルよりいいと思う理由
* SQLの内容が階層を辿って結合なので、閉包テーブルより直感的
* 付け替えの時も自分の親 ID を変更するだけでいいので簡単

WITH 句は仮想テーブルをメモリ上に作成するので、大量のデータを扱うのには向いていない  
とのことなので、 TreeItems の内容が大きくなるようなユースケースではあんまりよくないかも

# 課題３

ツリー構造を実現しようとした時に発生する。
* 販売実績計算プログラムの集計単位の管理（複数の結果をまとめて計算したり、まとめて計算した結果をさらにまとめたり）
