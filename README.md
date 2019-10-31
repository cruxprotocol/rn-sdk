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


Refer to the documentation at [@cruxprotocol/js-sdk](https://github.com/cruxprotocol/js-sdk)
