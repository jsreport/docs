
The most common way to print pdf report from jsreport is to use [chrome-pdf](https://jsreport.net/learn/chrome-pdf) recipe and let it convert html into pdf. This is very productive but sometimes you want to have better control over pdf rendering. In this case you should consider using `Fop-pdf` recipe. `Fop-pfg` recipe is using [apache fop](http://xmlgraphics.apache.org/fop/) to print pdf reports. The report must be defined using special xml format called [fo](http://xmlgraphics.apache.org/fop/fo.html). This format can be nicely combined with [templating engines](https://jsreport.net/learn/templating-engines) to dynamically assemble report definition.

`fop-pdf` is running on [playground](https://playground.jsreport.net) and [jsreport online](https://jsreportonline.net) but you need to explicitly install it for your on-prem jsreport server. To fullfill this you need to download [apache fop](http://xmlgraphics.apache.org/fop/) and java and also add its locations into your executable PATH environment variable.

## Configuration

Use `fop-pdf` node in the standard [config](/learn/configuration) file.

```js
"extensions": {
  "fop-pdf": {
    // max output size allowed in bytes, if the pdf output is greater than
    // this value the rendering will be terminated with an error.
    // Defaults to 500kb (500 * 1024)
    "maxOutputSize": 500 * 1024,
    // the max size of the collected logs from FOP process, if log is greater than this value
    // the log will be trimmed. Defaults to 1000 characters
    "maxLogEntrySize": 1000
  }
}
```

<iframe src='https://playground.jsreport.net/studio/workspace/gkxJuycgR5/110?embed=1' width="100%" height="400" frameborder="0"></iframe>
