
> Protect office documents with password

## Installation

```
npm i @jsreport/jsreport-office-password
```

## Studio
Every template with recipe producing an office document gets additional properties menu "office-password". There you can fill the desired password which will be demanded after you open the document in the desktop office. The studio office preview doesn't support passwords protection therefore the extension skip it when returning preview. However you can use the "Download" option from the "Run" button and receive the password protected document right from the studio.

![office-password](/img/office-password.png)

This extension uses jsreport  encryption API to protect the stored password. You need to configure the encryption secret key or disable the encryption. See [encryption configuration](/learn/configuration#encryption-configuration) for the details.

## API password protection

You can send the password also directly in the API render call.

```js
POST http://jsreportserver:5488/api/report

{ 
  "template": {
    "name": "my template producing office doc",    
    "officePassword": {     
       "password":  "password"
    }    
  }
}
```