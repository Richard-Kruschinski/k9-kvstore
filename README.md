# K-9 KV Store  
A compact, persistent key–value store written in Rust.

K-9 is a small but carefully designed storage engine that serializes keys and values into a binary format, supported by CRC32 integrity checks and a custom header layout.  
The project focuses on correctness, data integrity, iteration without heap allocation, and clean separation between borrowed and owned value representations.

---

## Features

- **Binary serialization** with a custom 13-byte header (`length`, `checksum`, `tag`)
- **Persistent storage** using `std::fs::read` and buffered writes
- **CRC32 validation** to detect corrupted entries on load
- **Support for multiple value types**  
  `Integer`, `Bool`, `Text`, and `Blob`
- **Separation of borrowed and owned values** for efficient, zero-copy reads
- **Entry-by-entry iteration** with no heap allocations
- **Full test suite**, including corruption testing and round-trip persistence tests

---

## Why “K-9”?
The project is named **K-9** because it behaves a bit like a quick retrieval dog.  
You throw data at it, and it fetches it back **fast**, **reliably**, and without hesitation.  
Just like a trained K-9 unit, our key-value store stays focused, efficient, and always ready to retrieve.

---

## Binary Format

Each serialized item consists of:

`[ header (13 bytes) ][ payload (... bytes) ]`


### Header layout:
- `length:   u64`  – size of checksum + tag + payload  
- `checksum: u32`  – CRC32 of the payload  
- `tag:      u8`   – type discriminator

All numeric fields use **little-endian** encoding (`to_le_bytes`).

Keys and values are stored as independent entries:

`[Key1][Value1][Key2][Value2]...`


This keeps parsing simple and robust.

---

## Example: Persisting Data

```rust
let mut store = KvStore::new();
store.insert(Key::Text("lang".into()), OwnedValue::Text("Rust".into()));

store.persist_to_file("data.bin")?;
```
## Example: Loading Data

```rust
let store = KvStore::load_from_file("data.bin")?;
let value = store.get_owned(&Key::Text("lang".into()))?;
```

## Running Tests
```cargo
cargo test
```

Tests include:
- basic CRUD functionality
- iteration order
- corruption detection via CRC mismatch
- zero-allocation iterator verification

## Design Goals

- Minimal & understandable storage layer
- Clean ownership model (BorrowedValue → OwnedValue conversion when needed)
- Simple file format with explicit boundaries
- No dependencies besides crc and thiserror
- Fast iteration without unnecessary memory allocation


## Project Structure
```
src/  
└── lib.rs       # Core storage implementation  
tests/  
└── kvstore_tests.rs  
examples/  
└── persist_demo.rs  
```

