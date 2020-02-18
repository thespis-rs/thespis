# thespis

A minimal actor model for Rust.

## References:

- [Actor Model of Computation: Scalable Robust Information Systems](https://arxiv.org/abs/1008.1459) by Carl Hewitt.
- video: [Hewitt, Meijer and Szyperski: The Actor Model (everything you wanted to know...)](https://youtu.be/7erJ1DV_Tlo)

## Design goals

Thespis has the following goals:

- target futures 0.3 and async/await immediately, so won't compile on stable for a while.
- separate interface from implementation. Clients can use the interface while freely chosing which implementation they want for each concept: Addr, Mailbox, Envelope, ... Everybody can make their own implementations, yet stay compatible with all other actors. Libraries can expose actors without choosing an implementation, and let the binary application choose.
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

This is currently in an early development phase and won't be published officially for at least another few months. It is functional however and you can have a look at the examples and integration tests in [thespis_impl](https://github.com/thespis-rs/thespis_impl) and [thespis_impl_remote](https://github.com/thespis-rs/thespis_impl_remote) to see it working. There is some untested API documentation already as well, so you can run `cargo doc --open` to see that.


### What works

- Basic actor functionality (send to and call) actors with `Send` messages. `send` is a one way operation, `call` will give you a return value of the handler.
- Recipients implement futures 0.3 Sink and Actors can be Observable generating a Stream, which can be connected to other actor's Addr and manipulated with all the stream combinators from the futures library.
- Remote actors without discovery, you need to directly have a Framed sink/stream over a custom wire format.
- Relaying messages to another process

### What's next (hopefully all in 2019)

- [ ] documentation and user guide
- [x] verifying it works in WASM
- [ ] re-evaluate the runtime (is now in the [naja_async_runtime](https://github.com/najamelan/async_runtime) crate), this needs some further thought. A nursery would be nice.
- [x] polish the wasm websockets so we can use remote actors to talk to server. (There is now ws_stream_wasm and ws_stream_tungstenite)
- [ ] benchmarking remote actors
- [x] some refactoring (in peer incoming we have a very long method)
- [ ] publish a version 0.1
- [ ] bounded mailbox, ringchannel?
- [ ] re-evaluate the wire fromat (maybe use capn'proto, flatbuffers or SBE)


## Main differences with other actor libraries

[Actix](https://github.com/actix/actix) is a great library that inspired me for much of the work on thespis. However a number of problems arose for me when trying to use it/work on it:
- not targeting async/await
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
- Supervised actors
- actix broker
- currently thespis_impl only has unbounded channels backing the mailbox
- ... surely more that I have yet to discover


As for other actor libraries, at the time of writing:
- [kay](https://github.com/aeplay/kay) has as good as no documentation :-( so I didn't look any further. They do have remote actors and a wire format that is also the in memory representation, which probably is fast. Thespis only has a provisional wire format for now, which does not have that property.
- [riker](https://riker.rs/) I admit that I didn't look into it much, but from the example code I saw, it looks as if an actor can only receive messages of one type, which seems like an unpleasant restriction that actix doesn't have.

## Alternatives

- actix
- axiom
- sealrs
- riker
- kay
