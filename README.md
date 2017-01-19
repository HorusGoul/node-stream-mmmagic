# node-stream-mmmagic

Node module to sniff the start of a stream (non-destructively) to detect the file type and encoding
when you don't have the luxury of being able to restart the stream again.

It does so by using [buffer-peek-stream](https://github.com/seangarner/node-buffer-peek-stream) to
get the first 16KB of the stream then send that to mmmagic (which uses libmagic).  Before it's
finished the peek stream will unshift the bytes it's received back onto the origin stream thereby
making it appear as if the origin stream was new.

```bash
npm install stream-mmmagic
```

## Use
```js
var magic = require('stream-mmmagic');

var input = fs.createReadStream('somefile.csv');

magic(input, function (err, mime, output) {
  if (err) throw err;

  console.log('TYPE:', mime.type);
  console.log('ENCODING:', mime.encoding);

  // will print the *whole* file
  output.pipe(process.stdout);
});

//- TYPE: text/plain
//- ENCODING: us-ascii
//- <the file content>
```

### `options.magicFile` Custom Magic File
A magic file is bundled with the mmmagic npm module but if you want to use your own then set the path to the file on
the `magicFile` option.

```js
const magicFile = '/usr/share/magic';
magic(input, {magicFile}, callback);
```

### `options.splitMime` Original Mime String
Use `{splitMime: false}` option to get back the original mime string instead of a split object.
```js
magic(input, {splitMime: false}, (err, mime, output) => {
  console.log(mime);
});
//- text/plain; charset=us-ascii
```


## LICENSE
MIT
