# fib-rpc

[![NPM version](https://img.shields.io/npm/v/fib-rpc.svg)](https://www.npmjs.org/package/fib-rpc)
[![Build Status](https://travis-ci.org/fibjs/fib-rpc.svg)](https://travis-ci.org/fibjs/fib-rpc)
[![Build status](https://ci.appveyor.com/api/projects/status/kqvsr2po0j0enlu1?svg=true)](https://ci.appveyor.com/project/richardo2016/fib-rpc)

Remote Procedure Calling for fibjs

## Introduction

`fib-rpc` is working on dispatching of remote procedure calling, it supports both http request and websocket(**recommended**).

obviously, it follows [JSON-RPC] protocol.

## Get Started

```
npm i -S fib-rpc
```

## Usage

you can use it with `http.Request`
```javascript
var js_remote = rpc.handler({
    foo: function (p1, p2) {
        return p1 + ',' + p2;
    }
});

function js_call(r) {
    var m = new http.Request();

    m.value = 'test/tttt/tttt/';
    m.json(r);

    js_remote(m);

    m.response.body.rewind();
    return m.response.readAll().toString();
}

var result = js_call({
    // method is required
    method: 'foo',
    // params is required and must be array
    params: [100, 200],
    // id is required
    id: 1234
})

assert.equal(result, '{"id":1234,"result":"100,200"}');
```

but in most cases, you may prefer using it with `ws.Socket`.
```javascript
const ws = require('ws')
const http = require('http')

const rpc = require('fib-rpc')

const svr = new http.Server(8811, ws.upgrade(
    rpc.handler(
        {
            test: function (v1, v2) {
                return v1 + v2;
            }
        }
    ))
);
svr.asyncRun();

const remoting = rpc.connect("ws://127.0.0.1:8811");

remoting.test(1, 2) // 3
```

Learn `connect` from test case `'websocket rpc'` in [exmaple](./examples/connect.js).

## Samples

View [Samples](./examples), test them like this:
```bash
# build lib first.
npm i && npm run build

# chdir to examples
cd exapmles
npm i

# run example
fibjs ./connect.js
fibjs ./open_handler-js.js
```

[test.js]:test.js#L123:1

[JSON-RPC]:https://www.jsonrpc.org/specification#overview