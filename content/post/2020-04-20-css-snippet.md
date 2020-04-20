---
title: "How to showcase CSS+JS+html snippets with Hugo?"
date: '2020-04-20'
tags:
  - css
  - hugo
slug: css-snippet
---

I've recently found myself having to write a bit of CSS or JS for Hugo websites.
_Note for usual readers: it is a topic not directly related to R, but you might have played with either or both CSS and JS for your R blog or Shiny app._
On a scale from [Peter Griffin programming CSS window blinds](https://www.youtube.com/watch?v=-pzckbNyqfc) to [making art with CSS](https://twitter.com/liatrisbian/status/1251239842861678592), I'm sadly much closer to the former[^css]; my JS knowledge is not better.
I often scour forums for answers to my numerous and poorly formulated questions, and often end up at code playgrounds like Codepen where folks showcase snippets of html on its own or with either or both CSS and JS, together with the resulting html document.
You can even edit the code and run it again.
Now, as I was listening to [an episode from the Lady bugs podcast about blogging](https://ladybug.dev/blogging-101), one of the hosts, Ali Spittel, mentioned integrating Codepens and I started wondering: **how can one showcase CSS+JS+html snippets on a Hugo website?**
In this post I shall go over three solutions, with and without Codepen.






# The easiest way: embed a Codepen

As reported in a [2018 blog post by Ryan Campbell](https://ryancampbell.blog/blog/codepen-shortcode/) and a [2019 blog post by Jeremy Kinson](https://kinson.io/post/embed-codepen/), Jorin Vogel [created and shared a Hugo shortcode for Codepen](https://github.com/jorinvo/hugo-shortcodes/blob/master/shortcodes/pen.html).

Save it under layouts/pen.html and voilà, you can call it! Below I'll embed a cool CSS art snippet by [Sarah L. Fossheim](https://fossheim.io/).

{{</* pen user="fossheim" id="oNjxrZa" */>}}

Gives

{{< pen user="fossheim" id="oNjxrZa" >}}

For a blog post showcasing code of yours, it might get a bit tiring to create and keep track of Codepens.
Moreover, you might want more ownership of your code.

# The DIY way: a custom shortcode for loading HTML, CSS, and JS code into an iFrame 

I was completely stuck trying to find out how to create and embed my own iframe and then luckily found [a perfect post by Josh Pullen](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls) _"How to Load HTML, CSS, and JS Code into an iFrame"_.
Good stuff!

Based on the code in the post, I created a shortcode called "snippet.html"


```html {linenos=table}
<h4>My snippet {{ .Get 0 }}</h4>
{{ $content := .Inner }}
{{ $content := replaceRE "```html" "\n **html code:** \n```html" $content }}
{{ $content := replaceRE "```css" "\n **css code:** \n```css" $content }}
{{ $content := replaceRE "```js" "\n **js code:** \n```js" $content }}

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

Lines 2 to 7 create and markdownify the three highlighted blocks, with a note on the language before each.

Then there is some non elegant string manipulation going on to extract the CSS, JS and html until line 58.

I then use the code [from Josh Pullen's post](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls) to create blob URLs and an iframe. 

* I create an iframe with the ID given as argument of the shortcode.

* Once the page is loaded, I call a function defined below, using code from [Josh Pullen's post](https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls), with the html, CSS and JS code, as well as the frame ID, as argument. The code creates blob URLs with the JS and CSS, then a blob URL with the html calling the JS and CSS, and finally assign that last blob URL to the iframe.

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

In this demo below I use [w3schools.com tutorial about buttons](https://www.w3schools.com/jsref/event_onclick.asp)

{{< highlight go >}}
{{</* snippet number42 */>}}
```css
p {
  color: red;
}
.button {
  background-color: pink;
}
```

```html
<p> lalala </p>
<button onclick="getTime()">What is the time?</button>
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
.button {
  background-color: pink;
}
```

```html
<p> lalala </p>
<button onclick="getTime()">What is the time?</button>
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

Clearly, my custom shortcode could do with... styling, which is sort of ironic, but this is left as an exercise to the reader.

# The compromise: own your code, link to Codepen

[^css]: Yes that's why this website could be prettier, but it has at least has a good colour!

# Conclusion

In this post I went other three ways to showcase CSS+JS+html snippets with Hugo: adding a custom shortcode for embedding Codepen; creating and styling your own shortcode where the code is loaded into an iframe; building upon such a code 