# HTML Element Plus

## The bits of Custom Elements I keep reimplementing in one handy class.

I have extended HTMLElement as `HTMLElementPlus` to contain the bits of functionality I keep reimplementing.

* [Features](https://github.com/AdaRoseCannon/html-element-plus#features)
* [Example](https://github.com/AdaRoseCannon/html-element-plus#example)
* [Example as Mixins for other Web Component Libraries](https://github.com/AdaRoseCannon/html-element-plus#example-using-mixins)

## Example

```js
import HTMLElementPlus from 'https://unpkg.com/html-element-plus/html-element-plus.js';
import html from 'https://unpkg.com/html-element-plus/noop.js';

class MyElement extends HTMLElementPlus {
	constructor () {
		super();
	}

	// Set the HTML for the template
	static get templateHTML() {
		return html`<style>
			:host pre {
				background-color: #333;
				color: white;
				display: inline-block;
				tab-size: 2;
				-moz-tab-size: 2;
				-webkit-tab-size: 2;
			}
		</style>
		<link href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/monokai.min.css" rel="stylesheet" >
		<slot></slot><pre><code ref="code"></code></pre>`;
	}

	allAttributesChangedCallback() {
		if (!this.shadowRoot) {
			this.attachShadow({mode: 'open'});
			this.shadowRoot.appendChild(this.templateContent);
		}
	}

	static defaultAttributeValue (name) {
		if (name === 'lang') return null;
	}

	static get observedAttributes() {
		return ['lang'];
	}
}

customElements.define('my-element', MyElement);
```

## Example Using Mixins

```js

/* Pick one or more of the following features:


allAttributesChanged:
* Adds a callback for when all the attributes have been parsed.
* Also provides optional methods for Attribute parsing and providing default values

	static get observedAttributes() {}
	static defaultAttributeValue (name) {}
	static parseAttributeValue (name, value) {}

emitEvent
* Provides el.emitEvent(name, details) for quick event firing

templateHelper
* Provides management for templates and automatically running ShadyCSS
* Set `static get templateHTML` to define your html, use el.templateContent to get it out

refs
* Access defined elements in the shadow dom by using the `ref="foo"` attribute.
* Read it later using el.refs.foo 

*/
import {
	allAttributesChanged,
	refs
} from 'https://unpkg.com/html-element-plus/mixins.js';

class MyElement extends refs(allAttributesChanged(HTMLElement)) {
	constructor () {
		super();
	}

	allAttributesChangedCallback() {
		if (!this.shadowRoot) {
			this.attachShadow({mode: 'open'});
			this.shadowRoot.innerHTML = '<div ref="test" id="test-div"></div>';
		}
	}

	static get observedAttributes() {
		return ['one', 'two'];
	}

	static defaultAttributeValue (name) {
		if (name === 'one') return this.parseAttributeValue('one', '1');
		if (name === 'two') return this.parseAttributeValue('two', '2');
	}

	static parseAttributeValue (name, value) {
		return parseInt(value);
	}
}
```

## Features

These are the current features.

### Collected Attribute Change Callbacks

Provides a callback when all attributes have been parsed, rather than one-by-one. `allAttributesChangedCallback` useful for waiting to handle all at once.

`allAttributesChangedCallback` gets called with an object with parsed attributes.

The parser can be set by setting the function `static parseAttributeValue(name, value)` in the class.

Default attribute values can be provided by setting the `static defaultAttributeValue(name)` function, so you can provide sensible fallback values.

This also gets called during the constructor if there are no attributes listed.

### Query the shadow dom by reference

E.g. an element in the shadow dom: `<span ref="foobar"></span>` can be queried using `this.refs.foobar`;

### Easy event firing.

Fire an event using `this.emitEvent('event-name', {foo: 'bar'});`

This can be listed for using, `el.addEventListener`;

### Automatic Template Creation

Setting templateHTML in your class to return a string of you template contents will build and populate a template. It will automatically run shadyDOM if you are using the polyfill. E.g.
