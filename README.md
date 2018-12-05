# deno-pako

> zlib port to deno

This project is port of [pako][] to deno.

## Example & API

Full docs - http://nodeca.github.io/pako/

```js
import pako from 'https://raw.githubusercontent.com/kt3k/deno-pako/master/index.js';

// Deflate
//
const input = new Uint8Array();
//... fill input data here
const output = pako.deflate(input);

// Inflate (simple wrapper can throw exception on broken stream)
//
const compressed = new Uint8Array();
//... fill data to uncompress here
try {
  const result = pako.inflate(compressed);
} catch (err) {
  console.log(err);
}

//
// Alternate interface for chunking & without exceptions
//

const inflator = new pako.Inflate();

inflator.push(chunk1, false);
inflator.push(chunk2, false);
...
inflator.push(chunkN, true); // true -> last chunk

if (inflator.err) {
  console.log(inflator.msg);
}

const output = inflator.result;

```

Sometime you can wish to work with strings. For example, to send
big objects as json to server. Pako detects input data type. You can
force output to be string with option `{ to: 'string' }`.

```js
import pako from 'https://raw.githubusercontent.com/kt3k/deno-pako/master/index.js';

const test = { my: 'super', puper: [456, 567], awesome: 'pako' };

const binaryString = pako.deflate(JSON.stringify(test), { to: 'string' });

//
// Here you can do base64 encode, make xhr requests and so on.
//

const restored = JSON.parse(pako.inflate(binaryString, { to: 'string' }));
```


## Notes

Pako does not contain some specific zlib functions:

- __deflate__ -  methods `deflateCopy`, `deflateBound`, `deflateParams`,
  `deflatePending`, `deflatePrime`, `deflateTune`.
- __inflate__ - methods `inflateCopy`, `inflateMark`,
  `inflatePrime`, `inflateGetDictionary`, `inflateSync`, `inflateSyncPoint`, `inflateUndermine`.
- High level inflate/deflate wrappers (classes) may not support some flush
  modes. Those should work: Z_NO_FLUSH, Z_FINISH, Z_SYNC_FLUSH.


## Prior art

Original implementation (in JS):

- [pako][] by Andrey Tupitsin and Vitaly Puzrin

Original implementation (in C):

- [zlib](http://zlib.net/) by Jean-loup Gailly and Mark Adler.


## License

- MIT - all files, except `/lib/zlib` folder
- ZLIB - `/lib/zlib` content

[pako]: https://github.com/nodeca/pako
