---
sidebar_position: 1
---

# The `wasmedge` CLI

After installing WasmEdge, you can use the `wasmedge` CLI to execute WASM files. We will cover how to run WASM files with WasmEdge on your machine and Docker images.

The `wasmedge` binary is a command line interface (CLI) program that runs WebAssembly programs.

- If the WebAssembly program contains a `main()` function, `wasmedge` would execute it as a standalone program in the command mode.
- If the WebAssembly program contains one or more exported public functions, `wasmedge` could invoke individual functions in the reactor mode.

By default, the `wasmedge` will execute WebAssembly programs in interpreter mode and execute the AOT-compiled `.so`, `.dylib`, `.dll`, or `.wasm` (universal output format) in AOT mode. If you want to accelerate the WASM execution, we recommend to [compile the WebAssembly with the AOT compiler](aot.md) first.

<!-- prettier-ignore -->
:::note
The original `wasmedgec` tool is changed to `wasmedge compile`. The [`wasmedge compile` CLI tool](aot.md) is the ahead-of-time compiler to compile the WebAssembly file into native code.
:::

```bash
$ wasmedge -v
wasmedge version {{ wasmedge_version }}
```

Users can run the `wasmedge -h` to realize the command line options quickly or [refer to the detailed `wasmedge` CLI options here](#options). The usage of the `wasmedge` tool will be:

```bash
$ wasmedge -h
USAGE
   wasmedge [OPTIONS] [--] WASM_OR_SO [ARG ...]

...
```

The `wasmedge` CLI tool will execute the wasm file in ahead-of-time(AOT) mode or interpreter mode. If the file has been compiled with `wasmedge compile`, then WasmEdge will execute it in AOT mode, otherwise, WasmEdge will execute it in interpreter mode.

## Options

The options of the `wasmedge` CLI tool are as follows:

1. `-v|--version`: Show the version information. Will ignore other arguments below.
2. `-h|--help`: Show the help messages. Will ignore other arguments below.
3. _(Optional)_ `--reactor`: Enable the reactor mode.
   - In the reactor mode, `wasmedge` runs a specified function exported by the WebAssembly program.
   - WasmEdge will execute the function whose name should be given in `ARG[0]`.
   - If an exported function names `_initialize`, the function will be executed with the empty parameter at first.
4. _(Optional)_ `--dir`: Bind directories into WASI virtual filesystem.
   - Use `--dir guest_path:host_path` to bind the host path into the guest path in WASI virtual system.
5. _(Optional)_ `--env`: Assign the environment variables in WASI.
   - Use `--env ENV_NAME=VALUE` to assign the environment variable.
6. _(Optional)_ Statistics information:
   - Use `--enable-time-measuring` to show the execution time.
   - Use `--enable-gas-measuring` to show the amount of used gas.
   - Use `--enable-instruction-count` to display the number of executed instructions.
   - Or use `--enable-all-statistics` to enable all of the statistics options.
7. _(Optional)_ Resource limitations:
   - Use `--time-limit MILLISECOND_TIME` to limit the execution time. Default value is `0` which specifies no limitation.
   - Use `--gas-limit GAS_LIMIT` to limit the execution cost.
   - Use `--memory-page-limit PAGE_COUNT` to set the limitation of pages(as size of 64 KiB) in every memory instance.
8. _(Optional)_ Execution mode:
   - Use `--force-interpreter` to forcibly run WASM in interpreter mode.
   - Use `--enable-jit` to enable Just-In-Time compiler for running WASM.
9. _(Optional)_ WebAssembly proposals:
   - Use `--disable-import-export-mut-globals` to disable the [Import/Export of Mutable Globals](https://github.com/WebAssembly/mutable-global) proposal (Default `ON`).
   - Use `--disable-non-trap-float-to-int` to disable the [Non-Trapping Float-to-Int Conversions](https://github.com/WebAssembly/nontrapping-float-to-int-conversions) proposal (Default `ON`).
   - Use `--disable-sign-extension-operators` to disable the [Sign-Extension Operators](https://github.com/WebAssembly/sign-extension-ops) proposal (Default `ON`).
   - Use `--disable-multi-value` to disable the [Multi-value](https://github.com/WebAssembly/multi-value) proposal (Default `ON`).
   - Use `--disable-bulk-memory` to disable the [Bulk Memory Operations](https://github.com/WebAssembly/bulk-memory-operations) proposal (Default `ON`).
   - Use `--disable-reference-types` to disable the [Reference Types](https://github.com/WebAssembly/reference-types) proposal (Default `ON`).
   - Use `--disable-simd` to disable the [Fixed-width SIMD](https://github.com/webassembly/simd) proposal (Default `ON`).
   - Use `--enable-multi-memory` to enable the [Multiple Memories](https://github.com/WebAssembly/multi-memory) proposal (Default `OFF`).
   - Use `--enable-tail-call` to enable the [Tail call](https://github.com/WebAssembly/tail-call) proposal (Default `OFF`).
   - Use `--enable-extended-const` to enable the [Extended Constant Expressions](https://github.com/WebAssembly/extended-const) proposal (Default `OFF`).
   - Use `--enable-threads` to enable the [Threads](https://github.com/webassembly/threads) proposal (Default `OFF`).
   - Use `--enable-function-reference` to enable the [Typed-Function References](https://github.com/WebAssembly/function-references) proposal (Default `OFF`).
   - Use `--enable-gc` to enable the [GC](https://github.com/WebAssembly/gc) proposal (Default `OFF`, interpreter mode only).
   - Use `--enable-exception-handling` to enable the [Exception Handling](https://github.com/WebAssembly/exception-handling) proposal (Default `OFF`, interpreter mode only).
   - Use `--enable-component` to enable the [Component Model](https://github.com/WebAssembly/component-model) proposal (Default `OFF`, loader phase only).
   - Use `--enable-all` to enable ALL proposals above.
10. WASM file (`/path/to/wasm/file`).
11. _(Optional)_ `ARG` command line arguments array.
    - In reactor mode, the first argument will be the function name, and the arguments after `ARG[0]` will be parameters of wasm function `ARG[0]`.
    - In command mode, the arguments will be the command line arguments of the WASI `_start` function. They are also known as command line arguments(`argv`) for a standalone C/C++ program.

## TensorFlow Tools

<!-- prettier-ignore -->
:::note
The `WasmEdge-tensorflow-tools` has been deprecated after the 0.12.1 version and replaced by the plug-ins after the 0.13.0 version.
:::

If users install WasmEdge from the install script with the option `-e tf,image`, the WasmEdge CLI tools with TensorFlow and TensorFlow-Lite extensions will also be installed.

- `wasmedge-tensorflow` CLI tool
  - The `wasmedge` tool with TensorFlow, TensorFlow-Lite, and `wasmedge-image` extensions.
  - Only on `x86_64` and `aarch64` Linux platforms and `x86_64` MacOS.
- `wasmedge-tensorflow-lite` CLI tool
  - The `wasmedge` tool with TensorFlow-Lite, and `wasmedge-image` extensions.
  - Only on `x86_64` and `aarch64` Linux platforms, Android, and `x86_64` MacOS.

## Examples

### Build and run a standalone WebAssembly app

The Hello World example is a standalone Rust application that can be executed by the [WasmEdge CLI](../build-and-run/cli). Its source code and build instructions are available [here](https://github.com/second-state/rust-examples/tree/main/hello).

You will need to have the [Rust compiler installed](https://github.com/second-state/rust-examples/blob/main/README.md#prerequisites), and then use the following command to build the WASM bytecode file from the Rust source code.

```bash
cargo build --target wasm32-wasip1 --release
```

You can then use the `wasmedge` command to run the program.

```bash
$ wasmedge target/wasm32-wasip1/release/hello.wasm
Hello WasmEdge!
```

#### Execute with `statistics` enabled

The CLI supports `--enable-all-statistics` flags for the statistics and gas metering.

You can run:

```bash
wasmedge --enable-all-statistics hello.wasm
```

The output will be:

```bash
Hello WasmEdge!
[2021-12-09 16:03:33.261] [info] ====================  Statistics  ====================
[2021-12-09 16:03:33.261] [info]  Total execution time: 268266 ns
[2021-12-09 16:03:33.261] [info]  Wasm instructions execution time: 251610 ns
[2021-12-09 16:03:33.261] [info]  Host functions execution time: 16656 ns
[2021-12-09 16:03:33.261] [info]  Executed wasm instructions count: 20425
[2021-12-09 16:03:33.261] [info]  Gas costs: 20425
[2021-12-09 16:03:33.261] [info]  Instructions per second: 81177218
[2021-12-09 16:03:33.261] [info] =======================   End   ======================
```

#### Execute with `gas-limit` enabled

The CLI supports `--gas-limit` flags for controlling the execution costs.

For giving sufficient gas as the example, you can run:

```bash
wasmedge --enable-all-statistics --gas-limit 20425 hello.wasm
```

The output will be:

```bash
Hello WasmEdge!
[2021-12-09 16:03:33.261] [info] ====================  Statistics  ====================
[2021-12-09 16:03:33.261] [info]  Total execution time: 268266 ns
[2021-12-09 16:03:33.261] [info]  Wasm instructions execution time: 251610 ns
[2021-12-09 16:03:33.261] [info]  Host functions execution time: 16656 ns
[2021-12-09 16:03:33.261] [info]  Executed wasm instructions count: 20425
[2021-12-09 16:03:33.261] [info]  Gas costs: 20425
[2021-12-09 16:03:33.261] [info]  Instructions per second: 81177218
[2021-12-09 16:03:33.261] [info] =======================   End   ======================
```

For giving insufficient gas as the example, you can run:

```bash
wasmedge --enable-all-statistics --gas-limit 20 hello.wasm
```

The output will be:

```bash
Hello WasmEdge!
[2021-12-23 15:19:06.690] [error] Cost exceeded limit. Force terminate the execution.
[2021-12-23 15:19:06.690] [error]     In instruction: ref.func (0xd2) , Bytecode offset: 0x00000000
[2021-12-23 15:19:06.690] [error]     At AST node: expression
[2021-12-23 15:19:06.690] [error]     At AST node: element segment
[2021-12-23 15:19:06.690] [error]     At AST node: element section
[2021-12-23 15:19:06.690] [error]     At AST node: module
[2021-12-23 15:19:06.690] [info] ====================  Statistics  ====================
[2021-12-23 15:19:06.690] [info]  Total execution time: 0 ns
[2021-12-23 15:19:06.690] [info]  Wasm instructions execution time: 0 ns
[2021-12-23 15:19:06.690] [info]  Host functions execution time: 0 ns
[2021-12-23 15:19:06.690] [info]  Executed wasm instructions count: 21
[2021-12-23 15:19:06.690] [info]  Gas costs: 20
```

### Call a WebAssembly function compiled from Rust

The [add](https://github.com/second-state/wasm-learning/tree/master/cli/add) program is written in Rust and contains an exported `add()` function. You can compile it into WebAssembly and use `wasmedge` to call the `add()` function. In this example, you will see how it is done from the CLI. It is often used when you embed WasmEdge into another host application, and need to call a WASM function from the host.

You will need to have the [Rust compiler](https://github.com/second-state/rust-examples/blob/main/README.md#prerequisites) installed, and then use the following command to build the WASM bytecode file from the Rust source code.

```bash
cargo build --target wasm32-wasip1 --release
```

You can execute `wasmedge` in reactor mode to invoke the `add()` function with two `i32` integer input parameters.

```bash
wasmedge --reactor add.wasm add 2 2
```

The output will be:

```bash
4
```

### Call a WebAssembly function written in WAT

We created the hand-written [fibonacci.wat](https://github.com/WasmEdge/WasmEdge/raw/master/examples/wasm/fibonacci.wat) and used the [wat2wasm](https://webassembly.github.io/wabt/demo/wat2wasm/) tool to convert it into the `fibonacci.wasm` WebAssembly program. It exported a `fib()` function which takes a single `i32` integer as the input parameter. We can execute `wasmedge` in reactor mode to invoke the exported function.

You can run:

```bash
wasmedge --reactor fibonacci.wasm fib 10
```

The output will be:

```bash
89
```

### JavaScript examples

Using WasmEdge as a high-performance, secure, extensible, easy-to-deploy, and [Kubernetes-compliant](https://github.com/second-state/wasmedge-containers-examples) JavaScript runtime is possible. There is no need to build a JavaScript app. You need to download the WasmEdge JavaScript runtime for Node.js.

- [Download the wasmedge_quickjs.wasm file here](https://github.com/second-state/wasmedge-quickjs/releases/download/v0.5.0-alpha/wasmedge_quickjs.wasm)
- [Download the modules.zip file here](https://github.com/second-state/wasmedge-quickjs/releases/download/v0.5.0-alpha/modules.zip) and then unzip it into the current folder as `./modules/`

```bash
wget https://github.com/second-state/wasmedge-quickjs/releases/download/v0.5.0-alpha/wasmedge_quickjs.wasm
wget https://github.com/second-state/wasmedge-quickjs/releases/download/v0.5.0-alpha/modules.zip
unzip modules.zip
```

Take a simple Javascript file for example. Save the following code as `hello.js`:

```javascript
args = args.slice(1);
print('Hello', ...args);
```

You can run:

```bash
wasmedge --dir .:. wasmedge_quickjs.wasm hello.js 1 2 3
```

The output will be:

```bash
Hello 1 2 3
```

The [qjs_tf.wasm](https://github.com/WasmEdge/WasmEdge/raw/master/examples/wasm/js/qjs_tf.wasm) is a JavaScript interpreter with [WasmEdge Tensorflow extension](https://www.secondstate.io/articles/wasi-tensorflow/) compiled into WebAssembly. To run [qjs_tf.wasm](https://github.com/WasmEdge/WasmEdge/raw/master/examples/wasm/js/qjs_tf.wasm), you must use the `wasmedge-tensorflow-lite` CLI tool, which is a build of WasmEdge with Tensorflow-Lite extension built-in. You can download a full [Tensorflow-based JavaScript example](https://github.com/second-state/wasmedge-quickjs/tree/main/example_js/tensorflow_lite_demo) to classify images.

```bash
# Download the Tensorflow example
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/aiy_food_V1_labelmap.txt
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/food.jpg
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/lite-model_aiy_vision_classifier_food_V1_1.tflite
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/main.js

$ wasmedge-tensorflow-lite --dir .:. qjs_tf.wasm main.js
label: Hot dog
confidence: 0.8941176470588236
```

## Docker images for the CLI tools

The Docker images in this section are mostly used for development purposes. They allow you to use WasmEdge tools in containerized Linux environments. If you want to containerize WASM apps, check out [this](../getting-started/quick_start_docker.md) section.

The `wasmedge/slim:{version}` Docker images provide a slim WasmEdge images built with [DockerSlim](https://dockersl.im) every releases.

- Image `wasmedge/slim-runtime:{version}` includes only WasmEdge runtime with `wasmedge` command.
- Image `wasmedge/slim:{version}` includes the following command line utilities:
  - `wasmedge`
  - `wasmedge compile`
- Image `wasmedge/slim-tf:{version}` includes the following command line utilities (DEPRECATED after 0.13.0):
  - `wasmedge`
  - `wasmedge compile`
  - `wasmedge-tensorflow-lite`
  - `wasmedge-tensorflow`
  - `show-tflite-tensor`
- The working directory of the release docker image is `/app`.

### Dockerslim Examples

After pulling the docker image successfully, you could use `wasmedge compile` and `wasmedge` to aot compile the wasm file and run the wasm app.

```bash
$ docker pull wasmedge/slim:{{ wasmedge_version }}

$ docker run -it --rm -v $PWD:/app wasmedge/slim:{{ wasmedge_version }} wasmedge compile hello.wasm hello.aot.wasm
[2022-07-07 08:15:49.154] [info] compile start
[2022-07-07 08:15:49.163] [info] verify start
[2022-07-07 08:15:49.169] [info] optimize start
[2022-07-07 08:15:49.808] [info] codegen start
[2022-07-07 08:15:50.419] [info] output start
[2022-07-07 08:15:50.421] [info] compile done
[2022-07-07 08:15:50.422] [info] output start

$ docker run -it --rm -v $PWD:/app wasmedge/slim:{{ wasmedge_version }} wasmedge hello.aot.wasm world
hello
world
```

Use `wasmedge-tensorflow-lite` ([link](https://github.com/WasmEdge/WasmEdge/tree/master/examples/js)):

<!-- prettier-ignore -->
:::note
The `WasmEdge-tensorflow-tools` has been deprecated after the 0.12.1 version. We'll update to use the WasmEdge plug-in in the future.
:::

```bash
$ docker pull wasmedge/slim-tf:0.12.1
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/aiy_food_V1_labelmap.txt
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/food.jpg
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/lite-model_aiy_vision_classifier_food_V1_1.tflite
$ wget https://raw.githubusercontent.com/second-state/wasmedge-quickjs/main/example_js/tensorflow_lite_demo/main.js

$ docker run -it --rm -v $PWD:/app wasmedge/slim-tf:0.12.1 wasmedge-tensorflow-lite --dir .:. qjs_tf.wasm main.js
label:
Hot dog
confidence:
0.8941176470588236
```
