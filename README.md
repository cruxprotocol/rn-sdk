### Using bundled version in React Native

After installing `@cruxpay/rn-sdk`, you will need to 
1. Install and link  `react-native-get-random-values` which works as a polyfill for the global crypto.getRandomValues
2. Next, you will have to install and link `@react-native-community/async-storage` so that you can use the [RNLocalStorage](https://gist.github.com/mascot6699/8a1a3cf2afa411f643fe9ec1bab3e9c2) implementation of `storage.StorageService` as shown below.

```javascript
// RNLocalStorage.js
import { CruxPay } from "@cruxpay/rn-sdk/dist/cruxpay-sdk-rn";
import AsyncStorage from "@react-native-community/async-storage";
class RNLocalStorage extends CruxPay.storage.StorageService {
    setItem = async (key, value) => AsyncStorage.setItem(key, value);
    getItem = async (key) => AsyncStorage.getItem(key);
}
export {RNLocalStorage};
```

3. You can then use the sdk by providing an instance of `RNLocalStorage` as storage option and importing `react-native-get-random-values` before importing `dist/cruxpay-sdk-rn.js` (the **bundled version** of installed sdk)

```javascript
import 'react-native-get-random-values';  // NOTE: necessary to imported first so that it can polyfill global crypto.getRandomValues
import {CruxPay} from "@cruxpay/rn-sdk/dist/cruxpay-sdk-rn";  // NOTE: this is from dist folder
import {RNLocalStorage} from "./RNLocalStorage";
const storage = new RNLocalStorage();
let cruxClientOptions = {
  getEncryptionKey: () => 'fookey',
  walletClientName: 'cruxdev',
  storage: storage
}
const cruxClient = new CruxPay.CruxClient(cruxClientOptions);
cruxClient.init().then(() => {
    console.log('CruxClient initialized');
}).catch((err) => {
    console.log('CruxClient error', err);
})
```

### Using bundled version in React Native

After installing `@cruxpay/rn-sdk`, you will need to 
1. Install [rn-nodeify](https://www.npmjs.com/package/rn-nodeify) which allows you to use node builtins in React Native.
```
npm install --save-dev rn-nodefiy
```
2. Hack the specific builtins required via:
```
./node_modules/.bin/rn-nodeify --install "process,path,crypto,assert,stream,events,url,vm" —hack @cruxpay/rn-sdk
```
3. Next, you will have to install and link `@react-native-community/async-storage` so that you can use the [RNLocalStorage](https://gist.github.com/mascot6699/8a1a3cf2afa411f643fe9ec1bab3e9c2) implementation of `storage.StorageService` as shown below.
```
npm install --save @react-native-community/async-storage
```

```javascript
// RNLocalStorage.js
import { CruxPay } from "@cruxpay/rn-sdk";
import AsyncStorage from "@react-native-community/async-storage";
class RNLocalStorage extends CruxPay.storage.StorageService {
    setItem = async (key, value) => AsyncStorage.setItem(key, value);
    getItem = async (key) => AsyncStorage.getItem(key);
}
export {RNLocalStorage};
```

4. You can then use the sdk by providing an instance of `RNLocalStorage` as storage option and importing `shim.js` before importing `@cruxpay/rn-sdk`

```javascript
import './shim.js';  // NOTE: necessary to imported first so that it can polyfill the node builtins
import {CruxPay} from "@cruxpay/rn-sdk";
import {RNLocalStorage} from "./RNLocalStorage";
let cruxClientOptions = {
  getEncryptionKey: () => 'fookey',
  walletClientName: 'cruxdev',
  storage: new RNLocalStorage()
}
const cruxClient = new CruxPay.CruxClient(cruxClientOptions);
cruxClient.init().then(() => {
    console.log('CruxClient initialized');
}).catch((err) => {
    console.log('CruxClient error', err);
})
```

Refer to the documentation at [@cruxprotocol/js-sdk](https://github.com/cruxprotocol/js-sdk)
