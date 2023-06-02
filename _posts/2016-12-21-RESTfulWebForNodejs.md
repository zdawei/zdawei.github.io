---
layout: post
title:  用nodejs搭建RESTful Web
date:   2016-12-20 20:21:08
categories: jekyll nodejs
tags: nodejs
---

# 1.node代码
```javascript
var http = require('http');
var url = require('url');
var items = [];

var server = http.createServer(function(req, res) {
	switch(req.method) {
		case 'POST' :
			var item = '';
			req.setEncoding('utf8');
			req.on('data', function(chunk) {
				item += chunk;
			});
			req.on('end', function() {
				items.push(item);
				res.end('OK\n');
			});
		break;
		case 'GET' :
			var body = items.map(function(item, i) {
				return i + ')' + item;
			}).join('\n') + '\n';
			res.setHeader('Content-Length', Buffer.byteLength(body));
			res.setHeader('Content-Type', 'text/plain; charset="utf-8"');
			res.end(body);
		break;
		case 'DELETE' :
			var path = url.parse(req.url).pathname;
			var i = parseInt(path.slice(1), 10);
			if(isNaN(i)) {
				res.statusCode = 400;
				res.end('Invalid item id\n');
			} else if(!items[i]) {
				res.statusCode = 404;
				res.end('Item not found\n');
			} else {
				items.splice(i, 1);
				res.end('OK\n');
			}
		break;
		case 'PUT' :
			var item = '';
			req.setEncoding('utf8');
			req.on('data', function(chunk) {
				item += chunk;
			});
			req.on('end', function() {
				var path = url.parse(req.url).pathname;
				var i = parseInt(path.slice(1), 10);
				if(isNaN(i)) {
					res.statusCode = 400;
					res.end('Invalid item id\n');
				} else if(!items[i]) {
					res.statusCode = 404;
					res.end('Item not found\n');
				} else {
					items[i] = item;
					res.end('OK\n');
				}
			});
		break;
		default :
		break;
	}
});
server.listen('3000');
console.log('is OK ');
```

# 2.curl命令
* `curl http://localhost:3000`是通过**GET**方法获取数据
* `curl -d 'message' http://localhost:3000`是通过**POST**方法添加`message`
* `curl -X DELETE http://localhost:3000/1`是通过**DELETE**方法删除第`1`项数据
* `curl -X PUT -d  'aaa'  http://localhost:3000/1`是通过**PUT**方法更新第`1`项数据为`aaa`
