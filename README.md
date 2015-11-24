webpack-localForage
===================

Deprecated. Use mozilla/localForage version 1.3.0(or above).

Webpack friendly https://github.com/mozilla/localForage (Offline storage, improved.)

----------
## API

http://mozilla.github.io/localForage/

## Changes w.r.t. mozilla/localForage :
- Removed promise module import.
- Removed all moduleType specific code.
- Lazy load localforage drivers using webpack bundle loader.
 
## Installation :
- Download the project or install via bower ```bower install webpack-localforage``` or npm ```npm install webpack-localforage```
- Install webpack ```npm install webpack``` with following loaders
  * bundle loader ```npm install bundle-loader```
  * imports loader ```npm install imports-loader```
  * exports loader ```npm install exports-loader``` 
- Install es6-promise polyfill (If you are targeting browsers with no es6 promise support). ```bower install es6-promise```

## Configuration :

``` javascript
var path = require('path');
/* Adjust the bower path according to your project structure */
var bowerComponentsPath = path.join(__dirname, '/bower_components');

var config = {
    module: {
            {
                test: /[\/]promise\.js$/,
                loaders: ['exports?Promise']
            },
            {
                test: /[\/](localforage|indexeddb|localstorage|websql)\.js$/,
                loaders: ['imports?this=>window']
            }
    },
    resolve: {
        alias: {
            localforage: path.join(bowerComponentsPath, 'webpack-localForage/src/localforage.js'),
            // A polyfill for ES6-style Promises
            promise: path.join(bowerComponentsPath, 'es6-promise/promise.js'),
        }
    }
};
module.exports = config;
```

## Usage

If you are targeting browsers with no promise support (http://caniuse.com/#feat=promises), lazy load polyfill.

``` javascript
var es6PromiseSupport =
        'Promise' in window &&
        /*
         * Some of these methods are missing from
         * Firefox/Chrome experimental implementations
         */
        'resolve' in window.Promise &&
        'reject' in window.Promise &&
        'all' in window.Promise &&
        'race' in window.Promise &&
        (function () {
            /*
             * Older version of the spec had a resolver object
             * as the arg rather than a function
             */
            var resolve;
            new window.Promise(function (r) {
                resolve = r;
            });
            return (typeof resolve === 'function');
        }());

if (!es6PromiseSupport) {
    var load = require('bundle?lazy!promise');
    load(function (promise) {
        callback();
    });
} else {
    callback();
}
var callback = function(){
    require('localforage');
}
```
