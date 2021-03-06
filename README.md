Crown PoS Explorer - 1.7.0
==========================

An Iquidus-based block explorer for the Crown PoS chain.

This project is a fork of 
[Crown Iquidus Explorer](https://github.com/walkjivefly/explorer) 

### See it in action

*  [DefunctEx](https://defunctex.com)

### Requires

*  node.js >= 0.10.x
*  mongodb >= 2.6.x
*  crownd

### Instructions

### Step 1 - Download and install the Crown client

    $ wget "https://github.com/Crowndev/crown-core/releases/download/v0.13.2.0/Crown-0.13.2.0-Linux64.zip" -O $dir/crown.zip

Install

    $ apt install unzip -y
    $ unzip -d $dir/crown $dir/crown.zip
    $ cp -f $dir/crown/*/bin/* /usr/local/bin/
    $ cp -f $dir/crown/*/lib/* /usr/local/lib/
    $ rm -rf $dir
    $ crownd

Edit the crown.conf file

        daemon=1
        server=1
        disablewallet=0
        rpcuser=YOURCROWNRPCUSER
        rpcpassword=YOURCROWNRPCPASS
        txindex=1
        rpcallowip=YOURPCIPADDRESS
        maxconnections=12

Download bootstrap (Optional)

    $ wget "https://nextcloud.crown.tech/nextcloud/s/RiyWmDLckmcXS6n/download" -O chain.7z
    $ unzip chain.7z
    $ mv bootstrap.dat /root/.crown

Start the wallet

    $ crownd

### Step 2 - Update Server

    $ apt update && sudo apt upgrade -y

### Step 3 - Get the source

    $ git clone https://github.com/defunctec/explorer explorer

Enter the explorer folder
    
    $ cd explorer

### Step 4 - Create database

Install Mongo:

    $ apt install mongodb -y

Create a directory for the database

    $ mkdir /root/explorer/mongodb/

Stop any mongo processes

    $ killall mongod

Start MongoD:
   
    $ mongod --dbpath /root/explorer/mongodb/ --fork --logpath explorer

Enter MongoDB cli:
   
    $ mongo

Create databse:

    > use explorerdb

Create user with read/write access:

    > db.createUser( { user: "CHANGEME", pwd: "CHANGEME", roles: [ "readWrite" ] } )

*note: If you're using mongo shell 2.4.x, use the following to create your user:

    > db.addUser( { user: "username", pwd: "password", roles: [ "readWrite"] })

Exit mongoDB

    > exit

### Step 5 - Install NodeJS
    
    $ apt-get install curl python-software-properties -y
    $ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
    $ apt-get install nodejs -y

### Step 6 - Install node modules

    $ npm install
    $ npm install --production

### Step 7 - Configure
    
    $ nano settings.json.template

Change the appropriate details, eg

    // database settings (MongoDB)
    "dbsettings": {
    "user": "YOURDBUSER",
    "password": "YOURDBPASS",
    "database": "explorerdb",
    "address": "localhost",
    "port": 27017
  },

    // wallet settings
    "wallet": {
    "host": "localhost",
    "port": 9341,
    "user": "YOURRPCUSER",
    "pass": "YOURRPCPASS"
  },

Change file name

    cp ./settings.json.template ./settings.json

### Step 8 - Enable firewall

Crown ports    

    $ ufw allow 9340
    $ ufw allow 9341

FTP/SSH

    $ ufw allow 22
    $ ufw allow 21

HTTP/HTTPS

    $ ufw allow 80
    $ ufw allow 433

DB/Node

    $ ufw allow 27017
    $ ufw allow 3001
    $ ufw enable


### Step 9 - Crontab

Open crontab

    $ crontab -e

Example crontab; update index every minute, market data every 2 minutes and peers every 5 minutes.

    */1 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
    */2 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js market > /dev/null 2>&1
    */5 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1

Save cron

### Step 10 - Install Forever to keep the javascript running

You can keep node running using forever

    $ npm install forever -g
    $ npm install forever-monitor

Make sure npm is fully installed

    $ npm install

You can start using

    $ forever start bin/cluster

And stop using (optional)

    $ forever stop bin/cluster

Sync the Crown blockchain with the explorer 

    $ node scripts/sync.js index update

### Known Issues

**script is already running.**

If you receive this message when launching the sync script then either: 
* a sync is currently in progress, or 
* a previous sync was killed before it completed. 

If you are certain a sync is not in progress, remove the index.pid from the tmp folder in the explorer root directory.

    rm tmp/index.pid

**exceeding stack size**

    RangeError: Maximum call stack size exceeded

Nodes default stack size may be too small to index addresses with many tx's. If you experience the above error while running sync.js the stack size needs to be increased.

To determine the default setting run

    node --v8-options | grep -B0 -A1 stack_size

To run sync.js with a larger stack size launch with

    node --stack-size=[SIZE] scripts/sync.js index update

Where [SIZE] is an integer higher than the default.

*note: SIZE will depend on which blockchain you are using, you may need to play around a bit to find an optimal setting*

### Donate

    BTC: 3C1VgNZK1M9evqMQaDBUGVCk1EBZU5ND8Q
    CRW: CRWKcbKuYkT35s4MJMwo8zjTjVe76aCsKUjG

### License

Copyright (c) 2015, Iquidus Technology  
Copyright (c) 2015, Luke Williams  
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice,
  this list of conditions and the following disclaimer in the documentation
  and/or other materials provided with the distribution.

* Neither the name of Iquidus Technology nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
