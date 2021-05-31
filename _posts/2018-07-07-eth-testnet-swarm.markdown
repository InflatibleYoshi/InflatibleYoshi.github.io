---
title: "Creating a private Ethereum testnet with Swarm in Ubuntu"
layout: post
tag: walkthrough
img: indigo/indigo.png
projects: true
draft: false
author: DanielAnanth
summary: "Creating a private Ethereum testnet with Swarm in Ubuntu"
jemoji: '<img class="emoji" title="ethereum" alt="ethereum" src="https://cdn.worldvectorlogo.com/logos/ethereum.svg" height="20" width="20" align="absmiddle">'
---

Ethereum testrpc is the default version of ethereum that you can use to test your Dapps using truffle. its methods are suitable for most of the functions that you'd want to implement, however they do not include connecting to __Swarm__, ethereum's persistent file storage protocol (It is important to note that both of these are in development so use Swarm at your own risk).  

Therefore if you would like to use these protocols in your app, it is necessary to use a geth private network, and I will show you how to set it up. This is assuming that you have a working instance of geth that you can access from the command line so if you don't follow the instructions [here](https://www.ethereum.org/cli#geth) to install geth.

Create a file titled __CustomGenesis.json__ with the code below. This is called the __genesis block__ which supplies the configuration for the private net. 
```
{
  "coinbase"   : "0x0000000000000000000000000000000000000001",
  "difficulty" : "0x20000",
  "extraData"  : "",
  "gasLimit"   : "0x2fefd8",
  "nonce"      : "0x0000000000000042",
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp"  : "0x00",
  "alloc": {
	"91669ebE17D588CD24d5d4a37d8191f8E8E411Ad": {
      "balance": "1337000000000000000000"
    }
  },
  "config": {
        "chainId": 34,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
  }
}
```
Move your CustomGenesis.json to a directory you would like your private net to be stored and initialize the private net with the command below.
```
geth init --datadir=/path/to/private/net /path/to/private/net/CustomGenesis.json
```
You can also get store the datadir in a environment variable like $DATADIR by typing `nano ~/.profile` and typing `DATADIR="/path/to/private/net/"`. Use `source ~/.profile` to commit the environment changes. 

You can run geth with the command:
```
geth --datadir $DATADIR --unlock 0 --shh --nodiscover --rpc --rpcaddr 127.0.0.1 --rpcport 8545 --rpccorsdomain "*" --rpcapi "admin,db,eth,debug,miner,net,shh,txpool,personal,web3"
```

*If you would like to run commands on the geth Javascript console, make sure you have geth running in a separate terminal and enter:*
```
geth attach /path/to/private/net/geth.ipc
```

Important Note about Swarm: If you're running Ubuntu 16.04 there is a very good chance that the swarm package installed points to a package about molecular clustering. A run of apt-cache policy swarm shows that the instance of swarm with higher priority is the one with a higher version number:
```
$ apt-cache policy swarm
swarm:
  Installed: 2.1.6-1
  Candidate: 1.6.6+build10111+xenial
  Version table:
 *** 2.1.6-1 500
        500 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 Packages
        100 /var/lib/dpkg/status
     1.6.6+build10111+xenial 500
        500 http://ppa.launchpad.net/ethereum/ethereum/ubuntu xenial/main amd64 Packages
```

To fix that, run `sudo nano /etc/apt/preferences` and change the swarm package preferences like so:
```
Package: swarm
Pin: version 1.6.6+build10111+xenial
Pin-Priority: 1001
```

In pin, make sure that you insert the number of the newest version of ethereum-swarm.
Running `apt-cache policy swarm` will return something like this: 

```
$ apt-cache policy swarm
swarm:
  Installed: 2.1.6-1
  Candidate: 1.6.6+build10111+xenial
  Version table:
 *** 2.1.6-1 500
        500 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 Packages
        100 /var/lib/dpkg/status
     1.6.6+build10111+xenial 1001
        500 http://ppa.launchpad.net/ethereum/ethereum/ubuntu xenial/main amd64 Packages
```
This heightened priority to the different swarm package will trigger a package downgrade to the lower version number.
Run these commands to install the correct version of swarm:

```
$ sudo apt-get clean all
$ sudo apt-get update                  
$ sudo apt-get install swarm
```

To set up swarm, set up an account by using `geth attach $DATADIR/geth.ipc` as shown above to attach to your running geth instance and run the command: `personal.newAccount("passphrase")`

Copy the hex code and add it to $BZZKEY in .profile exactly like what we did for DATADIR above.

Then to run swarm, make sure geth is running then run this command: 
```
swarm --bzzaccount $BZZKEY \
       --swap \
       --datadir $DATADIR \
       --ethapi $DATADIR/geth.ipc \
       --maxpeers 0 \
       --bzznetworkid 34 \
```

You will be prompted for a password so enter what you entered for the passphrase for your account and then the swarm instance will have been created. 