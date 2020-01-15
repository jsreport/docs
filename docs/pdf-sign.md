
> Sign pdf using a certificate

## Installation

```
npm i jsreport-pdf-sign
```

## Sign using studio

Open studio and upload p12 certificate as new [asset](/learn/assets).
For the testing purposes you can create your p12 certificate using openssl

```
openssl genrsa -out myKey.pem
openssl req -new -key myKey.pem -out cert.csr
openssl x509 -req -in cert.csr -signkey myKey.pem -out cert.crt
openssl pkcs12 -export -in cert.crt -inkey myKey.pem -out cert.p12
```

If your certificate has a password, fill it in through asset pdf-settings properties inside the studio.

<br/>
![pdf sign password](/img/pdf-sign-password.png)
<br/>

In this case, you need to configure or disable also [jsreport  encryption](learn/configuration#encryption-configuration) because this extension uses the encryption API to protect password before storing.

Then create a new template and link the certificate asset with it.

<br/>
![pdf sign template](/img/pdf-sign-template.png)
<br/>

The last step is to verify the pdf is properly signed. Install the certificate on your client machine and make sure it is trusted. Then open the Adobe Acrobat and you should see the message "Signed and all signatures are valid". 

<br/>
![pdf sign acrobat](/img/pdf-sign-acrobat.png)
<br/>

## Sign using API

You may want to avoid storing your certificates inside jsreport store. In this case, you can send the encoded certificate inline in your rendering request.

```js
POST http://jsreportserver:5488/api/report

{ 
  "template": {
    "name": "my template",
    "pdfSign": {
      "certificateAsset": {
        "content": "base64 encoded certificate"
        "encoding": "base64",
        "password":  "certificate password"
      },
      "reason": "I am signing this pdf"
    }
  }
}
```