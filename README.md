# thespis

A minimal actor model for Rust

## Design goals

Thespis has the following goals:

- separate interface from implementation. Clients can use the interface while freely chosing which implementation they want for each concept: Addr, Mailbox, Envelope,
- users don't pay for what they don't use: if you are on a single threaded machine, you shouldn't pay thread sync. If you are doing actors in process, you shouldn't pay for network logic...
- run everywhere: no_std, single/multithreaded, all targets: wasm, embedded, win, unix, mac, ...


## Open questions

- How to get rid of the deduplication of Send vs !Send messages and the threading overhead (actually the overhead is not there on the channels if the Sender and Receiver are in the same thread I think, at least our current single threaded impl also uses the channels).
- Should we standardize different remote addresses by putting an interface for them in iface? Currently just impls...

## Tools:

- async thread sync: qutex


## Before publish

- verify object safety of all traits in interface
- remove all allow( dead_code, unused_imports )
- verify every TODO, FIXME
- deny missing_docs, missing_debug_impl
- verify all different use cases are documented and tested
- verify whether there are any unsafes and if not, forbid unsafe
- verify log crate use
- guide level docs
- verify targets, no_std?, wasm, cross platforms

