### A Dataflow Engine for clojure

This package is a dataflow engine for Clojure modeled loosely on the
"Cells" model, but using Clojure Ref's to manage state rather than
Agents.

### Status

This is here for purely Archival purposes.  
You should use javelin.

### Usage

```clojure
(def df
  (build-dataflow [
    ; Source cells are set by the user
    (cell :source fred 1)

    ; Reference other cells by ?name
    (cell greg (+ ?fred ?mary))

    (cell joan (+ ?fred ?mary))

    ; Order does not matter
    (cell :source mary 0)

    (cell joan (* ?fred ?mary))

    ; Multiple joans exist, ?*name returns a collection
    (cell sally (apply + ?*joan))

    ; validators can return exceptions.  ?-name gets the old value
    (cell :validator (when (number? ?-greg)
                    (when (<= ?greg ?-greg)
                        (throwf Exception "Non monotonic"))))
  ]))

(add-cell-watcher (get-cell df 'sally)
                nil
                (fn [key cell o n]
                    (printf "sally changed from %s to %s\n" o n)))

(full-update df)
(update-values df {'fred 1 'mary 1})
(update-values df {'fred 5 'mary 1})
(update-values df {'fred 0 'mary 0}) ; Will throw Non monotonic exception

(get-value df 'fred)
(get-values df 'joan)
(get-value df 'sally)
(get-value df 'greg)
```

### TODO

* lein
* test
* docs

