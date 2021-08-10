jsreport extensions can contain also UI part and inject additional functionality to the jsreprot studio. This is very common in the most of the standard extensions and the studio API is fully open also to the custom extensions.

## Get started

1. clone or download [jsreport-extension-starter-kit](https://github.com/jsreport/jsreport-extension-starter-kit)
2. `npm install`
3. `npm start`

## Source transpilation

Extensions including the studio code, needs to contain directory `/studio` in it's root and file `main.js` inside. The content of `main.js` file is then during the jsreport startup copied  with all other extensions into a single bundle and evaluated during the studio initialization.

It is the responsibility of the extension to do the javascript transpilation and bundling. The `main.js` file should contain the final code to be run in the browser. Fortunately there is package [jsreport-studio-dev](https://github.com/jsreport/jsreport-studio-dev) which automates the bundling and transpilation for you. This is what you can find also in [jsreport-extension-starter-kit](https://github.com/jsreport/jsreport-extension-starter-kit), where the actual sources for the extension are in the file `main_dev.js` which are compiled into `main.js` through the prepublish script.

## Extension source

Interaction with jsreport studio API is done through `Studio` object which can be imported as ES6 module from `jsreport-studio`.
```js
import Studio from 'jsreport-studio'

Studio.readyListeners.push(async () => {
  Studio.openModal(() => <span>Hello from the custom extension</span>)
})
```

The `Studio` object represents a facade around whole studio API. It contains functions for opening new tabs, adding custom entity types or hooks for listening to events. The list of `Studio` members can be found separately in [Studio API reference](https://jsreport.net/learn/studio-api-reference).

## Technologies
jsreport studio is based on the technology stack formed mainly around [reactjs](https://reactjs.org/), [redux](https://redux.js.org/) and [webpack](https://webpack.js.org/). You don't need to be an expert on these technologies however it is recommended you get at least a bit familiar with it before developing jsreport custom extension.

## Libraries
The extension build automatically links the common libraries from jsreport studio core and keeps the final `main.js` size minimal. You can for example import `react` module without installing it and it will just work

```js
import React, { Component } from 'react'

export default class MyComponent extends Component {
}
```

Currently you can import in this way the following npm modules
`react, react-dom, react-list, superagent, bluebird, filesaver.js-npm, socket.io-client`

If wish to use other library, you can simply install it and import it. It will be bundled into the final `main.js` during the extension build.

## Examples
As mentioned previously, the most of the standard extensions are including also UI part. It is quite often very simple and can serve well as an example.

[jsreport-debug](https://github.com/jsreport/jsreport-debug), [jsreport-phantom-pdf](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-phantom-pdf), [jsreport-scripts](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-scripts), and [more...](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-core#list-of-extensions)

## Reference

The Studio API reference is availible [here](https://jsreport.net/learn/studio-api-reference).
