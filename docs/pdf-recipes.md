jsreport supports several [recipes](/learn/recipes) which can be used to render pdf documents. There isn't just one because each html to pdf conversion technology used by recipes has its pros and cons. There is simply no clear winner and to provide the best option to every use case jsreport implements several of them. This article should help with choosing the right one based on the specific requirements.

## Pdf recipes comparison
| features | [phantom-pdf ](learn/phantom-pdf)| [wkhtmltopdf](learn/wkhtmltopdf) | [electron-pdf](https://github.com/bjrmatos/jsreport-electron-pdf) | [weasyprint-pdf](https://github.com/jsreport/jsreport-weasyprint-pdf)
| --- | :---: | :---: | :---: | :---: |
| technology | [phantomjs](http://phantomjs.org/) | [wkhtmltopdf](https://wkhtmltopdf.org/) | [electron](https://github.com/electron/electron)| [weasyprint](http://weasyprint.org/)|
| header & footer| yes | yes | no | yes |
| nested link `<a href="#">link</a>` | no | yes | yes | yes |
| latest css/ js | no | no | yes | no
| programmatic print trigger | yes | no | yes | yes
| [css page rule](https://www.w3.org/TR/css3-page/) | no | no | no | yes
| speed | fast | medium | fast | slow
| deployment - installation| default | npm | npm and xvfb | complex - prefer docker

jsreport has also in the preview [chrome-pdf](https://jsreport.net/learn/chrome-pdf) recipe which uses [headless chrome](https://developers.google.com/web/updates/2017/04/headless-chrome) to print pdf. This recipe basically uses the same underlying rendering technology as [electron-pdf](https://github.com/bjrmatos/jsreport-electron-pdf) so the capabilities of both recipes are the same.

The second pdf recipe which is not listed in the table is [fop-pdf](https://jsreport.net/learn/fop-pdf). This is because the recipe uses fundamentally different approach which is not based on the html to pdf conversion. The [fop-pdf](https://jsreport.net/learn/fop-pdf) usage makes sense in scenarios which requires the full control over the output pdf because the learning curve for the source markdown can be longer.