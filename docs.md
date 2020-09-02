---
title: KeyshapeDiagram docs
---

# KeyshapeDiagram: `ksd.js`

# What

If you have...

* an **animated SVG diagram**
  made with [Keyshape](https://www.keyshapeapp.com)
  (an SVG animation drawing app for Mac)
* captions for each step of the animation (you add markers to the timeline
  for each step)
* a web page you want it to play on

...then this wee bit of JavaScript (KeyshapeDiagram) will add buttons
so you can step/run/reset it, whilst displaying the corresponding captions.

Great for diagrams that let people figure out what's going on in their own time!

## Example

See [an example](examples/ksd-example-external).

## How

Code: see the HTML below (or look at the source in [these examples](examples)).

Process:

* create diagram in Keyshape with _named timeline markers_
* export as SVG (with embedded or external JS)
* add it to an `<object>` element with `class="ksd"` 
* drop an element _inside the object_ (e.g., `<div>` or `<ol>`) whose children
  are captions, one per timeline marker
* add the JavaScript (`ksd.js` or its contents)
* add the styling for the buttons (`ksd.css` or its contents)
* tweak some options (e.g., layout order of buttons/captions/diagram)
* optionally add no-JavaScript fallback


## More detail

When you've exported the SVG, embed it with an `<object>` tag with class `ksd`,
together with (optional) captions. Captions must be in a single container (any
tag name) with class `ksd-captions`. They'll be matched against the timeline
markers in the order they appear, unless you explicitly add the name of the
marker it applies to with `data-ksd-id` (or `id`... but be careful because that
might not be unique in the DOM you're inserting it into).

The KeyshapeDiagram code automatically triggers a call to
`window.KsDiagram.init()` when it loads: this detects and initialises any
suitable diagrams it finds in the DOM. If you add more diagrams _after_ this
has happened, you can initialise them by calling `window.KsDiagram.init()`
again. You can pass a scope (e.g., a fragment of the DOM) in as an argument to
`init()` — by default, it is the whole `document`.

### Method 1: using external files

For example, with a Keyshape animation in `my-diagram.svg` whose primary
timeline has markers you've called `start`, `middle`, and `end`, you can
embed it like this (by hauling in the `.svg`, `.js`, and `.css` files):

```html
<link href="keyshape-diagram.css" rel="stylesheet" type="text/css" />
<object class="ksd" data="my-diagram.svg" type="image/svg+xml">
  <ol class="ksd-captions">
    <li>
      This is caption shown before you press
      <em>step</em> or <em>run</em>.
    </li>
    <li>
      This is displayed while the animation runs,
      starting from the first timeline marker to
      the next one (say, "middle").
    </li>
    <li>
      This caption is shown as the animation
      runs from "middle" to "end".
    </li>
  </ol>
</object>
<div class="ksd-no-js">
  This content is displayed if JavaScript (and hence
  animation) is not supported on this page load.
</div>
<script src="keyshape-diagram.js"></script>
```


If the final (terminating) timeline marker is not called `end`, you must
explicitly tell KeyshapeDiagram what it is: put a `data-ksd-end-marker`
attribute on the `<object>`.


### In-line

If you don't want to use external files, you can do the same thing but have
everything in-line. In some cases this might be more convenient — for example,
embedding a diagram into a single page without worrying about any other setup.


## Keyshape requirements

[Keyshape](https://www.keyshapeapp.com) is a neat little app for making SVG
animations on the Mac. A key capability that this KeyshapeDiagram code is
exploiting is that it lets you name the markers on the timeline: those are used
as the breakpoints in the animation.

* The last marker on the timeline _must_ be named, and if it's name is not `end`
  you must tell KeyshapeDiagram what it _is_ called with `data-ksd-end-marker`.
* It's probably best not to make endlessly looping animations: this relies on
  it ending.

Things work fine if you export from Keyshape with JavaScript animation
embedded. However, if you're fully in control of the environment you're
deploying to, it's probably more efficient to handle the Keyshape animation
library separately. Your call.

CSS animations should work the same, provided they still respond to Keyshape's
named-markers API.

## Graceful degradation

If the target browser does not support SVG — frankly very rare, these days —
the captions will be displayed as a fall back (because they are within the
`<object>` tag). You can style those with regular CSS (including, if required,
hiding them).

Any elements with the class `ksd-no-js` are hidden if JavaScript is enabled.
Use these for fall-back content.

Currently the SVG diagram _is_ displayed if JavaScript is not available; maybe
that will change (depending on more `data-ksd-` settings?): that's for
future development.

## Extra settings

Add `data-` attributes to the `<object>` element for more control:
  
| attribute                | purpose         |
| ------------------------ | --------------- |
| `data-ksd-button-labels` | Customising the labels displayed on the buttons                 |
| `data-ksd-layout`        | Controlling the order of layout (buttons, diagram, captions)    |
| `data-ksd-end-marker`    | Nominating the end-of-animation marker name (if it isn't `end`) |

See below for details for each of these settings.

Because you're putting them on the `<object>` element containing one diagram
  within it, so can be different for other diagrams on the same page.
  
### Button labels

The default labels on the buttons are **step**, **run** and **stop** (and
**reset** and **stopping**). You can override any of these with:

    data-ksd-button-labels="stop:halt, stopping: halting"

If you are generating these programmatically (e.g., an i18n build process)
a JSON object in there also works, so this is equivalent:

    data-ksd-button-labels='{"stop":"halt", "stopping":"halting"}'

Any labels you don't specify remain the (English) defaults.

### End of timeline

The KeyshapeDiagram code assumes the end-of-animation marker is called `end`.
If it isn't, add it explicitly:

    data-ksd-end-marker="finished"

### Layout

The default layout is `b-d-c`:

* `b` buttons above...
* `d` diagram above...
* `c` captions

...but you can change it by setting the layout with any order:

    data-ksd-layout="d-b-c"

The hyphens are optional (characters other than `b`, `c`, and `d` are ignored).

You can change the layout further with CSS.



## CSS and IDs of the elements

The components have extra classes to allow you to select them for further
styling (the layout control only affects their relative order in the DOM). They
also have IDs so you can manipulate them further if you want to.

| Item                        | class              | id                      |
| --------------------------- | ------------------ | ----------------------- |
| `<div>` containing buttons  | `ksd-button-block` | `ksd-button-block-$$$`  |
| step `<button>`             | `ksd-step`         | `ksd-step-$$$`          |
| run `<button>`              | `ksd-run`          | `ksd-run-$$$`           |
| stop/reset `<button>`       | `ksd-stop`         | `ksd-stop-$$$`          |
| element containing captions | `ksd-captions`     | `ksd-captions-$$$`      |
| caption                     | `ksd-caption`      | `ksd-caption-$$$-@@@`   |

* `$$$` is the ID of the diagram. This will be whatever `id` you gave the
  `<object>` tag, or an ID allocated by KeyshapeDiagram if you didn't.
  (Furthermore if you _did_ allocate one but it was the same as another
  diagram's, KeyshapeDiagram will have amended it). The ID allocated is
  returned by the `KsDiagram.add_diagram(container)` function, but if you
  didn't call that yourself you can find all the diagram `id`s in the
  `window.KsDiagram.diagram_ids` array (or inspect the `data-ksd-id` 
  attribute that has magically appeared on the `<object>`).
* `@@@` is the ID of the corresponding timeline marker.


## Dev/implementation notes

* Everything KeyshapeDiagram adds to the DOM is prefixed with `ksd-` to avoid
  namespace clashes (e.g., CSS and DOM IDs). So avoid using those unless you
  deliberately mean to override stuff.

* Something about removing the embedded captions (from the `<object>` tag) 
  consistently but unexpectedly broke the SVG animation _just in Safari_, so
  that' might be why that code looks a bit idiosyncratic way. It is. Sorry.

* Should be fine with multiple diagrams on the same page.

* Should be resilient to the JS being loaded more than once (occupational
  hazard for material being embedded in things like Moodle).

* Maintaining aspect ratio of animations is still not straightforward in CSS
  but that's not quite the remit of this project. However, full-page examples
  like [this example](examples/ksd-example-external) seem to be pretty
  well-behaved with CSS like, e.g., `object.ksd{max-height:60vmin;}`
  







