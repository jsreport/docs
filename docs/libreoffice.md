

Extension using [LibreOffice](https://www.libreoffice.org/) to convert reports to the office-supported formats and to send the reports to the printers.

## Installation

```
npm i @jsreport/jsreport-libreoffice
```

Install [LibreOffice](https://www.libreoffice.org/) based on your OS.

## Usage

1. Prepare a template generating an office document like xlsx or docx
2. Open `libreoffice` properties and specify the desired output `Format`, like pdf or specify the `Printer` name where to send the output.
3. Alternatively, you can choose from the various `Pdf Export options`. The documentation for the options can be found [here](https://help.libreoffice.org/latest/en-US/text/shared/guide/pdf_params.html).

## Usage in script

#### Convert the report output
```js
const jsreport = require('jsreport-proxy')
async function afterRender(req, res) {
  const pdf = await jsreport.libreOffice.convert(res.content, 'pdf', {
    pdfExportSelectPdfVersion: 15
  })
  res.content = pdf.content
}
```

#### Send the output to the printer
```js
const jsreport = require('jsreport-proxy')
async function afterRender(req, res) {
  // send the report output to the default printer
  await jsreport.libreOffice.print(res.content, 'default')
}
```

## Configuration

The `soffice` binary is expected to be found in the default paths, however, you can also explicitly configure the binary path.
```js
{ 
  "extensions": {
	"libreoffice": {
		"sofficePath": "/soffice/path"
	}
  }
}
```










