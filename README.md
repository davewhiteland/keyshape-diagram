---
permalink: /
---

# Keyshape Diagram

See [the overview](https://davewhiteland.github.io/keyshape-diagram/).

JavaScript support for stepping through animated SVG diagrams produced by
[Keyshape](https://www.keyshapeapp.com).

* create diagram in Keyshape with _named timeline markers_
* export as SVG (with embedded or external JS)
* add it to an `<object>` element with `class="ksd"` 
* add HTML captions (caption id = timeline marker id)
* add the KeyshapeDiagram JS (`ksd.js`) to the page
* add the styling for the buttons (`ksd.css`)
* tweak some options (e.g., layout order of buttons/captions/diagram)
* optionally add no-JavaScript fallback

What you get:

* three buttons for stepping, running, and stopping (or resetting) the animation
* captions displayed as the animation progresses


## Files

* [JavaScript and CSS](https://github.com/davewhiteland/keyshape-diagram/tree/main/src) in `src/`
* [examples](https://davewhiteland.github.io/keyshape-diagram/examples) in `examples/`
* [documentation](https://davewhiteland.github.io/keyshape-diagram/docs) in `docs.md`


---

MIT License: _note:_ example animations may contain embedded JavaScript from
Keyshape which is not part of this project.