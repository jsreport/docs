## Basics
jsreport extension adding support for versioning entities and providing API as well as the studio UI for common commands like commit, diff, revert or history. Just install the extension and you should see the new UI in studio.

![version control](https://jsreport.net/img/version-control.gif)

## Git
The extension by default uses its own implementation to track changes and stores the diffs in an extra entity. This very lightweight solution which works with all supported [template stores](/learn/template-stores). However you may like to use more powerful [git](https://git-scm.com/) instead and track changes made to the underlying files if you use [file system store](/learn/fs-store).

To start using local git you need to additionally install the [jsreport-version-control-git](https://github.com/jsreport/jsreport/tree/master/packages/jsreport-version-control-git) extension.
```bash
npm install jsreport-version-control-git
```

And configure the `version-control` to use it for tracking changes.
```js
{
  "extensions": {
    "versionControl": { "provider": "git" }
  }
}
```

This keeps the UI the same as when using the plain `version-control`. However on behind it initializes local git repository in `data` folder and delegate the version tracking to git. Note that this package bundles the git inside and there is no need to have it installed on the target machine.
