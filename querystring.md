# [Query String](https://nodejs.org/dist/latest-v8.x/docs/api/querystring.html)

## querystring.stringify(obj[, sep[, eq[, options]]])

- obj \<Object> 需要序列化的对象
- sep \<string> 默认 `&`，可覆盖
- eq \<string> 默认 `=`，可覆盖
- options 
  - encodeURIComponent \<Function> 默认用 `querystring.escape()` 进行编码

```javascript
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' });
// returns 'foo=bar&baz=qux&baz=quux&corge='

querystring.stringify({ foo: 'bar', baz: 'qux' }, ';', ':');
// returns 'foo:bar;baz:qux'
```

## querystring.parse(str[, sep[, eq[, options]]])

- str \<string> 需要解析的 URL query string
- sep \<string> 默认 `&`，可覆盖
- eq \<string> 默认 `=`，可覆盖
- options 
  - decodeURIComponent \<Function> 默认用 `querystring.unescape()` 进行解码
  - maxKeys \<number> 设定最大数量用来解析的 keys，默认为 1000，将其设置为 0 可以解除限制

## querystring.escape(str) & querystring.unescape(str)

querystring 模块默认的编码解码方式（UTF-8 编码解码），必要时可以被覆盖（通过 encodeURIComponent 和 decodeURIComponent 参数）