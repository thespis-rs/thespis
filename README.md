# thespis

A minimal actor model for Rust.

## References:

- [Actor Model of Computation: Scalable Robust Information Systems](https://arxiv.org/abs/1008.1459) by Carl Hewitt.
- video: [Hewitt, Meijer and Szyperski: The Actor Model (everything you wanted to know...)](https://youtu.be/7erJ1DV_Tlo)

## Design goals

Thespis has the following goals:

- separate interface from implementation. Clients can use the interface while freely chosing which implementation they want for each concept: Addr, Mailbox, ... Everybody can make their own implementations, yet stay compatible with all other actors. Libraries can expose actors without choosing an implementation, and let the binary application choose.
- users don't pay for what they don't use: if you are on a single threaded machine, you shouldn't pay thread sync. If you are doing actors in process, you shouldn't pay for network logic...
- run everywhere: no_std, single/multithreaded, all targets: wasm, embedded, win, unix, mac, ...

For the moment I failed to make it work conveniently with messages that are `!Send` and I don't think `no_std` is going to work for now.


## Library Structure

This repository has submodules which are actual crates:

- [thespis_iface](https://github.com/thespis-rs/thespis_iface) and [thespis_iface_remote](https://github.com/thespis-rs/thespis_iface_remote) hold the interfaces (only traits).
- [thespis_impl](https://github.com/thespis-rs/thespis_impl) and [thespis_impl_remote](https://github.com/thespis-rs/thespis_impl_remote) hold a reference implementation.
- [thespis_derive](https://github.com/thespis-rs/thespis_derive) holds the code for deriving traits like `Actor`
- [thespis_guide](https://github.com/thespis-rs/thespis_guide) will hold the mdbook user guide (yet to be written)

## State of the project

This is currently in alpha release. _thespis_remote_ has not seen an alpha release yet, but it works. You can have a look at the examples and integration tests in [thespis_impl](https://github.com/thespis-rs/thespis_impl) and [thespis_impl_remote](https://github.com/thespis-rs/thespis_impl_remote) to see it working. There is some API documentation already as well, so you can run `cargo doc --open` to see that. The [guide level docs](https://thespis-rs.github.io/thespis_guide/) are in development.


### What works

- Basic actor functionality (send to and call) actors with `Send` messages. `send` is a one way operation, `call` will give you a return value from the handler.
- Addr implement futures 0.3 Sink and Actors can be Observable generating a Stream, which can be connected to other actor's Addr and manipulated with all the stream combinators from the futures library.
- Remote actors without discovery, you need to have a direct `AsyncRead`/`AsyncWrite` connection.
- Relaying messages to another process.
- Actor supervision.
- log diagnostics with tracing.
- works on Wasm
- The channel between `Addr` and `Mailbox` can be chosen by the user as long as it is `Sink` and `Stream` compatible.

### Roadmap

- [x] verifying it works in WASM
- [x] re-evaluate the runtime. Now uses async_executors. A [nursery now exists](https://crates.io/crates/async_nursery).
- [x] polish the wasm websockets so we can use remote actors to talk to server. (There is now ws_stream_wasm and ws_stream_tungstenite)
- [x] some refactoring (in peer incoming we have a very long method)
- [x] bounded mailbox, ringchannel? Now takes `Sink + Clone` in `Addr` and `Stream` in `Inbox`, so it's possible to plug in different channel impls.
- [ ] benchmarking remote actors
- [ ] flesh out tests for remote.
- [-] documentation and user guide
- [-] publish a version 0.1. Currently in alpha except remote.
- [-] re-evaluate the wire fromat (maybe use capn'proto, flatbuffers or SBE), is now generic so it can be changed. The interface might move to a separate crate again.


## Main differences with other actor libraries

[Actix](https://github.com/actix/actix) is a great library that inspired me for much of the work on thespis. However a number of problems arose for me when trying to use it/work on it:
- stuff that gets in the way: ActorFuture, MessageResponse trait, dns-resolvers, ...
- no remote actors
- not working on WASM
- SLOC of 6000 - thespis: 700 for almost similar functionality
- half of the user guide is WIP
- in thespis handle methods are async and have access to mutable self without Rc<RefCell<X>> and without need for ActorFuture.

Thus it seemed easier to start over and question every design decision. In many cases, actix has made the right choices. The api is neat and thespis ressembles it for the basic traits (Actor/Message/Handler<M>/Recipient, Addr, ...). In fact, **without the inspiration from actix, and how to tackle some of the design problems, I would not have been able to write thespis**.

Actix does have features that thespis doesn't have:
- stopping an actor (in thespis you have to drop all Addresses to it)
- Registries
- actix broker
- ... surely more that I have yet to discover


As for other actor libraries, at the time of writing:
- [kay](https://github.com/aeplay/kay) has as good as no documentation :-( so I didn't look any further. They do have remote actors and a wire format that is also the in memory representation, which probably is fast. Thespis only has a provisional wire format for now, which does not have that property.
- [riker](https://riker.rs/) I admit that I didn't look into it much, but from the example code I saw, it looks as if an actor can only receive messages of one type, which seems like an unpleasant restriction that actix doesn't have.

- in the mean time, actor libraries have spawned like mushrooms, so I have lost track. I think there are still very few that have remote actors.

## Alternatives

- actix
- axiom
- sealrs
- riker
- kay
