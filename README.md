# hx

A modern hiccup compiler, targeted at maximal React interop.

## Usage

```clojure
(require '[hx.react :as hx])
(require '[react :as react])

(react/render
  (hx/compile
    $[:span {:style {:font-weight "bold"}} "Hello, world!"])
             
  (. js/document getElementById "app"))
```

## Goals

1. **Simple** interop between vanilla React features and CLJS code, to ease
   adoption of new features & technologies in the JS world.
   
2. **Performant** parsing of hiccup syntax; impact is minimized by using macros,
   to remove the need for runtime parsing of hiccup and minimize marshalling of
   CLJS data.
   
3. **Extensible** API so that parsing, analysis & code generation of the hiccup
   compiler can evolve to meet the needs of different ecosystems.
   
   
## Top-level API

### hx.react/compile: ([& form])

This macro takes in an arbitrary clojure form. It parses all `$` to mean "parse
the next form into React `createElement` calls."

Example usage:

```clojure
(require '[hx.react :as hx])

(hx/compile
   (let [numbers [1 2 3 4 5]]
     $[:ul {:style {:list-style-type "square"}}
       (map #(do $[:li {:key %} %])
            numbers)]))
```

Will become the equivalent:

```clojure
(let [numbers [1 2 3 4 5]]
  (createElement "ul" #js {:style #js {:listStyleType "square"}}
    (map #(do (createElement "li" #js {:key %} %)])
         numbers)]))
```

### hx.react/defnc: ([name props-bindings & body])

This macro is just like `defn`, but has some helpers for defining functional
React components. Takes a name, props bindings and a body that will be passed to
`hx.react/compile`.

Example usage:
```clojure
(require '[hx.react :as hx])

(hx/defnc greeting [{:keys [name] :as props}]
  $[:span {:style {:font-size "24px"}}
    "Hello, " name "!"])

(react/render
  (hx/compile
    $[greeting {:name "Tara"}])
    
  (. js/document getElementById "app"))
```

## License

Copyright © 2018 Will Acton

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
