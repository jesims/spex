# spex
<!-- [![Build Status](https://secure.travis-ci.org/mpenet/spex.png?branch=master)](http://travis-ci.org/mpenet/spex) -->

Small utility/extension library for `clojure.spec`.

Subject to changes/breakage. Use at own risk.

At the moment it does only two things and does not rely on spec
internals tricks at the cost of some user friendlyness sometimes.

* adds a sugar to create namespaces within a ns
  If you are in the user namespace
  `(spex/rel-ns 'foo.bar)` would create user.foo.bar

* adds a metadata registry for registered specs, it currently supports
  variants of `vary-meta!`, `with-meta!`, `meta`, adds
  `unregister-meta!` and `with-doc`.

  you can then write code like:
  ```clj
  (-> (s/def ::foo string?)
      (spex/vary-meta! {:something :you-need}))

   ;; and retrieve the values with spex/meta
  (spex/meta ::foo) => {:something :you-need}
  ```
  But it would get tedious quickly when you alias specs and in cases where you might want to inherit from a "hierarchy" of specs
  If you `clojure.core/derive` one spec with another you can use the other arity of `spex/meta` and get an aggregate of values
  ```clj
  (s/def ::bar string?)

  (derive ::bar ::foo)

  (s/meta ::bar) => {:something :you-need}

  (spex/vary-meta! ::bar {:another :key})
  ;; just the meta of ::bar
  (s/meta ::bar) => {:another :key}

  :; merged meta of ::foo and ::bar
  (s/meta ::bar true) => {:something :you-need, :another :key}
  ```

  and `with-doc` is just sugar on top of all this to add docstrings to specs


  ```clj
  (with-doc ::foo "bla bla bla")
  ```

  All the functions that mutate the metadata of a spec return the spec
  key, that makes chaining easier:

  ```clojure
  (-> (s/def ::foo string?)
      (spex/vary-meta! {:something :you-need})
      (cond->
        something?
        (spex/vary-meta! {:something-else :you-might-need})))
  ```
## Installation

spex is [available on Clojars](https://clojars.org/cc.qbits/spex).

Add this to your dependencies:

```clojure
[cc.qbits/spex "0.1.0-SNAPSHOT"]
```

Please check the
[Changelog](https://github.com/mpenet/spex/blob/master/CHANGELOG.md)
if you are upgrading.

## License

Copyright © 2016 [Max Penet](http://twitter.com/mpenet)

Distributed under the
[Eclipse Public License](http://www.eclipse.org/legal/epl-v10.html),
the same as Clojure.
