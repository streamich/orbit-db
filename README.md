# orbit-client

## Introduction

***VERY MUCH WIP! WILL NOT WORK WHEN CLONED, orbit-server REQUIRED!***

Client library to interact with orbit-server. Implements the levelDOWN API without get(key, cb).

orbit-server uses linked lists on top of IPFS. orbit-server not *yet* released, working on it.

### TODO
- Tests for .remove(...)
- Local caching of messages
- Use HTTPS instead of HTTP (channel password are sent in plaintext atm)
- API for fetching user info
- OrbitNetwork
    + channel system (join, part, pub/sub)

## API
    connect(host, username, password)

    channel(name, password)

        .add(data: String)

        .put(key, data: String)

        .remove(hash or key)

        .iterator([options])

        .setMode(modes)

        .delete()

## Usage
```javascript
var async       = require('asyncawait/async');
var OrbitClient = require('./OrbitClient');

var host = 'localhost:3006'; // orbit-server address

async(() => {
    // Connect
    const orbit = OrbitClient.connect(host, username, password); // OrbitClient

    const channelName = 'hello-world';

    // Send a message
    const head = orbit.channel(channelName).send('hello'); // <ipfs-hash>

    // Delete a message
    orbit.channel(channelName).remove(head);

    // Iterator options
    const options  = { limit: -1 }; // fetch all messages, default is 1
    // { 
    //   gt: <hash>, 
    //   gte: <hash>,
    //   lt: <hash>,
    //   lte: <hash>,
    //   limit: 10,
    //   reverse: true
    // }

    // Get messages
    const iter  = orbit.channel(channelName).iterator(options); // Symbol.iterator
    const next  = iter.next(); // { value: <item>, done: false|true}
    // OR:
    // var all = iter.collect(); // returns all elements as an array

    // OR:
    // for(let i of iter)
    //   console.log(i.hash, i.item.Data.seq);

    // Remove element
    orbit.channel(channelName).remove(next.value.hash); // remove first element iterator returns

    // Set modes
    const password = 'hello';
    const channelModes;
    channelModes = orbit.channel(channel).setMode({ mode: "+r", params: { password: password } }); // { modes: { r: { password: 'hello' } } }
    channelModes = orbit.channel(channel, password).setMode({ mode: "+w", params: { ops: [orbit.user.id] } }); // { modes: { ... } }
    channelModes = orbit.channel(channel, password).setMode({ mode: "-r" }); // { modes: { ... } }
    channelModes = orbit.channel(channel, '').setMode({ mode: "-w" }); // { modes: {} }

    // Delete channel
    const result = orbit.channel(channelName, channelPwd).delete(); // true | false
})();
```