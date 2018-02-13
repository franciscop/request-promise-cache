# request-promise-cache

Request.js wrapper that uses promises and cache:

```javascript
const request = require('request-promise-cache');

request({
  url: 'https://google.com/',
  cacheTTL: 60 * 60 * 1000,  // 1h = 60min * 60s * 1000ms
  cacheLimit: 12
}).then(function(ret) {
  console.log(ret); // {body: body, response: response, error: error}
}).catch(function(ret) {
  console.error(ret); // {response: response, error: error}
});
```



## Options

All of the original [request library's options](https://github.com/request/request#requestoptions-callback), plus the following:

* `cacheKey: url (string)`, the cache key to use. Defaults to `baseUrl + url`, but you might want to change if you use querystring or some of the advanced `request` features.
* `cacheTTL: undefined (number)`. The **milliseconds** that the cache will be valid before a new request is sent out. If used with `cacheLimit`, whichever comes first will take precedence.
* `cacheLimit: undefined (number)`, automatically expire a cache entry after X amount of reads, if used with `cacheTTL`, whichever comes first will take precedence.
* `fresh: false (boolean)`, delete the cached entry and get a fresh one



## Asynchronous calls with the same `cacheKey`

If you make 2 or more requests with the same `cacheKey` at the _same_ time, and of course, the response comes back within the `cacheTTL` of the first request, __only__ 1 request will go out, the rest will wait for it and resolve at the _same_ time.



## Dependencies

* [request](https://github.com/request/request)
* [nano-cache](https://github.com/akhoury/nano-cache)



## Other promise libraries?

By default, this module uses the native javascript [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) introduced in Node.js 0.12+, however you can use it with others, by passing your own `Promise` constructor


```javascript
// if you want to use bluebird for example
// just do this once, somewhere in your app, ideally whatever file loads first, i.e. app.js
var request = require('request-promise-cache').use( require('bluebird').Promise )

// you don't have to do it again in the same app's other files
```



#### Tested with

* [bluebird](https://github.com/petkaantonov/bluebird)
* [when](https://github.com/cujojs/when)
* [q](https://github.com/kriskowal/q)
* and native [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

If you want me to test another one, just add it and make a pull request to the [`promiseTypes`](https://github.com/akhoury/request-promise-cache/blob/e81bce12c13d47562bd1f2324a65cdc12a2072cb/tests/index.js#L22-L39)



## Extras

On the returned `request` object, you can:

* `request.original` access the original request function,
* `request.defaults()` another request object function generator, which is used exactly like the original [`request.defaults`](https://github.com/request/request#requestdefaultsoptions) but this one will return a __promisified__ request with the __caching__.
* `request.cache` is the cache instance using, which is a [`nano-cache`](https://github.com/akhoury/nano-cache) instance, say you need to `request.cache.clear()`



## License

MIT
