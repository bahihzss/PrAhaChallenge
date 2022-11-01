## 課題1

### User テーブル の mailAddress に対する CHECK

アプリケーション側でのバリデーションで十分と考える

ASIL で考えてみた
https://zenn.dev/dowanna6/articles/asil_programming

#### 重大度（各案がどれだけ深刻な事態を招く可能性があるか）

登録されているユーザーにメールが遅れない、登録されたユーザーがログインできないぐらいの問題であれば発生しても大きな問題にはならない

#### 曝露可能性（その深刻な事態はどんな確率で起こり得るのか）

メールアドレスはフォームでチェックすると思うので、発生しずらい

#### 制御可能性（その深刻な事態が起きたとき、エンジニアにできることはあるのか）

例え発生しても、対象のユーザーのメールアドレスを修正するだけなので容易


### 退会ユーザーを Trigger で生成するか

絶対しない

ドメイン知識がドメイン層のコードに残らずインフラに実装されてしまう  
性能の問題から DB を変更したい場合などに、とってもめんどくさいことになる

### gender カラムを ENUM にするか

https://github.com/bahihzss/PrAhaChallenge/blob/main/anti-pattern-6.md

使ってる制約が違うだけで、アンチパターン6とほとんど同じかなと思う

gender は