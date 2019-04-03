# thespis

A minimal actor model for Rust

## Design goals

Thespis has the following goals:

- separate interface from implementation. Clients can use the interface while freely chosing which implementation they want for each concept: Addr, Mailbox, Envelope,
- users don't pay for what they don't use: if you are on a single threaded machine, you shouldn't pay thread sync. If you are doing actors in process, you shouldn't pay for network logic...
- run everywhere: no_std, single/multithreaded, all targets: wasm, embedded, win, unix, mac, ...


## Before publish

- remove all allow( dead_code, unused_imports )
- verify every TODO, FIXME
- deny missing_docs, missing_debug_impl
- verify all different use cases are documented and tested
- verify whether there are any unsafes and if not, forbid unsafe
- verify log crate use
- error handling
- guide level docs
- verify targets, no_std?, wasm, single_threaded

