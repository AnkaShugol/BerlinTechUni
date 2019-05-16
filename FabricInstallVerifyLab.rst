Section 1:  Lab Overview
========================
In this lab, you will start with a basic Ubuntu 16.04.5 server instance running on IBM LinuxONE Community Cloud.  Ubuntu updates were applied such that at this time the level of Ubuntu is *16.04.5 LTS* and the Linux kernel is at level *4.4.0-139*.  In addtion, several software prerequisites have already been installed on this instance for you, including:

*	Docker and Docker Compose
*	Node.js and npm
*	Golang compiler
*	other necessary packages

During the lab, you will download four Hyperledger Fabric source code repositories, build various artifacts such as Docker images and executable binaries from the source code, and run three tests to verify that your Hyperledger Fabric installation is in working order.

You will first download the *fabric* repository, which contains the source code for the core functionality of Hyperledger Fabric.  Using this source code, you will build the Docker images that comprise the core Hyperledger Fabric services as well as native executable binaries that are available as an alternative to using the Docker images. Then you will download the *fabric-samples* repository and run a basic Hyperledger Fabric network and utilize the Hyperledger Fabric Command Line Interface (CLI) to verify that core Hyperledger Fabric functionality is working correctly.

Then you will download another Hyperledger Fabric source code repository, *fabric-ca*, and build from this source the artifacts necessary to run the test.
 
Section 2: Download, build and test the Hyperledger Fabric CLI
==============================================================

In this section, you will:

*	Download the source code repository containing the core Hyperledger Fabric functionality
*	Use the source code to build Docker images that contain the core Hyperledger Fabric functionality
* Use the source code to build native executable binaries that provide Hyperledger Fabric utilities and that also provide an alternative to using Docker images.
* Download a repository containing Hyperledger Fabric samples
*	Test for success by running one of the samples called "Build Your First Network", which will provide a test of core Hyperledger Fabric functionality.

**Step 2.1:** Create the following directory path with this command.  Make sure you are in your home directory when you enter it. If you are following these steps exactly, you already are.  If you strayed away from your home directory, I'm assuming you're smart enough to get back there or at least smart enough to ask for help::

 ubuntu@techumaster:~$ mkdir -p git/src/github.com/hyperledger
 ubuntu@techumaster:~$
 
**Step 2.2:** Navigate to the directory you just created::

 ubuntu@techumaster:~$ cd git/src/github.com/hyperledger/
 ubuntu@techumaster:~/git/src/github.com/hyperledger$
 
**Step 2.3:** Use the software tool *git* to download the source code of the Hyperledger Fabric core package from the official place where it lives::

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ git clone -b v1.4.1 --depth 1 https://gerrit.hyperledger.org/r/fabric
 Cloning into 'fabric'...
 remote: Counting objects: 5508, done
 remote: Finding sources: 100% (5508/5508)
 remote: Total 5508 (delta 322), reused 4447 (delta 322)
 Receiving objects: 100% (5508/5508), 20.11 MiB | 28.71 MiB/s, done.
 Resolving deltas: 100% (322/322), done.
 Checking connectivity... done.
 Note: checking out 'd700b43476e803c864c48021e63a78543b60e17e'.

 You are in 'detached HEAD' state. You can look around, make experimental
 changes and commit them, and you can discard any commits you make in this
 state without impacting any branches by performing another checkout.

 If you want to create a new branch to retain commits you create, you may
 do so (now or later) by using -b with the checkout command again. Example:

   git checkout -b <new-branch-name>

**Step 2.4:** Switch to the *fabric* directory, which is the top-level directory of where the *git* command put the code it just downloaded::

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ cd fabric
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric$

**Step 2.5:** You will use a program called *make*, which is used to build software projects, in order to build Docker images for Hyperledger Fabric.  But first, run this command to show that your system does not currently have any 
Docker images stored on it.  The only output you will see is the column headings::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric$ docker images
 REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

**Step 2.6:** That will change in a few minutes.  Enter the following command, which will build the Hyperledger Fabric Docker images via the *docker* argument on the *make* command- and also build native executable binaries via the *release* argument on the *make* command.  You can ‘wrap’ the *make* command, which is what will do all the work, in a *time* command, which will give you a measure of the time, including ‘wall clock’ time, required to run the *make* command::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric$ time make docker release
   .
   .  (output not shown here)
   .
 real	4m42.012s
 user	1m14.507s
 sys	0m12.121s

**Step 2.7:** Run *docker images* again and you will see several Docker images that were just created. You will notice that many of the Docker images at the top of the output were created in the last few minutes.  These were created by the *make docker release* command.  The Docker images that are a few months old were downloaded from Hyperledger Fabric's public DockerHub repository.  Your output should look similar to that shown here, although your *image ids* will be different for the images that were created in the last few minutes::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric$ docker images
 REPOSITORY                     TAG                 IMAGE ID            CREATED              SIZE
 hyperledger/fabric-tools       latest                         36d8a7db8056        About a minute ago   1.52GB
 hyperledger/fabric-tools       s390x-1.4.1-snapshot-d700b43   36d8a7db8056        About a minute ago   1.52GB
 hyperledger/fabric-tools       s390x-latest                   36d8a7db8056        About a minute ago   1.52GB
 <none>                         <none>                         10ea31c0bee7        About a minute ago   1.58GB
 hyperledger/fabric-buildenv    latest                         172f9563573f        3 minutes ago        1.47GB
 hyperledger/fabric-buildenv    s390x-1.4.1-snapshot-d700b43   172f9563573f        3 minutes ago        1.47GB
 hyperledger/fabric-buildenv    s390x-latest                   172f9563573f        3 minutes ago        1.47GB
 hyperledger/fabric-ccenv       latest                         602af5999747        3 minutes ago        1.41GB
 hyperledger/fabric-ccenv       s390x-1.4.1-snapshot-d700b43   602af5999747        3 minutes ago        1.41GB
 hyperledger/fabric-ccenv       s390x-latest                   602af5999747        3 minutes ago        1.41GB
 hyperledger/fabric-orderer     latest                         63f428514b07        4 minutes ago        147MB
 hyperledger/fabric-orderer     s390x-1.4.1-snapshot-d700b43   63f428514b07        4 minutes ago        147MB
 hyperledger/fabric-orderer     s390x-latest                   63f428514b07        4 minutes ago        147MB
 hyperledger/fabric-peer        latest                         b415d479b21c        4 minutes ago        153MB
 hyperledger/fabric-peer        s390x-1.4.1-snapshot-d700b43   b415d479b21c        4 minutes ago        153MB
 hyperledger/fabric-peer        s390x-latest                   b415d479b21c        4 minutes ago        153MB
 hyperledger/fabric-baseimage   s390x-0.4.14                   6e4e09df1428        3 months ago         1.38GB
 hyperledger/fabric-baseos      s390x-0.4.14                   4834a1e3ce1c        3 months ago         120MB

**Step 2.8:** Navigate to up one directory level::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric$ cd ..
 ubuntu@techumaster:~/git/src/github.com/hyperledger$

**Step 2.9:** Download the *fabric-samples* repository, which, if I told you it contains Hyperledger Fabric samples, would you believe me?

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ git clone -b v1.4.1 --depth 1 https://gerrit.hyperledger.org/r/fabric-samples
 Cloning into 'fabric-samples'...
 remote: Counting objects: 570, done
 remote: Finding sources: 100% (570/570)
 remote: Total 570 (delta 49), reused 498 (delta 49)
 Receiving objects: 100% (570/570), 382.69 KiB | 0 bytes/s, done.
 Resolving deltas: 100% (49/49), done.
 Checking connectivity... done.
 Note: checking out 'bb39b6ed0947aa6f5d23d64b8ad65b1a64384bbb'.

 You are in 'detached HEAD' state. You can look around, make experimental
 changes and commit them, and you can discard any commits you make in this
 state without impacting any branches by performing another checkout.

 If you want to create a new branch to retain commits you create, you may
 do so (now or later) by using -b with the checkout command again. Example:

   git checkout -b <new-branch-name>

**Step 2.10:** Navigate to the *fabric-samples* directory which contains the repository you just cloned::

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ cd fabric-samples/
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples$ 
 
**Step 2.11:** Now here's the thing- there is a way to just download the Hyperledger Fabric Docker images and native executable binaries and it is documented in the *README.md* file in this directory. 
But you don't need to do that because you just built everything you need from the source code.
But the sample script you will run soon expects to find a couple of binary files in the *fabric-samples/bin* directory.
Create that directory with this command::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples$ mkdir bin
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples$ 

**Step 2.12:** The sample script you will run soon expects to find a program named *cryptogen*, a program named *configtxgen* , and a program named *configtxglator* in the *fabric-samples/bin* directory.
Let's oblige it by copying these two programs from the directory where there were placed courtesy of the *release* argument of the *make docker release* command from *Step 2.6*::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples$ cp -ipv ../fabric/release/linux-s390x/bin/c* bin/.
 '../fabric/release/linux-s390x/bin/configtxgen' -> 'bin/./configtxgen'
 '../fabric/release/linux-s390x/bin/configtxlator' -> 'bin/./configtxlator'
 '../fabric/release/linux-s390x/bin/cryptogen' -> 'bin/./cryptogen'

**Step 2.13:** Navigate to the *first-network* directory because this is the sample that you will use in this lab::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples$ cd first-network/
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$
 
**Step 2.14:** You will run the test using a script named *byfn.sh* in this directory. There are several arguments to this script. Invoke the script with the *-h* argument in order to display some help text::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ ./byfn.sh -h
 Usage: 
   byfn.sh <mode> [-c <channel name>] [-t <timeout>] [-d <delay>] [-f <docker-compose-file>] [-s <dbtype>] [-l <language>] [-o <consensus-type>] [-i <imagetag>] [-v]
     <mode> - one of 'up', 'down', 'restart', 'generate' or 'upgrade'
       - 'up' - bring up the network with docker-compose up
       - 'down' - clear the network with docker-compose down
       - 'restart' - restart the network
       - 'generate' - generate required certificates and genesis block
       - 'upgrade'  - upgrade the network from version 1.3.x to 1.4.0
     -c <channel name> - channel name to use (defaults to "mychannel")
     -t <timeout> - CLI timeout duration in seconds (defaults to 10)
     -d <delay> - delay duration in seconds (defaults to 3)
     -f <docker-compose-file> - specify which docker-compose file use (defaults to docker-compose-cli.yaml)
     -s <dbtype> - the database backend to use: goleveldb (default) or couchdb
     -l <language> - the chaincode language: golang (default) or node
     -o <consensus-type> - the consensus-type of the ordering service: solo (default) or kafka
     -i <imagetag> - the tag to be used to launch the network (defaults to "latest")
     -v - verbose mode
   byfn.sh -h (print this message)

 Typically, one would first generate the required certificates and 
 genesis block, then bring up the network. e.g.:

	byfn.sh generate -c mychannel
	byfn.sh up -c mychannel -s couchdb
 byfn.sh up -c mychannel -s couchdb -i 1.4.0
	byfn.sh up -l node
	byfn.sh down -c mychannel
 byfn.sh upgrade -c mychannel

 Taking all defaults:
 	byfn.sh generate
 	byfn.sh up
 	byfn.sh down

The last three lines of the output from the help shows the simplest way to perform this test.
If you took all defaults, you would be generating artifacts to create a channel named *mychannel*, using chaincode written in the *Go* language, an orderering consensus type of *solo*, and using *levelDB* as the state database.

**Step 2.15:** Run *byfn.sh* with the *generate* argument in order to generate the necessary cryptographic material and channel configuration for a channel named *mychannel*.  If you wish to use a different channel name, maybe named after your favorite child or pet or car or football team or spouse, use the *-c* argument as well, but be aware that channel names must start with a lowercase character, and contain only lowercase characters, numbers, and a small number of punctuation characters such as a dash (*-*) and a couple others I can't remember right now.  You'll also have to use this channel name on other inovcations of *byfn.sh* in subsequent steps.  Just don't use uppercase letters, okay?  (You can use uppercase numbers if that makes you feel better).  The below examples show creating a channel named *tim*. Use *tim* or create your own name or leave the *-c* argument off altogether to use the default of *mychannel*.  You decide.  I trust your judgement. So go ahead and issue this command or your customized ::

 bcuser@ubuntu16045:~/git/src/github.com/hyperledger/fabric-samples/first-network$ ./byfn.sh generate -c tim
 Generating certs and genesis block for channel 'tim' with CLI timeout of '10' seconds and CLI delay of '3' seconds
 Continue? [Y/n] Y
 proceeding ...
 /home/bcuser/git/src/github.com/hyperledger/fabric-samples/first-network/../bin/cryptogen

 ##########################################################
 ##### Generate certificates using cryptogen tool #########
 ##########################################################
 + cryptogen generate --config=./crypto-config.yaml
 org1.example.com
 org2.example.com
 + res=0
 + set +x

 /home/bcuser/git/src/github.com/hyperledger/fabric-samples/first-network/../bin/configtxgen
 ##########################################################
 #########  Generating Orderer Genesis block ##############
 ##########################################################
 CONSENSUS_TYPE=solo
 + '[' solo == solo ']'
 + configtxgen -profile TwoOrgsOrdererGenesis -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
 2019-01-15 11:35:34.252 EST [common.tools.configtxgen] main -> INFO 001 Loading configuration
 2019-01-15 11:35:34.284 EST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 002 orderer type: solo
 2019-01-15 11:35:34.284 EST [common.tools.configtxgen.localconfig] Load -> INFO 003 Loaded configuration: /home/bcuser /git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.317 EST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 004 orderer type: solo
 2019-01-15 11:35:34.317 EST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 005 Loaded configuration: /home/bcuser/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.319 EST [common.tools.configtxgen] doOutputBlock -> INFO 006 Generating genesis block
 2019-01-15 11:35:34.319 EST [common.tools.configtxgen] doOutputBlock -> INFO 007 Writing genesis block
 + res=0
 + set +x

 #################################################################
 ### Generating channel configuration transaction 'channel.tx' ###
 #################################################################
 + configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID tim
 2019-01-15 11:35:34.390 EST [common.tools.configtxgen] main -> INFO 001 Loading configuration
 2019-01-15 11:35:34.422 EST [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/bcuser /git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.452 EST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 003 orderer type: solo
 2019-01-15 11:35:34.452 EST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/bcuser/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.452 EST [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 005 Generating new channel configtx
 2019-01-15 11:35:34.453 EST [common.tools.configtxgen] doOutputChannelCreateTx -> INFO 006 Writing new channel tx
 + res=0
 + set +x

 #################################################################
 #######    Generating anchor peer update for Org1MSP   ##########
 #################################################################
 + configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID tim -asOrg Org1MSP
 2019-01-15 11:35:34.518 EST [common.tools.configtxgen] main -> INFO 001 Loading configuration
 2019-01-15 11:35:34.548 EST [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/ubuntu/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.579 EST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 003 orderer type: solo
 2019-01-15 11:35:34.579 EST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/ubuntu/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.579 EST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 005 Generating anchor peer update
 2019-01-15 11:35:34.579 EST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 006 Writing anchor peer update
 + res=0
 + set +x

 #################################################################
 #######    Generating anchor peer update for Org2MSP   ##########
 #################################################################
 + configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx -channelID tim -asOrg Org2MSP
 2019-01-15 11:35:34.656 EST [common.tools.configtxgen] main -> INFO 001 Loading configuration
 2019-01-15 11:35:34.703 EST [common.tools.configtxgen.localconfig] Load -> INFO 002 Loaded configuration: /home/ubuntu/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml
 2019-01-15 11:35:34.737 EST [common.tools.configtxgen.localconfig] completeInitialization -> INFO 003 orderer type: solo
 2019-01-15 11:35:34.737 EST [common.tools.configtxgen.localconfig] LoadTopLevel -> INFO 004 Loaded configuration: /home/ubuntu/git/src/github.com/hyperledger/fabric-samples/first-network/configtx.yaml 
 2019-01-15 11:35:34.737 EST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 005 Generating anchor peer update
 2019-01-15 11:35:34.737 EST [common.tools.configtxgen] doOutputAnchorPeersUpdate -> INFO 006 Writing anchor peer update
 + res=0
 + set +x

**Step 2.16:** Issue the following command to start the Hyperledger Fabric network using Node.js chaincode (the *-l node* argument and value), using CouchDB as the state database (the *-s couchdb* argument and value), the Kafka orderer consensus algorithm (the *-o kafka* argument and value, and the channel named *tim* (the *-c tim* argument and value).
If your channel is not named *tim* use your correct channel name instead.
If you want to try one of the other options for chaincode language, state database, or consensus algorithm, I applaud your curiosity and leave the determination of which options to choose as an exercise for the reader)::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ ./byfn.sh up -l node -s couchdb -o kafka -c tim 
 Starting for channel 'tim' with CLI timeout of '10' seconds and CLI delay of '3' seconds and using database 'couchdb'
 Continue? [Y/n] Y
  .
  . (output not shown)
  .
 ========= All GOOD, BYFN execution completed =========== 

  _____   _   _   ____   
 | ____| | \ | | |  _ \  
 |  _|   |  \| | | | | | 
 | |___  | |\  | | |_| | 
 |_____| |_| \_| |____/  

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ 

**Step 2.17:** If you were following the output closely you may have noticed a few spots where the output paused for a little bit. 
This occurred at the point where a peer was building a new Docker image for chaincode.
This occurs three times during the script.
Additionally, Node.js chaincode takes longer for its Docker images to be built than does Go chaincode, so if you chose *-l node* the pause may have been long enough for you to worry a bit.
But it's all good.

Run this command to see the three Docker images created for the chaincode.
These images will start with *dev* because this is the default name of a Hyperledger Fabric network and this script does not override the default::
 
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ docker images dev-*
 REPOSITORY                                                                                             TAG                  IMAGE ID            CREATED             SIZE
 dev-peer1.org2.example.com-mycc-1.0-26c2ef32838554aac4f7ad6f100aca865e87959c9a126e86d764c8d01f8346ab   latest              e35e3da4fba1        2 hours ago         1.55GB
 dev-peer0.org1.example.com-mycc-1.0-384f11f484b9302df90b453200cfb25174305fce8f53f4e94d45ee3b6cab0ce9   latest              47f93d1b8fff        2 hours ago         1.55GB
 dev-peer0.org2.example.com-mycc-1.0-15b571b3ce849066b7ec74497da3b27e54e0df1345daff3951b94245ce09c42b   latest              9929e2385ef9        2 hours ago         1.55GB

The second part of the Docker images name is the name of the peer for which the chaincode was created.
This sample uses chaincode in three peers.

**Step 2.18:** List the Docker containers that are running as part of this sample with this command::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ docker ps --all
 CONTAINER ID        IMAGE                                                                                                  COMMAND                  CREATED             STATUS              PORTS                                              NAMES
 fa737dc9aee3        dev-peer1.org2.example.com-mycc-1.0-26c2ef32838554aac4f7ad6f100aca865e87959c9a126e86d764c8d01f8346ab   "/bin/sh -c 'cd /usr…"   2 hours ago         Up 2 hours                                                             dev-peer1.org2.example.com-mycc-1.0
 7ce34a33cf7b        dev-peer0.org1.example.com-mycc-1.0-384f11f484b9302df90b453200cfb25174305fce8f53f4e94d45ee3b6cab0ce9   "/bin/sh -c 'cd /usr…"   2 hours ago         Up 2 hours                                                             dev-peer0.org1.example.com-mycc-1.0
 0f7e7f6cad01        dev-peer0.org2.example.com-mycc-1.0-15b571b3ce849066b7ec74497da3b27e54e0df1345daff3951b94245ce09c42b   "/bin/sh -c 'cd /usr…"   2 hours ago         Up 2 hours                                                             dev-peer0.org2.example.com-mycc-1.0
 89ecb0e31b7f        hyperledger/fabric-tools:latest                                                                        "/bin/bash"              2 hours ago         Up 2 hours                                                             cli
 2f2018fc2ed5        hyperledger/fabric-peer:latest                                                                         "peer node start"        2 hours ago         Up 2 hours          0.0.0.0:7051->7051/tcp, 0.0.0.0:7053->7053/tcp     peer0.org1.example.com
 0a4229f3cd70        hyperledger/fabric-peer:latest                                                                         "peer node start"        2 hours ago         Up 2 hours          0.0.0.0:8051->7051/tcp, 0.0.0.0:8053->7053/tcp     peer1.org1.example.com
 ffa62624adee        hyperledger/fabric-peer:latest                                                                         "peer node start"        2 hours ago         Up 2 hours          0.0.0.0:10051->7051/tcp, 0.0.0.0:10053->7053/tcp   peer1.org2.example.com
 aaa39c85d095        hyperledger/fabric-kafka:latest                                                                        "/docker-entrypoint.…"   2 hours ago         Up 2 hours          9092-9093/tcp                                      kafka.example.com
 c7e02afa7bb6        hyperledger/fabric-peer:latest                                                                         "peer node start"        2 hours ago         Up 2 hours          0.0.0.0:9051->7051/tcp, 0.0.0.0:9053->7053/tcp     peer0.org2.example.com
 a03388dec89b        hyperledger/fabric-couchdb                                                                             "tini -- /docker-ent…"   2 hours ago         Up 2 hours          4369/tcp, 9100/tcp, 0.0.0.0:6984->5984/tcp         couchdb1
 5ca1cbd3ffea        hyperledger/fabric-couchdb                                                                             "tini -- /docker-ent…"   2 hours ago         Up 2 hours          4369/tcp, 9100/tcp, 0.0.0.0:8984->5984/tcp         couchdb3
 0c4626556748        hyperledger/fabric-couchdb                                                                             "tini -- /docker-ent…"   2 hours ago         Up 2 hours          4369/tcp, 9100/tcp, 0.0.0.0:5984->5984/tcp         couchdb0
 ed05786131fc        hyperledger/fabric-orderer:latest                                                                      "orderer"                2 hours ago         Up 2 hours          0.0.0.0:7050->7050/tcp                             orderer.example.com
 a13d07c1425b        hyperledger/fabric-zookeeper:latest                                                                    "/docker-entrypoint.…"   2 hours ago         Up 2 hours          2181/tcp, 2888/tcp, 3888/tcp                       zookeeper.example.com
 f587cedb24e7        hyperledger/fabric-couchdb                                                                             "tini -- /docker-ent…"   2 hours ago         Up 2 hours          4369/tcp, 9100/tcp, 0.0.0.0:7984->5984/tcp         couchdb2

You may have a different number of containers running depending on what options you chose for your *byfn up* command.
If you did not choose *couchdb* as your state databse with the *-s* argument then you will not see the four *couchdb* containers (one for each peer).
If you did not choose the *Kafka* orderer consensus algorithm with the *-o* argument then you will not see the *kafka* and *zookeeper* containers.

**Step 2.19:** Run the following command pipe to show a more verbose output of the *docker ps* command which comes courtesy of the *--no-trunc* argument and then pipes the output through *grep* with a judicious string, *dev-peer* to filter the output such that you only see the output for the three chaincode containers::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ docker ps --no-trunc | grep dev-peer
 fa737dc9aee372a6c3b231218581f10487b40b42fd368e09bd62908277c5f3f7   dev-peer1.org2.example.com-mycc-1.0-26c2ef32838554aac4f7ad6f100aca865e87959c9a126e86d764c8d01f8346ab   "/bin/sh -c 'cd /usr/local/src; npm start -- --peer.address peer1.org2.example.com:7052'"   2 hours ago         Up 2 hours                                                             dev-peer1.org2.example.com-mycc-1.0
 7ce34a33cf7baeae846f83ffcd4db57607c42282be784afa5ee62fe3805d7df1   dev-peer0.org1.example.com-mycc-1.0-384f11f484b9302df90b453200cfb25174305fce8f53f4e94d45ee3b6cab0ce9   "/bin/sh -c 'cd /usr/local/src; npm start -- --peer.address peer0.org1.example.com:7052'"   2 hours ago         Up 2 hours                                                             dev-peer0.org1.example.com-mycc-1.0
 0f7e7f6cad01215d306b2020c9e6d190aff69b47bc6b93c4fc46e4ae522f8ce1   dev-peer0.org2.example.com-mycc-1.0-15b571b3ce849066b7ec74497da3b27e54e0df1345daff3951b94245ce09c42b   "/bin/sh -c 'cd /usr/local/src; npm start -- --peer.address peer0.org2.example.com:7052'"   2 hours ago         Up 2 hours                                                             dev-peer0.org2.example.com-mycc-1.0

In the above output you'll see the command used to start the container-  e.g., *"/bin/sh -c 'cd /usr/local/src; npm start -- --peer.address peer1.org2.example.com:7052'"* for the first container listed.
The *npm start* command is your clue that this is a Node.js container- *npm* is the de facto Node.js package manage, despite the authors of *npm* claiming that *npm* is not an acronym.  (I'm pretty sure this is true, and if not I'll modify this when the npm lawyers deliver the cease-and-desist letter to me).

**Step 2.20:** Run this command to stop the Hyperledger Network stood up in *Step 2.16*::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ ./byfn.sh down 
 Stopping for channel 'mychannel' with CLI timeout of '10' seconds and CLI delay of '3' seconds
 Continue? [Y/n] Y
  .
  . (output not shown)
  .
  
**Step 2.21:** Run this command to see if there are any running Docker containers::
  
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-samples/first-network$ docker ps --all                     
 CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
 
There ain't no Docker containers no more.

**Step 2.22:** Run this command to see if there are any Docker images for the chaincode::

 bcuser@ubuntu16045:~/git/src/github.com/hyperledger/fabric-samples/first-network$ docker images dev-*
 REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
 
There are no Docker chaincode images remaining as well-  the *byfn.sh down* command cleaned up nicer than a debonair dandy getting ready for a night on the town!

**Recap:** In this section, you:

*	Downloaded the main Hyperledger Fabric source code repository
*	Ran *make* to build the project’s Docker images and native binaries
*       Downloaded the Hyperledger Fabric *fabric-samples* respository
*	Ran the *first-network* sample to verify core Hyperledger functionality
*	Cleaned up afterwards
 
Section 3: Install the Hyperledger Fabric Certificate Authority
===============================================================

In the prior section, the end-to-end test that you ran supplied its own security-related material such as keys and certificates- everything it needed to perform its test.  
Therefore it did not need the services of a Certificate Authority.

Almost all "real world" Hyperledger Fabric networks will not be this static-  new users, peers and organizations will probably join the network.  
They will need Public Key Infrastructure (PKI) x.509 certificates in order to participate.  
The Hyperledger Fabric Certificate Authority (CA) is provided by the Hyperledger Fabric project in order to issue these certificates.

The next major goal in this lab is to run the Hyperledger Fabric Node.js SDK’s end-to-end test.  
This test makes calls to the Hyperledger Fabric Certificate Authority (CA). 
Therefore, before we can run that test, you will get started by downloading and building the Hyperledger Fabric CA.

**Step 3.1:** Use *cd* to navigate three directory levels up, to the *hyperledger* directory::

 bcuser@ubuntu16045:~/git/src/github.com/hyperledger/fabric/examples/e2e_cli$ cd ~/git/src/github.com/hyperledger
 bcuser@ubuntu16045:~/git/src/github.com/hyperledger$

**Step 3.2:** Get the source code for the Fabric CA using *git*::

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ git clone -b v1.4.1 --depth 1 https://gerrit.hyperledger.org/r/fabric-ca
 Cloning into 'fabric-ca'...
 remote: Counting objects: 2089, done
 remote: Finding sources: 100% (2089/2089)
 remote: Total 2089 (delta 188), reused 1860 (delta 188)
 Receiving objects: 100% (2089/2089), 6.04 MiB | 0 bytes/s, done.
 Resolving deltas: 100% (188/188), done.
 Checking connectivity... done.
 Note: checking out '27fbd69ab2d0f07212b382eb04aa85c904d2c300'.

 You are in 'detached HEAD' state. You can look around, make experimental
 changes and commit them, and you can discard any commits you make in this
 state without impacting any branches by performing another checkout.

 If you want to create a new branch to retain commits you create, you may
 do so (now or later) by using -b with the checkout command again. Example:

   git checkout -b <new-branch-name>


**Step 3.3:** Navigate to the *fabric-ca* directory, which is the top directory of where the *git* command put the code it just downloaded::

 ubuntu@techumaster:~/git/src/github.com/hyperledger$ cd fabric-ca
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-ca$

**Step 3.4:** Enter the following command, which will build the Hyperledger Fabric CA image.  Just like you did with the *fabric* repo, ‘wrap’ the *make* command, which is what will do all the work, in a *time* command, which will give you a measure of the time, including ‘wall clock’ time, required to build the image::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-ca $ time FABRIC_CA_DYNAMIC_LINK=true make docker
   .
   .  (output not shown here)
   .
 real	1m29.510s
 user	0m0.313s
 sys	0m0.160s
 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-ca$

**Step 3.5:** Enter the *docker images* command and you will see at the top of the output the Docker image that was just created for the Fabric Certificate Authority::

 ubuntu@techumaster:~/git/src/github.com/hyperledger/fabric-ca$ docker images
 REPOSITORY                      TAG                 IMAGE ID            CREATED              SIZE
 hyperledger/fabric-ca          latest                         fb40d26bc7a1        55 seconds ago      317MB
 hyperledger/fabric-ca          s390x-1.4.1                    fb40d26bc7a1        55 seconds ago      317MB
 hyperledger/fabric-tools       latest                         36d8a7db8056        3 days ago          1.52GB
 hyperledger/fabric-tools       s390x-1.4.1-snapshot-d700b43   36d8a7db8056        3 days ago          1.52GB
 hyperledger/fabric-tools       s390x-latest                   36d8a7db8056        3 days ago          1.52GB
 <none>                         <none>                         10ea31c0bee7        3 days ago          1.58GB
 hyperledger/fabric-buildenv    latest                         172f9563573f        3 days ago          1.47GB
 hyperledger/fabric-buildenv    s390x-1.4.1-snapshot-d700b43   172f9563573f        3 days ago          1.47GB
 hyperledger/fabric-buildenv    s390x-latest                   172f9563573f        3 days ago          1.47GB
 hyperledger/fabric-ccenv       latest                         602af5999747        3 days ago          1.41GB
 hyperledger/fabric-ccenv       s390x-1.4.1-snapshot-d700b43   602af5999747        3 days ago          1.41GB
 hyperledger/fabric-ccenv       s390x-latest                   602af5999747        3 days ago          1.41GB
 hyperledger/fabric-orderer     latest                         63f428514b07        3 days ago          147MB
 hyperledger/fabric-orderer     s390x-1.4.1-snapshot-d700b43   63f428514b07        3 days ago          147MB
 hyperledger/fabric-orderer     s390x-latest                   63f428514b07        3 days ago          147MB
 hyperledger/fabric-peer        latest                         b415d479b21c        3 days ago          153MB
 hyperledger/fabric-peer        s390x-1.4.1-snapshot-d700b43   b415d479b21c        3 days ago          153MB
 hyperledger/fabric-peer        s390x-latest                   b415d479b21c        3 days ago          153MB
 hyperledger/fabric-zookeeper   latest                         5db059b03239        3 months ago        1.42GB
 hyperledger/fabric-kafka       latest                         3bbd80f55946        3 months ago        1.43GB
 hyperledger/fabric-couchdb     latest                         7afa6ce179e6        3 months ago        1.55GB
 hyperledger/fabric-baseimage   s390x-0.4.14                   6e4e09df1428        3 months ago        1.38GB
 hyperledger/fabric-baseos      s390x-0.4.14                   4834a1e3ce1c        3 months ago        120MB

You may have noticed that for many of the images, the *Image ID* appears more than once, e.g., once with a tag of *latest*,  once with a tag such as *s390x-1.4.0-snapshot-d700b43*, and once with a tag of *s390x-latest*. An image can actually be given any number of tags. Think of these *tags* as nicknames, or aliases.  In our case the *make* process first gave the Docker image it created a descriptive tag, *s390x-1.4.1-snapshot-d700b43* in the case of the *fabric* repo, and *s390x-1.4.0* in the case of the *fabric-ca* repo, and then it also ‘tagged’ it with a new tag, *latest*.  It did that for a reason.  When you are working with Docker images, if you specify an image without specifying a tag, the tag defaults to the name *latest*. So, for example, using the above output, you can specify either *hyperledger/fabric-ca*, *hyperledger/fabric-ca:latest*, or *hyperledger/fabric-ca:s390x-1.4.1*, and in all three cases you are asking for the same image, the image with ID *fb40d26bc7a1*.

**Recap:** In this section, you downloaded the source code for the Hyperledger Fabric Certificate Authority and built it.  That was easy.
 

*** End of Lab! ***
