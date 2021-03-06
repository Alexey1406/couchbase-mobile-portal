---
permalink: guides/couchbase-lite/native-api/peer-to-peer/index.html
---

Two instances of Couchbase Lite can directly sync with each other without a server (peer-to-peer). At least one of them must use the Couchbase Lite Listener library, which enables any Couchbase Lite database to become the remote in a replication by listening on a TCP port and by exposing the standard replication endpoints on that port.

![](../img/docs-listener-diagram.png)

It becomes an alternate entry-point into the data store. Another peer can therefore use the URL and port number in the replicator to sync data to and from the database currently listening.

Some typical Listener use cases include:

- Synchronizing with selected peer devices on the same LAN network.
- Offline/online: use peer-to-peer in conjunction with Sync Gateway.

### Installing the Listener library

Refer to the [installation guide](./../../../../installation/index.html) of the platform of your choice to install the 
Listener component. The Couchbase Lite Listener is coupled to Couchbase Lite. Both frameworks should always have the same release version.

### Configuring

To begin using the Listener you must create an instance by specifying a manager instance and port number. 

<div class="tabs"></div>

```objective-c+
CBLManager* manager = [CBLManager sharedInstance];
self.listener = [[CBLListener alloc] initWithManager:manager port:55000];
self.listener.passwords = @{@"hello": @"pw123"};
[listener start:nil];
```

```swift+
let manager = CBLManager.sharedInstance()
self.listener = CBLListener(manager: manager, port: 55000)
self.listener.passwords = ["hello": "pw123"]
self.listener.start(nil)
```

```java+android+
Manager manager = new Manager((Context) getApplicationContext(), Manager.DEFAULT_OPTIONS);
Credentials credentials = new Credentials("hello", "pw123");
listener = new LiteListener(manager, 55000, credentials);
Thread thread = new Thread(listener);
thread.start();
```

```c+
Manager manager = Manager.SharedInstance;
listener = new CouchbaseLiteTcpListener (manager, 55000, CouchbaseLiteTcpOptions.AllowBasicAuth);
listener.SetPasswords(new Dictionary<string, string>() { { "hello", "pw123" } });
listener.Start ();
```

Make sure to keep a reference to the listener instance in your application otherwise it may be garbage collected and unexpectedly stop listening for 	incoming HTTP requests. In the example above, the listener instance is initialized as an instance property of the class.

Basic authentication is the recommended approach for protecting database access on the LAN. The listening peer can optionally be initialized with a list of username/password pairs. The peer that intends to run the replication must provide the same username/password `http://username:password@hostname:port/dbname`.

## Discovery

Once you have set up the Listener as an endpoint for other peers to replicate to or from, you can use different discovery methods to browse for peers and subscribe to those of interest.

This section covers two ways to discover peers:

- Using a QR code to encode the peer's remote URL.
- DNS Service Discovery (DNS-SD, aka Bonjour).

### QR code

#### PhotoDrop

[PhotoDrop](https://github.com/couchbaselabs/photo-drop) is a P2P sharing app similar to the iOS AirDrop feature that you can use to send photos across devices. The source code is available for iOS and Android. The QR code is used for advertising an adhoc endpoint URL that a sender can scan and send photos to.

### DNS-SD

DNS Service Discovery is a [standard](https://www.ietf.org/rfc/rfc6763.txt) for discovering services based on a service type. It's usually coupled with [Multicast DNS](https://tools.ietf.org/html/rfc6762), which allows devices to broadcast their existence and services on a LAN without requiring a DNS server. These technologies are usually referred to as Bonjour, which is Apple's name for its implementation, but they're available under other names on most operating systems. Android calls them Network Service Discovery.

The first step to using Bonjour for peer discovery is to advertise a service with the following properties:

- **Type:** Bonjour can be used by many other types of devices on the LAN (printers, scanners, other apps etc). The service type is a way to interact only with peers whose service type is the same.
- **Name:** A string to serve as identifier for other peers. It should be unique for each peer. It does not need to be human-readable.
- **Port:** The port number the Listener is running on.
- **Metadata:** Optional data that will be sent in the advertizment packets (the size limit is around 1.5KB).

To browse for peers on the network, each implementation has an asynchronous API to get notified as peers go online and offline from the network. Given this method of device discovery is platform specific, we recommend to follow the guides below. Once a peer device is discovered and the hostname is resolved, you can start a push and/or pull replication in the same way you would with Sync Gateway.

#### Resources

Useful resources to work with mDNS include:

- **Bonjour for iOS and Mac applications:** The Couchbase Lite SDK exposes part of the Bonjour API for an easier integration. The official documentation for iOS and Mac applications can be found in the [NSNetService Programming Guide](https://developer.apple.com/library/mac/documentation/Networking/Conceptual/NSNetServiceProgGuide/Introduction.html).
- **NSD for Android applications:** The de facto framework for Android is called Network Service Discovery (NSD) and is compatible with Bonjour since Android 4.1. The official guide can be found in the [Android NSD guide](https://developer.android.com/training/connect-devices-wirelessly/nsd.html).
- **JmDNS:** Implementation in Java that can be used in Android and Java applications ([official repository](https://github.com/jmdns/jmdns)).

#### Bonjour browsers

Bonjour browsers are useful to monitor devices broadcasting a particular service on the LAN ([OS X Bonjour browser](http://www.macupdate.com/app/mac/13388/bonjour-browser), [iOS app](https://itunes.apple.com/gb/app/discovery-bonjour-browser/id305441017), [Windows browser](http://hobbyistsoftware.com/bonjourbrowser))

## Connecting

Once the IP address of another device is known you can start replicating data to or from that peer. However, there are some good practice guidelines to follow in order to replicate the changes as they are persisted to a particular node.

### Filter functions

It may be desirable to use [filter functions](../replication/index.html#filtered-replications) to replicate only the documents
 of interest to another peer. Filter functions in a peer-to-peer context are executed when the start method on the replication object is called. This is a major difference with the Sync Function available on Sync Gateway that builds the access rules when documents are saved to the Sync Gateway database.

### Port allocation

If the port number passed to the Listener is hardcoded, there is a small chance that another application may already be using it. To avoid this scenario, specifying a value of 0 for the port in the Listener constructor will let the TCP stack pick a random available port.

### Remote UUID

The replication algorithm keeps track of what was last synchronized with a particular remote database. To identify a remote, it stores a hash of the remote URL http://hostname:port/dbname and other properties such as filters, filter params etc. In the context of peer-to-peer, the IP address will frequently change which will result in a replication starting from scratch and sending over every single document although they may have already been replicated in the past. You can override the method of identifying a remote database using the remoteUUID property of the replicator. If specified, it will be used in place of the remote URL for calculating the remote checkpoint in the replication process.

## Security

In addition to using basic authentication, it is also possible to enable SSL over peer-to-peer connections.

### SSL for Peer-to-peer

<div class="tabs"></div>

```objective-c+
if (![listener setAnonymousSSLIdentityWithLabel: @"MyApp SSL" error: &error])
    // handle error
```

```swift+
if !listener.setAnonymousSSLIdentityWithLabel("MyApp SSL", error: error) {
   // handle error }
```

```java+android+
No code example is currently available.
```

```c+
var path = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), "unit_test.pfx");
var cert = X509Manager.GetPersistentCertificate("127.0.0.1", "123abc", path);
CouchbaseLiteTcpListener listener = new CouchbaseLiteTcpListener(manager, 0, CouchbaseLiteTcpOptions.UseTLS, cert);
```

The Listener is now serving SSL using an automatically generated identity.

#### Wait, Is This Secure?

Yes and no. It encrypts the connection, which is unquestionably much better than not using SSL. But unlike the usual SSL-in-a-browser approach you're used to, it doesn't identify the server/listener to the client. The client has to take the cert on faith the first time it connects.
