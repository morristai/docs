---
sidebar_position: 2
---

# Embed a standalone WASM app

The WasmEdge Go SDK can [embed standalone WebAssembly applications](https://github.com/second-state/WasmEdge-go-examples/tree/master/go_ReadFile) — ie a Rust application with a `main()` function compiled into WebAssembly.

## The WASM app in Rust

Our [demo Rust application](https://github.com/second-state/WasmEdge-go-examples/tree/master/go_ReadFile/rust_readfile) reads from a file. Note that the WebAssembly program's input and output data are now passed by the STDIN and STDOUT.

```rust
use std::env;
use std::fs::File;
use std::io::{self, BufRead};

fn main() {
  // Get the argv.
  let args: Vec<String> = env::args().collect();
  if args.len() <= 1 {
    println!("Rust: ERROR - No input file name.");
    return;
  }

  // Open the file.
  println!("Rust: Opening input file \"{}\"...", args[1]);
  let file = match File::open(&args[1]) {
    Err(why) => {
      println!("Rust: ERROR - Open file \"{}\" failed: {}", args[1], why);
      return;
    },
    Ok(file) => file,
  };

  // Read lines.
  let reader = io::BufReader::new(file);
  let mut texts:Vec<String> = Vec::new();
  for line in reader.lines() {
    if let Ok(text) = line {
      texts.push(text);
    }
  }
  println!("Rust: Read input file \"{}\" succeeded.", args[1]);

  // Get stdin to print lines.
  println!("Rust: Please input the line number to print the line of file.");
  let stdin = io::stdin();
  for line in stdin.lock().lines() {
    let input = line.unwrap();
    match input.parse::<usize>() {
      Ok(n) => if n > 0 && n <= texts.len() {
        println!("{}", texts[n - 1]);
      } else {
        println!("Rust: ERROR - Line \"{}\" is out of range.", n);
      },
      Err(e) => println!("Rust: ERROR - Input \"{}\" is not an integer: {}", input, e),
    }
  }
  println!("Rust: Process end.");
}
```

## Compile the Rust code into Wasm

Next, let's compile the application into WebAssembly.

```bash
git clone https://github.com/second-state/WasmEdge-go-examples.git
cd rust_readfile
cargo build --target wasm32-wasip1
# The output file will be target/wasm32-wasip1/debug/rust_readfile.wasm
```

## The Go Host app

The Go source code to run the WebAssembly function in WasmEdge is as follows.

```go
package main

import (
  "os"
  "github.com/second-state/WasmEdge-go/wasmedge"
)

func main() {
  wasmedge.SetLogErrorLevel()

  var conf = wasmedge.NewConfigure(wasmedge.REFERENCE_TYPES)
  conf.AddConfig(wasmedge.WASI)
  var vm = wasmedge.NewVMWithConfig(conf)
  var wasi = vm.GetImportModule(wasmedge.WASI)
  wasi.InitWasi(
    os.Args[1:],     // The args
    os.Environ(),    // The envs
    []string{".:."}, // The mapping directories
  )

  // Instantiate wasm. _start refers to the main() function
  vm.RunWasmFile(os.Args[1], "_start")

  vm.Release()
  conf.Release()
}
```

## Run the compiled WASM file from Go host

::note Make sure you have installed [Go, WasmEdge, and WasmEdge Go SDK](intro.md). ::

Next, let's build the Go application with the WasmEdge Go SDK.

```bash
go build
```

Run the Golang application.

```bash
$ ./read_file rust_readfile/target/wasm32-wasip1/debug/rust_readfile.wasm file.txt
Rust: Opening input file "file.txt"...
Rust: Read input file "file.txt" succeeded.
Rust: Please input the line number to print the line of file.
# Input "5" and press Enter.
5
# The output will be the 5th line of `file.txt`:
abcDEF___!@#$%^
# To terminate the program, send the EOF (Ctrl + D).
^D
# The output will print the terminate message:
Rust: Process end.
```

More examples can be found at [the WasmEdge-go-examples GitHub repo.](https://github.com/second-state/WasmEdge-go-examples)
