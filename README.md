# hypercore-swarm-stream

Create a readable and/or writable stream of a hypercore feed on a [hyperdrive-archive-swarm](https://github.com/karissa/hyperdrive-archive-swarm).

```
npm install hypercore-swarm-stream
```

## Usage 

```js
var swarmStream = require('./')
var signatures = require('sodium-signatures')

var keys = signatures.keyPair()
var publisher = swarmStream(keys.secretKey)
var consumer = swarmStream(keys.publicKey)
publisher.write('hello')
consumer.once('data', function (block) {
  console.log(block) // hello
})
```

## API

#### `var stream = swarmStream([key], [options])`

Same as [hypercore-create-stream](https://github.com/lukeburns/hypercore-create-stream) with an additional `exit` option, which closes the swarm when the stream ends.

`key` is either a public or private key. If it is a public key, then the stream will be readable only. If it is a private key, then the stream will be both readable and writable. If it is undefined, then a new feed is created with public and private keys `stream.key` and `stream.secretKey`.

All `options` are optional.

```js
{
  db: leveldb instance,
  static: boolean,
  storage: object,
  tail: boolean,
  start: integer,
  end: integer,
  exit: boolean
}
```

#### `stream.swarm`

The hyperdrive swarm instance.

#### `stream.close(callback)`

Leave swarm.

#### `stream.on('close')`

Event emitted upon closing the swarm.

#### `stream.on('connection', connection, info)`

Emitted when you connect to another peer. Info is an object that contains info about the connection

``` js
{
  type: 'tcp', // the type, tcp or utp
  initiator: true, // wheather we initiated the connection or someone else did
  channel: Buffer('...'), // the channel this connetion was initiated on. only set if initiator === true
  host: '127.0.0.1', // the remote address of the peer.
  port: 8080, // the remote port of the peer.
  id: Buffer('...') // the remote peer's peer-id.
}
```