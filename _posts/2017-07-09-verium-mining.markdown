---
title: "CPU-Friendly Currencies: Mining on Older Machines"
layout: post
tag: walkthrough
img: indigo/indigo.png
projects: true
draft: false
author: DanielAnanth
summary: "CPU-Friendly Currencies: Mining on Older Machines"
jemoji: '<img class="emoji" title="mining" alt="mining" src="https://cdn.worldvectorlogo.com/logos/ethereum.svg" height="20" width="20" align="absmiddle">'
---


Do you have an old server or computer with a lot of CPU processing power that is not currently in use?

Have you heard of people buying several graphics cards to mine cryptocurrency but aren't willing to take the dive yourself?

Then don't worry, there are several CPU friendly mining alternatives! I made ~80$ a week using the CPU power of some old servers lying around.

There are hundreds, maybe even thousands of cryptocurrencies but the best one to CPU mine is Verium. I tried mining Riecoin and Monero with subpar results, making only a couple cents a day. 

The first step is to download the Verium Mining Vault from https://portal.vericoin.info/ onto any machine. After downloading, run it, create a passphrase and remember it, and then go to the 'receive' tab, this is your address that all Verium will be directed to. 

The second step is to install Ubuntu 16.04 onto the computer you would like to mine and update it, which is self explanatory and there are many online resources for that.

The third step is to install VeriumMiner onto your ubuntu installation and this will require a bit of terminal which I will go through in detail.

### Install prequisites:

```
sudo apt-get install automake autoconf pkg-config libcurl4-openssl-dev libjansson-dev libssl-dev libgmp-dev make g++ git
```

### Install VeriumMiner:

```
git clone https://github.com/effectsToCause/veriumMiner
cd veriumMiner
./autogen.sh
./configure CFLAGS="-march=native" --with-crypto --with-curl
make
```

Next you should join a mining pool in order to get a stable income with mining. Sign up with the receive address you found and create a worker with a username and password.

Run veriumMiner with the following command (while you are inside the veriumMiner folder).

```
./cpuminer -t (#CPUCORES) -o stratum+tcp://<site>:<port> -u username.workername -p workerpass
```

And that's it! Run this and watch the money come in. 

P.S. I'd also recommend auto payout on the pool website since transaction costs will be unprofitable for an on demand withdrawal.
