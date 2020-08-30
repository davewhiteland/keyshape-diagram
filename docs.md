---
title: KeyshapeDiagram docs
---

# KeyshapeDiagram: `ksd.js`

<div style="background:#ffdddd;border:1px solid red;padding:0.5em 1em; margin:1em 0;text-align:center">
  Work in progress!
</div>

KeyshapeDiagram is a wee JavaScript program which adds the buttons and manages
the presentation of animated SVG diagrams that have been created with
[Keyshape](https://www.keyshapeapp.com) (an SVG animation drawing app for Mac).

## Example

See [an example](examples/ksd-example-external).

## How

* create diagram in Keyshape with _named timeline markers_
* export as SVG (with embedded or external JS)
* add it to an `<object>` element with `class="ksd"` 
* add HTML captions (caption id = timeline marker id)
* add this `ksd.js` to the page
* add the `ksd.css` styling for the buttons
* tweak some options (e.g., layout order of buttons/captions/diagram)
* optionally add no-JavaScript fallback

What you get:

* three buttons for stepping, running, and stopping (or resetting) the animation
* captions displayed as the animation progresses


## More detail

When you've exported the SVG, embed it with an `<object>` tag with class `ksd`,
together with (optional) captions. Captions must be in a single container (any
tag name) with class `ksd-captions`, with ids matching the names of the timeline
markers.

The KeyshapeDiagram code automatically triggers a call to
`window.KsDiagram.init()` when it loads: this detects and initialises any
suitable diagrams it finds in the DOM. If you add more diagrams _after_ this
has happened, you can initialise them by calling `window.KsDiagram.init()`
again. You can pass a scope (e.g., a fragment of the DOM) in as an argument to
`init()` — by default, it is the whole `document`.

### External files

For example, with a Keyshape animation whose primary timeline has markers you've
called `start`, `next`, and `end`:

```html
<link href="keyshape-diagram.css" rel="stylesheet" type="text/css" />

<object class="ksd" data="my-diagram.svg?global=paused" type="image/svg+xml">
  <ol class="ksd-captions">
    <li id="start">
      This is shown before you press <em>step</em> or <em>run</em>.
    </li>
    <li id="next">
      This is displayed while the animation runs (starting from timeline marker
      "start") up to timeline marker "next".
    </li>
    <li id="end">
      This is the caption that is shown as the animation runs
      from "next" to "end".
    </li>
  </ol>
</object>
<div class="ksd-no-js">
  Optionally add content that is displayed if JavaScript (and hence animation)
  is not supported on this page load.
</div>

<script src="keyshape-diagram.js"></script>
```

If the final (terminating) timeline marker is not called `end`, you must
explicitly tell KeyshapeDiagram what it is: put a `data-ksd-end-marker`
attribute on the `<object>`.


### In-line

It's also possible to embed everything in-line. In some cases this might be
more convenient — for example, embedding a diagram into a single page without
worrying about any other setup.


## Keyshape requirements

[Keyshape](https://www.keyshapeapp.com) is a neat little app for making SVG
animations on the Mac. A key capability that the KeyshapeDiagram code is
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

Currently the SVG diagram _is_ displayed if JavaScript is not available, but
that is probably going to change: a work-in-progress.

## Extra settings

Add `data-` attributes to the `<object>` element for more control:
  
| attribute                | purpose         |
| ------------------------ | --------------- |
| `data-ksd-button-labels` | Customising the labels displayed on the buttons                 |
| `data-ksd-layout`        | Controlling the order of layout (buttons, diagram, captions)    |
| `data-ksd-end-marker`    | Nominating the end-of-animation marker name (if it isn't `end`) |
  
This implies these settings apply to individual diagrams, which might matter
if you've got more than one on the same page.
  
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


## CSS

Each of the components has `ksd` class, and extra classes to allow you to
select them for further styling (the layout control only affects their
relative order in the DOM).

The three buttons are in a `<div>` with class `ksd-button-block`.

The captions are in a tag (type depends on what you used) with class 
`ksd-captions`.

If you didn't give the diagram (that is, the `<object>` element) a unique id,
KeyshapeDiagram with allocate one. The id is also used to generate ids for
the components and their children, so you can access them programmatically
too. _TODO! This is a work in progress!_

## Dev/implementation notes

* TODO: although diagrams are granted unique IDs if they haven't already got
  one, that's not fully bubbling down into their children yet — and it should
  to allow CSS and JS custom manipulation.

* something about removing the embedded captions (from the object tag) in
  consistently nut unexpectedly broke the SVG animation in Safari, so that's
  now handled in an idiosyncratic way

* should be fine with multiple diagrams on the same page

* should be resilient to the JS being loaded more than once (occupational
  hazard for material being embedded in things like Moodle)

* maintaining aspect ratio of animations is still not straightforward in
  CSS but that's not quite the remit of this project. However 
  [this example](examples/ksd-example-external) seems to be pretty well-behaved
  with CSS: `object.ksd { max-height: 60vmin; }`
  







