> **Sources on GitHub [jsreport-html-to-text](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-html-to-text)**

This tutorial shows how you can add a custom recipe into jsreport.

jsreport is quite often used to render and send emails. Where the email body is usually in HTML format, but sometimes it is required to send the body just in plain text. In this tutorial, you will create a recipe that converts HTML into nicely structured text which can be
then used for plain text emails.

## Get started

The first git clone [extension starter kit](https://github.com/jsreport/jsreport-extension-starter-kit) and follow the installation's instructions. Afterward, you should have the first extension ready. Let's create the recipe and integrate it into the extension now.

## jsreport.config.js
Every extension contains the specific file `jsreport.config.js`. This file represents the basic configuration for the extension. It should be already in place in the starter kit, so let's just change the extension's name inside.

```js
//jsreport.config.js
module.exports = {
  'name': 'html-to-text',
   ...
}
```

## Server

Before starting implementing the recipe, we will need to install an additional third-party library that will help us with the html => text conversion logic. The one that exactly fits our needs can be found [here](https://github.com/werk85/node-html-to-text). To add it to the project you just need to call:

```bash
 npm install html-to-text --save
```

Start with the recipe registration in the main process. In the extension main entry point.

```js
// main.js
module.exports = function (reporter, definition) {
  reporter.extensionsManager.recipes.push({
    name: 'html-to-text'
  })
}
```

This was just the registration, but the recipe will be executed in the worker thread.
You need to register the recipe execution function in the worker entry point.

```js
// worker.js
const convert = require('html-to-text')

module.exports = (reporter, definition) => {
  reporter.extensionsManager.recipes.push({
    name: 'html-to-text',
    execute: (request, response) => {
      response.content = Buffer.from(convert.htmlToText(response.content.toString()))
      response.meta.contentType = 'text/plain'
      response.meta.fileExtension = 'txt'
    }
  })
}
```

The recipe execution function accepts `request` and `response` parameters. There is already a buffer with output from templating engine in `response.content` which we just convert into the text using previously installed package, then we set some metadata information (`response.meta.contentType`, `response.meta.fileExtension`) about the output and we are done.

## Testing

Now you can hit `npm start` and you should be able to reach jsreport studio on `http://localhost:5488` and test the recipe.

## Distributing

The best way to distribute the new recipe to the public audience is through the [npm](https://www.npmjs.com/). To prepare the package for publishing you should change the package name, author, and other attributes in `package.json` file. Afterward you can simply type

```bash
npm publish
```

and the package will be publicly available in the npm.


## Adding options
[html-to-text](https://github.com/werk85/node-html-to-text) conversion accepts several parameters like the number of characters on the line. Let's attach this configuration option into the jsreport template and expose it to the database as well as API.

You can reach the facade over jsreport storage as well as [odata](http://www.odata.org/) API through `reporter.documentStore`. First, you use `registerComplexType` to register the new custom object structure `HtmlToText` and then add it as a property into the jsreport template type. The loaded values or values from the API call can be then find on the `request.template.htmlToText.wordWrap`.

```js
// main.js
reporter.documentStore.registerComplexType('htmlToText', {
  wordWrap: { type: 'Edm.Number' }
})

const templateType = reporter.documentStore.model.entityTypes['TemplateType']
templateType.htmlToText = { type: 'jsreport.htmlToText' }
```

The worker part can now reach the options at the request template and property htmlToText.
```js
//worker.js
const convert = require('html-to-text')

module.exports = (reporter, definition) => {
  reporter.extensionsManager.recipes.push({
    name: 'html-to-text',
    execute: (request, response) => {
      response.content = Buffer.from(convert.htmlToText(response.content.toString(), request.template.htmlToText || {}))
      response.meta.contentType = 'text/plain'
      response.meta.fileExtension = 'txt'
    }
  })
}
```

## Adding UI
The final missing piece is to let the user change the `wordWrap` number in the studio UI. 

The jsreport studio is using [reactjs](https://facebook.github.io/react/) so you need to create first a react component that will represent the options panel displayed on the left side of the studio. Let's create this component in `studio/Properties.js`

```js
import React, { Component } from 'react'

export default class Properties extends Component {
  render () {
    const { entity, onChange } = this.props
    const recipeProps = entity.htmlToText || {}

    return (
      <div className='properties-section'>
        <div className='form-group'><label>word wrap</label>
          <input
            title='Wrap the line after x characters' type='number' placeholder='130' min='0' max='1000'
            value={recipeProps.wordWrap}
            onChange={(v) => onChange({_id: entity._id, htmlToText: {wordWrap: v.target.value}})} />
        </div>
      </div>
    )
  }
}
```

The important thing here is that the components receive `entity` in their `props`. This is used to display the currently stored value. The second important attribute passed in `props` is the `onChange` function which is supposed to be called to notify about the user changes in the properties form.

The second step to finish the UI is to register the properties component we just created. This needs to be done in the `studio/main_dev.js` file which represents the main entry point to the extension's UI part.

```js
import Properties from './Properties.js'
import Studio from 'jsreport-studio'

Studio.addPropertiesComponent('html-to-text', Properties, (entity) => entity.__entitySet === 'templates' && entity.recipe === 'html-to-text')
```

Calling `Studio.addPropertiesComponent` we add the new properties component with the title `html-to-text`, the react function rendering the form implemented in `Properties.js`, and the function determining when the properties form should be included in the left panel.

Now start the studio again and see the result

```bash
npm start
```

## More information
There is a dedicated article about extending the jsreport studio - [Extending studio](/learn/extending-studio) and also many existing extensions which you can use as examples for further study available [on github](https://github.com/jsreport/jsreport/tree/master/packages).
