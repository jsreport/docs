<a name="Studio"></a>

## Studio
Main facade and API for extensions. Exposed as global variable Studio. It can be also imported from jsreport-studio
when using extensions default webpack configuration

**Kind**: global class  
**Access:** public  

* [Studio](#Studio)
    * [.initializeListeners](#Studio+initializeListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.readyListeners](#Studio+readyListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.previewListeners](#Studio+previewListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.templateEditorModeResolvers](#Studio+templateEditorModeResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityTreeIconResolvers](#Studio+entityTreeIconResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityTreeFilterItemResolvers](#Studio+entityTreeFilterItemResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.locationResolver](#Studio+locationResolver)
    * [.toolbarVisibilityResolver](#Studio+toolbarVisibilityResolver)
    * [.referencesLoader](#Studio+referencesLoader)
    * [.shouldOpenStartupPage](#Studio+shouldOpenStartupPage)
    * [.api](#Studio+api) ⇒ <code>\*</code>
    * [.entitySets](#Studio+entitySets) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.extensions](#Studio+extensions) ⇒ <code>Object</code>
    * [.rootUrl](#Studio+rootUrl) ⇒ <code>string</code>
    * [.TextEditor](#Studio+TextEditor) ⇒ <code>TextEditor</code>
    * [.SplitPane](#Studio+SplitPane) ⇒ <code>SplitPane</code>
    * [.Popover](#Studio+Popover) ⇒ <code>Popover</code>
    * [.EntityTree](#Studio+EntityTree) ⇒ <code>EntityTree</code>
    * [.EntityTreeButton](#Studio+EntityTreeButton) ⇒ <code>EntityTreeButton</code>
    * [.addEntitySet(entitySet)](#Studio+addEntitySet)
    * [.addToolbarComponent(toolbarComponent, position)](#Studio+addToolbarComponent)
    * [.addEntityTreeWrapperComponent(entityTreeWrapperComponent)](#Studio+addEntityTreeWrapperComponent)
    * [.addEntityTreeToolbarComponent(entityTreeToolbarComponent)](#Studio+addEntityTreeToolbarComponent)
    * [.addEntityTreeItemComponent(entityTreeItemComponent, position)](#Studio+addEntityTreeItemComponent)
    * [.addTabTitleComponent(key, component)](#Studio+addTabTitleComponent)
    * [.addEditorComponent(key, component)](#Studio+addEditorComponent)
    * [.addPropertiesComponent(string, component, shouldDisplay)](#Studio+addPropertiesComponent)
    * [.setRequestHeader(key, value)](#Studio+setRequestHeader)
    * [.addApiSpec(obj)](#Studio+addApiSpec)
    * [.setPreviewFrameSrc(frameSrc)](#Studio+setPreviewFrameSrc)
    * [.openModal(componentOrText, options)](#Studio+openModal)
    * [.preview()](#Studio+preview)
    * [.collapseLeftPane()](#Studio+collapseLeftPane)
    * [.openTab(tab)](#Studio+openTab)
    * [.loadEntity(id, force)](#Studio+loadEntity) ⇒ <code>Promise</code>
    * [.unloadEntity(id)](#Studio+unloadEntity)
    * [.addEntity(entity)](#Studio+addEntity)
    * [.updateEntity(entity)](#Studio+updateEntity)
    * [.saveEntity(id)](#Studio+saveEntity) ⇒ <code>Promise</code>
    * [.addExistingEntity(entity)](#Studio+addExistingEntity)
    * [.replaceEntity(oldId, entity)](#Studio+replaceEntity)
    * [.removeEntity(id)](#Studio+removeEntity)
    * [.startProgress()](#Studio+startProgress)
    * [.stopProgress()](#Studio+stopProgress)
    * [.updateHistory()](#Studio+updateHistory)
    * [.getSettings()](#Studio+getSettings) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.setSetting(key, value)](#Studio+setSetting)
    * [.getSettingValueByKey(key, shouldThrow)](#Studio+getSettingValueByKey)
    * [.getEntityByShortid(shortid, shouldThrow)](#Studio+getEntityByShortid) ⇒ <code>Object</code> &#124; <code>null</code>
    * [.getActiveEntity()](#Studio+getActiveEntity) ⇒ <code>Object</code>
    * [.getLastActiveTemplate()](#Studio+getLastActiveTemplate) ⇒ <code>Object</code> &#124; <code>null</code>
    * [.getAllEntities()](#Studio+getAllEntities) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.getReferences()](#Studio+getReferences) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.resolveUrl(path)](#Studio+resolveUrl) ⇒ <code>String</code>
    * [.relativizeUrl(path)](#Studio+relativizeUrl) ⇒ <code>String</code>

<a name="Studio+initializeListeners"></a>

### studio.initializeListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence during initialization

**Kind**: instance property of <code>[Studio](#Studio)</code>  
<a name="Studio+readyListeners"></a>

### studio.readyListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence after the app has been rendered

**Kind**: instance property of <code>[Studio](#Studio)</code>  

<a name="Studio+previewListeners"></a>

### studio.previewListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence when preview process starts.

**Kind**: instance property of <code>[Studio](#Studio)</code>  

<a name="Studio+templateEditorModeResolvers"></a>

### studio.templateEditorModeResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve ace editor mode for template content. This is used by custom templating engines
to add highlighting support for jade,ejs...

**Kind**: instance property of <code>[Studio](#Studio)</code>

<a name="Studio+entityTreeIconResolvers"></a>

### studio.entityTreeIconResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve entity icon in entity tree, function accepts entity and returns string like fa-cog

**Kind**: instance property of <code>[Studio](#Studio)</code>

<a name="Studio+entityTreeFilterItemResolvers"></a>

### studio.entityTreeFilterItemResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve filtering in entity tree, function accepts entity, entitySets and filter info, should return boolean to determine if item should be skipped or not

**Kind**: instance property of <code>[Studio](#Studio)</code>

<a name="Studio+locationResolver"></a>

### studio.locationResolver
Sets the function returning the browser url path
(defaultCalculatedPath, currentEntity) => String

**Kind**: instance property of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| fn | <code>function</code> |

<a name="Studio+toolbarVisibilityResolver"></a>

### studio.toolbarVisibilityResolver
Set the function retunring the visibility flag for particular toolbar button
('Save All') => return true

**Kind**: instance property of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| fn | <code>function</code> |

<a name="Studio+referencesLoader"></a>

### studio.referencesLoader
Override the default entities references loading with custom function
(entitySet) => Promise([array])

**Kind**: instance property of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| fn | <code>function</code> |

<a name="Studio+shouldOpenStartupPage"></a>

### studio.shouldOpenStartupPage
Optionally you can avoid displaying default startup page

**Kind**: instance property of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| trueOrFalse | <code>Boolean</code> |

<a name="Studio+api"></a>

### studio.api ⇒ <code>\*</code>
Provides methods get,patch,post,del for accessing jsreport server

**Kind**: instance property of <code>[Studio](#Studio)</code>  
**Example**  
```js
await Studio.api.patch('/odata/tasks', { data: { foo: '1' } })
```
<a name="Studio+entitySets"></a>

### studio.entitySets ⇒ <code>Array.&lt;Object&gt;</code>
Get registered entity sets, each one is object { visibleName: 'foo', nameAttribute: 'name' }

**Kind**: instance property of <code>[Studio](#Studio)</code>  
<a name="Studio+extensions"></a>

### studio.extensions ⇒ <code>Object</code>
Object[name] with registered extensions and its options

**Kind**: instance property of <code>[Studio](#Studio)</code>  
<a name="Studio+rootUrl"></a>

### studio.rootUrl ⇒ <code>string</code>
absolute root url to the server, like http://localhost/reporting

**Kind**: instance property of <code>[Studio](#Studio)</code>  

<a name="Studio+TextEditor"></a>

### studio.TextEditor ⇒ <code>TextEditor</code>
Ace editor React wrapper

**Kind**: instance property of <code>[Studio](#Studio)</code>  
**Example**  
```js
export default class DataEditor extends TextEditor { ... }
```

<a name="Studio+SplitPane"></a>

### studio.SplitPane ⇒ <code>SplitPane</code>
Component used to split content with sliders

**Kind**: instance property of <code>[Studio](#Studio)</code>

<a name="Studio+Popover"></a>

### studio.Popover ⇒ <code>Popover</code>
Component used to show content in a popover

**Kind**: instance property of <code>[Studio](#Studio)</code>  

<a name="Studio+EntityTree"></a>

### studio.EntityTree ⇒ <code>EntityTree</code>
Component used to visualise entities

**Kind**: instance property of <code>[Studio](#Studio)</code>  

<a name="Studio+EntityTreeButton"></a>

### studio.EntityTreeButton ⇒ <code>EntityTreeButton</code>
Component used to add actions in EntityTree toolbar

**Kind**: instance property of <code>[Studio](#Studio)</code>

<a name="Studio+addEntitySet"></a>

### studio.addEntitySet(entitySet)
Add new entity set, which will be automatically loaded through OData and displayed in the entity tree

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| entitySet | <code>Object</code> |

**Example**  
```js
Studio.addEntitySet({ name: 'data', visibleName: 'sample data' })
```
<a name="Studio+addToolbarComponent"></a>

### studio.addToolbarComponent(toolbarComponent, position)
Add React component which will be displayed in toolbar

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| toolbarComponent | <code>ReactComponent</code> &#124; <code>function</code> |  |  |
| position | <code>String</code> | <code>left</code> | left, right, settings or settingsBottom |

<a name="Studio+addEntityTreeWrapperComponent"></a>

### studio.addEntityTreeWrapperComponent(entityTreeWrapperComponent)
Add React component which will be displayed as a wrapper/container for entity tree

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| entityTreeWrapperComponent | <code>ReactComponent</code> &#124; <code>function</code> |  |  |

<a name="Studio+addEntityTreeToolbarComponent"></a>

### studio.addEntityTreeToolbarComponent(entityTreeToolbarComponent)
Add React component which will be displayed in toolbar of entity tree

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| entityTreeToolbarComponent | <code>ReactComponent</code> &#124; <code>function</code> |  |  |

<a name="Studio+addEntityTreeItemComponent"></a>

### studio.addEntityTreeItemComponent(entityTreeItemComponent, position)
Add React component which will be displayed when rendering an item of entity tree

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| entityTreeItemComponent | <code>ReactComponent</code> &#124; <code>function</code> |  |  |
| position | <code>String</code> | <code>left</code> | right, groupRight or container |

<a name="Studio+addTabTitleComponent"></a>

### studio.addTabTitleComponent(key, component)
Add React component which will be used as tab title

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Description |
| --- | --- | --- |
| key | <code>String</code> | used in openTab |
| component | <code>ReactComponent</code> &#124; <code>function</code> |  |

<a name="Studio+addEditorComponent"></a>

### studio.addEditorComponent(key, component)
Add component used in tab as content editor

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Description |
| --- | --- | --- |
| key | <code>String</code> | key used in openTab({ editorComponentKey: ... , use entity set name if the editor should represent the main entity editor |
| component | <code>ReactComponent</code> &#124; <code>function</code> |  |

<a name="Studio+addPropertiesComponent"></a>

### studio.addPropertiesComponent(string, component, shouldDisplay)
Add component used in the left Properties secion

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Description |
| --- | --- | --- |
| string | <code>function</code> &#124; <code>String</code> | or title function used to render the section title |
| component | <code>ReactComponent</code> &#124; <code>function</code> |  |
| shouldDisplay | <code>function</code> |  |

<a name="Studio+setRequestHeader"></a>

### studio.setRequestHeader(key, value)
Set additional custom header to all api calls

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| key | <code>String</code> |
| value | <code>String</code> |

<a name="Studio+addApiSpec"></a>

### studio.addApiSpec(obj)
Merges in the object defining the api which is used in api fialog

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| obj | <code>Object</code> |

<a name="Studio+setPreviewFrameSrc"></a>

### studio.setPreviewFrameSrc(frameSrc)
Override the right preview pane with additional content
setPreviewFrameSrc('data:text/html;charset=utf-8,foooooooo')

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| frameSrc | <code>String</code> |

<a name="Studio+openModal"></a>

### studio.openModal(componentOrText, options)
Opens modal dialog.

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Description |
| --- | --- | --- |
| componentOrText | <code>ReacrComponent</code> &#124; <code>String</code> |  |
| options | <code>Object</code> | passed as props to the react component |

<a name="Studio+preview"></a>

### studio.preview()
Invoke preview process for last active template

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+collapseLeftPane"></a>

### studio.collapseLeftPane()
Collapse left pane

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+openTab"></a>

### studio.openTab(tab)
Open and activate new editor tab

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| tab | <code>Object</code> |

**Example**  
```js
//open entity editor
Studio.openTab({ _id: 'myentityid' })
//open custom page
Studio.openTab({ key: 'StartupPage', editorComponentKey: 'startup', title: 'Statup' })
```
<a name="Studio+loadEntity"></a>

### studio.loadEntity(id, force) ⇒ <code>Promise</code>
Loads entity, which reference is already present in the ui state, from the remote API

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default |
| --- | --- | --- |
| id | <code>String</code> |  |
| force | <code>Boolean</code> | <code>false</code> |

<a name="Studio+unloadEntity"></a>

### studio.unloadEntity(id)
Remove the additional entity properties from the state, keep just meta and id

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| id | <code>String</code> |

<a name="Studio+addEntity"></a>

### studio.addEntity(entity)
Add entity to the state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| entity | <code>Object</code> |

<a name="Studio+updateEntity"></a>

### studio.updateEntity(entity)
Update entity in the state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| entity | <code>Object</code> |

<a name="Studio+saveEntity"></a>

### studio.saveEntity(id) ⇒ <code>Promise</code>
Call remote API and persist (insert or update) entity

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| id | <code>String</code> |

<a name="Studio+addExistingEntity"></a>

### studio.addExistingEntity(entity)
Adds already existing (persisted) entity into the UI state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param |
| --- |
| entity |

<a name="Studio+replaceEntity"></a>

### studio.replaceEntity(oldId, entity)
Replace the existing entity in the state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| oldId | <code>String</code> |
| entity | <code>Object</code> |

<a name="Studio+removeEntity"></a>

### studio.removeEntity(id)
Remove entity from the state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| id | <code>String</code> |

<a name="Studio+startProgress"></a>

### studio.startProgress()
Show ui signalization for running background operation

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+stopProgress"></a>

### studio.stopProgress()
Hide ui signalization for running background operation

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+updateHistory"></a>

### studio.updateHistory()
Synchronize the location with history

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+getSettings"></a>

### studio.getSettings() ⇒ <code>Array.&lt;Object&gt;</code>
Get all settings from state

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+setSetting"></a>

### studio.setSetting(key, value)
Save one setting in state and persist it on the server

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| key | <code>String</code> |
| value | <code>Object</code> |

<a name="Studio+getSettingValueByKey"></a>

### studio.getSettingValueByKey(key, shouldThrow)
Get one setting value from the state

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default |
| --- | --- | --- |
| key | <code>String</code> |  |
| shouldThrow | <code>Boolean</code> | <code>true</code> |

<a name="Studio+getEntityByShortid"></a>

### studio.getEntityByShortid(shortid, shouldThrow) ⇒ <code>Object</code> &#124; <code>null</code>
Searches for the entity in the UI state based on specified the shortid

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type | Default |
| --- | --- | --- |
| shortid | <code>String</code> |  |
| shouldThrow | <code>Boolean</code> | <code>true</code> |

<a name="Studio+getActiveEntity"></a>

### studio.getActiveEntity() ⇒ <code>Object</code>
Returns the currently selected entity or null

**Kind**: instance method of <code>[Studio](#Studio)</code>  
<a name="Studio+getLastActiveTemplate"></a>

### studio.getLastActiveTemplate() ⇒ <code>Object</code> &#124; <code>null</code>
Returns last active entity

**Kind**: instance method of <code>[Studio](#Studio)</code>

<a name="Studio+getAllEntities"></a>

### studio.getAllEntities() ⇒ <code>Array.&lt;Object&gt;</code>
Get all entities including meta attributes in array

**Kind**: instance method of <code>[Studio](#Studio)</code>  

<a name="Studio+getReferences"></a>

### studio.getReferences() ⇒ <code>Array.&lt;Object&gt;</code>
Get references to entities

**Kind**: instance method of <code>[Studio](#Studio)</code>

<a name="Studio+resolveUrl"></a>

### studio.resolveUrl(path) ⇒ <code>String</code>
Get the path in absolute form like /api/images and make it working also for jsreport running on subpath like myserver.com/reporting/api/images

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| path | <code>String</code> |

<a name="Studio+relativizeUrl"></a>

### studio.relativizeUrl(path) ⇒ <code>String</code>
**This method is deprecated, use .resolveUrl**

Get the path in absolute form like /api/images and make it working also for jsreport running on subpath like myserver.com/reporting/api/images

**Kind**: instance method of <code>[Studio](#Studio)</code>  

| Param | Type |
| --- | --- |
| path | <code>String</code> |
