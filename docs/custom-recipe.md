> **Sources on GitHub [jsreport-html-to-text](https://github.com/jsreport/jsreport-html-to-text)**

This tutorial shows how you can add a custom recipe into jsreport.

jsreport is quite often used to render and send emails. Where the email body is usually in html format but sometimes it is required to send the body just in the plain text. In this tutorial you will create a recipe which converts html into nicely structured text which can be
then used for plain text emails.

## Get started

The first download [extension starter kit](https://github.com/jsreport/jsreport-extension-starter-kit) and follow the installations instructions. Afterwards you should have the first extension ready. Lets create the recipe and integrate it into the extension now.

## jsreport.config.js
Every extension contains specific file `jsreport.config.js`. This file represents the basic configuration for the extension. It should be already in place in starter kit, so lets just change the extension name inside to something meaningful.

```js
module.exports = {
  'name': 'html-to-text',
  'main': 'lib/main.js',
  'dependencies': ['templates']
}
```

## Server

Before starting implementing the recipe, we will need to install additional thirdparty library that will help us with the html => text conversion logic. The one that exactly fits our needs can be found [here](https://github.com/werk85/node-html-to-text). To add it to the project you just need to call:

```bash
 npm install html-to-text --save
```

Now lets open the `lib/main.js` file and add implementation of the recipe.

```js
const convert = require('html-to-text')

module.exports = (reporter, definition) => {
  reporter.extensionsManager.recipes.push({
    name: 'html-to-text',
    execute: (request, response) => {
      response.content = new Buffer(convert.fromString(response.content.toString()))

      response.meta.contentType = 'text/plain'
      response.meta.fileExtension = 'txt'
    }
  })
}
```

As you see the `main.js` file exports the function which is called by jsreport during the initialization. The only thing we do in this function's body for now is adding the new recipe into the `reporter.extensionsManager.recipes` collection.

The recipe execution function accepts `request` and `response` parameters. There is already a buffer with output from templating engine in `response.content` which we just convert into the text using previously installed package, then we set some metadata information (`response.meta.contentType`, `response.meta.fileExtension`) about the output and we are done.

## Testing

Now you can hit `npm start` and you should be able to reach jsreport studio on `http://localhost:5488` and test the recipe.

## Distributing

The best way to distribute the new recipe into the public audience is through the [npm](https://www.npmjs.com/). To prepare the package for publishing you should change the package name, author and other attributes in `package.json` file. Afterwards you can simply type

```bash
npm publish
```

and the package will be publicly available in npm.


## Adding options
[html-to-text](https://github.com/werk85/node-html-to-text) conversion accepts several parameters like number of characters on the line. Lets attach this configuration option into the jsreport template and expose it into database as well as API.

You can reach the facade over jsreport storage as well as [odata](http://www.odata.org/) API through `reporter.documentStore`. First you use `registerComplexType` to register new custom object structure `HtmlToText` and then add it as a property into the jsreport template type. The loaded values or values from the API call can be then find on the `request.template.htmlToText.wordWrap`.

```js
reporter.documentStore.registerComplexType('htmlToText', {
  wordWrap: { type: 'Edm.Number' }
})

const templateType = reporter.documentStore.model.entityTypes['TemplateType']
templateType.htmlToText = { type: 'jsreport.htmlToText' }

reporter.extensionsManager.recipes.push({
  name: 'html-to-text',
  execute: (request, response) => {
    response.content = new Buffer(convert.fromString(response.content.toString()))

    response.meta.contentType = 'text/plain'
    response.meta.fileExtension = 'txt'
  }
})

```

## Adding UI
The final missing piece is to let the user to change the `wordWrap` number in the studio UI. Lets add the same experience as setting a header and other options when using `phantom-pdf` recipe.

The jsreport studio is using [reactjs](https://facebook.github.io/react/) so you need to create first a react component which will represent the options panel displayed in the left side of the studio. Lets create this component in `studio/Properties.js`

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

The important thing here is that the components receives `entity` in its `props`. This is is used to display the currently stored value. The second important attribute passed in `props` is the `onChange` function which is supposed to be called to notify about the user changes in the properties form.

The second step to finish the ui is to register the properties component we just created. This needs to be done in the `studio/main_dev.js` file which represents the main entry point to the extension's UI part.

```js
import Properties from './Properties.js'
import Studio from 'jsreport-studio'

Studio.addPropertiesComponent('html-to-text', Properties, (entity) => entity.__entitySet === 'templates' && entity.recipe === 'html-to-text')
```

Calling `Studio.addPropertiesComponent` we add the new properties component with title `html-to-text`, the react function rendering the form implemented in `Properties.js` and the function determining when the properties form should be included in the left panel.

Now start the studio again and see the result

```bash
npm start
```

## More information
There is dedicated article about extending the jsreport studio - [Extending studio](https://jsreport.net/learn/extending-studio) and also many  existing extensions which you can use as examples for further study available [on github](https://github.com/jsreport/jsreport-core#list-of-extensions).
