

> Extension using [unoconv](https://github.com/dagwieers/unoconv) to convert reports to the OpenoOffice/LibreOffice supported formats

## Installation

### Linux
```
npm i @jsreport/jsreport-unoconv
apt-get install unoconv
```

### Windows

1. Install Python
2. Download [unoconv](https://raw.githubusercontent.com/unoconv/unoconv/master/unoconv) script.
3. Install [LibreOffice](https://www.libreoffice.org/)
4. `npm i @jsreport/jsreport-unoconv`
5. Configure jsreport

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


## Parallel run
The LibreOffice doesn't support parallel run of the requests. The workaround is to use a global lock on unoconv

```
{ 
  "extensions": {
    "unoconv": {
      "command": "/usr/bin/timeout 30 flock -w 20 lockfile /usr/bin/unoconv"
    }
  }
}
```

This workaround was originally described [on forum](https://forum.jsreport.net/topic/2976/unoconv-pdf-conversion-having-problems-with-simultaneous-requests/3).

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










