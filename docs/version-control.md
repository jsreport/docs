## Basics
jsreport extension adding support for versioning entities and providing API as well as the studio UI for common commands like commit, diff, revert or history. Just install the extension and you should see the new UI in studio.

![version control](https://jsreport.net/img/version-control.gif)

The extension works well with all supported [template stores](/learn/template-stores). The commits are stored as entities in the used [template store](/learn/template-stores)
and the diffs are stored in the used [blob storage](/learn/blob-storages).

## Git

This extension is very convenient because it runs without other dependencies. However, you can also use [git](https://git-scm.com/) for templates versioning instead.
The whole jsreport application can be part of the git repository with the following `.gitignore`.

```
data/fs.version
data/fs.journal
data/fs.lock
data/profiles
data/monitoring
data/reports
data/storage
logs
node_modules
```

To avoid confusion, you can also disable the version control extension, and use your prefered git client.
```js
{
  "extensions": { 
    "version-control": { 
      "enabled": false 
    }
  }
}
```