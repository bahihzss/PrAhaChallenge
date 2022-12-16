# 課題1
* NULL との比較 (=,<>,>,<) は全て NULL （比較結果が UNKNOWN という意味らしい）
* AND: コンポーネントの条件が両方ともTRUEの場合にTRUEを戻す。どちらかがFALSEの場合にはFALSEを戻します。それ以外の場合はUNKNOWNを戻す。
* OR: コンポーネントの条件のどちらかがTRUEの場合にTRUEを戻す。両方ともFALSEの場合はFALSEを戻します。それ以外の場合はUNKNOWNを戻す。
* IS NULL: IS を使えば NULL かどうかの比較ができる
* IS NOT NULL: IS NULL の否定

## SELECT NULL = 0;
`NULL`  
NULL との比較

## NULL = NULL (以下、SELECT部分を省略)
`NULL`  
NULL との比較

## NULL <> NULL
`NULL`  
NULL との比較

## NULL AND TRUE
`NULL`  
両方とも TRUE でもなく、どちらかが FALSE でもないので UNKNOWN の表現である NULL になる

## NULL AND FALSE
`0`  
右辺が FALSE なので FALSE の表示である 0 が返ってくる

## NULL OR TRUE
`1`  
右辺が TRUE なので TRUE の表示である 1 が返ってくる

## NULL IS NULL
`1`  

## NULL IS NOT NULL
`0`  

# 課題2

![NULL をなくす](https://lucid.app/publicSegments/view/64678ceb-7e51-49e3-8500-53cc13f90237/image.png)
中間テーブルを作る

# 課題3

デフォルト値にした値を持っているのか、値を持つ存在がないのかの区別がつかなくなっちゃう

値を保つ対象がそもそも存在しないという場合を表現するには、課題2のように別のエンティティに切り出すとかが解決することがあるので、
NULLを撲滅することを目的にするんじゃなくて、NULLがあるならより良いデータモデルを見つけられるかもと思ってみるとハッピー。
