# HTML Element Plus

## The bits of Custom Elements I keep reimplementing in one handy class.

I have extended HTMLElement as `HTMLElementPlus` to contain the bits of functionality I keep reimplementing.

## Example:

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
