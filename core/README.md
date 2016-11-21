# jsonrpc-core
Transport agnostic rust implementation of JSON-RPC 2.0 Specification.

[![Build Status][travis-image]][travis-url]

[travis-image]: https://travis-ci.org/ethcore/jsonrpc-core.svg?branch=master
[travis-url]: https://travis-ci.org/ethcore/jsonrpc-core

[Documentation](http://ethcore.github.io/jsonrpc-core/jsonrpc_core/index.html)

- [x] - server side
- [x] - client side

## Example

`Cargo.toml`


```
[dependencies]
jsonrpc-core = "4.0"
```

`main.rs`

```rust
extern crate jsonrpc_core;

use jsonrpc_core::*;

struct SayHello;
impl SyncMethodCommand for SayHello {
    fn execute(&self, _params: Params) -> Result<Value, Error> {
        Ok(Value::String("hello".into()))
    }
}

fn main() {
	let io = IoHandler::new();
	io.add_method("say_hello", SayHello);

	let request = r#"{"jsonrpc": "2.0", "method": "say_hello", "params": [42, 23], "id": 1}"#;
	let response = r#"{"jsonrpc":"2.0","result":"hello","id":1}"#;

	assert_eq!(io.handle_request_sync(request), Some(response.to_owned()));
}
```

### Asynchronous responses

`main.rs`

```rust
extern crate jsonrpc_core;

use jsonrpc_core::*;

struct SayHello;
impl MethodCommand for SayHello {
    fn execute(&self, _params: Params, ready: Ready) {
        ready.ready(Ok(Value::String("hello".into())))
    }
}

fn main() {
	let io = IoHandler::new();
	io.add_async_method("say_hello", SayHello);

	let request = r#"{"jsonrpc": "2.0", "method": "say_hello", "params": [42, 23], "id": 1}"#;
	let response = r#"{"jsonrpc":"2.0","result":"hello","id":1}"#;

	io.handle_request(request, move |res| {
		assert_eq!(res, Some(response.to_owned()));
	});
}
```

### Publish-Subscribe
See examples directory.