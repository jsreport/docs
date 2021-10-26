`html-with-browser-client` creates html output with attached [browser client script](https://jsreport.net/learn/browser-client).

The HTML output is then extended with [jsreport](https://jsreport.net/learn/browser-client) global object. That can be used to invoke jsreport server rendering directly from the output report.

See the [browser client docs](https://jsreport.net/learn/browser-client) for API details.

**The recipe simply links the browser client for you, but you can also stick with a plain HTML recipe and link the client lib yourself.**
```html
<script src={{browserClientLink}}></script>
```

## Export part of the report to PDF
The most simple scenario. You have html report but you want to additionally add controls for printing particular parts into PDF.

```html
<div id='printedArea'>
    <h1>Hello world</h1>
</div>
<input type='button' onclick='print()' value='print me'></input>
<script>
    function print() {
        jsreport.render({
          template: {
              content: document.getElementById('printedArea').innerHTML,
              engine: 'none',
              recipe: 'chrome-pdf'
        }}).then(r => r.download('report.pdf')).catch(console.error);
    }
</script>
```

## Drill down to subreport
Also a very common scenario. The report is too complex to display at once and you want to let the users drill down to particular sections.

The master template can contain several links to the detailed drill down. Every link can then render a different template and also push additional information through data property.

```html
Hello from master...
<input type='button' onclick='detail()' value='Drill down'></input>
<script>
    const masterData = {{{toJS this}}}
    function detail() {
        jsreport.render({
            template: {
                name: 'detail'
            },
            data: { ...masterData, detailId: 'foo' }
        })
        .then(r => r.toString())
        .then((detailStr => {
            document.open()
            document.write(detailStr)
            document.close()
        }))
        .catch(console.error)
    }
</script>
```

Or the drill-down doesn't need to display detail in the whole page but just in the iframe.

```js
<input type='button' onclick='detail()' value='Drill down'></input>
<iframe id='detailFrame' frameborder='0' style='width:100%;height:100%;z-index:50'></iframe>
<script>
    document.getElementById('detailFrame').onload = function () {
        this.style.height = this.contentWindow.document.body.scrollHeight + 'px';  
    }    
    function detail() {
        jsreport.render({          
            template: {
                name: 'detail'
            }
        })
        .then(r => r.toString())
        .then((detailStr => {
            const iframeDocument = document.getElementById('detailFrame').contentDocument
            iframeDocument.open()
            iframeDocument.write(detailStr)
            iframeDocument.close()
        }))
        .catch(console.error)
    }
</script>
```