

> Export entities and store them into a single file and later recover the state or import to a different instance

![export](/img/export.gif)

## Migrating templates
This extension can be used to move templates to different servers as well to different servers using different template stores. This means you can for example copy templates from default file system store to [mssql store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-mssql-store) or [postgres store](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-postgres-store).

## API

### Export into stream
```js
POST: /api/export
BODY: {
  //optional
  selection: ["id1", "id2" ...]
}
```

### Import from stream

```js
POST: /api/import
BODY: /* multipart import.jsrexport */
```

You can find how to send multiplart through [postmant here](http://stackoverflow.com/a/16022213/1660996).

## CLI

The export/import can be called also using [jsreport cli](/learn/cli).

```
jsreport export jsreportExport.jsrexport
jsreport import jsreportExport.jsrexport
```

This can be done also remotely using `--serverUrl` switch. This is handy for deployment automation.

```
jsreport export --serverUrl=myjsreport.com jsreportExport.jsrexport
jsreport import --serverUrl=myjsreport.com jsreportExport.jsrexport
```

The cli export commands support also authentication, importing and exporting specific folder or specific entities, validation and also full import. See details in the cli help.

```
jsreport help export
jsreport help import
```
