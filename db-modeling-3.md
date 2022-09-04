# 回答

![https://lucid.app/publicSegments/view/38d43c72-176e-4292-8069-83edf60161b0/image.png](https://lucid.app/publicSegments/view/38d43c72-176e-4292-8069-83edf60161b0/image.png)

## 差分で管理する

https://qiita.com/from_kyushu/items/f9f2b7f5fa85cfb695dd
こんなのを使うと良さそう

## ディレクトリ構造（ツリー構造）どうやってつくる？

→ 閉包テーブルっていうのが良さそう

### 閉包テーブル

コンテンツと親子関係を別のテーブルで管理する方法。
よくできてる・・・。

![https://lucid.app/publicSegments/view/36dfe61e-d199-4273-b2a3-fcfb9b8fc04b/image.png](https://lucid.app/publicSegments/view/36dfe61e-d199-4273-b2a3-fcfb9b8fc04b/image.png)

[https://teitei-tk.hatenablog.com/entry/2020/11/30/130000](https://teitei-tk.hatenablog.com/entry/2020/11/30/130000)

[https://qiita.com/uchinami_shoichi/items/5fa52f340003107d46c1#closure-table](https://qiita.com/uchinami_shoichi/items/5fa52f340003107d46c1#closure-table)

[https://kentama.hatenablog.com/entry/2016/07/30/221603](https://kentama.hatenablog.com/entry/2016/07/30/221603)

- DirectoryPath の各行には先祖 `ancestorId` / 子孫 `descendantId` を共有するノードの組み合わせを保存する。
- これはツリー状の離れた位置にあるノードも含めたすべてのノードが対象になる。
- また、各行のノードには自分自身を参照するレコードも追加する。
- `pathLength` は階層の差を表す。

### データ例

例えば、以下のようなディレクトリ構造がある場合

![https://lucid.app/publicSegments/view/d0c7c08a-fbc7-4fa9-9034-83987da0a69c/image.png](https://lucid.app/publicSegments/view/d0c7c08a-fbc7-4fa9-9034-83987da0a69c/image.png)

各テーブルのレコードは以下のようになる

#### **Directory**

| id | name |
| --- | --- |
| root | ルート |
| tech | 技術記事 |
| manual | マニュアル |
| phone | 電話対応 |

#### **DirectoryPath**

| ancestorId | descendantId | pathLength |
| --- | --- | --- |
| root | root | 0 |
| root | tech | 1 |
| root | manual | 1 |
| root | phone | 2 |
| tech | tech | 0 |
| manual | manual | 0 |
| manual | phone | 1 |
| phone | phone | 0 |

### クエリの例

#### 「マニュアル」の下にあるディレクトリの一覧を取得する

```sql
SELECT 
  Directory.*, DirectoryPath.pathLength
FROM
  Directory
INNER JOIN
	DirectoryPath
ON
	Directory.id = DirectoryPath.descendantId
WHERE
	DirectoryPath.ancestorId = 'manual'
ORDER BY
  DirectoryPath.pathLength ASC;
```

| id | name | pathLength |
| --- | --- | --- |
| manual | マニュアル | 0 |
| phone | 電話対応 | 1 |

#### 「電話対応」の祖先ディレクトリを一覧で取得する

```sql
SELECT 
  Directory.*, DirectoryPath.pathLength
FROM
  Directory
INNER JOIN
	DirectoryPath
ON
	Directory.id = DirectoryPath.ancestorId
WHERE
	DirectoryPath.descendantId = 'phone'
ORDER BY
  DirectoryPath.pathLength ASC;
```

| id | name | pathLength |
| --- | --- | --- |
| phone | 電話対応 | 0 |
| manual | マニュアル | 1 |
| root | ルート | 2 |

#### 「マニュアル」の下に「出荷対応」を追加する

```sql
INSERT INTO Directory
  (id, name)
VALUES 
  ("shipping", "出荷対応");

INSERT INTO DirectoryPath
  (ancestorId, descendantId, pathLength)
SELECT
    DirectoryPath.ancestorId, 'shipping', DirectoryPath.pathLength + 1
FROM
    DirectoryPath
WHERE
    DirectoryPath.descendantId = 'manual'
UNION ALL
    SELECT 'shipping', 'shipping', 0;
```

#### 「マニュアル」以下のディレクトリを全て削除する

```sql
DELETE FROM
  Directory
WHERE 
  id IN (SELECT descendantId FROM DirectoryPath WHERE ancestorId = 'manual');

DELETE FROM
  DirectoryPath
WHERE 
  descendantId IN (SELECT descendantId FROM DirectoryPath WHERE ancestorId = 'manual');
```

#### 「技術記事」を「マニュアル」配下につけかえる

```sql
# 祖先ディレクトリから自身および配下へのパスを削除
DELETE FROM 
  DirectoryPath
WHERE
  descendantId IN (SELECT descendantId FROM DirectoryPath WHERE ancestorId = 'manual')
AND 
  ancestorId IN (SELECT ancestorId FROM DirectoryPath WHERE descendantId = 'manual' AND ancestorId != descendantId);

# 移動先に対して新しいパスを生成
INSERT INTO DirectoryPath
  (ancestorId, descendantId, pathLength)
SELECT 
  super.ancestorId,
  super.descendantId,
  super.pathLength + sub.pathLength + 1
FROM
  DirectoryPath AS super
CROSS JOIN 
  DirectoryPath AS sub
WHERE super.descendantId = 'tech' AND sub.ancestorId = 'manual';
```

変更になるパスを削除して、新しいパスを追加するだけ

## 課題２：順序どうやって実現する？

* DocumentDirectory に order カラムを追加する
