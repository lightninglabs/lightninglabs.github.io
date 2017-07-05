---
layout: post
title:  "Lightning Apps and the Emerging Developer Ecosystem on LND"
date:   2017-07-05 08:00:00 -0800
categories: software lnd lightning
---

<img src="http://imgur.com/CNnMvfM.jpg" alt="users tip on Slack" style="width: 35em;"/>

_By Max Fang_

`lnd` is still in alpha, but we're already seeing a handful of developers
building on top of it. In this post you can find a summary of the progress made
thus far, starting first with application-level projects (**"Lapps"**), a few
user interfaces, and concluding with repositories and utilities that developers
on Lightning will find helpful.

---

# Lapps

## Slack Tipbot

*by François Masurel* ([@francoismasurel](https://twitter.com/francoismasurel))

[https://github.com/CryptoFR/ln-tip-slack](https://github.com/CryptoFR/ln-tip-slack)

What's the first thing a Bitcoiner builds with micropayments? A tip bot! The
`lnd`-based successor to ChangeTip, François 'Mably' Masurel and team built a
tip bot on Slack, which is still WIP but usable on Bitcoin testnet. After
depositing funds, you simply have to type ``/lntip <amount in satoshis> @<Slack
username>`` to send a tip. You can try it out here:
[https://lnd-testnet-2.mably.com/](https://lnd-testnet-2.mably.com/)

![image alt text](http://imgur.com/eHLym7J.jpg)

## Voltachain

*by Louis Congard ([@Aldhyr](https://twitter.com/Aldhyr)), Ali El Husseini,
Tangui Clairet, Pierre Lorcery ([@tulsene](https://twitter.com/tulsene)),
François Masurel ([@francoismasurel](https://twitter.com/francoismasurel))*

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Tired but so happy to win the 1st prize ex-aequo of the <a href="https://twitter.com/hashtag/merkleweek?src=hash">#merkleweek</a> hackathon with the <a href="https://twitter.com/hashtag/VoltaChain?src=hash">#VoltaChain</a> team!! So many people to thanks &lt;3 <a href="https://t.co/mHuEgoEz0o">pic.twitter.com/mHuEgoEz0o</a></p>&mdash; Louis (@Aldhyr) <a href="https://twitter.com/Aldhyr/status/841055635286458370">March 12, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Voltachain is a pay-as-you-go solution for electric vehicle energy purchases,
using `lnd` as its basis for micropayments. Rethinking the current model of
flat-rate pricing, Voltachain allows people to pay for only the power that they
actually consume. Furthermore, since Voltachain is peer-to-peer and users are
not locked into centralized vendors, anyone can easily spin up a pay-as-you-go
charging service of their own.

The team demoed their PoC live at the Merkleweek hackathon in Paris using a
smartphone connected to a charging station that initially produced no charge.
Clicking a button on a web interface completed a payment from the phone to the
charging station, and the phone lit up at once and promptly began charging.

`lnd` could also be used for video streaming, internet by the megabyte, and
paid API calls. Props to Voltachain for being the first to apply Bitcoin
micropayments to energy and taking home first place at Merkleweek!

![image alt text](https://i.imgur.com/nuDTQun.png)

*Sequence diagram of the Voltachain PoC*

## "Monetizing Full Nodes"

*by Michael Folkson ([@michaelfolkson](https://twitter.com/michaelfolkson)),
Alex Bosworth ([@alexbosworth](https://twitter.com/alexbosworth)), and Nathan
Basanese ([@NathanBasanese](https://twitter.com/NathanBasanese))*

[https://github.com/michaelfolkson/bcoin-hackathon](https://github.com/michaelfolkson/bcoin-hackathon)

![image alt text](https://i.imgur.com/vahIpgG.png)

As the Bitcoin blockchain grows bigger in size, it gets more and more
cost-prohibitive for individuals in the network to run full nodes, potentially
leading to centralization. To directly address this problem, Michael Folkson,
Alex Bosworth, and Nathan Basanese built "Monetizing Full Nodes," a solution
allowing full nodes to generate revenue and potentially even profits.
Monetizing Full Nodes utilizes `lnd` for payments, bcoin for full nodes, and
RiskBazaar as a "risk contract". In particular, full nodes could generate
revenue by serving as oracles for other nodes seeking information about the
state of the Bitcoin network. A RiskBazaar contract would facilitate the
information exchange, while the Lightning Network would facilitate the payment
of the associated fees to the full node. Their application took home first
place at the bcoin hackathon this March in SF!

You can read their [explanation of the
project](https://medium.com/purse-essays/bcoin-hackathon-bitcoin-for-your-thoughts-3b7e7c067970)
and read their [demo
slides](https://www.slideshare.net/michaelfolkson/bcoin-hackathon-slides).

---

# User Interfaces

## Lightning MacOS GUI Wallet

*by Alex Bosworth ([@alexbosworth](https://twitter.com/alexbosworth))*

[https://github.com/alexbosworth/lnd-gui](https://github.com/alexbosworth/lnd-gui)

Alex Bosworth built the first `lnd` User Interface for Mac OS! You can see a
preview of it below:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Working on a <a href="https://twitter.com/lightning">@lightning</a> wallet - looking forward to scaling and micropayment dreams made real. <a href="https://t.co/ZjAOTefCzq">pic.twitter.com/ZjAOTefCzq</a></p>&mdash; Alex UASF Bosworth (@alexbosworth) <a href="https://twitter.com/alexbosworth/status/844030573131706368">March 21, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

## Lightweight LND Dashboard

*by François Masurel ([@francoismasurel](https://twitter.com/francoismasurel))*

[https://github.com/mably/lncli-web](https://github.com/mably/lncli-web)

François 'Mably' Masurel and team also built a web client and dashboard for
`lnd` written in NodeJS / Angular. A few of the other `lnd` projects have used
this web client as a starting point, speaking to the usefulness of this
project. Here are a few screenshots:

![image alt text](https://i.imgur.com/d6lh9YN.png)

![image alt text](https://i.imgur.com/7jhnPmU.png)

You can find more screenshots here: [https://imgur.com/a/LgWcs](https://imgur.com/a/LgWcs)

## Mobile and Desktop app for Lightning

*by Jack Mallers ([@JackMallers](https://twitter.com/JackMallers))*

We are excited to hear that there's developer energy toward building a mobile
and desktop application for the Lightning Network. This in-progress app plans
to support both bitcoin and litecoin, featuring a slick UI. Watch Alex 'buy a
coffee' for certain special guest of his here:

<blockquote class="twitter-tweet" data-lang="en"><p lang="en" dir="ltr">Working on a mobile and desktop application for the Lightning Network 😃 <a href="https://twitter.com/lightning">@lightning</a> <a href="https://twitter.com/hashtag/bitcoin?src=hash">#bitcoin</a> <a href="https://twitter.com/hashtag/ltc?src=hash">#ltc</a> <a href="https://t.co/iXu4Rk4Xk9">pic.twitter.com/iXu4Rk4Xk9</a></p>&mdash; Jack UASF Mallers (@JackMallers) <a href="https://twitter.com/JackMallers/status/857357930777149440">April 26, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

---

# Developer Infrastructure

Lightning Labs recently hired me to work on developer tooling and resources -
stay tuned for the full site! If you want to try building on `lnd` now, there
are a few tools and guides at your disposal:

## Getting Started

[https://github.com/lightningnetwork/lnd/blob/master/README.md](https://github.com/lightningnetwork/lnd/blob/master/README.md)

To get started, follow this root README.md of the `lnd` repository as well as
the associated installation guides.

## Guide: Creating a Lightning Cluster

[https://github.com/lightningnetwork/lnd/blob/master/docker/README.md](https://github.com/lightningnetwork/lnd/blob/master/docker/README.md)

This guide details how to run a local dockerized cluster of `lnd` nodes,
connecting the major components together, opening/closing channels, and
connecting to the Lightning faucet node. Accessing the standard manual via `lnd
--help` at the command line is also helpful.

## Guide: `lnd` gRPC server

[https://github.com/lightningnetwork/lnd/blob/master/docs/grpc/javascript.md](https://github.com/lightningnetwork/lnd/blob/master/docs/grpc/javascript.md)

[https://github.com/lightningnetwork/lnd/blob/master/docs/grpc/python.md](https://github.com/lightningnetwork/lnd/blob/master/docs/grpc/python.md)

The primary way to connect applications to `lnd` is through the gRPC server. We
have written guides on how to write a simple gRPC client for both Javascript
and Python. If you're not familiar with the concept of RPC, you can also read
more about gRPC itself at [http://www.grpc.io/](http://www.grpc.io/)

## `lnd` REST API

[https://github.com/lightningnetwork/lnd/blob/master/lnrpc/rpc.swagger.json](https://github.com/lightningnetwork/lnd/blob/master/lnrpc/rpc.swagger.json)

Lightning features a gRPC proxy that allows you to query a LND node with
familiar HTTP requests, which can make development a lot easier for you, for
those who are not familiar with gRPC. Here are some example `curl` requests by
@roasbeef:
[https://gist.github.com/Roasbeef/624c02cd5a90a44ab06ea90e30a6f5f0](https://gist.github.com/Roasbeef/624c02cd5a90a44ab06ea90e30a6f5f0)

## Lightning Faucet

[https://faucet.lightning.community/](https://faucet.lightning.community/)
[https://ltc.faucet.lightning.community/](https://ltc.faucet.lightning.community/)

![image alt text](https://i.imgur.com/4qtSudA.png)

A faucet for Lightning Network is currently deployed on the Bitcoin and
Litecoin testnets. Instead of sending Bitcoin directly on-chain to the targeted
user, the faucet will open a channel with the user. Read the code and more
information here:
[https://github.com/lightninglabs/lightning-faucet](https://github.com/lightninglabs/lightning-faucet)

## Code Contribution Guidelines

[https://github.com/lightningnetwork/lnd/blob/master/docs/code_contribution_guidelines.md](https://github.com/lightningnetwork/lnd/blob/master/docs/code_contribution_guidelines.md)

And lastly, for those who may be interested in building `lnd` itself, the code
contribution guidelines will outline the skills and readings required,
development practiced, and code approval process. You can start by tackling
[beginner Github
issues](https://github.com/lightningnetwork/lnd/labels/beginner).
