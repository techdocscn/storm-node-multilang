# storm-node-multilang

一个基于 **node.js** 的 [Storm][0] 多语言支持实现

## 样本

### 产生 Spout

```javascript
	
	var Spout = require('storm-node-multilang').Spout;

	//The Spout constructor takes a definition function,
	//an input stream and an output stream
    var sentenceEmitter = new Spout(function(events) {
		var collector = null;
		var seqId = 1;

		//You can listen to the spout "open", "ack" and "fail" events to
		events.on('open', function(c) {
			collector = c;
		});

		events.on('ack', function(messageId) {
			console.log(messageId + ' acked');
		});

		events.on('fail', function(messageId) {
			console.log(messageId + ' failed');
		});

		//The definition function must return a function used as
		//the nextTuple function.
		return function(cb) {
			
			collector.emit(["hello"], seqId++);
			cb();

		}

	}, process.stdin, process.stdout);

	
	process.stdin.setEncoding('utf8');
	process.stdin.resume();
    
``` 


### 产生 Bolt

```javascript
	
	var Bolt = require('storm-node-multilang').Bolt;

	//The Bolt constructor takes a definition function,
	//an input stream and an output stream
    var joinBolt = new Bolt(function(events) {
		var collector = null;

		//You can listen to the bolt "prepare" event to
		//fetch a reference to the OutputCollector instance
		events.on('prepare', function(c) {
			collector = c;
		});

		//The definition function must return a function used as
		//the execute function.
		return function(tuple, cb) {
		
			collector.emit(tuple.values.join(', '));
			collector.ack(tuple);
			cb();
		}

	}, process.stdin, process.stdout);

	process.stdin.setEncoding('utf8');
	process.stdin.resume();
    
``` 

## 如何用 Storm 测试我的 node.js spouts 和 bolts ?

为了测试你的 node.js spouts 和 bolts，你需要建立一个storm 集群。如果你不想运行 VM 和一个
Zookeeper 集群的话会很麻烦。

Storm 提供了一个 'LocalCluster' 类用来在本地测试 topology。

可能测试你的 topologies 的最简单办法是使用我们的 [storm-local-multilang][1] 项目

## 安装

    $ npm install storm-node-multilang

## 运行测试

首先在 repo 下面运行以下命令，安装依赖库文件：

    $ npm install

然后运行测试（需要 `mocha`）：

    $ npm test

## 版权

Copyright (c) 2012 Michaël Schwartz, EPOKMEDIA <info@epokmedia.fr>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is furnished
to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.



[0]: https://github.com/nathanmarz/storm
[1]: https://github.com/epokmedia/storm-local-multilang