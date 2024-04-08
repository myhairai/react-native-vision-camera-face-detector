## 📚 Introduction

`react-native-vision-camera-face-detector` is a React Native library that integrates with the Vision Camera module to provide face detection functionality. It allows you to easily detect faces in real-time using the front camera and visualize the detected faces on the screen.

If you like this package please give it a ⭐ on [GitHub](https://github.com/nonam4/react-native-vision-camera-face-detector).

## 🏗️ Features

- Real-time face detection using front camera
- Integration with Vision Camera library
- Adjustable face visualization with customizable styles
- Base64 frame convertion

## 🧰 Installation

```bash
yarn add react-native-vision-camera-face-detector
```

Then you need to add `react-native-worklets-core` plugin to `babel.config.js`. More details [here](https://react-native-vision-camera.com/docs/guides/frame-processors#react-native-worklets-core).

## 💡 Usage

Recommended way:
```jsx
import { 
  StyleSheet, 
  Text, 
  View 
} from 'react-native'
import { 
  useEffect, 
  useState 
} from 'react'
import {useCameraDevice} from 'react-native-vision-camera'
import {
  Camera,
  DetectionResult
} from 'react-native-vision-camera-face-detector'
import { Worklets } from 'react-native-worklets-core'

export default function App() {
  const device = useCameraDevice('front')

  useEffect(() => {
    (async () => {
      const status = await Camera.requestCameraPermission()
      console.log({ status })
    })()
  }, [device])

  const handleFacesDetection = Worklets.createRunInJsFn( (
    result: DetectionResult
  ) => { 
    console.log( 'detection result', result )
  })

  return (
    <View style={{ flex: 1 }}>
      {!!device? <Camera
        style={StyleSheet.absoluteFill}
        device={device}
        faceDetectionCallback={ handleFacesDetection }
        faceDetectionOptions={ {
          // detection settings
        } }
      /> : <Text>
        No Device
      </Text>}
    </View>
  )
}
```

Or default way:
```jsx
import { 
  StyleSheet, 
  Text, 
  View 
} from 'react-native'
import { 
  useEffect, 
  useState 
} from 'react'
import {
  Camera,
  useCameraDevice,
  useFrameProcessor
} from 'react-native-vision-camera'
import { 
  detectFaces,
  DetectionResult 
} from 'react-native-vision-camera-face-detector'
import { Worklets } from 'react-native-worklets-core'

export default function App() {
  const device = useCameraDevice('front')

  useEffect(() => {
    (async () => {
      const status = await Camera.requestCameraPermission()
      console.log({ status })
    })()
  }, [device])

  const handleFacesDetection = Worklets.createRunInJsFn( (
    result: DetectionResult
  ) => { 
    console.log( 'detection result', result )
  })
  const frameProcessor = useFrameProcessor((frame) => {
    'worklet'
    const result = detectFaces( {
      frame,
      options: {
        // detection settings
      }
    })
    handleFacesDetection(result)
  }, [handleFacesDetection])

  return (
    <View style={{ flex: 1 }}>
      {!!device? <Camera
        style={StyleSheet.absoluteFill}
        device={device}
        frameProcessor={frameProcessor}
      /> : <Text>
        No Device
      </Text>}
    </View>
  )
}
```

OBS: Returned face bounds, landmarks and contours are now natively downscaled to screen size. No extra function needed.

See example app for more details.

## 🔧 Troubleshooting

Here is a common issue when trying to use this package and how you can try to fix it:

- `Regular javascript function cannot be shared. Try decorating the function with the 'worklet' keyword...`:
  - If you're using `react-native-reanimated` maybe you're missing [this](https://github.com/mrousavy/react-native-vision-camera/issues/1791#issuecomment-1892130378) step.

If you find other errors while using this package you're wellcome to open a new issue or create a PR with the fix.

## 👷 Built With

- [React Native](https://reactnative.dev/)
- [Google MLKit](https://developers.google.com/ml-kit)
- [Vision Camera](https://react-native-vision-camera.com/)

## 🔎 About

This package was tested using the following:

- `react-native`: `0.73.6` (new arch disabled)
- `react-native-vision-camera`: `4.0.0-beta.11`
- `react-native-worklets-core`: `0.4.0`
- `react-native-reanimated`: `3.8.1`
- `expo`: `50.0.14`

Min O.S version:

- `Android`: `SDK 26` (Android 8)
- `IOS`: `14`

## 📚 Author

Made with ❤️ by [nonam4](https://github.com/nonam4)
