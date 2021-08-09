## Basics

jsreport extension allowing to freeze editing of entities to avoid accidental changes in the production.

## Hard freeze in config

You can avoid jsreport templates editing through config file:
```js
{
  "extensions" {
    "freeze": {
      "hardFreeze": true
    }
  }
}
```

## Freeze in studio

The Second option is to freeze editing in jsreport studio. This can be found in menu `Actions->Freeze Editing`. Note this options is available only for the jsreport administrator. The editing can be also afterwards enabled again using menu `Actions->Release Freeze`.
