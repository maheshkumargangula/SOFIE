# Registration of Extensions

## Sunbird Third Party App (Client App) Integration API Spec

### Register API

> **POST - api/client-app/v1/register**

#### Request

```javascript
{
  "request": {
    "client-app": {
       "name": "", // Required. Name of the app. For ex: "Sunbird ReadAlong"
       "logo": "", // Required. Logo of the app. This is used to show the user to select the extension app he chooses to open with.
       "provider": {
          "name": "", // Required. Name of the company that has built the app
          "copyright": "", // Optional. Any copyright information to be shown
          "license": "" // Optional. License of the app.
       },
       "android": {
          "packageId": "", // Required. A fully qualified app name matching play store app id. For ex: "com.sunbird.readalong.app"
          "appVersion": "", // Required. Version of the app. This is used by sunbird to check if the app is installed locally
          "compatibilityVer": "" // Required. Compatible with which version of sunbird app.
       },
       "ios": {
          "packageId": "", // Required. A fully qualified app name matching app store app id. For ex: "com.sunbird.readalong.app"
          "appVersion": "", // Required. Version of the app. This is used by sunbird to check if the app is installed locally
          "urlScheme": "", // Required. The URL scheme of the app. Used for the app invocation.
          "compatibilityVer": "" // Required. Compatible with which version of sunbird app.
       },
       "target": { // Required. To provided contextual targeting/linkking of the app. For ex: Show "Open With" only for PDF content.
          "mimeType": [], // Optional. Show the app in supported list only when matched the given mime types. For ex: mimeType: ["application/pdf"] would target only PDF content
          "contentType": [], // Optional. Show the app in supported list only when matched the given content type. For ex: mimeType: ["application/pdf"] would target only PDF content
          ....// Match on all content attribute. Refer to the content model spec linked in the "Content Model Specification" section
       }
    }
  }
}
```

#### Response

```javascript 
{
  "id": "api.client-app.register",
  "ver": "3.0",
  "ts": "2021-02-10T20:24:45ZZ",
  "params": {
    "resmsgid": "3be02c4b-3324-41a3-afd8-60f6be0584d2",
    "msgid": null,
    "err": null,
    "status": "successful",
    "errmsg": null
  },
  "responseCode": "OK",
  "result": {
    "client-id": "do_1131697204035993601314",
    "client-key": "1607631885207"
  }
}
```

### Client App Read API

> TODO
> 

### Client App Update API

> TODO
> 
