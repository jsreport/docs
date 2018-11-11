
jsreport supports several [recipes](/learn/recipes) which can be used to render pdf documents. There isn't just one because each html to pdf conversion technology used by recipes has its pros and cons. There is simply no clear winner and to provide the best option to every use case jsreport implements several of them. This article should help with choosing the right one based on the specific requirements.

## Pdf recipes comparison
| features | [chrome-pdf ](/learn/chrome-pdf)| [phantom-pdf ](/learn/phantom-pdf)| [wkhtmltopdf](learn/wkhtmltopdf) | [electron-pdf](https://github.com/bjrmatos/jsreport-electron-pdf) | [weasyprint-pdf](https://github.com/jsreport/jsreport-weasyprint-pdf)
| --- | :---: | :---: | :---: | :---: |
| technology | [chrome headless](https://developers.google.com/web/updates/2017/04/headless-chrome) | [phantomjs](http://phantomjs.org/) | [wkhtmltopdf](https://wkhtmltopdf.org/) | [electron](https://github.com/electron/electron)| [weasyprint](http://weasyprint.org/)|
| header & footer| yes | yes | yes | no | yes |
| nested link `<a href="#">link</a>` | yes | no | yes | yes | yes |
| latest css/ js | yes | no | no | yes | no
| programmatic print trigger | yes | yes | no | yes | yes
| [css page rule](https://www.w3.org/TR/css3-page/) | no | no | no | no | yes
| speed | fast | fast | medium | fast | slow
| deployment - installation | default | npm | npm | npm and xvfb | complex - prefer docker

[phantom-pdf ](/learn/phantom-pdf) recipe supports using [phantomjs](http://phantomjs.org/) in version `1.9` as well as the newer one `2.1`. The second mentioned version has better support for newer css however it is less stable and lacks support for repeating `thead` when table spawns multiple pages.

A pdf recipe which is not listed in the table is [fop-pdf](/learn/fop-pdf). This is because the recipe uses fundamentally different approach which is not based on the html to pdf conversion. The [fop-pdf](/learn/fop-pdf) usage makes sense in scenarios which requires the full control over the output pdf because the learning curve for the source markdown can be longer.
