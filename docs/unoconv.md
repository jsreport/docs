> Extension using [unoconv](https://github.com/dagwieers/unoconv) to convert reports to the OpenoOffice/LibreOffice supported formats

## Installation

### Linux
```
npm i jsreport-unoconv
apt-get install unoconv
```

### Windows

1. Install Python
2. Download [unoconv](https://github.com/dagwieers/unoconv) script.
3. Install [LibreOffice](https://www.libreoffice.org/)
4. Configure jsreport

```js
{ 
  "extensions": {
    "unoconv": {
      "command": "python [unoconvScriptPath]"
    }
  }
}
```

## Usage

1. Prepare template generating an office document like xlsx or docx
2. Open unoconv properties and specify desired output format like pdf
3. Run the template

![unoconv](https://jsreport.net/img/unoconv.gif)

## Configuration

```js
{ 
  "extensions": {
    "unoconv": {
      "command": "custom unoconv command"
    }
  }
}
```










