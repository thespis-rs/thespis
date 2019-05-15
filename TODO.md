## Useful Tools:

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

