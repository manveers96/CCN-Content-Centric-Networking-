
# CCN (CONTENT CENTRIC NETWORKING)
Content-Centric Networking (CCN) is a new networking architecture proposed for
the future Internet. It has the potential to provide better results in terms of band-
width usage, scalability and security as compared to the current IP-based architec-
ture. The main idea behind content based addressing is to address content instead
of addressing hosts, thus separating content from its location. As content traverses
the network, routers and hosts either cache the location(s) where they can find a
copy of the content or cache an actual copy of the content, enabling future requests
for that content to be delivered by any node that currently holds a copy of the
content. This enables the use of multiple sources and multiple destinations natively
by the network (i.e., creating a multi-path topology to access the content).

CCN Packets:
There are two types of CCN packet :-
1. Interest Packet : Similar to \http get".
2. Data Packet : Similar to \http response".
Both are encoded in an efficient binary XML.

<img src="https://github.com/manveers96/CCN-Content-Centric-Networking-/blob/master/ccnpackets.jpg"/>

# WORKING

CCN is designed to be implemented on top of or alongside TCP/IP, so that it will
work with the current networks. Content is made available by repositories announc-
ing the names of content they serve to adjacent routers, who in turn distribute this
information into the network via the Interior Gateway Protocol (IGP).
The content matches the request if the ContentName in the interest is a prefix
to the ContentName in the ContentStore. If the content is not available, then the
router checks if it matches its list of previously sent, unanswered interests stored
in a Pending Interest Table (PIT) to ensure that a duplicate interest is not
forwarded. If the request is in the PIT, then the router adds this new interest to
the existing entry in the PIT. If no matches are found in the ContentStore or PIT,
then the router checks its CCN Forwarding Information Base (FIB) to see if it
knows any other node that might have the content available (longest match lookup
is used in cases of multiple matching entries), if so it forwards the interest to them
and adds an entry to the PIT with this information. If the router does not have
a copy of the content and does not know where to find one, then the interest is
simply discarded; the requesting application is itself responsible for re-expressing
interest if it is not satisfied (as well as dealing with cases where packets are lost in
the network).

When the name of the content is matched the node sends the content as an
authenticated data packet back on the interface that the interest arrived on. Nodes
receiving the data packet should check if it has any entries in its PIT matching the
content, if so, then the data packet should also be forwarded to the interface that
this interest arrived on. The ContentStore stores a copy of the content if desired.
When all of the content has been received, the PIT entry is removed.
Content is transferred backward across the chain of nodes that the interest packet
traversed, with each CCN capable node storing either a copy of the content or the
location it came from and removing PIT entries until the content is delivered to the
application who initiated the request for this content. Any future request for this
content can then be served by any of the nodes who have cached a copy in their
ContentStore, making the network an efficient content delivery network.
The most suitable cache replacement policies in a content caching network are
Least Recently Used (LRU) and Least Frequently Used (LFU).

<img src="https://github.com/manveers96/CCN-Content-Centric-Networking-/blob/master/ccnworking.png"/>

# Implementation procedure for Live Video Streaming through VLC plugin in CCN ----->

1) Install following packages on each node:
sudo apt-get update
sudo apt-get install libssl-dev libexpat1-dev libpcap-dev libxml2-utils vlc
openjdk-7-jdk ant git-core gcc athena-jot python-dev make libvlc-dev libvlccore-dev
libvlc-dev

2) Install CCNx on each node:
cd
git clone https://github.com/ProjectCCNx/ccnx.git
cd ccnx
./configure
make
sudo make install

3) Start CCNx daemon on each node:
ccndstart

4) Make a directory that will act as repository , providing persistent storage of
CCNx content backed by a file system:
mkdir ~/ccnDir
export CCNR_DIRECTORY = ~/ccnDir

5) Start ccnr daemon:
ccnr &

6) Add all interfaces to CCNx:
ccndc add ccnx:/ udp ip_of_system_connected
ccndc add ccnx:/ udp ip_of_h1
ccndc add ccnx:/ udp ip_of_r2
ccndc add ccnx:/ udp ip_of_r4

7) Configure vlc:
sudo sed -i 's/geteuid/getppid/' /usr/bin/vlc

8) Install vlc plugin:
cd ~/ccnx/apps/vlc
make -f Makefile.Linux
sudo make install

9) Publish content, here h1 acts as publisher, so in h1:
ccnputfile ccnx:/h1/filename srcFilePath

10) Now to access it in subscriber:
ccngetfile ccnx:/h1/filename destFilePath

11)For video streaming:
vlc ccnx:///ccnx_uri

The live streaming of video will start working after the above steps.
