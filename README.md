
# react-native-esptouch2

## One should know that
This is a Unofficial project. The official demo is below:

[EsptouchForAndroid](https://github.com/EspressifApp/EsptouchForAndroid)

[EsptouchForIOS](https://github.com/EspressifApp/EsptouchForIOS)

## Getting started

`$ npm install react-native-esptouch2 --save`

### Mostly automatic installation

~~`$ react-native link react-native-esptouch2`~~

This should be handled automatically since React Native 0.60

### Manual installation


#### iOS

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules` ➜ `react-native-esptouch2` and add `RNEsptouch.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libRNEsptouch.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project (`Cmd+R`)<

#### Android

1. Open up `android/app/src/main/java/[...]/MainActivity.java`
  - Add `import com.rickl.rn.esptouch.RNEsptouchPackage;` to the imports at the top of the file
  - Add `new RNEsptouchPackage()` to the list returned by the `getPackages()` method
2. Append the following lines to `android/settings.gradle`:
  	```
  	include ':react-native-esptouch2'
  	project(':react-native-esptouch2').projectDir = new File(rootProject.projectDir, 	'../node_modules/react-native-esptouch2/android')
  	```
3. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
  	```
      compile project(':react-native-esptouch2')
  	```


## Additional react native modules needed for getting location service permission
Any libraries that can get the device's Location and Network information.

My recommendations:

`react-native-network-info`

`expo-location`

## Usage
```javascript

import React, {
  useEffect,
  useState,
} from 'react';
import RNEspTouch from 'react-native-esptouch2';
import * as Location from 'expo-location';
import { NetworkInfo } from 'react-native-network-info';

function SmartConfiguration() {
  const [location, setLocation] = useState(null);
  const [errorMsg, setErrorMsg] = useState(null);
  const [ssid, setSsid] = useState(null);
  const [bssid, setBssid] = useState(null);
  const [result, setResult] = useState(null);

  useEffect(() => {
    (async () => {
      const { status } = await Location.requestPermissionsAsync();
      if (status !== 'granted') {
        setErrorMsg('Permission to access location was denied');
        return;
      }

      const _location = await Location.getCurrentPositionAsync({});
      setLocation(_location);

      const _ssid = await NetworkInfo.getSSID();
      const _bssid = await NetworkInfo.getBSSID();

      setSsid(_ssid);
      setBssid(_bssid);

      RNEspTouch.initESPTouch();

      const password = 'password';
      const _result = await RNEspTouch.startSmartConfigWithSSID(
        _ssid,
        _bssid,
        password,
        1
      );
      setResult(_result);
    })();
    return function cleanup() {
      RNEspTouch.finish();
    };
  }, []);
  
  if (errorMsg) {
    return (
      <View>
        <Text>{errorMsg}</Text>
      </View>
    );
  }
  
  return (
    <View>
      {!result ? (
        <Text>Configuring...</Text>
      ) : (
        <Text>{result.code === 200 ? 'Success!' : 'Failed'}</Text>      
      )}
    </View>
  );
}

```
<img src="./images/1574120354357.jpg" alt="1574120354357.jpg"  /> <img src="./images/1574120350599.jpg" alt="1574120350599.jpg"  />
## Licence
[Licence](https://github.com/EspressifApp/EsptouchForIOS/blob/master/ESPRESSIF_MIT_LICENSE_V1.LICENSE)
