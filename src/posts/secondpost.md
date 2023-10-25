---
title: Rust로 간단한 Cli program 만들기 
date: 2023-10-24T10:10:10
author: StudyDev
tags:
  - Rust
  - clap
---

## 처음 시작하는 Cli program

Cli란?
Command line interface의 약자로서 터미널을 통해 사용자가 컴퓨터와 상호작용을 하는 것을 뜻합니다.

Rust에서는 기본적으로 **std::env::args**를 통해서 Command-line에서 아규먼트를 읽어올 수 있습니다.

```rust
fn main() {
    let message = std::env::args().nth(1)
        .expect("Missing the message");

    println!("{}", message);
    println!(" \\");
    println!("  \\");
    println!("(｡•̀ᴗ-)✧");
}
```
여기서 n번째 아규먼트를 읽기위해 .nth()를 사용합니다.
또한 .nth()는 실패할 수 있는 함수이기에 expect나 unwrap을 통해서 에러처리를 해야합니다.

하지만 인자가 많아지고 프로그램이 더 많아지면 에러처리나 명령어를 사용함에 있어서 복잡해 질 수 있습니다.
clap이라는 크레이트를 사용해 보겠습니다.

### 설치
```rust
cargo add clap --features derive
```

dervie 기능을 사용하기 위해 위와 같은 명령어를 터미널에 입력합니다. 
derive 기능은 struct에서 일부 구문 분석 코드를 자동으로 생성하는 매크로를 추가하는데요.
#[derive(Parser)]와 같이 struct 위에 주석을 달 수 있습니다.


```rust
// Cargo.toml
[dependencies]
clap = { version = "4.4.6", features = ["derive"] }
```
설치가 잘 되었다면 Cargo.toml 파일의 dependencies 항목에 위와같이git  들어가 있게 됩니다.

## 구조체 생성 및 함수 작성
main.rs로 돌아가 사용해봅시다.

```rust
use clap::Parser;

#[derive(Parser)]
struct Options {
    message: String,
}

fn main() {
    let options = Options::message;
    let message = options.message;
    // ...
}
```
위에서 설명했던 주석으로 Options 구조체가 clap 명령줄 인수의 정의임을 나타냅니다.
Parser 매크로는 이에 따라 인수 파서를 생성합니다.

이어 우리가 설정한 파서를 사용하기 위해 main 함수에서 **Options::message**를 사용해서 호출해줍니다.
그리고 이를 통해 개별 필드에 접근할 수 있게 message 변수를 설정해줍니다.

```rust
cargo run -- --help
    Finished dev [unoptimized + debuginfo] target(s) in 0.05s
     Running `target/debug/clisay --help`
Usage: clisay <MESSGAE>

Arguments:
  <MESSGAE>

Options:
  -h, --help  Print help
```

출력된 메시지에서 볼 수 있듯이 인수 섹션 아래에 <MESSAGE>라는 위치 인수가 있습니다. 
메시지 인수를 전달하지 않으면, clap은 에러 메시지를 반환합니다.

## 커스텀 에러메시지 사용하기
하지만 지금 정의된 구조체만 가지고는 사용자가 어떤 인자를 전달해야 하는지 모르고 그 기능또한 사용하기 어렵갰죠?

struct를 다음과 같이 변경해 줍시다.

```rust
#[derive(Parser)]
struct Options {
    #[clap(default_value = "Hello")]
    /// 인삿말을 전달합니다.
    messgae: String
}
```
그러면 다음과 같이 출력되는데요.

```rust
cargo
 run -- --help
    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target/debug/clisay --help`
Usage: clisay [MESSGAE]

Arguments:
  [MESSGAE]  인삿말을 전달합니다 [default: Hello]

Options:
  -h, --help  Print help
```
default_value로 "Hello"를 설정해주어서 그냥 실행한다면 처음과 같은 결과를 얻을 수 있습니다.

다음번에는 조금 더 다양한 기능들을 추가해 보도록 하겠습니다.