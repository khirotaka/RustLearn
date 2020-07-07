# 数当てゲーム

## 最初の一歩

```rust
use std::io;

fn main() {
    println!("Guess the number!");
    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess).expect("Failed to line");
    println!("You guessd: {}", guess);
}
```

1. 一行目 `std::io` はその名の通り標準ライブラリのIOライブラリを呼んでくる。  
1. `let mut guess = String::new();` は変数の定義を行っている。
Rustでは、変数は標準で不変(immutable)である。可変にするには、`let mut` にする必要がある。  
`io::stdin().read_line(&mut guess).expect("Failed to line");` 
は標準入力で文字列を受け取り、変数`guess` に代入する。(`io::stdin().read_line(&mut guess)`)
`.expect("Failed to line")`はエラー時の出力


`&` の意味は、引数が参照であることを表す。  
`read_line()`メソッドはユーザが標準入力したものすべてを取り出し、文字列に格納することなので、 格納する文字列を引数として取ります。
この文字列引数は、可変である必要があります。ユーザが標準入力したものすべてを取り出し、文字列に格納することなので、
格納する文字列を引数として取ります。この文字列引数は、可変である必要があります。
`read_line()`は渡された文字列にユーザが入力したものを入れ込むだけでなく、 値も返します (今回は、`io::Result`)
このResult型は、列挙型。Result型に関しては、列挙子は`Ok`か`Err`です。`Ok`列挙子は、処理が成功したことを表し、
中に生成された値を保持します。`Err`列挙子は、処理が失敗したことを意味し、`Err`は、処理が失敗した過程や、 理由などの情報を保有します。

## 最終的なコード
```rust
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is: {}", secret_number);
    println!("Please input your guess.");

    loop {
        let mut guess = String::new();

        io::stdin().read_line(&mut guess).expect("Failed to line");
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
        println!("You guess: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too Small!"),
            Ordering::Greater => println!("Too Big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}

```

1行目は外部ライブラリ(クレート) `rand` を利用することを宣言している。  
3~5行目の `use`句で使用するクレートのモジュールを宣言している。(?) Pythonの `from ... import ...` 的な?  
`let secret_number = rand::thread_rng().gen_range(1, 101);` はレンジ1~100までの乱数を生成。  
Rustのループ(while (?)) は `loop {}` を用いる。  
`let mut guess = String::new();` は空の`String`オブジェクトを生成している。ここで、これは定数ではなく変数である事に注意。  
```rust
let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
```
`parse`メソッドは、文字列から数値への変換に成功したら、結果の数値を保持する`Ok`値を返します。
この`Ok`値は、最初のアームのパターンにマッチし、この`match`式は`parse`メソッドが生成し、 `Ok`値に格納した`num`の値を返すだけです。
その数値が最終的に、生成している新しい`guess`変数として欲しい場所に存在します。

parseメソッドは、文字列から数値への変換に失敗したら、エラーに関する情報を多く含むErr値を返します。
このErr値は、最初のmatchアームのOk(num)というパターンにはマッチしないものの、
2番目のアームのErr(_)というパターンにはマッチするわけです。この_は、包括値です; 
この例では、 保持している情報がどんなものでもいいから全てのErr値にマッチさせたいと宣言しています。
従って、プログラムは2番目のアームのコードを実行し(continueですね)、これは、loopの次のステップに移り、
再度予想入力を求めるようプログラムに指示します。
故に実質的には、 プログラムはparseメソッドが遭遇しうる全てのエラーを無視するようになります！