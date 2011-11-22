# EARLY ACCESS - Socket.IO on PubNub

Now you can use Socket.IO with PubNub! Take advangate of the Socket.IO API 
leveraging the Human Perceptive Real-time PubNub Cloud Infrastructure.
We believe Socket.IO is the jQuery of Networking.
Socket.IO is a project that makes WebSockets and realtime possible in
all browsers. It also enhances WebSockets by providing built-in multiplexing,
automatic scalability, automatic JSON encoding/decoding, and
even more with PubNub.

![Socket.IO on Pubnub](http://pubnub.s3.amazonaws.com/assets/socket.io-on-pubnub-2.png "Socket.IO on Pubnub")

## Enhanced Socket.IO with PubNub

We enhanced Socket.IO with PubNub.
Socket.IO with PubNub does not require a Node.JS backend.
This means your code is more
simple and you have extra time to build your
app rather than fiddling with the back-end server code.
Additionally the JS lib payload has been improved for Mobile apps.

## Simplified Socket.IO API Usage

By default, broadcasting is turned on.  This means when you use
emit() or send() functions, broadcasting to all connections occurs 
except to the connection where the message came from.

## Simplifed Features with Socket.IO on PubNub

+ Enhanced User Tracking Presence Events (join, leave).
+ Counts of Active User Connections.
+ Socket Connection Events (connect, disconnect, reconnect).
+ Multiplexing many channels on one socket.
+ Smart Broadcasting (broadcast with auto-recovery on failure).
+ Disconnect from a Channel.
+ Acknowledgements of Message Receipt.
+ Stanford Crypto Library with AES Encryption.
+ Geo Data with Latitude/Longitude. [comming soon]
+ Batching of Publishes (Send multiple messages at once). [comming soon]
+ Private Messaging. [comming soon]
+ Server Side Events. [comming soon]

## How to use

First, include `pubnub.js` and `socket.io.js`:

```html
<script src=http://cdn.pubnub.com/pubnub-3.1.min.js></script>
<script src="socket.io.js"></script>
<script>
  var socket = io.connect('http://pubsub.pubnub.com');
  socket.on( 'news', function (data) {
    console.log(data);
    socket.emit( 'my-other-event', { my: 'data' } );
  } );
</script>
```

## Short recipes

### Sending and receiving events.

Socket.IO allows you to emit and receive custom events.
Reserved Events are: `connect`, `message`, `disconnect`,
`reconnect`, `join` and `leave`.

```js
// Use PubNub Setup for Your PubNub Account
var pubnub_setup = {
    channel       : 'my_mobile_app',
    publish_key   : 'demo',
    subscribe_key : 'demo',
    ssl           : false
};

var socket = io.connect( 'http://pubsub.pubnub.com', pubnub_setup );

socket.on( 'connect', function() {
    console.log('Connection Established! Ready to send/receive data!');
    socket.send('sock');
} );

socket.on( 'message', function(message) {
    console.log(message);
} );

socket.on( 'disconnect', function() {
    console.log('my connection dropped');
} );

// Extra event in Socket.IO provided by PubNub
socket.on( 'reconnect', function() {
    console.log('my connection has been restored!');
} );

```

### User Presence (Room Events: join, leave)

Sometimes you want to put certain sockets in the same room, so that it's easy
to broadcast to all of them together.

Think of this as built-in channels for sockets. Sockets `join` and `leave`
rooms in each channel.

```js
var chat = io.connect( 'http://pubsub.pubnub.com/chat', pubnub_setup );
chat.on( 'leave', function(user) {
    console.log('user left');
} );
chat.on( 'join', function(user) {
    console.log('user joined');
} );
```

### Enhanced Presence with User Counts.

Often you will want to know how many users are connected to a channel (room).
To get this information you simply access the user_count function.

```js
var chat = io.connect( 'http://pubsub.pubnub.com/chat', pubnub_setup );
chat.on( 'leave', function(user) {
    console.log(
        'User left. There are %d users left.',
        chat.get_user_count()
    );
} );
chat.on( 'join', function(user) {
    console.log(
        'User joined! There are %d users.',
        chat.get_user_count()
    );
} );
```

### Stanford Encryption AES

To keep super secret messages private, you can use the password feature
of Socket.IO on PubNub.  You will be able to encrypte and decrypt 
automatically client side.  This means all data transmitted is encrypted
and unreadable to everyone without the correct password.

It is simply to have data encrypted and automatically decrypt on receipt.
Simply add the `password` entry in the `pubnub_setup` object.

```html
<script src=http://cdn.pubnub.com/pubnub-3.1.min.js></script>
<script src=crypto.js></script>
<script src=socket.io.js></script>
<script>
    // Include a Password in the PubNub Setup Object.
    var pubnub_setup = {
        channel       : 'my_mobile_app',
        publish_key   : 'demo',
        subscribe_key : 'demo',
        password      : 'MY-PASSWORD',  // Encrypt with Password
        ssl           : false
    };

    // Setup Encrypted Channel
    var encrypted = io.connect(
        'http://pubsub.pubnub.com/secret',
        pubnub_setup
    );

    // Listen for Connection Ready
    encrypted.on( 'connect', function() {
        // Send an Encrypted Messsage
        encrypted.send({ my_encrypted_data : 'Super Secret!' });
    } );

    // Receive Encrypted Messages
    encrypted.on( 'message', function(message) {
        // Print Unencrypted Data
        console.log(message.my_encrypted_data);
    } );
</script>
```

This feature will automatically encrypte and decrypt messages
using the Stanford JavaScript Crypto Library with AES.
You can mix encrypted and unencrypted channels with the
channel multiplexing feature by excluding a password from the
`pubnub_setup` object.

NOTE: If a password doesn't match, then the message will not be received.
Make sure authorized users have the correct password!

### Using it just as a cross-browser WebSocket

If you just want the WebSocket semantics, you can do that too.
Simply leverage `send` and listen on the `message` event:

```html
<script>
  var socket = io.connect('http://pubsub.pubnub.com/');
  socket.on('connect', function () {
    socket.send('hi');

    socket.on('message', function (msg) {
      // my msg
    });
  });
</script>
```

### Restricting yourself to a namespace

If you have control over all the messages and events emitted for a particular
application, using the default `/` namespace works.

If you want to leverage 3rd-party code, or produce code to share with others,
socket.io provides a way of namespacing a `socket`.

This has the benefit of `multiplexing` a single connection. Instead of
socket.io using two `WebSocket` connections, it'll use one.

The following example defines a socket that listens on '/chat' and one for
'/news':

```html
<script>
  var chat = io.connect('http://pubsub.pubnub.com/chat')
    , news = io.connect('http://pubsub.pubnub.com/news');

  chat.on('connect', function () {
    chat.emit('hi!');
  });

  news.on('news', function () {
    news.emit('woot');
  });
</script>
```

### Getting Acknowledgements (Receipt Confirmation)

Sometimes, you might want to get a callback when the message was delivered to
confirmed the message reception.

```js
  var socket = io.connect(); // TIP: auto-discovery
  socket.on( 'connect', function () {
    socket.emit( 'important-message', {data:true}, function (receipt) {
      // Message Delivered!
      console.log(data);
    });
  });
```

## License 

(The MIT License)

Copyright (c) 2011 PubNub Inc.

Copyright (c) 2011 Guillermo Rauch <guillermo@learnboost.com>

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.