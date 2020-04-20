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
On a scale from [Peter Griffin programming CSS window blinds](https://www.youtube.com/watch?v=-pzckbNyqfc) to [making art with CSS](https://twitter.com/liatrisbian/status/1251239842861678592), I'm sadly much closer to the former; my JS knowledge is not better.
I often scour forums for answers to my numerous and poorly formulated questions, and often end up at code playgrounds like Codepen where folks showcase snippets of html on its own or with either or both CSS and JS, together with the resulting html document.
You can even edit the code and run it again.
Now, as I was listening to the Lady bugs podcast episode about blogging, one of the hosts mentioned linking Codepen from blog posts and I started wondering about how one could showcase CSS+JS+html snippets on a Hugo website.
In this post I shall go over three solutions, with and without Codepen.

https://www.w3schools.com/jsref/event_onclick.asp

https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls

# The DIY way: a custom shortcode for loading HTML, CSS, and JS code into an iFrame 

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

# The easy way: embed a Codepen

# The compromise: own your code, link to Codepen