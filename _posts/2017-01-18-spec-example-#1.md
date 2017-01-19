---
layout: post
title: "Spec example #1"
date: 2017-01-18
categories: clojure
tags: 
 - programming
 - clojure
 - spec
 - spec examples
---

Recently, I've uploaded a [repository to github](github.com/MrDallOca/spec-examples) with some interesting examples that I've found missing in the web 'bout [spec](https://clojure.org/guides/spec) and [received a suggestion](https://github.com/MrDallOca/spec-examples/issues/1) of making it a series of blog posts. So here it go!

## Example #1: Simple macro for mapping keybindings

This one is a simple example.

Consider the following macro:

```clojure
=> (map-bindings "CTRL-c s" ((println "You pressed CTRL-c s!")
                             (println "Yes, you!"))
                 "CTRL-s"   ((println "You pressed CTRL-s!")
                             (println "Are you trying to save a file?")))

{(3 115) #function[user/eval18254/fn--18255],
 (19) #function[user/eval18254/fn--18257]}
```

It takes pairs of `string` and list of forms and returns a map of number lists and functions (that were made from the lists of forms).

One possible application for this kind of macro is easily binding keystrokes to "actions" for later resolving them (similarly to Emacs).

Enough of explanations, let's get down to the `spec`!

### Spec stuff

If you don't know much how to use regex, I strongly recommend you taking a tour by the following websites:

- [Regex quickstart](http://www.rexegg.com/regex-quickstart.html)
- [Regexr](http://regexr.com/)

Then, after learning regex, you don't how to use `clojure.spec`, take a look at the [offcial guide](https://clojure.org/guides/spec)

Here follows the spec that meets the requirements of map-bindings

```klipse
(require '[clojure.spec :as s])

(def keystroke-combo-reg
  #"((CTRL-)?\w\s)*(CTRL-)?\w")

(s/def ::keystroke-combo (s/and string? #(re-matches keystroke-combo-reg %)))
(s/def ::list-of-forms (s/+ list?))

(s/def ::binding (s/cat :keystroke-combo ::keystroke-combo
                        :list-of-forms ::list-of-forms))

(s/def ::map-bindings (s/+ ::binding))
```

Does it work?

We'll find out.


```klipse
(s/conform ::map-bindings
           '("CTRL-c s" ((println "You pressed CTRL-c s!")
                         (println "Yes, you!"))
             "CTRL-s"   ((println "You pressed CTRL-s!")
                         (println "Are you trying to save a file?"))))
```

Oh my, it does work!

You can find the source code of map-binding and it's complete spec [here](github.com/MrDallOca/spec-examples)


