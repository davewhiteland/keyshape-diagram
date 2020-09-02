# Keyshape Diagram

JavaScript support for stepping through animated SVG diagrams produced by
[Keyshape](https://www.keyshapeapp.com).

What you get:

* three buttons for stepping, running, and stopping (or resetting) the animation
* captions displayed as the animation progresses


<link href="src/ksd.css" media="screen, projector, print" rel="stylesheet" type="text/css" />
<div class="example" style="border:1px solid #ccc; padding:1em; margin:1em 0;">
  <object class="ksd" id="server-svg" data="examples/http-200.svg" type="image/svg+xml">
    <ol class="ksd-captions">
      <li id="start">
        The example starts with a call to <code>example.com/cat.html</code>
      </li>
      <li id="get-cat-html">
        The client (browser) makes a GET request for the resource (<code>cat.html</code>)
      </li>
      <li id="200-cat-html">
        The webserver serves the GET request: it sends a response
        with <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" target="_blank">code&nbsp;200</a>
        (“success”) and <code>cat.html</code> as the payload.
      </li>
      <li id="get-css-and-img">
        The browser parses the HTML page and tries to display it.
        The page contains CSS and an image, so the browser requests
        each of these with new GET requests.
      </li>
      <li id="200-css-and-img">
        The server responds to each request with 
        <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" target="_blank">code&nbsp;200</a>
        (“success”) and the requested file as the payload.
      </li>
      <li id="end">
        The CSS arrives and is applied (the page goes pink). The
        image arrives and is displayed (the cat appears).
      </li>
    </ol>
  </object>
  <div class="ksd-no-js">
    An interactive version of this diagram is available with JavaScript enabled.
  </div>
</div>

How you get it:

* create diagram in Keyshape with _named timeline markers_
* export as SVG (with embedded or external JS)
* add it to an `<object>` element with `class="ksd"` 
* drop an element _inside the object_ (e.g., `<div>` or `<ol>`) whose children
  are captions, one per timeline marker
* add the JavaScript (`ksd.js` or its contents)
* add the styling for the buttons (`ksd.css` or its contents)
* tweak some options (e.g., layout order of buttons/captions/diagram)
* optionally add no-JavaScript fallback


## Files

* [examples](https://davewhiteland.github.io/keyshape-diagram/examples) in `examples/` (more to come)
* [documentation](https://davewhiteland.github.io/keyshape-diagram/docs) in `docs.md`
* [JavaScript and CSS](https://github.com/davewhiteland/keyshape-diagram/tree/main/src) in `src/`

### Sample HTML

```html
<link href="keyshape-diagram.css" rel="stylesheet" type="text/css" />
<object class="ksd" data="my-diagram.svg" type="image/svg+xml">
  <ol class="ksd-captions">
    <li>
      This is shown before you press <em>step</em> or <em>run</em>.
    </li>
    <li>
      This is displayed while the animation runs (starting from timeline marker
      "start") up to timeline marker "next".
    </li>
    <li>
      This is the caption that is shown as the animation runs
      from "next" to "end".
    </li>
  </ol>
</object>
<div class="ksd-no-js">
  This content is displayed if JavaScript (and hence animation)
  is not supported on this page load.
</div>
<script src="keyshape-diagram.js"></script>
```

<script src="src/ksd.js" type="text/javascript"></script>
