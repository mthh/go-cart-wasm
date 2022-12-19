## Go-cart-wasm

`Go-cart-wasm` is a JS/WASM library for making flow-based cartograms (as described in "*Gastner, Seguy, and More (2018). Fast flow-based algorithm for creating density-equalizing map projections. Proceedings of the National Academy of Sciences USA, 115:E2156-E2164*"), purely in the browser.

This is a port of the [reference implementation](https://github.com/Flow-Based-Cartograms/go_cart) provided by the authors (however we needed to slightly modify some minor aspects of the code, and our modified version is located [here](https://github.com/Flow-Based-Cartograms/go_cart/tree/wasm-mod)).

### Usage

The library is available as a JS module, and can be used as follows:

```js
initGoCart()
  .then((GoCartModule) => {
    const data = { // The GeoJSON containing the data to be transformed
      type: 'FeatureCollection',
      features: ...
    };
    const fieldName = 'POP2021'; // The name of the field that contains the data on which the cartogram will be based
    
    const dataCartogram = GoCartModule.makeCartogram(data, fieldName); // Call the function that creates the cartogram
    
    console.log(dataCartogram); // The resulting GeoJSON
  });
```

Optionally, you can also pass a `config` object as argument to the `initGoCart` function, which can contain the `locateFile` property: if set, it will be used to locate the WASM file (which is needed by the library). For example:

```js
initGoCart({
  locateFile: (path) => {
    return 'js/cart.wasm';
  },
})
  .then((GoCartModule) => {
    ...
  });
```

Note that the data that is passed to the `makeCartogram` function must be a valid GeoJSON FeatureCollection, its features must be of type Polygon or MultiPolygon and the field name must be a valid property of its features.
Moreover, the data have to be in a projected reference coordinate system that maintains the areas of the polygons (e.g. Lambert-93 / EPSG:2154 for Metropolitan France, EPSG:3035 for the European Union, etc.). Performing the calculation in geographic coordinates (e.g. EPSG:4326) would give erroneous results.

### Installation for development

Requirements:
- The **Just** command runner (https://github.com/casey/just)
- EMSDK (https://emscripten.org/docs/getting_started/downloads.html)
- NPM (https://www.npmjs.com/get-npm)

1) Install Just, install EMSDK.
2) Activate the various environment variables of EMSDK in the current terminal (`source ./emsdk_env.sh` in emsdk directory).
3) Run `npm run build` to build the WASM module and the JS wrapper.
4) Get the built files from the `dist` directory.

Note that this has only been tested on GNU/Linux and that these instructions may need to be modified to work on Mac OS X and Windows.

### License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
Underlying code by Gastner, Seguy, and More (2018) is licensed under a modified MIT License - see their [LICENSE](go_cart/LICENSE) file for details.
