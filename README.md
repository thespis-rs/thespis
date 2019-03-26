# thespis

A minimal actor model for Rust

## Design goals

Thespis has the following goals:

- separate interface from implementation. Clients can use the interface while freely chosing which implementation they want for each concept: Addr, Mailbox, Envelope,
- users don't pay for what they don't use: if you are on a single threaded machine, you shouldn't pay thread sync. If you are doing actors in process, you shouldn't pay for network logic...
- run everywhere: no_std, single/multithreaded, all targets: wasm, embedded, win, unix, mac, ...

