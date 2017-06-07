# [URL](https://nodejs.org/dist/latest-v8.x/docs/api/url.html)

## URL Strings and URL Objects 

url 模块能够将一个 url 字符串解析成 url 对象，从而获取一些信息。

url 模块提供了两个 API，之前解析都是用的 `url.parse(myURL)` 方式（[the Legacy API](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_legacy_url_api)），我们不再建议使用（但是还没被废弃，单独维护，仅仅为了后向兼容），建议使用 [The WHATWG URL API](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_the_whatwg_url_api)（Added in: v7.0.0），这样可以做到 [客户端](https://developer.mozilla.org/en-US/docs/Web/API/URL/URL) 与服务端的一致。

注意，在浏览器中 URL 是个全局变量，而在服务端，URL 必须通过 `require('url').URL` 去使用。

```javascript
const URL = require('url').URL;
const myURL =
  new URL('https://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash');
```

## The WHATWG URL API 

### Class: URL 

#### Constructor: new URL(input[, base])

- input \<string> 需要解析的 url 字符串
- base \<string> | \<URL> 如果 input 变量不是绝对路径，可以指定 base URL 

```javascript
const myURL = new URL('/foo', 'https://example.org/');
  // https://example.org/foo 
```

#### Sample 

以如下 url 为例：

```
http://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash
```

|     API      |                  Value                   |      Can set?      |                 Notice                 |
| :----------: | :--------------------------------------: | :----------------: | :------------------------------------: |
|     hash     |                  #hash                   | :heavy_check_mark: |                                        |
|     host     |            sub.host.com:8080             | :heavy_check_mark: |                                        |
|   hostname   |               sub.host.com               | :heavy_check_mark: |           和 url.host 就差个端口号            |
|     href     | http://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash | :heavy_check_mark: |         和调用 toString() 方法效果一致          |
|    origin    |         http://sub.host.com:8080         |        :x:         |                                        |
|   password   |                   pass                   | :heavy_check_mark: |                                        |
|   pathname   |                 /p/a/t/h                 | :heavy_check_mark: |                                        |
|     port     |                   8080                   | :heavy_check_mark: | 如果赋值成 default ports（比如 403），就会自动设置为空串） |
|   protocol   |                  http:                   | :heavy_check_mark: |                                        |
|    search    |              ?query=string               | :heavy_check_mark: |             set 时不需要输入 `?`             |
| searchParams | URLSearchParams { 'query' => 'string' }  |        :x:         |   read-only, 如果要设置，请用 url.search 覆盖    |
|   username   |                   user                   | :heavy_check_mark: |                                        |
|  toString()  | http://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash |                    |               同 url.href               |
|   toJSON()   | http://user:pass@sub.host.com:8080/p/a/t/h?query=string#hash |                    |               同 url.href               |

### Class: URLSearchParams

URLSearchParams 接口的功能看起来和 querystring 模块类似，不过后者适用范围更广（可以指定分隔符），而前者只为 URL query 设计，干的事更纯粹。

#### Constructor: new URLSearchParams()

实例化一个新的空的 URLSearchParams 对象 

#### Constructor: new URLSearchParams(string)

- string \<string> A query string 

前置的 `?` （如果有）会被忽略。

```javascript
const { URLSearchParams } = require('url');
let params;

params = new URLSearchParams('user=abc&query=xyz');
console.log(params.get('user'));
  // Prints 'abc'
console.log(params.toString());
  // Prints 'user=abc&query=xyz'

params = new URLSearchParams('?user=abc&query=xyz');
console.log(params.toString());
  // Prints 'user=abc&query=xyz'
```

#### Constructor: new URLSearchParams(obj)

- obj \<Object> 键值对组成的对象

和 querystring 模块不同的是，不会有相同的 key 出现，逗号分隔数组。

```javascript
const { URLSearchParams } = require('url');
const params = new URLSearchParams({
  user: 'abc',
  query: ['first', 'second']
});
console.log(params.getAll('query'));
  // Prints ['first,second']
console.log(params.toString());
  // Prints 'user=abc&query=first%2Csecond'

// use querystring
const params2 = querystring.stringify({
  user: 'abc',
  query: ['first', 'second']
});

console.log(params2); // user=abc&query=first&query=second
```

#### Constructor: new URLSearchParams(iterable)

- iterable \<Iterable> 任何 iterable object 

详见 [文档](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_constructor_new_urlsearchparams_iterable)

#### urlSearchParams.append(name, value)

- name \<string> 
- value \<string>

#### urlSearchParams.delete(name)

- name \<string> 

Remove all name-value pairs whose name is `name`

#### urlSearchParams.get(name)

- name \<string>
- Returns: \<string> or null 

Returns the value of the first name-value pair whose name is `name`. If there are no such pairs, `null` is returned.

#### urlSearchParams.getAll(name)

- name \<string>
- Returns: \<Array>

Returns the values of all name-value pairs whose name is `name`. If there are no such pairs, an empty array is returned.

#### urlSearchParams.set(name, value)

- name \<string>
- value \<string> 

如果有名为 name 的 key，则改变其 value 值，然后删除余下的（如果还有）名为 name 的键值对；如果没有，则添加。

```javascript
const { URLSearchParams } = require('url');

const params = new URLSearchParams();
params.append('foo', 'bar');
params.append('foo', 'baz');
params.append('abc', 'def');
console.log(params.toString());
  // Prints foo=bar&foo=baz&abc=def

params.set('foo', 'def');
params.set('xyz', 'opq');
console.log(params.toString());
  // Prints foo=def&abc=def&xyz=opq
```

#### urlSearchParams.has(name) 

- name \<string>
- Returns: \<boolean>

#### urlSearchParams.keys()

- Returns: \<Iterator>

```javascript
const { URLSearchParams } = require('url');
const params = new URLSearchParams('foo=bar&foo=baz');
for (const name of params.keys()) {
  console.log(name);
}
  // Prints:
  // foo
  // foo 
```

#### urlSearchParams.values()

- Returns: \<Iterator>

#### urlSearchParams.entries()

- Returns: \<Iterator>

#### urlSearchParams.forEach(fn[, thisArg])

- fn \<Function> 迭代每个 name-value 的方法
- thisArg \<Object> 指定 fn 方法里的 this 对象 

```javascript
const URL = require('url').URL;
const myURL = new URL('https://example.org/?a=b&c=d');
myURL.searchParams.forEach((value, name, searchParams) => {
  console.log(name, value, myURL.searchParams === searchParams);
});
  // Prints:
  // a b true
  // c d true
```

#### urlSearchParams.toString() 

- Returns: \<string> 

返回序列化的 query string

#### urlSearchParams.sort()

根据 name 进行排序


### url.format(URL[, options])

new URL(myURL) 的逆操作，详见 [文档](https://nodejs.org/dist/latest-v8.x/docs/api/url.html#url_url_format_url_options)