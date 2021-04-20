# react-native-facetec-zoom

unofficial react-native bridge for Facetec's [Zoom SDK](https://dev.zoomlogin.com/). 
Warning: this library was created from 2 other open-source libraries:
- Tradle's [react-native-facetec-zoom](https://github.com/tradle/react-native-facetec-zoom) (works only for iOS).
- Maherzaidoune [react-native-facetec](https://github.com/maherzaidoune/react-native-facetec#readme) (works only for android)

## Functions

- liveness for both android and iOS


## Install

### Common

Add this lib as a file dependecy to your package.json.

This module depends on [react-native-image-store](https://github.com/tradle/react-native-image-store), so you'll need to npm install and react-native link that one too.

### iOS

This library has been tested with version 9.0.1 of the iOS SDK

First, download `FaceTecSDK.framework` from one of these sources:

- [Zoom SDK](https://dev.facetec.com/downloads)

Unzip the file, locate `FaceTecSDK.framework`, copy it to your `ios/` directory, and drag it to your project in XCode (Check the `Copy items if needed` option when asked)

Add a Copy File phase to your Xcode project and have `FaceTecSDK.framework` copied to Destination `Frameworks`

If you have an Objective-C project, add a blank Swift file to your project (File -> New -> Swift File), with a bridging header (it will prompt you to auto-create one).

add `NSCameraUsageDescription` to your Info.plist, e.g.:

```xml
<key>NSCameraUsageDescription</key>
<string>verify liveness with Zoom</string>
```

Exported methods and values with implimintation: 

```javascript
let initialized
  let verifying

  const initialize = async opts => {
    initialized = false

    const result = await native.initialize({
      ...defaults.initialize,
      ...opts,
      licenseKey: opts.licenseKey
    })

    initialized = result.success
    return result
  }

  const getVersion = () => native.getVersion()
  const verify = async (opts = {}) => {
    if (!initialized) {
      throw new NotInitializedError('initialize me first!')
    }

    if (verifying) {
      throw new VerificationPendingError('only one verification can be done at a time')
    }

    let result
    try {
      result = await native.verify({
        ...defaults.verify,
        ...opts,
      })

      result.status = statusToString(result.status)
      return result
    } finally {
      verifying = false
    }
  }

  return {
    preload: native.preload,
    initialize,
    verify,
    getVersion,
  }
```

First you have to initialize sdk, then use `verify`.

### Android

No additional steps required.

Then use like this:

````javascript
import {
  init,
  enroll,
  authenticateUser,
  livenessCheck,
} from 'react-native-facetec';

init(
    () => {
    console.log('init success');
    },
    () => console.log('init fail')
);

enroll(
    'USER_ID',
    (resp) => {
        //JSON.parse(resp)
        console.log('enroll ' + resp);
    },
    (error) => console.log('enroll ' + error)
);

livenessCheck(
    (resp) => {
        console.log('livenessCheck ' + resp);
    },
    (error) => console.log('livenessCheck ' + error)
)

authenticateUser(
    'USER_ID',
    (resp) => {
        console.log('authenticateUser ' + resp);
    },
    (error) => console.log('authenticateUser ' + error)
)

CheckId(
    'USER_ID',
    (resp) => {
        console.log('CheckId ' + resp);
        let data = JSON.parse(params);
        data.FrontImagesCompressedBase64 // front id image data:image/jpeg;base64
        data.BackImagesCompressedBase64 // back id image data:image/jpeg;base64
    },
    (error) => console.log('CheckId ' + error)
)
````

## Usage

See example app at https://github.com/tradle/rnzoomexample
```
