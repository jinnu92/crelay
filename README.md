# crelay
#### Controlling different relay cards for home automation with a Linux software

### About
This software is intended to run on Linux systems to control different relay cards in a unified way. It provides several interfaces for controlling the relays locally or remotely. The software was designed with the following requirements in mind:  

 - simple, intuitive usage/interface
 - as little depenencies as possible
 - lightweight, can run on simple devices
 - easily expandable (adding relay card types and user interfaces)

New relay cards support can be added by providing the cards driver code for reading and setting the relays.
Currently the following relay cards are supported:  

- Conrad USB 4-channel relay card (http://www.conrad.de/ce/de/product/393905), see <i>Note 1</i> below
- Generic GPIO controlled relays


### Features
- Command line mode and daemon mode with Web GUI
- Automatic detection of communication port
- Reading of current relay states
- Setting of new relay states
- Single pulse generation on relay contact
- HTTP API for external clients (e.g. Smartphone/tablet apps)
- Multiple relay card type support  

### Not yet supported (to do)
- ThinkSpeak Talkback App
- Multiple card support
- Access control for Web GUI and HTTP API
- Programmable timers for relay actions
- Other useful things  

### Screenshots

#### Web GUI
![Screenshot](https://raw.github.com/ondrej1024/crelay/master/screenshots/crelay-screenshot.png)
<br><br>

#### Command line interface

    $ crelay 
    This is a utility to control the Conrad USB 4-relay card.
    The program can be run in interactive (command line) mode or in daemon mode with
    built-in web server.

    Interactive mode:
        crelay [<relay number>] [ON|OFF]

           The state of any relay can be read or it can be changed to a new state.
           If only the relay number is provided then the current state is returned,
           otherwise the relays state is set to the new value provided as second parameter.
           The USB communication port is auto detected. The first compatible device
           found will be used.

    Daemon mode:
        crelay -d [<relay1_label> [<relay2_label> [<relay3_label> [<relay4_label>]]]] 

           In daemon mode the built-in web server will be started and the relays
           can be completely controlled via a Web browser GUI or HTTP API.
           Optionally a personal label for each relay can be supplied which will
           be displayed next to the relay name on the web page.

           To access the web interface point your Web browser to the following address:
           http://<my-ip-address>:8000

           To use the HTTP API send a POST or GET request from the client to this URL:
           http://<my-ip-address>:8000/gpio                                                  
<br>
### HTTP API
An HTTP API is provided to access the server from external clients. This API is compatible with the PiRelay Android app. Therefore this app can be used on your Android phone to control <i>crelay</i> remotely.

- API url  <pre><i>ip_address[:port]</i>/gpio</pre>
- Method: <pre>POST or GET</pre>
- Reading relay states<br>
    Required Parameter: none
- Setting relay state<br> 
    Required Parameter: <pre>pin=[1|2|3|4], status=[0|1|2] where 0=off 1=on 2=pulse</pre>
- Response from server:
<pre>
Relay 1:[0|1]
Relay 2:[0|1]
Relay 3:[0|1]
Relay 4:[0|1]
</pre>
<br>

### Installation

* Clone git repository :  
<pre>
    git clone https://github.com/ondrej1024/crelay
    cd crelay
</pre>

* Alternatively get latest source code version :  
<pre>
    wget https://github.com/ondrej1024/crelay/archive/master.zip
    unzip master.zip
    cd crelay-master
</pre>

* Build and install :  
<pre>
    cd src
    make
    sudo make install
</pre>
<br>

##### Note 1:
The relay card software provided by Conrad is Windows only and uses a binary runtime DLL which implements the communication protocol between the host computer and the card. Thanks to a raspberrypi.org forum member, the communication protocol was discovered and made public. This made it possible to develop an open source driver for the Conrad card which can run on any Linux distribution with the cp210x kernel driver installed.

It needs the cp210x kernel driver for the Silabs CP2104 chip with GPIO support. The official in-kernel cp210x driver does currently not yet support GPIO operations. Therefore the Silabs driver from their home page needs to be used:
http://www.silabs.com/products/mcu/pages/usbtouartbridgevcpdrivers.aspx

Unfortunately the kernel internal interfaces are continuously changing and the Silabs drivers don't built just like that for any given kernel version. Therefore, for your convenience, the cp210x directory contains the patched driver sources and pre-built binary drivers for selected distros and kernel versions (currently only Raspberry Pi binaries are provided, contributions are welcome).
