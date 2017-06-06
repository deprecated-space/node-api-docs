# [Path](https://nodejs.org/dist/latest-v8.x/docs/api/path.html)

## path.basename(path[, ext])

- path \<string>
- ext \<string> 文件扩展名
- Returns: \<string>

返回路径的最后一部分

```javascript
console.log(path.basename('a/b/c/d')) // d
console.log(path.basename('a/b/c/d/')) // d
console.log(path.basename('a/b/index.htm')) // index.htm
console.log(path.basename('a/index.htm', '.htm')) // index 
```

## path.dirname(path)

- path \<string>
- Returns: \<string>

返回路径所在的目录（感觉是 path.basename() 的补集）

```javascript
console.log(path.dirname('a/b/c')) // a/b
console.log(path.dirname('/a/b/c/')) // /a/b
console.log(path.dirname('a/b/c/d.htm')) // a/b/c
console.log(path.dirname('a/b/c/d.htm/')) // a/b/c 
```

## path.extname(path)

- path \<string>
- Returns: \<string>

返回该路径所指向文件的扩展名，如果没有，则返回空串

```javascript
console.log(path.extname('/a/b.htm')) // .htm
console.log(path.extname('a/b/c.js.map')) // .map
console.log(path.extname('index.')) // .
console.log(path.extname('index')) // ''
console.log(path.extname('a/.index')) // ''
```

## path.join([...paths])

- ...paths \<string> 
- Returns: \<string>

将多个路径组合成一个路径

```javascript
console.log(path.join('a', '/b', 'c.js')) // a/b/c.js
console.log(path.join('a', '/b', '..')) // a 
```

## path.parse(path)

- path \<string>
- Returns: \<Object>

解析路径

```javascript
console.log(path.parse('/a/b/c.js'))
// { root: '/', dir: '/a/b', base: 'c.js', ext: '.js', name: 'c' }
```

## path.format(pathObject)

path.parse() 的相反操作，详见文档

## path.normalize(path)

- path \<string>
- Returns: \<string> 

将路径规范化，解析路径中的 `..` 以及 `.`

```javascript
console.log(path.normalize('a/b/c/.././d.js')) // a/b/d.js  
```

## path.relative(from, to)

- from \<string>
- to \<string>
- Returns: \<string>

求解两个路径之间的相对路径，即路径 `from` 经过何种操作可以到达路径 `to`

```javascript
console.log(path.relative('/a', '/b')) // ../b 
```

## path.resolve([...paths])

- ...paths \<string>
- Returns: \<string>

相当于从左到右一直 cd，并返回一个 **绝对路径**。注意与 path.join() 的区别

```javascript
console.log(path.resolve('a', '/b')) ; // /b 
```

## path.isAbsolute(path)

- path \<string>
- Returns: \<boolean>

判断是否是绝对路径

```javascript
console.log(path.isAbsolute('a/b/c/')) // false
console.log(path.isAbsolute('/a/b/c')) // true
console.log(path.isAbsolute('.')) // false
console.log(path.isAbsolute('./a/b/c.js')) // false
console.log(path.isAbsolute('../a/b/c.js')) // false
```

## path.delimiter 

路径分隔符

- Win 中返回 `;`
- POSIX 中返回 `:`

```javascript
console.log(path.delimiter) // :

console.log(process.env.PATH.split(path.delimiter))
// [ '/Users/fish/.nvm/versions/node/v7.9.0/bin',
//   '/usr/local/bin',
//   '/usr/bin',
//   '/bin',
//   '/usr/sbin',
//   '/sbin',
//   '/usr/local/mysql/bin',
//   '/usr/local/Cellar/nginx/1.12.0/bin' ]
```

## path.sep 

- `\` on Win
- `/` on POSIX  

系统分隔符