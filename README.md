IPEx integration/staging tree
================================

http://www.ipex.org

Copyright (c) 2009-2013 Bitcoin Developers
Copyright (c) 2011-2013 Litecoin Developers

What is IPEx?
----------------

IPEx is a lite version of Bitcoin using scrypt as a proof-of-work algorithm.
 - 2.5 minute block targets
 - subsidy halves in 840k blocks (~4 years)
 - ~84 million total coins

The rest is the same as Bitcoin.
 - 50 coins per block
 - 2016 blocks to retarget difficulty

For more information, as well as an immediately useable, binary version of
the IPEx client sofware, see http://www.ipex.org.

License
-------

IPEx is released under the terms of the MIT license. See `COPYING` for more
information or see http://opensource.org/licenses/MIT.

Development process
-------------------

Developers work in their own trees, then submit pull requests when they think
their feature or bug fix is ready.

If it is a simple/trivial/non-controversial change, then one of the IPEx
development team members simply pulls it.

If it is a *more complicated or potentially controversial* change, then the patch
submitter will be asked to start a discussion (if they haven't already) on the
[mailing list](http://sourceforge.net/mailarchive/forum.php?forum_name=bitcoin-development).

The patch will be accepted if there is broad consensus that it is a good thing.
Developers should expect to rework and resubmit patches if the code doesn't
match the project's coding conventions (see `doc/coding.txt`) or are
controversial.

The `master` branch is regularly built and tested, but is not guaranteed to be
completely stable. [Tags](https://github.com/bitcoin/bitcoin/tags) are created
regularly to indicate new official, stable release versions of IPEx.

Testing
-------

Testing and code review is the bottleneck for development; we get more pull
requests than we can review and test. Please be patient and help out, and
remember this is a security-critical project where any mistake might cost people
lots of money.

### Automated Testing

Developers are strongly encouraged to write unit tests for new code, and to
submit new unit tests for old code.

Unit tests for the core code are in `src/test/`. To compile and run them:

    cd src; make -f makefile.unix test

Unit tests for the GUI code are in `src/qt/test/`. To compile and run them:

    qmake BITCOIN_QT_TEST=1 -o Makefile.test bitcoin-qt.pro
    make -f Makefile.test
    ./ipex-qt_test

### Usage
    In the leveldb folder of the src folder, there is a script called build_detect_platform, it needs to be made executable.

Since I built this on an Ubuntu based system Mint this little tutorial will only cover the build environment for Ubuntu variants, you can look in the doc folder for information on how to set up for your specific system. If someone can figure out how to build the code using MS or MAC and would like to publish it here let me know.

To set up the build environment use this script:

# !/bin/bash

sudo apt install build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils python3 -y
sudo apt install libboost-all-dev -y
sudo apt install software-properties-common -y
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt update
sudo apt install libdb4.8-dev libdb4.8++-dev -y
This should work with Ubuntu 12.04 and later.

Once you have the build environment set and you have extracted your code that you’ve downloaded navigate to the src folder and open a terminal in that folder. Type make -f makefile.unix and the build will start. If successful you will have an executable file called yourcoind where yourcoin is that name you have given your awesome crypto currency. I call mine vetcoin because I am a veteran and why not have a crypto currency for vets?

Building the qt wallet is simple just install Qt Creator: sudo apt install qtcreator -y and open the bitcoin-qt.pro file in yourcoin folder, configure then build all.

Warning: If you do any editing of the code DO NOT, I repeat, DO NOT comment out or otherwise remove {NULL, NULL} in either the dnsseed functions in net.cpp or your qt wallet will crash with a segmentation fault. I found this out the hard way.

Once you have your daemon and qt programs built it’s time to set up at least two nodes to test out your awesome new coin. This can easily be done through a virtual machine with a bridged adapter so that they will show up on your home network. You will need to setup the build environment again and build the daemon only, there will be no need of the qt wallet because they are going to be seed nodes only.

In both virtual machines and your host machine you will need to create a data folder called .yourcoin in your home folder and then create a yourcoin.conf file inside the .yourcoin folder. Inside the configuration file of all three machines you will need:

rpcuser=yourcoinrpc
rpcpassword=some random password

In the configuration file of the two seed nodes you will need to also add prune=500 I am not sure what the prune does but that is what I got from my google search.

In the host machine configuration file you will need to add the following lines:

testnet=1
daemon=1
server=1
gen=0
addnode=ip address to seed1:19333
addnode=ip addres to seed2:19333

I am assuming you already know how to use ifconfig in the terminal to find the network ip address for your two virtual machines. To start the seed nodes navigate to the src folder or wherever you have the build binary, open the a terminal in the folder and type: ./yourcoind -testnet -daemon

Do this in both vm’s, but before we start anything in the host machine we need a miner to mine the coins. I downloaded a binary for a simple cpu miner and placed it in the same folder as the yourcoin-qt binary. You can get the miner here and be up and running lickety split.

Once you have the miner start ./yourcoind -testnet -daemon and then launch the wallet in a terminal from the build folder it is in like this ./yourcoind-qt -testnet, this will launch your wallet on the test net.  Your wallet will say out of sync but don’t worry about that. From the same terminal you launched the wallet launch you miner thusly: ./minerd -o localhost:19332 -u yourcoinrpc -p the password you set in the yourcoin.conf file. The miner should begin mining using your cpu. If you have a dual or multicore cpu it will start more than one mining thread. You should be able to find the first block rather quickly. I found two blocks within about five minutes.

That’s the very basics of it, if you want others to mine and use your new coin you will have to set up your own seed nodes and hard code them into the source. Build your own community around it, get a development team to maintain the code, and build the infrastructure to support it in the long term.
