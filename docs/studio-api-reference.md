<a name="Studio"></a>

## Studio
Main facade and API for extensions. Exposed as global variable Studio. It can be also imported from jsreport-studio
when using extensions default webpack configuration

**Kind**: global class  
**Access**: public  

* [Studio](#Studio)
    * [.initializeListeners](#Studio+initializeListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.readyListeners](#Studio+readyListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.runListeners](#Studio+runListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.reportPreviewStyleResolvers](#Studio+reportPreviewStyleResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityNewListeners](#Studio+entityNewListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entitySaveListeners](#Studio+entitySaveListeners) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.templateEditorModeResolvers](#Studio+templateEditorModeResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityTreeIconResolvers](#Studio+entityTreeIconResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityTreeFilterItemResolvers](#Studio+entityTreeFilterItemResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityTreeDropResolvers](#Studio+entityTreeDropResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.entityEditorComponentKeyResolvers](#Studio+entityEditorComponentKeyResolvers) ⇒ <code>Array.&lt;function()&gt;</code>
    * [.locationResolver](#Studio+locationResolver)
    * [.toolbarVisibilityResolver](#Studio+toolbarVisibilityResolver)
    * [.referencesLoader](#Studio+referencesLoader)
    * [.shouldOpenStartupPage](#Studio+shouldOpenStartupPage)
    * [.removeHandler](#Studio+removeHandler)
    * [.api](#Studio+api) ⇒ <code>\*</code>
    * [.entitySets](#Studio+entitySets) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.extensions](#Studio+extensions) ⇒ <code>Object</code>
    * [.engines](#Studio+engines) ⇒ <code>Array.&lt;string&gt;</code>
    * [.rootUrl](#Studio+rootUrl) ⇒ <code>string</code>
    * [.TextEditor](#Studio+TextEditor) ⇒ <code>TextEditor</code>
    * [.SplitPane](#Studio+SplitPane) ⇒ <code>SplitPane</code>
    * [.Popover](#Studio+Popover) ⇒ <code>Popover</code>
    * [.Popup](#Studio+Popup) ⇒ <code>Popup</code>
    * [.EntityTree](#Studio+EntityTree) ⇒ <code>EntityTree</code>
    * [.EntityTreeButton](#Studio+EntityTreeButton) ⇒ <code>EntityTreeButton</code>
    * [.MultiSelect](#Studio+MultiSelect) ⇒ <code>MultiSelect</code>
    * [.FileInput](#Studio+FileInput) ⇒ <code>MultiSelect</code>
    * [.EntityRefSelect](#Studio+EntityRefSelect) ⇒ <code>EntityRefSelect</code>
    * [.addEntitySet(entitySet)](#Studio+addEntitySet)
    * [.addToolbarComponent(toolbarComponent, position)](#Studio+addToolbarComponent)
    * [.addEntityTreeWrapperComponent(entityTreeWrapperComponent)](#Studio+addEntityTreeWrapperComponent)
    * [.addEntityTreeToolbarComponent(entityTreeToolbarComponent)](#Studio+addEntityTreeToolbarComponent)
    * [.addEntityTreeItemComponent(entityTreeItemComponent, position)](#Studio+addEntityTreeItemComponent)
    * [.addEntityTreeContextMenuItemsResolver(fn)](#Studio+addEntityTreeContextMenuItemsResolver)
    * [.addTabTitleComponent(key, component)](#Studio+addTabTitleComponent)
    * [.addEditorComponent(key, component, reformat)](#Studio+addEditorComponent)
    * [.addPropertiesComponent(string, component, shouldDisplay)](#Studio+addPropertiesComponent)
    * [.addPreviewComponent(type, component, opts)](#Studio+addPreviewComponent)
    * [.addStartupComponent(startupComponent)](#Studio+addStartupComponent)
    * [.setRequestHeader(key, value)](#Studio+setRequestHeader)
    * [.preview(params)](#Studio+preview)
    * [.updatePreview(id, params)](#Studio+updatePreview)
    * [.clearPreview()](#Studio+clearPreview)
    * [.openModal(componentOrText, options)](#Studio+openModal)
    * [.run()](#Studio+run)
    * [.collapseEntity()](#Studio+collapseEntity)
    * [.collapseLeftPane()](#Studio+collapseLeftPane)
    * [.collapsePreviewPane()](#Studio+collapsePreviewPane)
    * [.openTab(tab)](#Studio+openTab)
    * [.openNewTab(tab)](#Studio+openNewTab)
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
    * [.apiFailed(e, ignoreModal)](#Studio+apiFailed)
    * [.updateHistory()](#Studio+updateHistory)
    * [.reset()](#Studio+reset)
    * [.getCurrentTheme()](#Studio+getCurrentTheme) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.getSettings()](#Studio+getSettings) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.setSetting(key, value)](#Studio+setSetting)
    * [.getSettingValueByKey(key, shouldThrow)](#Studio+getSettingValueByKey)
    * [.getEntityById(_id, shouldThrow)](#Studio+getEntityById) ⇒ <code>Object</code> \| <code>null</code>
    * [.getEntityByShortid(shortid, shouldThrow)](#Studio+getEntityByShortid) ⇒ <code>Object</code> \| <code>null</code>
    * [.getActiveEntity()](#Studio+getActiveEntity) ⇒ <code>Object</code>
    * [.getLastActiveTemplate()](#Studio+getLastActiveTemplate) ⇒ <code>Object</code> \| <code>null</code>
    * [.getAllEntities()](#Studio+getAllEntities) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.getTextEditor()](#Studio+getTextEditor) ⇒ <code>Object</code> \| <code>null</code>
    * [.getReferences()](#Studio+getReferences) ⇒ <code>Array.&lt;Object&gt;</code>
    * [.resolveUrl(path)](#Studio+resolveUrl) ⇒ <code>String</code>
    * [.resolveEntityPath(entity)](#Studio+resolveEntityPath) ⇒ <code>String</code>

<a name="Studio+initializeListeners"></a>

### studio.initializeListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence during initialization

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+readyListeners"></a>

### studio.readyListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence after the app has been rendered

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+runListeners"></a>

### studio.runListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence when run template preview process starts.

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+reportPreviewStyleResolvers"></a>

### studio.reportPreviewStyleResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions invoked in sequence when a report preview is render.
It should return an object describing the styles to apply to the Frame preview of the report

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entityNewListeners"></a>

### studio.entityNewListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions invoked in sequence when new entity is about to be added.

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entitySaveListeners"></a>

### studio.entitySaveListeners ⇒ <code>Array.&lt;function()&gt;</code>
Array of async functions invoked in sequence when editor entity save starts.

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+templateEditorModeResolvers"></a>

### studio.templateEditorModeResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve ace editor mode for template content. This is used by custom templating engines
to add highlighting support for jade,ejs...

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entityTreeIconResolvers"></a>

### studio.entityTreeIconResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve entity icon in entity tree, function accepts entity and returns string like fa-cog

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entityTreeFilterItemResolvers"></a>

### studio.entityTreeFilterItemResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve filtering in entity tree,
function accepts entity, entitySets and filter info, should return boolean to determine if
item should be skipped or not

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entityTreeDropResolvers"></a>

### studio.entityTreeDropResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve drop into entity tree

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+entityEditorComponentKeyResolvers"></a>

### studio.entityEditorComponentKeyResolvers ⇒ <code>Array.&lt;function()&gt;</code>
Array of functions used to resolve entity editor component editor, function accepts entity and returns string represent the component editor key

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+locationResolver"></a>

### studio.locationResolver
Sets the function returning the browser url path
(defaultCalculatedPath, currentEntity) => String

**Kind**: instance property of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| fn | <code>function</code> | 

<a name="Studio+toolbarVisibilityResolver"></a>

### studio.toolbarVisibilityResolver
Set the function retunring the visibility flag for particular toolbar button
('Save All') => return true

**Kind**: instance property of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| fn | <code>function</code> | 

<a name="Studio+referencesLoader"></a>

### studio.referencesLoader
Override the default entities references loading with custom function
(entitySet) => Promise([array])

**Kind**: instance property of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| fn | <code>function</code> | 

<a name="Studio+shouldOpenStartupPage"></a>

### studio.shouldOpenStartupPage
Optionally you can avoid displaying default startup page

**Kind**: instance property of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| trueOrFalse | <code>Boolean</code> | 

<a name="Studio+removeHandler"></a>

### studio.removeHandler
Override the default entity remove behavior
(id) => {})

**Kind**: instance property of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| fn | <code>function</code> | 

<a name="Studio+api"></a>

### studio.api ⇒ <code>\*</code>
Provides methods get,patch,post,del for accessing jsreport server

**Kind**: instance property of [<code>Studio</code>](#Studio)  
**Example**  
```js
await Studio.api.patch('/odata/tasks', { data: { foo: '1' } })
```
<a name="Studio+entitySets"></a>

### studio.entitySets ⇒ <code>Array.&lt;Object&gt;</code>
Get registered entity sets, each one is object { visibleName: 'foo' }

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+extensions"></a>

### studio.extensions ⇒ <code>Object</code>
Object[name] with registered extensions and its options

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+engines"></a>

### studio.engines ⇒ <code>Array.&lt;string&gt;</code>
Array of registeresd engines

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+rootUrl"></a>

### studio.rootUrl ⇒ <code>string</code>
absolute root url to the server, like http://localhost/reporting

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+TextEditor"></a>

### studio.TextEditor ⇒ <code>TextEditor</code>
Ace editor React wrapper

**Kind**: instance property of [<code>Studio</code>](#Studio)  
**Example**  
```js
export default class DataEditor extends TextEditor { ... }
```
<a name="Studio+SplitPane"></a>

### studio.SplitPane ⇒ <code>SplitPane</code>
Component used to split content with sliders

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+Popover"></a>

### studio.Popover ⇒ <code>Popover</code>
Component used to show content in a popover

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+Popup"></a>

### studio.Popup ⇒ <code>Popup</code>
Component used to show content in a popup

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+EntityTree"></a>

### studio.EntityTree ⇒ <code>EntityTree</code>
Component used to visualise entities

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+EntityTreeButton"></a>

### studio.EntityTreeButton ⇒ <code>EntityTreeButton</code>
Component used to add actions in EntityTree toolbar

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+MultiSelect"></a>

### studio.MultiSelect ⇒ <code>MultiSelect</code>
Component used for multi-select options

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+FileInput"></a>

### studio.FileInput ⇒ <code>MultiSelect</code>
Component used for file upload

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+EntityRefSelect"></a>

### studio.EntityRefSelect ⇒ <code>EntityRefSelect</code>
Component used to select entity refs

**Kind**: instance property of [<code>Studio</code>](#Studio)  
<a name="Studio+addEntitySet"></a>

### studio.addEntitySet(entitySet)
Add new entity set, which will be automatically loaded through OData and displayed in the entity tree

**Kind**: instance method of [<code>Studio</code>](#Studio)  

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

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| toolbarComponent | <code>ReactComponent</code> \| <code>function</code> |  |  |
| position | <code>String</code> | <code>left</code> | left, right, settings or settingsBottom |

<a name="Studio+addEntityTreeWrapperComponent"></a>

### studio.addEntityTreeWrapperComponent(entityTreeWrapperComponent)
Add React component which will be displayed as a wrapper/container for entity tree

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| entityTreeWrapperComponent | <code>ReactComponent</code> \| <code>function</code> | 

<a name="Studio+addEntityTreeToolbarComponent"></a>

### studio.addEntityTreeToolbarComponent(entityTreeToolbarComponent)
Add React component which will be displayed in toolbar of entity tree

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| entityTreeToolbarComponent | <code>ReactComponent</code> \| <code>function</code> | 

<a name="Studio+addEntityTreeItemComponent"></a>

### studio.addEntityTreeItemComponent(entityTreeItemComponent, position)
Add React component which will be displayed when rendering an item of entity tree

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| entityTreeItemComponent | <code>ReactComponent</code> \| <code>function</code> |  |  |
| position | <code>String</code> | <code>right</code> | right, groupRight or container |

<a name="Studio+addEntityTreeContextMenuItemsResolver"></a>

### studio.addEntityTreeContextMenuItemsResolver(fn)
Add a fn to resolve items for the context menu at Entity Tree

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| fn | <code>function</code> | 

<a name="Studio+addTabTitleComponent"></a>

### studio.addTabTitleComponent(key, component)
Add React component which will be used as tab title

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| key | <code>String</code> | used in openTab |
| component | <code>ReactComponent</code> \| <code>function</code> |  |

<a name="Studio+addEditorComponent"></a>

### studio.addEditorComponent(key, component, reformat)
Add component used in tab as content editor

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| key | <code>String</code> | key used in openTab({ editorComponentKey: ... , use entity set name if the editor should represent the main entity editor |
| component | <code>ReactComponent</code> \| <code>function</code> |  |
| reformat | <code>function</code> | function handling reformatting code |

<a name="Studio+addPropertiesComponent"></a>

### studio.addPropertiesComponent(string, component, shouldDisplay)
Add component used in the left Properties section

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| string | <code>function</code> \| <code>String</code> | or title function used to render the section title |
| component | <code>ReactComponent</code> \| <code>function</code> |  |
| shouldDisplay | <code>function</code> |  |

<a name="Studio+addPreviewComponent"></a>

### studio.addPreviewComponent(type, component, opts)
Add component used in the MainPreview section

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| type | <code>String</code> | name of the new type of content that is going to display in MainPreview |
| component | <code>ReactComponent</code> | the component that is responsible of rendering the new type of content |
| opts | <code>Object</code> | options related to the presence of elements in the MainPreview title bar (tabs, actions, defaultActiveTab) |

<a name="Studio+addStartupComponent"></a>

### studio.addStartupComponent(startupComponent)
Add component displayed at the startup page top

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| startupComponent | <code>ReactComponent</code> \| <code>function</code> | 

<a name="Studio+setRequestHeader"></a>

### studio.setRequestHeader(key, value)
Set additional custom header to all api calls

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| key | <code>String</code> | 
| value | <code>String</code> | 

<a name="Studio+preview"></a>

### studio.preview(params)
Render new content/data inside the MainPreview of studio

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| params | <code>Object</code> | metadata about the preview |

<a name="Studio+updatePreview"></a>

### studio.updatePreview(id, params)
Updates the content/data inside the MainPreview of studio

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| id | <code>String</code> | Preview id of the content to update |
| params | <code>Object</code> | new metadata about the preview to update |

<a name="Studio+clearPreview"></a>

### studio.clearPreview()
Cleans the content inside the MainPreview of studio

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+openModal"></a>

### studio.openModal(componentOrText, options)
Opens modal dialog.

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| componentOrText | <code>ReactComponent</code> \| <code>String</code> |  |
| options | <code>Object</code> | passed as props to the react component |

<a name="Studio+run"></a>

### studio.run()
Invokes run template preview process, when no template is passed it is invoked
for the last active template

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+collapseEntity"></a>

### studio.collapseEntity()
Collapse entity in EntityTree

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+collapseLeftPane"></a>

### studio.collapseLeftPane()
Collapse left pane

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+collapsePreviewPane"></a>

### studio.collapsePreviewPane()
Collapse preview pane

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+openTab"></a>

### studio.openTab(tab)
Open and activate new editor tab

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| tab | <code>Object</code> | 

**Example**  
```js
// open entity editor
Studio.openTab({ _id: 'myentityid' })
// open custom page
Studio.openTab({ key: 'StartupPage', editorComponentKey: 'startup', title: 'Statup' })
```
<a name="Studio+openNewTab"></a>

### studio.openNewTab(tab)
Open tab for a new entity

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| tab | <code>Object</code> | 

**Example**  
```js
// open entity editor
Studio.openTab({ entitySet, entity, name })
```
<a name="Studio+loadEntity"></a>

### studio.loadEntity(id, force) ⇒ <code>Promise</code>
Loads entity, which reference is already present in the ui state, from the remote API

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default |
| --- | --- | --- |
| id | <code>String</code> |  | 
| force | <code>Boolean</code> | <code>false</code> | 

<a name="Studio+unloadEntity"></a>

### studio.unloadEntity(id)
Remove the additional entity properties from the state, keep just meta and id

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| id | <code>String</code> | 

<a name="Studio+addEntity"></a>

### studio.addEntity(entity)
Add entity to the state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| entity | <code>Object</code> | 

<a name="Studio+updateEntity"></a>

### studio.updateEntity(entity)
Update entity in the state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| entity | <code>Object</code> | 

<a name="Studio+saveEntity"></a>

### studio.saveEntity(id) ⇒ <code>Promise</code>
Call remote API and persist (insert or update) entity

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| id | <code>String</code> | 

<a name="Studio+addExistingEntity"></a>

### studio.addExistingEntity(entity)
Adds already existing (persisted) entity into the UI state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param |
| --- |
| entity | 

<a name="Studio+replaceEntity"></a>

### studio.replaceEntity(oldId, entity)
Replace the existing entity in the state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| oldId | <code>String</code> | 
| entity | <code>Object</code> | 

<a name="Studio+removeEntity"></a>

### studio.removeEntity(id)
Remove entity from the state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| id | <code>String</code> | 

<a name="Studio+startProgress"></a>

### studio.startProgress()
Show ui signalization for running background operation

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+stopProgress"></a>

### studio.stopProgress()
Hide ui signalization for running background operation

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+apiFailed"></a>

### studio.apiFailed(e, ignoreModal)
Emits an error that shows the message in a modal

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Description |
| --- | --- | --- |
| e | <code>Error</code> |  |
| ignoreModal | <code>Boolean</code> | defaults to false |

<a name="Studio+updateHistory"></a>

### studio.updateHistory()
Synchronize the location with history

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+reset"></a>

### studio.reset()
Clear the current state and reload internally studio

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getCurrentTheme"></a>

### studio.getCurrentTheme() ⇒ <code>Array.&lt;Object&gt;</code>
Get the current theme (it will check localstorage for user preference and fallback to the default theme configured)

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getSettings"></a>

### studio.getSettings() ⇒ <code>Array.&lt;Object&gt;</code>
Get all settings from state

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+setSetting"></a>

### studio.setSetting(key, value)
Save one setting in state and persist it on the server

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| key | <code>String</code> | 
| value | <code>Object</code> | 

<a name="Studio+getSettingValueByKey"></a>

### studio.getSettingValueByKey(key, shouldThrow)
Get one setting value from the state

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default |
| --- | --- | --- |
| key | <code>String</code> |  | 
| shouldThrow | <code>Boolean</code> | <code>true</code> | 

<a name="Studio+getEntityById"></a>

### studio.getEntityById(_id, shouldThrow) ⇒ <code>Object</code> \| <code>null</code>
Searches for the entity in the UI state based on specified _id

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default |
| --- | --- | --- |
| _id | <code>String</code> |  | 
| shouldThrow | <code>Boolean</code> | <code>true</code> | 

<a name="Studio+getEntityByShortid"></a>

### studio.getEntityByShortid(shortid, shouldThrow) ⇒ <code>Object</code> \| <code>null</code>
Searches for the entity in the UI state based on specified shortid

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type | Default |
| --- | --- | --- |
| shortid | <code>String</code> |  | 
| shouldThrow | <code>Boolean</code> | <code>true</code> | 

<a name="Studio+getActiveEntity"></a>

### studio.getActiveEntity() ⇒ <code>Object</code>
Returns the currently selected entity or null

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getLastActiveTemplate"></a>

### studio.getLastActiveTemplate() ⇒ <code>Object</code> \| <code>null</code>
Returns last active entity

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getAllEntities"></a>

### studio.getAllEntities() ⇒ <code>Array.&lt;Object&gt;</code>
Get all entities including meta attributes in array

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getTextEditor"></a>

### studio.getTextEditor() ⇒ <code>Object</code> \| <code>null</code>
If exists get a text editor instance of an opened text editor by name

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+getReferences"></a>

### studio.getReferences() ⇒ <code>Array.&lt;Object&gt;</code>
Get references to entities

**Kind**: instance method of [<code>Studio</code>](#Studio)  
<a name="Studio+resolveUrl"></a>

### studio.resolveUrl(path) ⇒ <code>String</code>
Get the path in absolute form like /api/images and make it working also for jsreport running on subpath like myserver.com/reporting/api/images

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| path | <code>String</code> | 

<a name="Studio+resolveEntityPath"></a>

### studio.resolveEntityPath(entity) ⇒ <code>String</code>
Assemble entity absolute path

**Kind**: instance method of [<code>Studio</code>](#Studio)  

| Param | Type |
| --- | --- |
| entity | <code>\*</code> | 

