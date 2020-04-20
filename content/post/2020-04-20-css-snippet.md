---
title: "What to know before you adopt Hugo/blogdown"
date: '2020-04-20'
tags:
  - css
  - hugo
slug: css-snippet
---

https://www.w3schools.com/jsref/event_onclick.asp

https://dev.to/pulljosh/how-to-load-html-css-and-js-code-into-an-iframe-2blc#solution-blob-urls


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
