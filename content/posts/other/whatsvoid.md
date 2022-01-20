# javascript:void(0)とは

![javascriptvoid](/images/other/javascriptvoid.png)

リンクにマウスカーソルをホバーした時`javascript:void(0)`と表示される。これは一体何なのか。

## aタグの`href`と`onclick`が共存している場合に有効
>`onclick`が実行されずに`href`のリンクに飛ばされてしまう。

そこで`javascript:void(0)`を`href`に記述する事で、リンクに飛ばされず`onclick`が実行できる。

仕組みとしては、`void(0)`というのは常に`undefined`を返す特性があるため、`href`記述された`javascript:void(0)`は`undefined`となり、結果的に何も実行されないということだ。