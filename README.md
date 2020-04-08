# Zennify Lunch n Learn: How the Web Works

## Slide Presentation Set Up

RevealJS is a framework for easily creating beautiful presentations using HTML. [Check out the live demo](https://revealjs.com/).
To add slides for a topic, modify the appropriate markdown file in the `sections` folder:
- How the Web Works (`sections/how-the-web-works.md`)
- How the App Works: Overview of Model-View-Controller (MVC) Architecture and the Front End Development (`sections/how-the-app-works-pt1.md`)
- How the App Works: Overview of Back End, Data and APIs (`sections/how-the-app-works-pt2.md`)

### Table of contents

- [Installation](#installation)
  - [Setup](#setup)
  - [Folder Structure](#folder-structure)
  - [Configuration](#configuration)
  - [Dependencies](#dependencies)
  - [Keyboard Bindings](#keyboard-bindings)
- [Usage Instructions](#usage-instructions)
  - [Slide Navigation](#slide-navigation)
  - [Markup](#markup)
  - [Markdown](#markdown)
  - [Element Attributes](#element-attributes)
  - [Slide Attributes](#slide-attributes)
  - [Fragments](#fragments)
  - [Code syntax highlighting](#code-syntax-highlighting)
- [Lazy Loading](#lazy-loading)
- [API](#api)
  - [Slide Changed Event](#slide-changed-event)
- [Theming](#theming)
- [Plugins](#plugins)

#### Setup

Open `index.html` in a browser to view it.

Some reveal.js features, like external Markdown and speaker notes, require that presentations run from a local web server. The following instructions will set up such a server as well as all of the development tasks needed to make edits to the reveal.js source code.

1. Install [Node.js](https://nodejs.org/) (9.0.0 or later)

1. Clone the Zennify LNL "How the Web Works" repository
   ```sh
   $ git clone https://github.com/Zennify/zennify-lnl-how-the-web-works.git
   ```

1. Navigate to the How the Web Works folder
   ```sh
   $ cd zennify-lnl-how-the-web-works
   ```

1. Install dependencies
   ```sh
   $ npm install
   ```

1. Serve the presentation and monitor source files for changes
   ```sh
   $ npm start
   ```

1. Open <http://localhost:8000> to view your presentation

   You can change the port by using `npm start -- --port=8001`.

#### Folder Structure

- **css/** Core styles without which the project does not function
- **js/** Like above but for JavaScript
- **plugin/** Components that have been developed as extensions to reveal.js
- **lib/** All other third party assets (JavaScript, CSS, fonts)

#### Configuration

At the end of your page you need to initialize reveal by running the following code. Note that all configuration values are optional and will default to the values specified [here](https://github.com/hakimel/reveal.js#configuration).

```javascript
Reveal.initialize({
    // ...options
});
````

#### Keyboard Bindings

If you're unhappy with any of the default keyboard bindings you can override them using the `keyboard` config option:

```javascript
Reveal.configure({
  keyboard: {
    13: 'next', // go to the next slide when the ENTER key is pressed
    27: function() {}, // do something custom when ESC is pressed
    32: null // don't do anything when SPACE is pressed (i.e. disable a reveal.js default binding)
  }
});
```


#### Dependencies

Reveal.js doesn't _rely_ on any third party scripts to work but a few optional libraries are included by default. These libraries are loaded as dependencies in the order they appear, for example:

```javascript
Reveal.initialize({
    dependencies: [
        // Interpret Markdown in <section> elements
        { src: 'plugin/markdown/marked.js', condition: function() { return !!document.querySelector( '[data-markdown]' ); } },
        { src: 'plugin/markdown/markdown.js', condition: function() { return !!document.querySelector( '[data-markdown]' ); } },

        // Syntax highlight for <code> elements
        { src: 'plugin/highlight/highlight.js', async: true },

        // Zoom in and out with Alt+click
        { src: 'plugin/zoom-js/zoom.js', async: true },

        // Speaker notes
        { src: 'plugin/notes/notes.js', async: true },

        // MathJax
        { src: 'plugin/math/math.js', async: true }
    ]
});
```

You can add your own extensions using the same syntax. The following properties are available for each dependency object:
- **src**: Path to the script to load
- **async**: [optional] Flags if the script should load after reveal.js has started, defaults to false
- **callback**: [optional] Function to execute when the script has loaded
- **condition**: [optional] Function which must return true for the script to be loaded

### Usage Instructions

#### Slide Navigation

Slides can be nested within other slides to create vertical stacks (see [Markup](#markup)). When presenting, you use the left/right arrows to step through the main (horizontal) slides. When you arrive at a vertical stack you can optionally press the up/down arrows to view the vertical slides or skip past them by pressing the right arrow. Here's an example showing a bird's-eye view of what this looks like in action:

<img src="https://static.slid.es/support/reveal.js-vertical-slides.gif" width="450">

#### Markup

Here's a barebones example of a fully working reveal.js presentation:
```html
<html>
	<head>
		<link rel="stylesheet" href="css/reveal.css">
		<link rel="stylesheet" href="css/theme/white.css">
	</head>
	<body>
		<div class="reveal">
			<div class="slides">
				<section>Slide 1</section>
				<section>Slide 2</section>
			</div>
		</div>
		<script src="js/reveal.js"></script>
		<script>
			Reveal.initialize();
		</script>
	</body>
</html>
```

The presentation markup hierarchy needs to be `.reveal > .slides > section` where the `section` represents one slide and can be repeated indefinitely. If you place multiple `section` elements inside of another `section` they will be shown as vertical slides. The first of the vertical slides is the "root" of the others (at the top), and will be included in the horizontal sequence. For example:

```html
<div class="reveal">
	<div class="slides">
		<section>Single Horizontal Slide</section>
		<section>
			<section>Vertical Slide 1</section>
			<section>Vertical Slide 2</section>
		</section>
	</div>
</div>
```

#### Markdown

It's possible to write your slides using Markdown. To enable Markdown, add the `data-markdown` attribute to your `<section>` elements and wrap the contents in a `<textarea data-template>` like the example below. You'll also need to add the `plugin/markdown/marked.js` and `plugin/markdown/markdown.js` scripts (in that order) to your HTML file.

This is based on [data-markdown](https://gist.github.com/1343518) from [Paul Irish](https://github.com/paulirish) modified to use [marked](https://github.com/chjj/marked) to support [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown). Sensitive to indentation (avoid mixing tabs and spaces) and line breaks (avoid consecutive breaks).

```html
<section data-markdown>
	<textarea data-template>
		## Page title

		A paragraph with some text and a [link](http://hakim.se).
	</textarea>
</section>
```

##### Configuring *marked*

We use [marked](https://github.com/chjj/marked) to parse Markdown. To customise marked's rendering, you can pass in options when [configuring Reveal](#configuration):

```javascript
Reveal.initialize({
    // Options which are passed into marked
    // See https://marked.js.org/#/USING_ADVANCED.md#options
    markdown: {
        smartypants: true
    }
});
```

##### External Markdown

You can write your content as a separate file and have reveal.js load it at runtime. Note the separator arguments which determine how slides are delimited in the external file: the `data-separator` attribute defines a regular expression for horizontal slides (defaults to `^\r?\n---\r?\n$`, a newline-bounded horizontal rule)  and `data-separator-vertical` defines vertical slides (disabled by default). The `data-separator-notes` attribute is a regular expression for specifying the beginning of the current slide's speaker notes (defaults to `notes?:`, so it will match both "note:" and "notes:"). The `data-charset` attribute is optional and specifies which charset to use when loading the external file.

When used locally, this feature requires that reveal.js [runs from a local web server](#setup).  The following example customises all available options:

```html
<section data-markdown="example.md"
         data-separator="^\n\n\n"
         data-separator-vertical="^\n\n"
         data-separator-notes="^Note:"
         data-charset="iso-8859-15">
    <!--
        Note that Windows uses `\r\n` instead of `\n` as its linefeed character.
        For a regex that supports all operating systems, use `\r?\n` instead of `\n`.
    -->
</section>
```

##### Element Attributes

Special syntax (through HTML comments) is available for adding attributes to Markdown elements. This is useful for fragments, amongst other things.

```html
<section data-markdown>
	<script type="text/template">
		- Item 1 <!-- .element: class="fragment" data-fragment-index="2" -->
		- Item 2 <!-- .element: class="fragment" data-fragment-index="1" -->
	</script>
</section>
```

##### Slide Attributes

Special syntax (through HTML comments) is available for adding attributes to the slide `<section>` elements generated by your Markdown.

```html
<section data-markdown>
	<script type="text/template">
	<!-- .slide: data-background="#ff0000" -->
		Markdown content
	</script>
</section>
```

##### Fragments

Fragments are used to highlight individual elements on a slide. Every element with the class `fragment` will be stepped through before moving on to the next slide. Here's an example: http://revealjs.com/#/fragments

The default fragment style is to start out invisible and fade in. This style can be changed by appending a different class to the fragment:

```html
<section>
    <p class="fragment grow">grow</p>
    <p class="fragment shrink">shrink</p>
    <p class="fragment strike">strike</p>
    <p class="fragment fade-out">fade-out</p>
    <p class="fragment fade-up">fade-up (also down, left and right!)</p>
    <p class="fragment fade-in-then-out">fades in, then out when we move to the next step</p>
    <p class="fragment fade-in-then-semi-out">fades in, then obfuscate when we move to the next step</p>
    <p class="fragment highlight-current-blue">blue only once</p>
    <p class="fragment highlight-red">highlight-red</p>
    <p class="fragment highlight-green">highlight-green</p>
    <p class="fragment highlight-blue">highlight-blue</p>
</section>
```

Multiple fragments can be applied to the same element sequentially by wrapping it, this will fade in the text on the first step and fade it back out on the second.

```html
<section>
    <span class="fragment fade-in">
        <span class="fragment fade-out">I'll fade in, then out</span>
    </span>
</section>
```

The display order of fragments can be controlled using the `data-fragment-index` attribute.

```html
<section>
    <p class="fragment" data-fragment-index="3">Appears last</p>
    <p class="fragment" data-fragment-index="1">Appears first</p>
    <p class="fragment" data-fragment-index="2">Appears second</p>
</section>
```

###### Fragment events

When a slide fragment is either shown or hidden reveal.js will dispatch an event.

Some libraries, like MathJax (see #505), get confused by the initially hidden fragment elements. Often times this can be fixed by calling their update or render function from this callback.

```javascript
Reveal.addEventListener( 'fragmentshown', function( event ) {
    // event.fragment = the fragment DOM element
} );
Reveal.addEventListener( 'fragmenthidden', function( event ) {
    // event.fragment = the fragment DOM element
} );
```

##### Code Syntax Highlighting

By default, Reveal is configured with [highlight.js](https://highlightjs.org/) for code syntax highlighting. To enable syntax highlighting, you'll have to load the highlight plugin ([plugin/highlight/highlight.js](plugin/highlight/highlight.js)) and a highlight.js CSS theme (Reveal comes packaged with the Monokai themes: [lib/css/monokai.css](lib/css/monokai.css)).

```javascript
Reveal.initialize({
    // More info https://github.com/hakimel/reveal.js#dependencies
    dependencies: [
        { src: 'plugin/highlight/highlight.js', async: true },
    ]
});
```

Below is an example with clojure code that will be syntax highlighted. When the `data-trim` attribute is present, surrounding whitespace is automatically removed.  HTML will be escaped by default. To avoid this, for example if you are using `<mark>` to call out a line of code, add the `data-noescape` attribute to the `<code>` element.

```html
<section>
    <pre><code data-trim data-noescape>
(def lazy-fib
  (concat
   [0 1]
   <mark>((fn rfib [a b]</mark>
        (lazy-cons (+ a b) (rfib b (+ a b)))) 0 1)))
    </code></pre>
</section>
```

###### Line Numbers & Highlights

To enable line numbers, add `data-line-numbers` to your `<code>` tags. If you want to highlight specific lines you can provide a comma separated list of line numbers using the same attribute. For example, in the following example lines 4 and 8-11 are highlighted:

```html
<pre><code class="hljs" data-line-numbers="4,8-11">
import React, { useState } from 'react';
 
function Example() {
  const [count, setCount] = useState(0);
 
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
</code></pre>
```

<img width="300" alt="line-numbers" src="https://user-images.githubusercontent.com/629429/55332077-eb3c4780-5494-11e9-8854-ba33cd0fa740.png">

###### Step-by-step Highlights

You can step through multiple code highlights on the same code block. Delimit each of your highlight steps with the `|` character. For example `data-line-numbers="1|2-3|4,6-10"` will produce three steps. It will start by highlighting line 1, next step is lines 2-3, and finally line 4 and 6 through 10.

#### Lazy Loading

When working on presentation with a lot of media or iframe content it's important to load lazily. Lazy loading means that reveal.js will only load content for the few slides nearest to the current slide. The number of slides that are preloaded is determined by the `viewDistance` configuration option.

To enable lazy loading all you need to do is change your `src` attributes to `data-src` as shown below. This is supported for image, video, audio and iframe elements.

```html
<section>
  <img data-src="image.png">
  <iframe data-src="http://hakim.se"></iframe>
  <video>
    <source data-src="video.webm" type="video/webm" />
    <source data-src="video.mp4" type="video/mp4" />
  </video>
</section>
```

##### Lazy Loading Iframes

Note that lazy loaded iframes ignore the `viewDistance` configuration and will only load when their containing slide becomes visible. Iframes are also unloaded as soon as the slide is hidden.

When we lazy load a video or audio element, reveal.js won't start playing that content until the slide becomes visible. However there is no way to control this for an iframe since that could contain any kind of content. That means if we loaded an iframe before the slide is visible on screen it could begin playing media and sound in the background.

You can override this behavior with the `data-preload` attribute. The iframe below will be loaded
according to the `viewDistance`.

```html
<section>
	<iframe data-src="http://hakim.se" data-preload></iframe>
</section>
```

You can also change the default globally with the `preloadIframes` configuration option. If set to
`true` ALL iframes with a `data-src` attribute will be preloaded when within the `viewDistance`
regardless of individual `data-preload` attributes. If set to `false`, all iframes will only be
loaded when they become visible.

#### API

The `Reveal` object exposes a JavaScript API for controlling navigation and reading state. See [API Documentation](https://github.com/hakimel/reveal.js#api).

#### Theming
The framework comes with a few different themes included. Each theme is available as a separate stylesheet. To change theme you will need to replace `black.css` (default) below with your desired theme name in `index.html`:

```
<link rel="stylesheet" href="css/theme/black.css" id="theme">
```

If you want to add a theme of your own see the instructions in `/css/theme/README.md`.

##### Slide Backgrounds

Slides are contained within a limited portion of the screen by default to allow them to fit any display and scale uniformly. You can apply full page backgrounds outside of the slide area by adding a `data-background` attribute to your `<section>` elements. Four different types of backgrounds are supported: color, image, video and iframe.

###### Color Backgrounds

All CSS color formats are supported, including hex values, keywords, `rgba()` or `hsl()`.

```html
<section data-background-color="#ff0000">
	<h2>Color</h2>
</section>
```

###### Image Backgrounds

By default, background images are resized to cover the full page. Available options:

| Attribute                        | Default    | Description |
| :------------------------------- | :--------- | :---------- |
| data-background-image            |            | URL of the image to show. GIFs restart when the slide opens. |
| data-background-size             | cover      | See [background-size](https://developer.mozilla.org/docs/Web/CSS/background-size) on MDN.  |
| data-background-position         | center     | See [background-position](https://developer.mozilla.org/docs/Web/CSS/background-position) on MDN. |
| data-background-repeat           | no-repeat  | See [background-repeat](https://developer.mozilla.org/docs/Web/CSS/background-repeat) on MDN. |
| data-background-opacity          | 1          | Opacity of the background image on a 0-1 scale. 0 is transparent and 1 is fully opaque. |

```html
<section data-background-image="http://example.com/image.png">
	<h2>Image</h2>
</section>
<section data-background-image="http://example.com/image.png" data-background-size="100px" data-background-repeat="repeat">
	<h2>This background image will be sized to 100px and repeated</h2>
</section>
```

###### Video Backgrounds

Automatically plays a full size video behind the slide.

| Attribute                        | Default | Description |
| :---------------------------     | :------ | :---------- |
| data-background-video            |         | A single video source, or a comma separated list of video sources. |
| data-background-video-loop       | false   | Flags if the video should play repeatedly. |
| data-background-video-muted      | false   | Flags if the audio should be muted. |
| data-background-size             | cover   | Use `cover` for full screen and some cropping or `contain` for letterboxing. |
| data-background-opacity          | 1       | Opacity of the background video on a 0-1 scale. 0 is transparent and 1 is fully opaque. |

```html
<section data-background-video="https://s3.amazonaws.com/static.slid.es/site/homepage/v1/homepage-video-editor.mp4,https://s3.amazonaws.com/static.slid.es/site/homepage/v1/homepage-video-editor.webm" data-background-video-loop data-background-video-muted>
	<h2>Video</h2>
</section>
```

###### Iframe Backgrounds

Embeds a web page as a slide background that covers 100% of the reveal.js width and height. The iframe is in the background layer, behind your slides, and as such it's not possible to interact with it by default. To make your background interactive, you can add the `data-background-interactive` attribute.

```html
<section data-background-iframe="https://slides.com" data-background-interactive>
	<h2>Iframe</h2>
</section>
```

Iframes are lazy-loaded when they become visible. If you'd like to preload iframes ahead of time, you can append a `data-preload` attribute to the slide `<section>`. You can also enable preloading globally for all iframes using the `preloadIframes` configuration option.


### Plugins

Plugins should register themselves with reveal.js by calling `Reveal.registerPlugin( 'myPluginID', MyPlugin )`. Registered plugin instances can optionally expose an "init" function that reveal.js will call to initialize them.

When reveal.js is booted up via `Reveal.initialize()`, it will go through all registered plugins and invoke their "init" methods. If the "init" method returns a Promise, reveal.js will wait for that promise to be fulfilled before finishing the startup sequence and firing the [ready](#ready-event) event. Here's an example of a plugin that does some asynchronous work before reveal.js can proceed:

```javascript
let MyPlugin = {
	init: () =>  new Promise( resolve => setTimeout( resolve, 3000 ) )
};
Reveal.registerPlugin( 'myPlugin', MyPlugin );
Reveal.addEventListener( 'ready', () => console.log( 'Three seconds later...' ) );
Reveal.initialize();
```

Note that reveal.js will *not* wait for init Promise fulfillment if the plugin is loaded as an [async dependency](#dependencies). If the plugin's init method does _not_ return a Promise, the plugin is considered ready right away and will not hold up the reveal.js startup sequence.

#### Retrieving Plugins

If you want to check if a specific plugin is registered you can use the `Reveal.hasPlugin` method and pass in a plugin ID, for example: `Reveal.hasPlugin( 'myPlugin' )`. If you want to retrieve a plugin instance you can use `Reveal.getPlugin( 'myPlugin' )`.