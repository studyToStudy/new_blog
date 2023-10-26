---
title: Rust로 간단한 Cli program 만들기 2편
date: 2023-10-26T10:10:10
author: StudyDev
tags:
  - Rust
  - clap
---

## Binary Flag 추가하기

clap에는 기능을 켜고 끄는 데 사용되는 토글 또는 스위치라고도 알려진 이진 플래그를 쉽게 추가할 수 있게 해주는데요.
우리 Cli program에는 캐릭터가 있죠, 이는 캐릭터의의 눈 두개를 맞춰보겠습니다.

```rust
#[derive(Parser)]
struct Options {
    #[clap(default_value = "Hello")]
    /// 인삿말을 전달합니다.
    message: String,
    #[clap(short = "e", long = "eyes")]
    eyes: bool,
}
```

eyes라는 bool 유형의 필드를 추가합니다. 
필드에 주석을 달아 flag의 short과 long 버전을 할당할 수 있습니다.
이는 --e, --eyes와 같은 역할을 수행하게 됩니다.

```rust
cargo run -- --help
   Compiling clisay v0.1.0 (/Users/junghoo515/Desktop/clisay)
    Finished dev [unoptimized + debuginfo] target(s) in 0.72s
     Running `target/debug/clisay --help`
Usage: clisay [OPTIONS] [MESSGAE]

Arguments:
  [MESSGAE]  인삿말을 전달합니다 [default: Hello]

Options:
  -l, --login
  -h, --help   Print help
```
이런 식으로 help에도 잘 나타나는것을 볼 수 있습니다.

이제 main함수를 바꿔줍시다.

```rust
fn main() {
    let options = Options::parse();
    let message = options.messgae;

    let eye = if options.eyes {"-"} else {"•̀"};


    println!("{}", message);
    println!(" \\");
    println!("  \\");
    println!("(｡{eye}ᴗ{eye})✧");
}
```

```rust
Hello
 \
  \
(｡•̀ᴗ•̀)✧
```
찡긋하던 눈 오른쪽 눈도 잘 바뀐것을 확인할 수 있습니다.

## STDERR 생성하기
지금까지 표준 출력(STDOUT)에 인쇄되는 println!()을 사용하여 출력을 생성했습니다. 
그러나 유닉스 계열 시스템에는 인쇄 오류에 대한 표준 오류(STDERR) 스트림도 있는데요. 
Rust는 eprintln!()이라고 불리는 println!()에 상응하는 STDERR을 제공합니다. 
예를 들어, 유저가 사람에게 "bark"라고 말하게 되면 하면 에러를 반환할 수 있습니다.

```rust
if message.to_lowercase() == "bark" {
        eprintln!("A Human shouldn't bark like a animal.")
    }

// ...
println!("{}", message);
```

```rust
// cargo run bark
A Human shouldn't bark like a animal.
bark
 \
  \
(｡•̀ᴗ•̀)✧
```
설정한 에러메시지가 잘 출력되네요.

## 터미널에 색상 입히기
요즘 터미널에는 다양한 기능뿐 아니라 색상또한 지원하여 가시성이나 사용성에 무게를 두는 경우도 있습니다.

rust에는 colored라는 크레이트가 있으니 사용해보도록 하겠습니다.

```rust
cargo add colored
```
colored 크레이트는 &str과 String을 impl 하는 Colorize Traits을 정의합니다. 

- Coloring the text: .red(), .green(), .blue().. etc.
- Coloring the background: .on_red(), .on_green(), etc.

다양한 기능은 크레이트 페이지를 확인해주세요.

우선 사용을 위해 프로젝트에 가져옵니다.
```rust
use colored::Colorize;
```
그리고 우리가 설정해준 에러메시지에 적용하겠습니다.
```rust
    println!(
        "{}",
        message.bright_blue().underline().on_purple()
    );
```
이렇게 하면 보라색 배경에 파란색 텍스트가 적용된 Hello가 출력됩니다.