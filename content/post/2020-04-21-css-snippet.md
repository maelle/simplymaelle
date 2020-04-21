---
title: "How to showcase CSS+JS+HTML snippets with Hugo?"
date: '2020-04-21'
tags:
  - css
  - hugo
slug: css-snippet
---

I've recently found myself having to write a bit of CSS or JS for websites made with Hugo.
_Note for usual readers: it is a topic not directly related to R, but you might have played with either or both CSS and JS for your R blog or Shiny app._
On a scale from [Peter Griffin programming CSS window blinds](https://www.youtube.com/watch?v=-pzckbNyqfc) to [making art with CSS](https://twitter.com/liatrisbian/status/1251239842861678592), I'm sadly much closer to the former; my JS knowledge is not better.
I often scour forums for answers to my numerous and poorly formulated questions, and often end up at code playgrounds like [Codepen](https://codepen.io/) where folks showcase snippets of HTML on its own or with either or both CSS and JS, together with the resulting HTML document.
You can even edit the code and run it again.
Quite neat!

Now, as I was listening to [an episode from the Lady bugs podcast about blogging](https://ladybug.dev/blogging-101), one of the hosts, [Ali Spittel](https://twitter.com/ASpittel), mentioned integrating Codepens into blog posts[^podcast], which sounds useful indeed, and I started wondering: **how could one showcase CSS+JS+HTML snippets on a Hugo website?**
In this post I shall go over three solutions, with and without Codepen, in all cases based on [custom shortcodes](https://gohugo.io/templates/shortcode-templates/#create-custom-shortcodes).

# The easiest way: Embed a Codepen

As reported in a [2018 blog post by Ryan Campbell](https://ryancampbell.blog/blog/codepen-shortcode/) and a [2019 blog post by Jeremy Kinson](https://kinson.io/post/embed-codepen/), Jorin Vogel [created and shared a Hugo shortcode for Codepen](https://github.com/jorinvo/hugo-shortcodes/blob/master/shortcodes/pen.html).

Save it under layouts/pen.html and voilà, you can call it! Below I'll embed a cool CSS art snippet by [Sarah L. Fossheim](https://fossheim.io/).

{{</* pen user="fossheim" id="oNjxrZa" */>}}

Gives

{{< pen user="fossheim" id="oNjxrZa" >}}

For a blog post showcasing code of yours, it might get a bit tiring to create and keep track of Codepens.
Moreover, you might want more ownership of your code.

# The DIY way: load HTML, CSS, and JS code into an iFrame 

I was completely stuck trying to find out how to create and embed my own iframe and then luckily found [a perfect post by Josh Pullen](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls) _"How to Load HTML, CSS, and JS Code into an iFrame"_, with a perfect definition of the problem _"If you've ever used JSFiddle, Codepen, or others, this problem will be familiar to you: The goal is to take some HTML, CSS, and JS (stored as strings) and create an iframe with the code loaded inside."_.  :raised_hands:
Good stuff!

Based on the code in the post, I created a shortcode called "snippet.html".
It is a [paired shortcode](https://gohugo.io/templates/shortcode-templates/#paired-example-highlight): there is input between two markers, and one option passed via the first marker.
It expects input like

{{< highlight go >}}
{{</* snippet an-id-unique-for-the-post */>}}
```css
// css code
```

```html
// HTML code
```

```js
// js code
```
{{</* /snippet */>}}
{{< /highlight >}}

The shortcode itself is shown below.

```html {linenos=table}
<h4>My snippet {{ .Get 0 }}</h4>
{{ $content := .Inner }}
{{ $content := replaceRE "```html" "\n **HTML code:** \n```html" $content }}
{{ $content := replaceRE "```css" "\n **CSS code:** \n```css" $content }}
{{ $content := replaceRE "```js" "\n **JS code:** \n```js" $content }}

{{ $content | markdownify }}

{{ $css := replaceRE "```html(.|\n)*?```" "$1" .Inner }}
{{ $css := replaceRE "```js(.|\n)*?```" "$1" $css }}
{{ $css := replaceRE "```css" "$1" $css }}
{{ $css := replaceRE "```" "$1" $css }}

{{ $js := replaceRE "```html(.|\n)*?```" "$1" .Inner }}
{{ $js := replaceRE "```css(.|\n)*?```" "$1" $js }}
{{ $js := replaceRE "```js" "$1" $js }}
{{ $js := replaceRE "```" "$1" $js }}

{{ $html := replaceRE "```css(.|\n)*?```" "$1" .Inner }}
{{ $html := replaceRE "```js(.|\n)*?```" "$1" $html }}
{{ $html := replaceRE "```html" "$1" $html }}
{{ $html := replaceRE "```" "$1" $html }}

<b>Result:</b><br>

<iframe id="{{ .Get 0 }}" allowfullscreen
      style="width:100%;height:100%;"></iframe>

<script src="/js/blob.js" type="text/javascript"></script> 
<script type="text/javascript">
document.addEventListener('DOMContentLoaded', function() {
   mySnippet('{{ $html }}', '{{ $js }}', '{{ $css }}', '{{ .Get 0 }}');
}, false);
</script> 

```

Lines 2 to 7 create and [markdownify](https://gohugo.io/functions/markdownify/) the three highlighted blocks, with a note on the language before each.
Using markdownify means the code will be [highlighted using Chroma](https://gohugo.io/content-management/syntax-highlighting/) without my having to make any further efforts.

Then there is some non elegant string manipulation going on to extract the CSS, JS and HTML, until line 58.

After that I use the code [from Josh Pullen's post](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls) to create blob URLs and an iframe. 

* I create an iframe with the ID given as argument of the shortcode.

* Once the page is loaded, I call a function defined below and saved under `/js/blob.js`, again recycling code from [Josh Pullen's post](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls), with the HTML, CSS and JS code, as well as the frame ID, as argument. The code creates blob URLs with the JS and CSS, then a blob URL with the HTML calling the JS and CSS, and finally assign that last blob URL to the iframe. :sparkles:

```js
function mySnippet(html, js, css, id) {
  const getGeneratedPageURL = ({ html, css, js }) => {
  const getBlobURL = (code, type) => {
    const blob = new Blob([code], { type });
    const url = URL.createObjectURL(blob);
    return url;
  };

  const cssURL = getBlobURL(css, 'text/css')
  const jsURL = getBlobURL(js, 'text/javascript')

  const source = `
    <html>
      <head>
      <meta charset="utf-8"/>
        ${css && `<link rel="stylesheet" type="text/css" href="${cssURL}" />`}
        ${js && `<script src="${jsURL}" type="text/javascript"></script>`}
      </head>
      <body>
        ${html || ''}
      </body>
    </html>
  `;
  return getBlobURL(source, 'text/html');
};

const url = getGeneratedPageURL({
  html: html,
  css: css,
  js: js
});

const getid = "#" + id;

const iframe = document.querySelector(getid);
iframe.src = url;
};
```

## Demos

In this demo below I use [w3schools.com tutorial about buttons](https://www.w3schools.com/jsref/event_onclick.asp).

{{< highlight go >}}
{{</* snippet number42 */>}}
```css
p {
  color: red;
}
button {
  background-color: pink;
}
```

```html
<p> Some text </p>
<button onclick="getTime()">What time is it??</button>
<p id="demo"></p>
```

```js
function getTime() {
  document.getElementById('demo').innerHTML = Date();
}
```
{{</* /snippet */>}}
{{< /highlight >}}

Gives

{{< snippet number42 >}}
```css
p {
  color: red;
}
button {
  background-color: pink;
}
```

```html
<p> Some text </p>
<button onclick="getTime()">What time is it??</button>
<p id="demo"></p>
```

```js
function getTime() {
  document.getElementById('demo').innerHTML = Date();
}
```
{{< /snippet >}}

Below is another, simpler, one.

{{< highlight go >}}
{{</* snippet number66 */>}}
```css
p {
  color: blue;
}
```

```html
<p> There is no JS in this snippet. </p>
```

{{</* /snippet */>}}
{{< /highlight >}}

Gives

{{< snippet number66 >}}
```css
p {
  color: blue;
}
```

```html
<p> There is no JS in this snippet. </p>
```

{{< /snippet >}}

## To-dos

Clearly, my custom shortcode could do with... styling, which is sort of ironic, but this is left as an exercise to the reader. :wink:

# The mix: own your code, present it through Codepen

A page of Codepen docs caught my attention: ["Prefill Embeds "](https://blog.codepen.io/documentation/prefill-embeds/) _CodePen Prefill Embeds allow you to enhance code that you are already displaying on your own website and transform it into an interactive environment._

Using them make you rely on Codepen, of course, but you can therefore use all of Codepen fixings (even preprocessing!).

I created another shortcode as a proof-of-concept, not encompassing all features. 
In this case I was able to use [nested shortcodes](https://gohugo.io/templates/shortcode-templates/#nested-shortcode-image-gallery).
In the previous solution I didn't find how I could do that given I needed to use the content of each block on its own and together in the iframe.

The shortcode expects input like

{{< highlight go >}}
{{</* prefillembed "A title for the pen" */>}}
  {{</* pcode css */>}}
  // CSS code
  {{</* /pcode */>}}
  
  {{</* pcode html */>}}
  // HTML code
  {{</* /pcode */>}}
  
  {{</* pcode js */>}}
  // JS code
  {{</* /pcode */>}}
  
{{</* /prefillembed */>}}
{{< /highlight >}}

The main shortcode code is quite simple:

```html 
<div 
  class="codepen" 
  data-prefill='{
    "title": "{{ .Get 0 }}"}'
  data-height="400" 
  data-theme-id="1"
  data-default-tab="html,result" 
>
  {{ .Inner }}
</div>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
```

The sub-shortcodes are not much more complicated.
An important aspect is the escaping of HTML, that Codepen docs warn about.
I felt quite proud knowing about `htmlEscape` but it was not enough, I had to pipe the output into `safeHTML` so I was no longer so full of myself after that. :smile_cat:

```html
<pre data-lang="{{ .Get 0 }}">
  {{ if eq (.Get 0) "html" }}
    {{ .Inner| htmlEscape | safeHTML }}
  {{ else }}
    {{ .Inner }}
  {{ end }}
</pre>
```

## Demo

```
{{</* prefillembed "My Pen" */>}}

  {{</* pcode css */>}}
  p {
    color: red;
  }
  button {
    background-color: pink;
  }
  {{</* /pcode */>}}
  
  {{</* pcode html */>}}
  <p> Some text </p>
  <button onclick="getTime()">What time is it??</button>
  <p id="demo"></p>
  {{</* /pcode */>}}
  
  {{</* pcode js */>}}
  function getTime() {
    document.getElementById('demo').innerHTML = Date();
  }
  {{</* /pcode */>}}
  
{{</* /prefillembed */>}}
```

gives

{{< prefillembed "My Pen" >}}

  {{< pcode css >}}
  p {
    color: red;
  }
  button {
    background-color: pink;
  }
  {{< /pcode >}}
  
  {{< pcode html >}}
  <p> Some text </p>
  <button onclick="getTime()">What time is it??</button>
  <p id="demo"></p>
  {{< /pcode >}}
  
  {{< pcode js >}}
  function getTime() {
    document.getElementById('demo').innerHTML = Date();
  }
  {{< /pcode >}}
  
{{< /prefillembed >}}

## To-dos

This shortcode could do with more parameterization to allow using [all features of Codepen's prefill embeds](https://blog.codepen.io/documentation/prefill-embeds/). 

# Conclusion

In this post I went over three ways to showcase CSS+JS+HTML snippets with Hugo: adding a custom shortcode for embedding Codepen; creating a custom shortcode thanks to which the code is displayed in highlighted code blocks but also loaded into an iframe; creating a custom shortcode that uses Codepen prefill embeds.
Each approach has its pros and cons depending on whether or not you want to rely on Codepen.
Please don't hesitate to share your alternative approaches or your extensions of my shortcodes!

Taking a step back, such shortcodes, if much improved, could maybe be shared in [a Hugo theme](https://discourse.gohugo.io/t/how-to-use-multi-theme/19413/4) as a developer toolbelt[^toolblet]? 
Even if copy-pasting shortcodes from someone else's repo, with attribution, works well too. :slight_smile:
It could contain shortcodes for developer websites that use OEmbed (so [not Stack Overflow](https://meta.stackexchange.com/questions/136277/can-we-support-oembed), [not GitHub](https://stackoverflow.com/a/44893092/5489251)), and [unfurling](https://medium.com/slack-developer-blog/everything-you-ever-wanted-to-know-about-unfurling-but-were-afraid-to-ask-or-how-to-make-your-e64b4bb9254) workarounds for others.
Quite a lot to explore!

[^toolblet]: I am using this term because of [Steph Locke's Hugo utility belt](https://github.com/lockedatapublished/hugo-utilitybelt).
[^podcast]: I looked up the [episode transcript](https://github.com/ladybug-podcast/ladybug-website/blob/master/transcripts/03-blogging-101.md) to find out which of the hosts said that because I can't recognize their voices (yet?). :grin:
