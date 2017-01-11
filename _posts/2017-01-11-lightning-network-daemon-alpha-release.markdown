---
layout: post
title:  "Alpha Release of the Lightning Network Daemon"
date:   2017-01-10 08:00:00 -0800
categories: release software lnd lightning
---

![Volcanic Lightning](https://upload.wikimedia.org/wikipedia/commons/f/f2/Rinjani_1994.jpg)

### LND - version 0.1-alpha

We're very excited to announce the release of v0.1-alpha of the 
Lightning Network Daemon (`lnd`)! This release marks a major milestone for the
Lightning Network as well as Bitcoin as a whole. With this release, `lnd` stands
as the most feature-complete implementation of Lightning yet and is now ready
for public testing by developers!

The signed release itself can be found [in our Github
repo](https://github.com/lightningnetwork/lnd/releases/tag/v0.1-alpha). 

The daemon is a full implementation of Lightning, capable of: opening channels
with peers, closing channels, completely handling all cooperative and
non-cooperative channel states, maintaining a fully authenticated and validated
channel graph, performing pathfinding within the network, passively forwarding
incoming payments, and sending outgoing onion-encrypted payments through the
network.

In this post, we'll take a brief tour of some of `lnd`'s high-level
architecture, the exposed developer APIs, `lnd`'s usage of the upcoming segwit
upgrade to Bitcoin, and finally `lnd`'s network capabilities.

### Golang

{:refdef: style="text-align: center;"}
![btcsuite](https://blog.conformal.com/wp-content/uploads/2014/03/hacking-bitcoin-with-go.png)
{: refdef}

The entirety of the `lnd` codebase has been built from scratch using
[Go](https://golang.org/), a new systems programming language that makes it
easy to build simple, reliable, and efficient software. We chose Go due to its
first-class language-level support for concurrency, expansive standard library,
memory safety, and simple language design. The last point holds significance as
we've developed the codebase for `lnd` with _readability_ in mind above all. 

Our goal is to make it as easy as possible to dive into the code either just to
learn about the Lightning Network protocol at a deeper level, or to start
contributing pull requests to the project. The codebase has extensive internal
documentation in the form of [literate
programming](https://en.wikipedia.org/wiki/Literate_programming)-inspired
comment blocks and an established testing infrastructure as we optimize for
developer-time rather than machine-time.

Any major Bitcoin codebase requires a solid, easy to use, and well-tested set
of underlying libraries to interact with the network to perform routine tasks
such as transaction generation and signing, script generation, watching the chain
for relevant transactions, etc. Our Bitcoin library of choice is
[btcsuite](https://github.com/btcsuite/), the very set of libraries that powers
[`btcd`](https://github.com/btcsuite/btcd), the full-node implementation which
is also written in pure-golang. 

With this initial release, `lnd` is dependent on `btcd` as a direct source to
the blockchain in order to fully manage channels and respond to relevant
on-chain events. We have a new Lightning compatible lite-client node for
Bitcoin in the works that will make the dependency optional in a future
release. In addition to utilizing the set of `btcsuite` libraries, several of our
developers such as [roasbeef](https://github.com/roasbeef) also contribute back
to the project as it's a critical piece of Bitcoin infrastructure.

### Developer APIs

{:refdef: style="text-align: center;"}
![grpc](https://i.redditmedia.com/ygt1m4hl-Jo2afoaZFrnQpivAG1zvebCfsKcE4LfQJ8.jpg?w=320&s=a6f0ebcccc72988061aa015d0a8978d6)
{: refdef}

`lnd` exposes a high-performance, efficient, and flexible RPC layer which is
powered by [gRPC](http://www.grpc.io/): a cross-platform open-source RPC
framework based on [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2) and
[protobufs](https://developers.google.com/protocol-buffers/). gRPC is able to
generate language-specific client-side libraries that allow for application
developers to seamlessly drive `lnd` from the comfort of their programming
lingua franca. Using this set of client libraries, interacting with `lnd` is as
easy as manipulating objects in their chosen language. At the time of this
release, the supported languages are: C++, Java, Python, Go, Ruby, G#, Node.js,
Objective-C and PHP.

In addition to providing a native-language client accessibility layer, `lnd`
also exposes an HTTP-REST interface that has the same capabilities as the gRPC
interface minus the bi-directional streaming RPC calls that gRPC provides.



### Lightning Channels -- Segwit Inside

![segwit](http://www.segwit.co/static/public/images/logo.png)

Individual payment channels composed as a series of interconnected links are
what make the Lightning Network possible. `lnd`'s channel design currently
utilizes the upcoming Bitcoin upgrade: segwit. Although alternative less-ideal channel
constructions are possible without segwit, by utilizing segwit we're able to
deploy the most efficient, flexible, and safe channel design. Additionally, the
script upgrade capabilities created by segwit provide a path for upgrades to
Bitcoin that will improve the privacy and efficiency of Lightning.  Examples of
such improvements include Elliptic-Curve Schnorr Signatures and unlocking new
op-codes that allow for more private end-to-end payments over Lightning by
randomizing the payment hash along the route.

Three very important features of Lightning enabled by segwit include:
[Unlinkable Outsourced Channel
Monitoring](https://scalingbitcoin.org/milan2016/presentations/D1%20-%208%20-%20Tadge%20Dryja.pdf),
infinite channel life time, and the two-stage HTLC scheme proposed by Mats
Jerratsch (later modified by Tadge Dryja). Without segwit, fully-automatic
channel outsourcing that includes the broadcast of a transaction that claims
_all_ funds in a contested channel by the outsourcer isn't possible. This is
because without a malleability fix, the transaction ID of the commitment
transaction can't be known ahead of time.  Similarly, the two-stage HTLC scheme
greatly improves the usability of Lightning by allowing a long revocation delay
with a short payment delay isn't safe to deploy without segwit.

`lnd`'s internal wallet is fully segwit-aware. The wallet is capable of [segwit
nested
p2sh](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki#p2wpkh-nested-in-bip16-p2sh)
addresses, which look like regular p2sh addresses, and also native segwit
addresses, which are more efficient and provide a greater degree of
scalability.  `lnd` implements [BIP
142](https://github.com/bitcoin/bips/blob/master/bip-0142.mediawiki), which
defines an address format for native segwit addresses. These addresses can be
used to send money between instances of `lnd` or software that supports the BIP
such as [bcoin](https://github.com/bcoin-org/bcoin). On testnet, a native BIP
142 p2wpkh address looks like:

    QWyzAWWPYUgdc6j6HxQpgvqKtyBo4xYjBkwr

### Onion-Routed Micropayments

The Lightning Network enables instant, end-to-end secure micropayments that
leverage a technique called the HTLC, or Hash-Time-Locked-Contract. Within
`lnd`, _all_ outgoing payments are dispatched using a [modified version of
Sphinx](https://github.com/lightningnetwork/lightning-rfc/blob/master/04-onion-routing.md),
which allows us to send payments across the network that are encrypted
end-to-end. There is no support for sending payments with plaintext routes,
meaning **privacy is the default**, as it should be.

The payment flow on Lighting is very similar to the flow on traditional
financial infrastructure such as PayPal or Stripe: the payee creates an
Invoice and sends that to the payer, who then fulfills the payment by sending a
payment through the network. `lnd` provides a [_preliminary_ payment request
encoding](https://github.com/lightningnetwork/lnd/tree/master/zpay32) scheme
that allows users to easily communicate payment conditions out-of-band. A
fully encoded payment request includes: the destination's public key, the
payment hash, and the value of the payment. A sample encoded payment request
looks like: 

    ycyr8brdjic6oak3bemztc5nupo56y3itq4z5q4qxwb35orf7fmj5phw8bx148zzipg3rh6t1btadpnxf7z1mnfd76hsw1eaoca3ot4uyyyyyyyyydbibt79jo1o
   
### Peer-to-Peer Network

All communications between `lnd` nodes are end-to-end encrypted by default using a
[modern cryptographic messaging
transport](https://github.com/lightningnetwork/lightning-rfc/blob/master/08-transport.md)
derived from the [Noise Protocol
framework](http://noiseprotocol.org/noise.html). As a result, no information,
not even the initial version handshake, is transmitted in plain-text amongst
`lnd` nodes. Once nodes connect, they begin to exchange _authenticated_ node
and channel data which are periodically gossiped throughout the network. All
advertised channels carry a channel authentication proof which proves that the
channel is anchored in an unspent output, and also binds the multi-sig keys
embedded within the Bitcoin blockchain to the public key identities of the
peers in the Lightning Network layer.

Once a node has a complete view of the channel graph, it's able to passively
forward incoming HTLCs according to the forwarding instructions embedded within
the onion-encrypted routing information. Path finding for outgoing payments
uses a set of heuristics to select a path with sufficient payment flow capacity
to carry the payment and which minimizes the required cumulative time-lock and
then required fees in that order.

The specification for the authenticated gossip network implemented within `lnd`
can be [found
here](https://github.com/lightningnetwork/lightning-rfc/blob/master/07-routing-gossip.md)
within the [Lighting Network
specifications](https://github.com/lightningnetwork/lightning-rfc).

### Lightning Network Specifications

The [set of documents defining the Lighting
Network](https://github.com/lightningnetwork/lightning-rfc) are called BOLTs:
Basis of Lightning Technologies. `lnd` doesn't yet fully implement all of the
behavior outlined within the specifications. The latest state of our
implementation of the BOLT’s can be tracked within our
[README](https://github.com/lightningnetwork/lnd/#lightning-network-specification-compliance).
In an upcoming release we aim to achieve full BOLT compliance as a preliminary
step, with interoperability with the other Lightning implementations coming at
a later date.

### Contributors

This release of `lnd` is the combined work of developers from around the world
and includes contributors from Lightning Labs, Bitfury, Blockchain Lab,
Chaincode Labs, and MIT DCI. The full list of contributors (in alphabetical
order by first name) is:

  * Aleksei Ostrovskiy
  * Alex Akselrod
  * Andrew Samokhvalov (andrew.shvv)
  * Bryan Bishop (kanzure)
  * Bryan Vu
  * Christopher Jämthagen
  * Evgeniy Scherbina 
  * John Newbery
  * Joseph Poon 
  * Mykola Sakhno
  * Olaoluwa Osuntokun (roasbeef)
  * Riccardo Casatta
  * Paul Capestany
  * Slava Zhigulin
  * Tadge Dryja


### Developers, Developers, Developers!

We look forward to engaging new developers as they tinker with `lnd` to create
novel Layer-2 application on top of the Lightning Network and also get a feel
for what's possible with this new technology. We've secured the `#lnd` channel
on freenode as a place dedicated to discussing `lnd`-related development and
support. 

---


_If you're also fascinated by the intersection of distributed systems,
peer-to-peer networks and applied cryptography we're exploring with Lightning,
then Lightning Labs is hiring for full-time positions. If interested, email
laolu32 at gmail.com!_
