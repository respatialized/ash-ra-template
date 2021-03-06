# Ash Ra Template Leiningen Plugin [![Clojars Project](https://img.shields.io/clojars/v/vivid/lein-art.svg?color=239922&style=flat-square)](https://clojars.org/vivid/lein-art)

Leiningen plugin for rendering [Ash Ra Template](https://github.com/vivid-inc/ash-ra-template) `.art` templates.



## Usage

Provided one or more ART template files, the `art` Leiningen task writes rendered output to a specified output dir.

In Leiningen `project.clj`:

```clojure
  # Add the lein-art Leiningen plugin:
  :plugins [[vivid/lein-art "0.5.0"]]

  # Render .art templates
  :art {:templates    COLL-OF-FILES
        :bindings     SEQ-OF-MAP-VAR-EDN-FILE
        :delimiters   MAP
        :dependencies MAP
        :output-dir   DIR}
```

Examples:

```clojure
  # Rendered output written to target/index.html
  :art {:templates ["index.html.art"]}

  # Renders all .art template files in the content/ directory to out/cdn/
  :art {:templates (filter (#.endsWith (.getName %) ".art")
                           (file-seq (clojure.java.io/file "content")))

        :bindings     [{:manufacturer     "Acme Inc."          # Map literal
                        :manufacture-year "2019"}
                       com.acme.data/all-data                  # Var, value is a map
                       "data/tabular.edn"]                     # EDN file; top-level form is a map

        :delimiters   vivid.art.delimiters/jinja

        :dependencies {'hiccup {:mvn/version "1.0.5"}
                       'com.acme.core {:mvn/version "1.0.0"    # Use local project from within template code
                                       :local/root  "."}}

        :output       "out/cdn"}
```

**Command-line** usage:

```
  $ lein art [template-file ...] [options]
```

and options:

```clojure
  -b, --bindings EDN-OR-VAR              Bindings made available to templates for symbol resolution
  -d, --delimiters EDN-OR-VAR    erb     Template delimiters
      --dependencies EDN-OR-VAR          Clojure deps map
  -o, --output-dir DIR           target  Write rendered files to DIR
```

From the CLI, the `art` Lein task takes a list of file paths to `.art` files (ART templates) and options.
CLI arguments can be freely mixed.

Depending on what types of values a particular option accepts and whether ART is running from within Lein or from a command-line invocation, ART attempts to interpret each argument in the following order:
1. As a map literal.
1. As the (un-)qualified name of a var.
1. As a path to an EDN file.
1. As an EDN literal.

`bindings` are processed in order of appearance where symbol redefinitions clobber prior values.
This might be important to you in the event of collisions.

`output-dir` will be created if necessary.
The `.art` filename extension is stripped from the rendered output filenames.
For example, `index.html.art` is rendered to the file `index.html`.
Output files will overwrite files that exist with the same filenames.



## Development

Run the tests with

```bash
lein test
```



## License

© Copyright Vivid Inc.
[EPL](LICENSE.txt) licensed.
