# SOFIE v0.5
A Specification for Open Feature Integration and Extensions

Documentation is categorised into multiple sections:
* Overview
* Registration Spec for Apps to Sunbird
    * Global Registration API
* Technical Specification for third party apps
    * Param Data Supported
    * Android
      * Intent Handling
* Content Model Specification
* Summary Event Specification    


## Overview
Intent of this specification is to integrate with external apps for feature extension (OR) feature integration of Sunbird Application. Following diagram represents the workflow of a 3rd party or custom extensions app registering and interacting with the sunbird app.

![Alt Text](attachments/2016411649/2164949010.png)




#### Sunbird Third Party App Interaction

![Alt Text](attachments/2016411649/2165604363.png)

---

## Registration Spec for Apps to Sunbird

Each third party app should be registered with Sunbird for the integrations to be enabled. The purpose of registration is to verify the authenticity of the app providing integration/extension to sunbird and also allow policy-makers (of the sunbird instance) to do appropriate review and accept the integration. 

Following is the info/specificiation required for the registration. The complete API details of the registration can be found [here](https://github.com/sunbird-specs/SOFIE/blob/main/registration.md)

```javascript
{
   name: "", // Required. Name of the app. For ex: "Sunbird ReadAlong"
   logo: "", // Required. Logo of the app. This is used to show the user to select the extension app he chooses to open with.
   provider: {
      name: "", // Required. Name of the company that has built the app
      copyright: "", // Optional. Any copyright information to be shown
      license: "" // Optional. License of the app.
   },
   android: {
      packageId: "", // Required. A fully qualified app name matching play store app id. For ex: "com.sunbird.readalong.app"
      appVersion: "", // Required. Version of the app. This is used by sunbird to check if the app is installed locally
      compatibilityVer: "" // Required. Compatible with which version of sunbird app.
   },
   ios: {
      packageId: "", // Required. A fully qualified app name matching app store app id. For ex: "com.sunbird.readalong.app"
      appVersion: "", // Required. Version of the app. This is used by sunbird to check if the app is installed locally
      urlScheme: "", // Required. The URL scheme of the app. Used for the app invocation.
      compatibilityVer: "" // Required. Compatible with which version of sunbird app.
   },
   target: { // Required. To provided contextual targeting/linkking of the app. For ex: Show "Open With" only for PDF content.
      mimeType: [], // Optional. Show the app in supported list only when matched the given mime types. For ex: mimeType: ["application/pdf"] would target only PDF content
      contentType: [], // Optional. Show the app in supported list only when matched the given content type. For ex: mimeType: ["application/pdf"] would target only PDF content
      ....// Match on all content attribute. Refer to the content model spec linked in the "Content Model Specification" section
   }
}
```
---

## Technical Specification for third party apps

### Param Data Supported

Following is the parameters that are passed to the 3rd party either as intent data (in android) or url scheme in (iOS)

| Params | Description | Data Type|
| -- | -- | -- |
| referrerPackageId | Package details of referrer app | String |
| referenceID | Reference ID to be sent back to sunbird app | String |
| mimeType | Content MimeType | String |
| authKey | Authorization Key for referrer app | String |
| contentUrl | Url of the content | String |
| profileContext | Basic profile info like name and avatar in JSONified String | String |

### Android

#### Intent Handling

* Need to create an intent filter in Android Manifest as follows 
```xml
<activity
    android:name="com.example.ExampleActivity"
    android:label="@string/title_example" >
    <intent-filter android:label="@string/play_view_web_example">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "http://sunbird.staginged.in/play” -->
        <data android:scheme="https"
              android:host="sunbird.staginged.in"
              android:pathPrefix="/play" />
    </intent-filter>
    <intent-filter android:label="@string/play_view_app_example">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with "example://play” -->
        <data android:scheme="example"
              android:host="play" />
    </intent-filter>
</activity>
```
* Implement Activity as follows
```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);

    Intent intent = getIntent();
    String action = intent.getAction();
    Uri data = intent.getData();
    /** Trigger the Business Logic of App */
}
```

### iOS

> To be added
---

## Content Model Specification
<details>
<summary>Expand API Documentation</summary>
The third party apps should do a HTTP GET call on the contentUrl parameter sent via the intent data.

```
GET intentdata.contentUrl

Headers {
  X-App-ID: <ID of the App>,
  X-App-Version: <Version of the App>,
  X-Device-ID: <Device Id>
}
```

```javascript
{
   id: "api.content.read",
   ver: "v1",
   ts: "2021-02-10 04:27:14:674+0000",
   params: {
      msgid: "90e184d1-c52c-106b-c410-6a168900ef05",
      err: "",
      status: "success",
      errmsg: ""
   },
   responseCode: "OK",
   result: {
      content: {} // Refer to the content model schema defined below
   }
}
```
For More Details on the content model refer Sunbird Documentation.
* content - https://github.com/sunbird-specs/LearningObjectModel/blob/main/v1/schemas/content/1.0/schema.json
* collection - https://github.com/sunbird-specs/LearningObjectModel/blob/main/v1/schemas/collection/1.0/schema.json
</details>

---

## Summary Event Specification

All 3rd party apps are required to at-least send back a summary of the content play session as per the sunbird telemetry spec. In addition the 3rd party apps can send any event as per [Sunbird Telemetry Spec](https://github.com/sunbird-specs/Telemetry/blob/main/specification.md)
<details>
<summary>Expand Summary Event</summary>
https://github.com/sunbird-specs/Telemetry/blob/main/v3_event_details.md/#summary

```javascript
{
  "edata": {
    "type": String, // Required. Type of summary. Free text. "session", "app", "tool" etc
    "mode": String, // Optional.
    "starttime": Long, // Required. Epoch Timestamp of app start. Retrieved from first event.
    "endtime": Long, // Required. Epoch Timestamp of app end. Retrieved from last event.
    "timespent": Double, // Required. Total time spent by visitor on app in seconds excluding idle time.
    "pageviews": Long, // Required. Total page views per session(count of CP_IMPRESSION)
    "interactions": Long, // Required. Count of interact events
    "envsummary": [{ // Optional
        "env": String, // High level env within the app (content, domain, resources, community)
        "timespent": Double, // Time spent per env
        "visits": Long // count of times the environment has been visited
    }],
    "eventssummary": [{ // Optional
        "id": String, // event id such as CE_START, CE_END, CP_INTERACT etc.
        "count": Long // Count of events.
    }],
    "pagesummary": [{ // Optional
        "id": String, // Page id
        "type": String, // type of page - view/edit
        "env": String, // env of page
        "timespent": Double, // Time taken per page
        "visits": Long // Number of times each page was visited
    }],
    "extra": [{ // Optional. Additional summary data specific to mime type or app. For ex: wordsPerMin
        "id": String, // Required. Key for the extra data
        "value": String // Required. Value for the extra data
    }]
  }
}
```
</details>



