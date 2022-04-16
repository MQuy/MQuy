### HTTP Cache

Resources are implicitly allowed to be cached by default.

There are cache layers:

- Browser caches: Owned by and dedicated to a single user, implemented in their web browser.
- Local proxy: might be installed by the user, or be managed by intermediaries (their company, their organization, or their internet provider). Caching a single resource for multiple users.
- CDN: controlled by the server. Caching the same response for multiple users, using the closet servers to reduce latency.

![cache layers](https://i.imgur.com/8tvIl0g.png)

_Request headers_: `If-None-Match` and `If-Modified-Since` appear based on the browser's understanding of the current values in HTTP Cache.
_Response headers_: `Cache-Control`, `ETag` and `Last-Modifed`.

✍️ Prefer using `Cache-Control` (HTTP/1.1) over `Pragma` (HTTP/1.0) and `Expries`.

#### ETag and If-None-Match

- the browser sends a GET `https://example.test/about.html`.
- the server returns the response along with its corresponding ETag value.
  ```go
  ETag: "234nfsdao09isf"
  ```
- later, the browser wants to get `https://example.test/about.html` again, it checks locally cached version has expired (through `Cache-Control` and `Expires` headers). If not, it gets from the local cached, otherwise, the browser sends a GET `https://example.test/about.html` to the server includes its previous copy of `ETag` in the `If-None-Match` field.
  ```go
  If-None-Match: "234nfsdao09isf"
  ```
- the server may compare the browser's ETag with the current resource's `ETag`. If not changed, the server may send back a very short response with an HTTP 304 Not Modified which tells the browser to use the locally cached version. However, if the `ETag` is not matched, the server will send a full response with the new ETag and the browser may decide to replace its previous cached version with the new response along with `ETag`.

#### Vary

Let's start with the example, your server supports gzip and uses `fastly.com` as CDN/proxy. By default, fastly caches requests based on path + query string and `Host` header. Now imagine two scenarios:

- The browser A doesn't support gzip compression, sends a GET request and uncompression response ends up in fastly cache. Later, browser B supports gzip compression, sends the same request, and fastly returns uncompression response. The response is bigger than it should be but it still works.
- Browser A supports gzip compression, sends a GET request and compression response ends up in fastly cache. Later, browser B doesn't support gzip compression, sends the same request, and fastly returns compression response. Browser B doesn't know what to do other than display gibberish.

`Vary` comes to recuse, it is the HTTP response header that informs a client the way a server uses to transmit the content, so a client can cache properly. In the fastly case above, if the server returns `Vary: Accept-Encoding`, fastly will have different caches version for each encoding.

![vary response header](https://i.imgur.com/OSpo441.png)

### References

- [Caching best practices](https://jakearchibald.com/2016/caching-best-practices/)
- [Default browers' caching strategy](https://stackoverflow.com/a/31852117/1349340)
- [Best practices for using Vary header](https://www.fastly.com/blog/best-practices-using-vary-header)
- [HTTP caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
