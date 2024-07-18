---
title: "Extracting names of functions defined in a script with treesitter"
date: '2024-07-18'
slug: extract-function-names-treesitter
output: hugodown::hugo_document
rmd_hash: 7422d54610a7c9c0

---

Coming back from a conference, we might be excited to install and try out the cool things we have heard about. I, going against the stream :fish:, decided to experiment with a tool I have *not* heard about last week, as I unfortunately missed [Davis Vaughan's talk about treesitter](https://www.youtube.com/watch?v=Gm0ikRBAfwc). Nonetheless, I caught the idea that treesitter is a parser of code, R code in particular. The treesitter *R package* uses the tree-sitter *C library*. There are awesome applications of treesitter among which :sparkles: code search for R on GitHub :sparkles: but I learnt to know a bit of treesitter by solving a boring use case.

## My use case: I won't copy-paste these function names

Have you noticed this nice error message you get if you try to use [`dplyr::across()`](https://dplyr.tidyverse.org/reference/across.html) directly?

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>dplyr</span><span class='nf'>::</span><span class='nf'><a href='https://dplyr.tidyverse.org/reference/across.html'>across</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00; font-weight: bold;'>Error</span><span style='font-weight: bold;'> in `dplyr::across()`:</span></span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>!</span> Must only be used inside data-masking verbs like `mutate()`,</span></span>
<span><span class='c'>#&gt;   `filter()`, and `group_by()`.</span></span>
<span></span></code></pre>

</div>

Kirill Müller got the idea to offer a similar behaviour to some igraph functions that exist only for use only within the square operator. Therefore I had to find all functions defined *within* this function (whose body has been simplified here):

    `[.igraph.vs` <- function(...) {
      # some code
      bla <- function(...) {
        # some code
      }
      blop <- function(...) {
        # some code
      }
    }

On the script outline I get on the right of my script in RStudio IDE[^1], I can see the function names (that would be `bla()` and `blop()` in the simplified chunk above) but not copy-paste from there.

I would know how to extract them with [xmlparsedata](/2024/05/15/refactoring-xml/) but I thought it might be an opportunity for me to have a look at treesitter. I went through different emotions as a beginner, not all of them positive :sob:, but I did get the function names in the end! :muscle:

## Get the whole tree from the script

Below, I dutifully followed the example on the [package homepage](https://davisvaughan.github.io/r-tree-sitter/index.html#example):

-   load the R language from the treesitter.r package, as far as I understand the only language available at this point for the R package.
-   load the parser for that language.
-   read in the text.
-   parse the text.

A last step is getting the tree root node as node, because I will query the whole script, and you can only query *nodes*, not *trees*.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>language</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter.r</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter.r/man/language.html'>language</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nv'>parser</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/parser.html'>parser</a></span><span class='o'>(</span><span class='nv'>language</span><span class='o'>)</span></span>
<span><span class='nv'>text</span> <span class='o'>&lt;-</span> <span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/read_lines.html'>read_lines</a></span><span class='o'>(</span><span class='s'>"/home/maelle/Documents/rigraph/R/iterators.R"</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span>collapse <span class='o'>=</span> <span class='s'>"\n"</span><span class='o'>)</span></span>
<span><span class='nv'>tree</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/parser-parse.html'>parser_parse</a></span><span class='o'>(</span><span class='nv'>parser</span>, <span class='nv'>text</span><span class='o'>)</span></span>
<span><span class='nv'>node</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/tree_root_node.html'>tree_root_node</a></span><span class='o'>(</span><span class='nv'>tree</span><span class='o'>)</span></span></code></pre>

</div>

## Find the node of the parent function of interest

The documentation page of [`treesitter::query()`](https://davisvaughan.github.io/r-tree-sitter/reference/query.html) recommends reading the documentation of tree-sitter about the [query syntax](https://tree-sitter.github.io/tree-sitter/using-parsers#query-syntax). That documentation features very useful examples.

Below I am looking for "binary_operator" whose left hand side is an identifier that I capture as "name", and whose right hand side is a "function_definition" I capture as "def". For some reason if I did not capture "def" then I got less information about the node. :shrug: I also use a predicate: the name of the function has to be equal to "`[.igraph.vs`".

To find out I was after a "function_definition", I parsed a few lines of made-up code to study the resulting tree.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>square_bracket_thing</span> <span class='o'>&lt;-</span> <span class='s'>'</span></span>
<span><span class='s'>(</span></span>
<span><span class='s'>((binary_operator</span></span>
<span><span class='s'>  lhs: (identifier) @name</span></span>
<span><span class='s'>  rhs: (function_definition)) @def</span></span>
<span><span class='s'>  (#eq? @name "`[.igraph.vs`"))</span></span>
<span><span class='s'>  </span></span>
<span><span class='s'>)</span></span>
<span><span class='s'>'</span></span>
<span><span class='nv'>square_bracket_query</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/query.html'>query</a></span><span class='o'>(</span><span class='nv'>language</span>, <span class='nv'>square_bracket_thing</span><span class='o'>)</span></span></code></pre>

</div>

Then I executed the query and extracted the node. In reality this took a bit more trial and error.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>square_bracket_thing_captures</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/query-matches-and-captures.html'>query_captures</a></span><span class='o'>(</span><span class='nv'>square_bracket_query</span>, <span class='nv'>node</span><span class='o'>)</span></span>
<span><span class='nv'>square_bracket_thing_captures</span></span>
<span><span class='c'>#&gt; $name</span></span>
<span><span class='c'>#&gt; [1] "def"  "name"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node</span></span>
<span><span class='c'>#&gt; $node[[1]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; `[.igraph.vs` &lt;- function(x, ..., na_ok = FALSE) &#123;</span></span>
<span><span class='c'>#&gt;   args &lt;- lazy_dots(..., .follow_symbols = FALSE)</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt;   ## If indexing has no argument at all, then we still get one,</span></span>
<span><span class='c'>#&gt;   ## but it is "empty", a name that is  ""</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt;   ## Special case, no argument (but we might get an artificial</span></span>
<span><span class='c'>#&gt;   ## empty one</span></span>
<span><span class='c'>#&gt;   if (length(args) &lt; 1 ||</span></span>
<span><span class='c'>#&gt;     (length(args) == 1 &amp;&amp; inherits(args[[1]]$expr, "name") &amp;&amp;</span></span>
<span><span class='c'>#&gt;       as.character(args[[1]]$expr) == "")) &#123;</span></span>
<span><span class='c'>#&gt;     return(x)</span></span>
<span><span class='c'>#&gt;   &#125;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt;   ## Special case: single numeric argument</span></span>
<span><span class='c'>#&gt;   if (length(args) == 1 &amp;&amp; inherits(args[[1]]$expr, "numeric")) &#123;</span></span>
<span><span class='c'>#&gt;     res &lt;- simple_vs_index(x, args[[1]]$expr, na_ok)</span></span>
<span><span class='c'>#&gt;     return(add_vses_graph_ref(res, get_vs_graph(x)))</span></span>
<span><span class='c'>#&gt;   &#125;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt;   ## Special case: single symbol argument, no such attribute</span></span>
<span><span class='c'>#&gt;   if (length(args) == 1 &amp;&amp; inherits(args[[1]]$expr, "name")) &#123;</span></span>
<span><span class='c'>#&gt;     graph &lt;- get_vs_graph(x)</span></span>
<span><span class='c'>#&gt;     if (!(as.character(args[[1]]$expr) %in% vertex_attr_names(graph))) &#123;</span></span>
<span><span class='c'>#&gt;       res &lt;- simple_vs_index(x, lazy_eval(args[[1]]), na_ok)</span></span>
<span><span class='c'>#&gt; <span style='font-style: italic;'>&lt;truncated&gt;</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>binary_operator <span style='color: #555555;'>[(479, 0), (637, 1)]</span></span></span>
<span><span class='c'>#&gt;   lhs: <span style='color: #00BB00;'>(</span>identifier <span style='color: #555555;'>[(479, 0), (479, 13)]</span><span style='color: #00BB00;'>)</span></span></span>
<span><span class='c'>#&gt;   operator: "&lt;-" <span style='color: #555555;'>[(479, 14), (479, 16)]</span></span></span>
<span><span class='c'>#&gt;   rhs: <span style='color: #00BB00;'>(</span>function_definition <span style='color: #555555;'>[(479, 17), (637, 1)]</span></span></span>
<span><span class='c'>#&gt;     name: "function" <span style='color: #555555;'>[(479, 17), (479, 25)]</span></span></span>
<span><span class='c'>#&gt;     parameters: <span style='color: #BB0000;'>(</span>parameters <span style='color: #555555;'>[(479, 25), (479, 48)]</span></span></span>
<span><span class='c'>#&gt;       open: "(" <span style='color: #555555;'>[(479, 25), (479, 26)]</span></span></span>
<span><span class='c'>#&gt;       parameter: <span style='color: #00BBBB;'>(</span>parameter <span style='color: #555555;'>[(479, 26), (479, 27)]</span></span></span>
<span><span class='c'>#&gt;         name: <span style='color: #BBBB00;'>(</span>identifier <span style='color: #555555;'>[(479, 26), (479, 27)]</span><span style='color: #BBBB00;'>)</span></span></span>
<span><span class='c'>#&gt;       <span style='color: #00BBBB;'>)</span></span></span>
<span><span class='c'>#&gt;       <span style='color: #00BBBB;'>(</span>comma <span style='color: #555555;'>[(479, 27), (479, 28)]</span><span style='color: #00BBBB;'>)</span></span></span>
<span><span class='c'>#&gt;       parameter: <span style='color: #00BBBB;'>(</span>parameter <span style='color: #555555;'>[(479, 29), (479, 32)]</span></span></span>
<span><span class='c'>#&gt;         name: <span style='color: #BBBB00;'>(</span>dots <span style='color: #555555;'>[(479, 29), (479, 32)]</span><span style='color: #BBBB00;'>)</span></span></span>
<span><span class='c'>#&gt;       <span style='color: #00BBBB;'>)</span></span></span>
<span><span class='c'>#&gt;       <span style='color: #00BBBB;'>(</span>comma <span style='color: #555555;'>[(479, 32), (479, 33)]</span><span style='color: #00BBBB;'>)</span></span></span>
<span><span class='c'>#&gt;       parameter: <span style='color: #00BBBB;'>(</span>parameter <span style='color: #555555;'>[(479, 34), (479, 47)]</span></span></span>
<span><span class='c'>#&gt;         name: <span style='color: #BBBB00;'>(</span>identifier <span style='color: #555555;'>[(479, 34), (479, 39)]</span><span style='color: #BBBB00;'>)</span></span></span>
<span><span class='c'>#&gt;         "=" <span style='color: #555555;'>[(479, 40), (479, 41)]</span></span></span>
<span><span class='c'>#&gt;         default: <span style='color: #BBBB00;'>(</span>false <span style='color: #555555;'>[(479, 42), (479, 47)]</span><span style='color: #BBBB00;'>)</span></span></span>
<span><span class='c'>#&gt;       <span style='color: #00BBBB;'>)</span></span></span>
<span><span class='c'>#&gt;       close: ")" <span style='color: #555555;'>[(479, 47), (479, 48)]</span></span></span>
<span><span class='c'>#&gt;     <span style='color: #BB0000;'>)</span></span></span>
<span><span class='c'>#&gt;     body: <span style='color: #BB0000;'>(</span>braced_expression <span style='color: #555555;'>[(479, 49), (637, 1)]</span></span></span>
<span><span class='c'>#&gt;       open: "&#123;" <span style='color: #555555;'>[(479, 49), (479, 50)]</span></span></span>
<span><span class='c'>#&gt;       body: <span style='color: #00BBBB;'>(</span>binary_operator <span style='color: #555555;'>[(480, 2), (480, 49)]</span></span></span>
<span><span class='c'>#&gt; <span style='font-style: italic;'>&lt;truncated&gt;</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[2]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; `[.igraph.vs`</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(479, 0), (479, 13)]</span><span style='color: #0000BB;'>)</span></span></span>
<span></span><span><span class='nv'>square_fn</span> <span class='o'>&lt;-</span> <span class='nv'>square_bracket_thing_captures</span><span class='o'>$</span><span class='nv'>node</span><span class='o'>[[</span><span class='m'>1</span><span class='o'>]</span><span class='o'>]</span></span></code></pre>

</div>

At this point I was already very proud of my tiny win but I did not have the "children functions" yet!

## Find the functions defined within the parent

I first considered creating a complicated nested query but I found no example of that. I did see someone telling StackOverflow they did a recursive query and for some reason that gave me the idea of parsing the text of the node, then look for functions in that text.

The query was simpler: looking for function definitions, only capturing the names on the left hand side.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>square_tree</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/parser-parse.html'>parser_parse</a></span><span class='o'>(</span><span class='nv'>parser</span>, <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/node_text.html'>node_text</a></span><span class='o'>(</span><span class='nv'>square_fn</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nv'>square_node</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/tree_root_node.html'>tree_root_node</a></span><span class='o'>(</span><span class='nv'>square_tree</span><span class='o'>)</span></span>
<span><span class='nv'>kiddos_source</span> <span class='o'>&lt;-</span> <span class='s'>'</span></span>
<span><span class='s'>(</span></span>
<span><span class='s'>(binary_operator</span></span>
<span><span class='s'>  lhs: (identifier) @name</span></span>
<span><span class='s'>  rhs: (function_definition))</span></span>
<span><span class='s'>)</span></span>
<span><span class='s'>'</span></span>
<span><span class='nv'>kiddos_query</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/query.html'>query</a></span><span class='o'>(</span><span class='nv'>language</span>, <span class='nv'>kiddos_source</span><span class='o'>)</span></span>
<span><span class='nv'>square_bracket_thing_captures</span> <span class='o'>&lt;-</span> <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/treesitter/man/query-matches-and-captures.html'>query_captures</a></span><span class='o'>(</span><span class='nv'>kiddos_query</span>, <span class='nv'>square_node</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>square_bracket_thing_captures</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; $name</span></span>
<span><span class='c'>#&gt;  [1] "name" "name" "name" "name" "name" "name" "name" "name" "name" "name"</span></span>
<span><span class='c'>#&gt; [11] "name" "name" "name" "name"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node</span></span>
<span><span class='c'>#&gt; $node[[1]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; `[.igraph.vs`</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(0, 0), (0, 13)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[2]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .nei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(29, 2), (29, 6)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[3]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; nei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(50, 2), (50, 5)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[4]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .innei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(53, 2), (53, 8)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[5]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; innei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(56, 2), (56, 7)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[6]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .outnei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(59, 2), (59, 9)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[7]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; outnei</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(62, 2), (62, 8)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[8]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .inc</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(65, 2), (65, 6)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[9]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; inc</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(78, 2), (78, 5)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[10]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; adj</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(81, 2), (81, 5)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[11]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .from</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(84, 2), (84, 7)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[12]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; from</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(96, 2), (96, 6)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[13]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; .to</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(99, 2), (99, 5)]</span><span style='color: #0000BB;'>)</span></span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $node[[14]]</span></span>
<span><span class='c'>#&gt; &lt;tree_sitter_node&gt;</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── Text ────────────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; to</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; ── S-Expression ────────────────────────────────────────────────────────────────</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB;'>(</span>identifier <span style='color: #555555;'>[(111, 2), (111, 4)]</span><span style='color: #0000BB;'>)</span></span></span>
<span></span></code></pre>

</div>

After that I was able to get the *names* of the children functions! I was actually only interested in those whose names start with a dot as all the other ones are deprecated anyway.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>kiddos_functions</span> <span class='o'>&lt;-</span> <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/map.html'>map_chr</a></span><span class='o'>(</span><span class='nv'>square_bracket_thing_captures</span><span class='o'>$</span><span class='nv'>node</span>, <span class='nf'>treesitter</span><span class='nf'>::</span><span class='nv'><a href='https://rdrr.io/pkg/treesitter/man/node_text.html'>node_text</a></span><span class='o'>)</span></span>
<span><span class='nv'>kiddos_functions</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/startsWith.html'>startsWith</a></span><span class='o'>(</span><span class='nv'>kiddos_functions</span>, <span class='s'>"."</span><span class='o'>)</span><span class='o'>]</span></span>
<span><span class='c'>#&gt; [1] ".nei"    ".innei"  ".outnei" ".inc"    ".from"   ".to"</span></span>
<span></span></code></pre>

</div>

Tada! Now I can go fix the issue I was tasked with.

## Conclusion

In this post I report on my first encounter with the treesitter package for parsing R code. Copy-pasting the few function names would surely have been faster, but sometimes you've got to sit and [learn something new](https://www.pipinghotdata.com/posts/2022-11-02-regular-intentional-and-time-boxed-yak-shaving/). :relaxed:

[^1]: No, I have not installed Positron yet.

