#### What is the IoT?
>[!definition] IoT (IEEE definition)
>A network of items, each embedded with sensors, which are connected to the Internet.

The function of the components of the IoT are (not necessarily all on the same device):

- Sensing: Sensors mounted on end devices.
- Data Collection: Devices with sensors can collect data about the environment and transmit it to the network. Metadata is also attached to measurements. 
- Data Processing (and Visualization): Either simple rolling processing of incoming data or more complex ML algorithms. Contains also steps such as sampling, aliasing, quantization, saturation, etc. . Might also produce dashboards to visualize and monitor the process.  
- Acting: Acting upon results from the data processing to make the process more efficient. Can be remote (a human manually activates some actuator remotely) or automated (acts on its own, without a human). Furthermore when it is automated it can be either direct (works promptly on its own) or indirect (gives advice to human operators).

#### System Architecture
There can be different architectures, the most used are:

- Client/Server: The Sender knows about the destination (Receiver) of its message.
- Publish/Subscribe: Senders publish their data to public logical distribution channels, Receivers can subscribe to such channels and download the information whenever they want. No one needs to know the whereabouts of other devices.

Properites of IoT systems are:

- Trustworthiness: availability, resilience, confidentiality, safety
- Architectural: modularity, scalability, distribution, legacy support, heterogeneity
- Functional: auto-configuration, service subscription etc.

Six functional domains are created:

![[Pasted image 20241011100833.png]]

- Physical Entity Domain (PED): physically monitored and controlled objects.
- Sensing & Controlling Domain (SCD): sensors, actuators, adaptation devices.
- Operation & Management Domain (OMD): Operation Support System (OSS) + Business Support System (BSS), provisioning, monitoring, assurance and optimization of performance.
- Resource Access & Interchange Domain (RAID): interface through which the services are offered to external entities.
- Application & Service Domain (ASD): interface to act internally with actuators and sensors or externally with API through RAID.
- User Domain (UD): human and digital users’ access point.

#### Network Architecture
![[Pasted image 20241011101910.png]]

The network architecture can be mainly of three types:

- One-level architecture: The endpoints know about the addressing scheme, typically implemented with cellular network technology.
- Two-level architecture (LPWAN): The endpoints know nothing about the routing protocol, they just send messages to a gateway which handles the routing. This is used when endpoints have energy or resources constraints (i.e. sensors) and a Low Power WAN (LPWAN) is used to connect them to the gateway. Ex. LoRa, SigFox, NB-IoT
- Two-level architecture (LAN): The enpoints are not resource constrained and implement the TCP/IP protocol stack. The local network is managed by an intermediate router.

##### Topologies

WANs are usually wired TCP/IP connections that can have different topologies:

- Bus: Every node is connected to the same medium, signals might collide (need to decide who gets the bus) and we have a single point of failure.

	![[Pasted image 20241011103034.png]]
	
- Token Ring: A token circulates in the ring, when a node needs to send something it takes the token when it passes and sends the message, then resends the token in the network. We need methods to recreate the token if it breaks but we eliminate the problem of having to choose who gets the bus.

	![[Pasted image 20241011103403.png]]

- Star: A central node controls all communications (master) of the other nodes (slaves). Single point of failure on shutdown of the master. Good for small networks.

	![[Pasted image 20241011103606.png]]

- Partial mesh: There are multiple connections between nodes to avoid single points of failure, more expensive but basically better on anything else.

	![[Pasted image 20241011103757.png]]

#### Enpoints
##### Sensors
A sensor is a device that is able to detect some measurable aspect of the world around it and convert it into a processable datum.
Sensors can be divided into:

- Fixed: stable position wrt the network topology. They are good because we don’t need dynamic addressing, routing and re-authentication. Since they are fixed they can also be connected to a main power line. They can have permanent bad connectivity based on how they where deployed.
- Mobile: they need to be connected to a cellular network or LPWAN. They experience bad connectivity only sometimes but they can’t be powered by the main line.
- Nomadic: they move but stand still during communications. They need re-authentication but no tracking.

Regarding the way they collect data, sensors can be divided into:

- Active: need to run current to get a measurement, they need a battery. Ex. LiDAR, radar, ultrasonic sensors.
- Passive: use the phenomenon itself to power the measurement. Ex. temperature sensors, photodetectors, PIR

##### Actuators
Actuators are endpoints that are able to perform direct actions, they can be divided into:

- Digital actuators: they perform on-off actions.
- Analog actuators: they produce continuous signals that can drive other devices, for example motors.
##### Gateways
Devices that stand between sensors/actuators and higher levels of system processing. They aid in data collection and provide internet access. They can also provide security, sometimes data storage and rarely analytics.
##### Fog nodes
Fog nodes are thought of as “the cloud close to the ground”. The idea is to lessen the stress on the cloud and get better latency by placing additional computation close to the endnodes. They are similar to gateways but more powerful.

#### Data plane functions
![[Pasted image 20241011105736.png]]

##### Data acquisition and processing
This part is divided into many smaller components, namely:

- Sampling: Take periodic measurements and represent the real signal with those measurements. Using Nyquist theorem we know that we should sample with at least twice the frequency of the highest frequency component we want to keep track of (otherwise Aliasing).
- Quantization: Digitalize the measurements, digital values are finite and quantized. This introduces an error.
- Saturation: Be careful of saturation, sensors have an upper limit which can be physical or dictated by the highest level of quantization.
- Hysteresis: Be careful of hysteresis, we need the sensor to be in its base state before measuring something. After a measurement there is a certain amount of time we need to wait for the sensor to go back to its original state.
- Non-linearities: We would like sensor’s responses to be linear but it is usually not possible, we should be careful in treating these non-linearities.
- Calibration: Sensors should be calibrated if we expect them to measure accurately.
- Error propagation: When we use multiple sensors, combined results have errors that need to be calculated starting from the errors produced by any single sensor on its own.
##### External Data Communication
Data needs to be shared between nodes and cloud. We have two modes of doing so:

- Push/Pull: A sensor sends data to the gateway when certain conditions are met (push) or the server explicitly requests a reading of a sensor (pull)
- Publish/Subscribe: Sensors publish data to a known place and over sensors/servers subscribe to read the data.

Data doesn’t have to be sent too often (it costs energy and makes the connection busy), instead we can store cumulative values in the sensors or only transmit a reading every so often. Then we can use interpolation to reconstruct the signal.

When we spot correlations between the measurements of different sensors we can save on sending readings or increase accuracy. Correlations can be:

- Spatial: i.e. traffic at the beginning and at the end of a bridge.
- Temporal: i.e. traffic in one spot.

##### Local Control
Provided in systems where latency is critical. Enables to execute predefined if-else control sequences. For example we might want a control on reaching an extreme temperature to trigger a shutdown, whithout having to wait for the cloud to answer.
##### Data storage
Data storage might be provided by the gateway. Having such local data storage helps with preserving bandwith and only communicating data that meets certain conditions.
##### Edge Analytics
These functionalities are provided either by the Fog node or advanced gateways, they are useful to preserve bandwidth and analyze in real-time with low latency. They can operate even when disconnected from the cloud (offline).
The decision on wether to put analysis at the edge or at the center of a network depends on many variables, namely: bandwidth, latency, value of local autonomy, security, energy and computational capacity.

#### Control plane functions
![[Pasted image 20241011105736.png]]\

Fully functional gateways may be fairly sophisticated computational and communications nodes that host and execute a variety of services: they need to be properly secured and managed.
Main control functionalities:

- Fault management (troubleshooting, error logging, and recovery).
- Remote monitoring, control, administration, and diagnostics.
- Remote firmware and software updates.
- Security updates.
- Metering (network bandwidth and software usage) and supervision.
- Provisioning and authentication.

We should first identify, quantify, and prioritize the risks for the system, and find a balance between costs and potential losses from residual risks.
##### Vulnerabilities
Vulnerabilities can originate from the architecture, the technology and the protocols. However they usually originate from implementation (poor code) or operations (poor configuration).
Some security measures are:

- Partitioning the system in security zones (different security levels).
- Adopt effective authentication protocols.
- Provide cryptographic protection.
- Install only from truste providers.
- Make patches.
##### Threat
Endpoints are the weak link of the system, they should be divided into a different security zone. Possibly introducing self-disconnection on detection of security risk.
##### Provisioning and authentication
The Management part of the control plane functions manages, first of all, the provisioning and authentication of new endpoints into the network.
First of all a new endpoint right to access the network is checked during authorization, then the endpoint is authenticated using a Challenge-response protocol (using secret keys).

Usually the endpoint doesn’t have the secret key but gets it through a TTP (Trusted Third Party) which it can connect to using a public key it is given during firmware installation.
##### Metering and supervision
The management part also need to supervise the performance and integrity of the network. This is done via “keepalive” messages sent to endpoints and metering functions used to check the functioning of connections.
#### QoS and Performance
##### Delay
One way to measure the performance of the network is with delay. Delay can be of different types:

- One-way delay (OWD): Time between the transmission of the first bit of a packet at the transmitter’s interface and the reception of the last bit of the packet at the receiver’s interface. This requires synchronized time (Network Time Protocol NTP).
- Two-way delay (TWD): Time between the transmittion of the first bit of a packet a the transmitter’s interface and the reception of the last bit of the packet at the same transmitter’s interface, after being reflected by the system at the other end.
##### Packet Loss
This can happen if: 

- The buffers of a node overflow.
- There are transmission errors, almost non-existant in LANs, very prominent in WLANs.
- Delay much larger than expected is simlar to loss.
##### Reliability
![[Pasted image 20241011125753.png]]

$R(t)$ is the probability that the system is working well at time t.
##### Availability
![[Pasted image 20241011125913.png]]
Can be measured in Mean Time To Failure (MTTF), Mean Time Between Failure (MTBF) or Mean Time To Repair (MTTR).
#### Energy Constraints
Many of the devices and operations needed consume energy, let’s see them:

- Computing: consumes energy for each cycle, corresponding to one basic op.
- Boards and PLC: used in industrial automation, arduinos used for more complex tasks. Smaller boards are also designed ad hoc to spend as few energy as possible.
- Communication: all the steps of communication spend energy. Encoding and error protection, preambles, modulation and transmission (for the antenna).

Energy can be delivered in various ways:

- Mains power: cabled power grids, they provide (virtually) unlimited power but only work on fixed systems, need us to lay down cables and might be dangerous in environments at risk of fires. Also they need transformers (expensive).
- Power over Ethernet: Ethernet can also transport power in the same cable as LAN. No need for transformers and only one cable is needed for connection and power.
- Powerline communication: opposite to PoE, data carried on the power line.
- Batteries: useful to attach to sensors that need it, use rechargeable batteries, use at little as possible and attach generators to recharge the batteries (if the battery runs out completely it is often impossible to recharge).
#### Spectrum
Spectrum is a finite resource due to collision and interference. The States regulate the use of parts of the spectrum based on geographical position.

- Licensed spectrum: Part of the spectrum that is given to private companies through an auction.
- Unlicensed spectrum: Part for everyone to use, it is still regulated.
- Reserved spectrum: Used for security, emergencies or military applications.
##### Unlicensed spectrum
It is usually referred to as Instrumental, Scientific and Medical (ISM) bands. It is used by Wi-Fis, Bluetooths, etc. 
In Europe, the European Commission’s Electronic Communications Committee (ECC) places caps on two values:

- Effective Radiated Power (ERP): It evaluates the emissions of a device.
- Duty Cicle (DC): The max percentage of “on” time of the device over one hour.
#### Short range IoT technologies
With short range we address those IoT technologies that employ cells which have a range of maximum $100 \,m$ . These technologies can be further categorized according to the reach of the radio link into:

- Vicinity: some $cm$ to a couple of $m$.
- Wireless Personal Area Networks (WPANs): up to $\sim 10 \, m$.
- Wireless Local Area Networks (WLANs): up to $\sim 100\,m$.
##### Radio Frequency IDentification (RFID)
Radio Frequency IDentificiation (RFID) is a method of storing and reading data using devices such as RFID tags and readers. Tags can store the information and readers can read/write information wirelessly. These devices are very useful and versatile but come with some security criticisms.
Tags have chips in which the information is stored but can be not powered. When a Reader tries to read the info, it sends a radio wave which powers the chip and gets coupled with it magnetically. Through this coupling the information can be transferred.
The transfer distance is usually $15\%$ of the wavelenght used so that it can be adjusted between some $cm$ and some $m$. 
##### Near Field Communications (NFC)
Near Field Communication (NFC) comprehends devices that mix the functionalities of RFID readers and tags.
##### Bluetooth
Bluetooth is a technology that was developed to substitute wired connections between computer and can now connect different types of devices over short distances. There are 3 main types of bluetooths:

- Bluetooth Classic (Basic rate/ Enhanced data rate - BR/EDR): Used in audio streaming, peripheral connections and file transfers.
- Bluetooth High Speed: Used in video streaming and tethering.
- Bluetooth Low Energy: Used in wearables, location totems or smart home appliances.
###### BR/EDR
In this kind of Bluetooth devices organize into smaller networks called piconets which are combined into the whole scatternet. Each piconet can host 8 active devices and up to 255 sleeping devices. One unit works as the master and uses a polling algorithm to orchestrate the connections.
It is built onto a Radio Layer with 3 different modulation schemes:

- GFSK
- 4-DQPSK
- 8-DQPSK

It works on the $2.4 \,GHz$ band which is divided into 79 channels of $1\,MHz$ each. Around $1600\,\text{times}/s$ the frequency of the communication is changed to avoid collisions (this needs coordination).
Over the Radio Layer there is a Baseband Layer that directs communication following a Time Division Duplex which allows for bi-directional communication where each slot is alternatively used by the master or the slave.
When the connection is initialized by the Master, the pattern of the frequency hops is generated and shared.
On top of these 2 layers there is a Logical Link Control and Adaptation Protocol (L2CAP) which supports 2 types of communication:

- Synchronous Connection Oriented (SCO): a synchronous, simmetric, connection oriented service used when latency is more important than integrity.
- Asynchronous Connection Less (ACL): Sends packets that can be 1,3 or 5 slots long and that are retransmitted if corrupted. Multislot packets reduce overhead. This method is used when integrity is more important than latency.
###### BLE
Specifically created for IoT low power devices. Only has $40$ chennels of size $2\,MHz$ and only supports GPSK modulation. 
Differently from the BR/EDR case, where nodes can only be either master or slave, in BLE there are 4 communication roles:

- Broadcaster: It periodically transmits advertisement, but doesn’t allow connections to start.
- Observer: Only listens to advertisement, doesn’t try to establis connections.
- Peripheral: A node that publishes advertisement and may accept connection requests, equivalent to the Slave.
- Central: A node that listens to advertisement and decides wether to open a connection or not, acting as a Master.
##### LR-WPANs
Low Rate Wireless Personal Area Networks are a standard defined by ISO and IEC under the code 802.x
It defines the PHY and MAC layers of the protocol and it has:

- Easy installation
- Reliability
- Extremely low cost
- Support for a large number of nodes
- Support for asynchronous communication

It supports two types of nodes:

- Full Function Device (FFD): has full capabilities, can relay messages and act as PAN coordinator. Can be distributed in any topology.
- Reduced Function Device (RFD): can only talk to FFD and can’t become PAN coordinator, cannot relay data (only end node) and can only be in star topology.

Networks that employ this protocol can operate in two modes: 

- Beacon-enabled mode: The PAN coordinator sends out beacon signals periodically to inform the nodes on when to send communications and when to ibernate. This messages define two different periods, one active and one inactive. During the former, there are 16 time slots, 1 for synchronization, 10 for Contention Access Period (CAP) that allow data transmission using CSMA/CA and 5 for Contention Free Protocol (CFP) which are assigned by the PAN coordinator to nodes with specific requirements of guaranteed time slots. During the inactive part, on the other hand, all nodes should stay asleep.
- Non-Beacon enabled mode: No beacon frames are automatically transmitted, a node should ask for them to be transmitted if needed and wait for the response of the coordinator.

802.15.4 can operate in three modes of security:

- Unsecured
- Access Control List
- Secured
###### ZigBee
ZigBee is an industrial standard built on top of 802.15.4 that adds two new layers:

- Network
- Application Layers

The network layer defines how the behaviour should be depending on the topology.
For example in star topology an FDD (coordinator) ititiates and mantains communications with the other FDDs and RFDs.
In mesh topology, other nodes that never sleep, called routers, are used to relay and forward the data from the coordinator to all other nodes.

The network layer also has to do with addressing. Each node has a Personal Area Network IDentifier (PAN ID), which is a 16 bit address decided by the coordinator during setup, and a Extended PAN ID (EPID) which is the MAC address given during  production.

Finally the network layer is tasked with routing. In this realm there are two possible behaviours, hierarchical (which follows the structure defined during network creation) and dynamic (which searches each time for the best possible path).

The application layer is further divided into 3 sub-layers:

- Application Support (APS)
- ZigBee device objects (ZDO)
- Application Framework (AF)

APS has many functionalities:

- Binding: matches together two devices based on their services and needs.
- Address mapping: manages the mapping between MAC addresses and network addresses.
- Security
- Service discovery: helps communication to devices that offer services that are needed.
- Group addressing: Enables broadcasting

ZDO also has many functionalities:

- Role definition: defines roles of nodes
- Network and security management
- Device discovery and network formation
- Network address management

AF helps developement by giving a sort of API.
###### 6LowPAN
6LowPAN is a standard aimed at implementing IPv6 onto a IEEE 802.15.4 network.
The fact of being able to address IoT devices with IP addresses brings many benefits:

- Connect directly to the device through the internet.
- Easily interface with other IP networks.
- Use well-known socket APIs.
- Possibilty to connect nearly infinite devices.

Obviously we also have some disadvantages:

- IPv6 assumes that a link is a single broadcast domain, but this is not always true.
- IPv6 includes security protocols that might be too complex for IoT devices.
- IPv6 and 802.15.4 networks are not a perfect fit, for example the maximum size of a frame in the 802.15.4 is 127 bytes but the minimum in IPv6 is 1280 bytes (we need header compression or fragmentation).

In regards to routing, the topology may change rapidly and the classical IPv6 routing is resource intensive (not good for IoT). We need to develop a new routing scheme: Routing Protocol for Low Power and Lossy Networks (RPL).
This protocol works by creating proactively (not on demand) a routing topology by creating Destination-Oriented Directed Acyclic Graphs (DODAGs) that show the best path to go the the edge router from any specific node. In case of multiple routers, multiple DODAGs are calculated and the traffic is split.

In order to create and maintain the DODAG, the RPL protocol requires each node to
send the following control packets, together with their own IPv6 address:

- DAO (Destination Advertisment Object): establish the downlink path (towards leafs).
- DIO (DODAG Informaton Object): establish the upward path (towards roots).
- DIS (DODAG Information Solicitation): solicitate the transmission of DIO messages.
- DAO-ACK (Destination Advertisement Object Acknowledgement).

There are two modes of operation:

- Storing: each node stores the info of the destinations reachable via the sub-DODAG.
- Non storing: only the root stores information.
#### Long Range IoT technologies
With long range IoT technologies we mean those technologies that make use of networks that span several kilometers.
Usually in these cases a cellular network is used which has high complexity and infrastructure cost and is not designed for energy efficiency.
For certain IoT applications, Low-Power Wide Area Networks have been developed that jointly optimize the combination of energy efficiency, data transfer potential, range, etc.
Usually the optimization tradeoff leads to systems which have:

- Large coverage (up to 10 km)
- Low communication
- Very high energy efficiency (up to 10 years)
- Low data rate per device, but high amount of devices
- Support mostly for static networks
- Easy implementation and deployment
##### Sigfox
First example of LPWAN developed by omonimous french company.
The idea was to make local operators manage the communication between the nodes and provide a subscription-based service called Sigfox cloud platform for data storage and processing.
For the PHY layer, Sigfox defines a proprietary Ultra-Narrow Band (UNB) technology where the energy is concentrated into a very small portion of the bandwidth. This leads to limited data rate but makes the system interference immune and less affected by noise.
The signal is trasmitted using Differential Binary Phase Shift Keying (DBPSK) which means that the signal flips phase if the bit is a 1 and doesn’t if the bit is a 0.

The size of information that can be transmitted is very small, only 72 bytes/h.

The transmission is initiated by end devices which wake up, send data, wait for possible ACK or ERR messages and go to sleep.
##### LoRa
LoRa stands for LOngRAnge, it was developed in france in 2009.
LoRa only implements the PHY layer, the MAC and higher layers are implemented by LoRaWAN.

Supports from 24 to 80 channels of 125 KHz each. The network operator can decide how many to implement, but needs at least 3 (868.10,868.30,868.50) for join requests.
The maximum payload is 230 bytes.

It uses a Chirp Spread Spectrum technology (CSS) to send the data in a way that is resilient to frequency specific noise. This method works by defining a Spreading Factor (SF) that defines the number of different messages you can send as $2^{SF}$ (in LoRa $SF=[7,12]$). Each message spans the entire bandwidth in the allocated time which is based on $SF$ and has a different shift based on which value is sent.

![[Pasted image 20241110121415.png]]

Incresing $SF$, also increases the number of bits per symbol linarly but increases exponentially the time needed to transmit one symbol. All in all, the bit rate decreases when increasing $SF$ but distance travelled increases.

Furthermore, $SF$’s are pseudo-orthogonal and don’t interfere with one another. Also, there is no interference in two communications with a difference in power of $6 dB$ even if they have the same $SF$.

The $SF$ is chosen dynamically based on the distance to the nearest gateway, it should be big enough to reach but not too big to increase consumption.

The structure of the message includes:

- Preamble: 8 symbols, used to notify the gateway that a message is coming.
- Mandatory preamble: 4.25 symbols, used to sync with gateway.
- PHY header: 8 symbols.
- Payload: 255 bytes.
- CRC: 0-2 bytes.

LoRaWAN implements higher layers such as MAC and Application.
The architecture defines 4 types of devices:

- End nodes: End devices carrying out sensing and actuating tasks. They have a 64 bits address DevEUI. 
- Gateway: Wireless base stations used to receive communications from end nodes in a star of stars topology. They have a 32 bit address DevAddr.
- Network server: Eliminates duplicates and routes data.
- Application server: Selects the best gateway to reach each end node.

The MAC layer defines 3 different classes of End nodes:

- Class A: Devices that always initiate communications on their own. They send a UL message and open two short received windows for communication (the second is openend only if the first doesn’t get a response). They have very low energy consumption but also high latency in communications.
- Class B: Devices that have bi-directional communications. They work like Class A but can also receive pings from the gateway to initiate a communication. Lower latency because an UL is not necessary, higher energy consumption.
- Class C: Devices in continuous receive mode. The RX2 window is open until the next UL or activity is present on RX1. Very low latency but very high energy consumption and can’t work only on battery.
##### NB-IoT
Since cellular networks are everywhere and are constantly being updated, trying to use them for IoT as well is a promising path. We can move forward in two ways:

- Adapting existing cellular solutions and protocols, which require lower investment but are also not really suited for IoT because of the protocols in place.
- Specifically design new ways to use existing infrastructure.

From the second option we obtain Narrow Band-IoT (NB-IoT) which is based on the design principles of 4G-LTE and is standardized by the 3GPP which usually are in charge of cellular mobile standards.

An NB-IoT network can work in different modes:

- Standalone: It operates inside of the frequencies previously used for GSM, which is being less and less used. It occupies 180 kHz which leaves 10 kHz per side in the GSM band.
- In-band: Occupies one of the 180 kHz slices of the existing LTE band.
- Guard-band: Occupies 180 kHz in the region where guard bands for LTE exists, not taking away anything from the LTE.

The PHY layer is the same as LTE. The time is divided into fixed slots of 10 ms, each divided into 10 1ms subframes, each divided into 2 0.5ms slots. Frequency-wise the Physical Resource Block (PRB) is formed of 12 subcarries of 15kHz, for a total of 180kHz.

During uplink we can use any combination of slots and subcarriers.

The MAC layer introduces Power-Saving Mode which consists in dropping parts of the protocol stack in the end nodes to decrease power consumption. When doing so, the network retains the information of the end device and keeps it registered.
This layer also implements Extended Idle mode DRX which allows a device to enter sleep mode while still monitoring the network for incoming data.

Repetition is also implemented dynamically (thanks to the calculation of an Enhaced Coverage Level ECL) based on the needed signal strength. This improves sentitivity because of coherent addition of repeated messages and inchoerent addition of random noise. Repetition also slow the process of transmission.
##### Industrial Ethernet
Ethernet can be used in industrial settings, and is useful thanks to its speed. Some modifications are needed in order to use it in industry.
Generally, Ethernet is not deterministic and doesn’t support Real-Time operations. Under this aspect we can divide Industrial Ethernet into 3 classes:

- Class 1: Ordinary Ethernet + TCP/IP stack. This has full compatibility with standard Ethernet networks but needs looser requirements on Real Time (latency $\geq$ 100 ms).
- Class 2: Ordinary Ethernet + Custom Apps. This needs a specific protocol stack that allows for Real Time operations (latency $\geq$ 10 ms). It is not compatible with IP network.
- Class 3: Modified Ethernet + Custom Apps. This has no compatibility with IP and no compatibility with standard Ethernet (needs specific modified Ethernet equipment). However this supports hard Real Time (latency $\geq$ 1 ms).

One application is synchronization. We do this by usisng IEEE 1588 over Ethernet, which works in the following way:

![[Pasted image 20241110144151.png]]

Another problem in applying Ethernet to industry is that classical Ethernet doesn’t take into account the requirements for an industrial scenario, namely: power consumption and limited space.
Single Pair Ethernet uses only a pair of twisted wires instead of the usual four. It has much lower data rate but higher maximum length and lower cost.

To avoid collisions, the master periodically sends an order of the cycle of transmissions and the slaves communicate or pass when it’s their turn, like in a ring token topology.

A widely used version of Industrial Ethernet is EtherCAT. It is a version that falls into Class 3, which means it has its own specific hardware to support Hard Real Time. It has Distributed Clocks thanks to a variant of IEEE 1588. The architecture employs a Daisy Chain Ring (bidirectional) which has deterministic communication.
#### IoT Applications
Applications that complement TCP and UDP to support more complex scenarios.
There are various reasons to implement middleware into these techonologies: heterogeneous types of data, different communication models, complexity of storage, real time requirements, etc.
##### HTTP
The World Wide Web is a distributed client-server service in which a client can use a browser to access pages through a server.
A browser is usually composed of three parts: a controller, client protocols and interpreters.
Documents and files are stored in servers which provide them when requested by a client.

Each page is identified by a Uniform Resource Locator (URL) code which contains information about the protocol to use, the host, the port and the path. Often the port is omitted so that the URL is in the form: protocol://host/path

One of these protocols is the HyperText Transfer Protocol (HTTP) which defines the way client-server programs can be written to request pages from the web. A client sends an HTTP request and a server sends back an HTTP response.

HTTP is built using TCP services so it is reliable. If Transport Layer Security is implemented on top of TCP, then HTTP becomes HTTPS.

HTTP requests can be of various types as seen below:

![[Pasted image 20241123194132.png]]

HTTP, however, is not very suited for IoT applications for a number of reasons:

- Very generic protocol, while IoT has very specific requirements.
- It is a client-server model, it can’t work as an event-based model.
- It is very verbose.
- Too many sophisticated options which are not necessary in IoT.

Light HTTP are proposed, such as HTTP/2 and HTTP/3 but they don’t seem a good solution either.
##### Websocket
For IoT, a Websocket is defined as a combination of two parts:

- A method to open a bidirectional TCP connection to be used via HTTP.
- A minimal set of allowed messages which are just enough for the application.

With Websocket, the client sends a GET request, the server responds and the bidirectional connection is initiated. The connection can support unsolicited events from both sides but the model is still client-server.

Websocket employs a minimal header, for efficiency, that only occupies 2 bytes. One bit for a finish flag (if the message is the last), 3 reserved bits, 4 opcode bits that allow to inidicate the type of message, 1 bit for a flag indicating if the message contains a mask or not and finally 7 bits for payload length. If the payload length is not suited to be into the 7 bits, they indicate 126  or 127 which means that the next 2  bytes or 8 bytes are for the payload length.
##### MQTT
Message Queuing Telemetry Transport is an example of an IoT-specific protocol. It was specifically thought of as a protocol for sensor event collection.
It is usually layered on top of TCP or TLS. It implements the publish-subscribe paradigm, in which a publisher sends a message to the brokers which then forward the message to all subscribers.

![[Pasted image 20241124000559.png]]

The Remaining Length part contains the sum of the dimension of the rest of the message in a data type called Variable Byte Integer. This method uses 1-4 bytes to store the information. In each byte only the last 7 bits are used for a numbe, while the first is used to say wether there are other bytes later or not.

Packet type is about the type of message that is being sent, for example Connect, Connack, Subscribe and Publish. Let’s see them:

- Connect: The variable header contains the protocol name, version, properties, flags and keepalive. Between the flags we have Username, Password and Clean start which are self explanatory. The Will retain flag is used to make the broker remember the message, if it is the last one arrived. This is useful so that new subscribers don’t have to wait for the next message but get one instantly. The Will message flag is to indicate that a message should be sent out to subscribers if the connection terminates ungracefully. The KeepAlive value in the header tells the most amount of time that can pass between two messages, since TCP is  known for having problems in one-sided discconnection.
- Connack: It is sent by the server after a conncetion attempt to inform about the status of the connection. It has no payload. There is a Reason Code part where the code relative to the status of the connection is written.
- Subscribe: It is a request of a client to be subscribed to a certain topic. It is followed by Suback, same with Unsubsrcibe and Unsuback. There is a part that signals whether or not to send Retained messages to the new device. There is also a QoS value which determines the maximum QoS that the server can use when forwarding messages to this device. It can be used to subscribe to multiple topics at the same time.
- Publish: It is used when a client is puclishing data to a topic and also when the server is forwarding the data to subscribed clients. It is followed by Puback, Pubrec, Pubrel and Pubcomp. It contains a DUP flag that signals whether the message is a resent duplicate or not. A QoS value which determines the QoS value of the message. Also contains a retain flag and the topic name.

We talked about a certain QoS level. If the subscription is initiated with level 0, then when sending messages from the topic, the messages are sent only once by the serve, not stored and retransmitted (sender doesn’t wait for acknowledgement).
If QoS level is 1, a message is stored after being sent until an acknowledge message is received by the server, otherwise it is retransmitted.
In QoS 2 the package is stored until a PUBREC is sent back, then the copy of the packet is cacelled and the server sends a PUBREL to release the index of the message, if the receiver sends back a PUBCOMP then the server frees the ID. 
##### CoAp
Another example of an IoT-specific protocol is the Constrained Application Protocol.
It is usually built on top of UDP and allows scarce resource devices to interact with the wider internet. It mixes concepts of client server paradigm and subscribe publish.
It allows clients to contact CoAp or HTTP servers and also allows peer to peer communications.
Since it is built on top of UDP it requires error handling. The messages are more compact than HTTP but not than MQTT.

The structure includes a Version value, a Type value which can indicate if and ACK is needed for this Request or if the message is an ACK or an error if it is a Response. It also has a code relative to the type of request or response, a message ID and a token needed to match requests to their responses.

The thing that is particular about this protocol is that a client can observe the status of a resource. This can be done with a GET message with the observer flag set to 0, which gives back a series of messages containing the measurements of the resource.
### Cloud
In IoT systems, the cloud is the point of aggregation and processing of data. It generally is composed of different parta:

- Storage
- Computing
- Analytics
#### Cloud Computing
Cloud computing allows users to access remote resources such as storage, networks and services. These resources are virtualized and can be adjusted dynamically to adapt to changes in load/demand. External access points are provided.

Cloud services can either be:

- Public: Everyone can access to subscription
- Private: They are dedicated to a specific company and tailored to them. More privacy but generally less resources.
- Hybrid: Both paradigms exist.

Virtualization is an important concept that allows for decoupling of hardware and software making it possible to run different virtual machines on a single physical machine. These virtual machines can also be created dynamically and scaled at will.

Clouds are usually spread on different data centers in different places/countrie. This is good for latency, redundancy and privacy regulations.
Some negative characteristics are lower availability wrt local computing, higher latency, no propriety.

There are different paradigms of cloud computing, namely Infrastructure as a Service (IaaS), Platform as a Service (PaaS) and Software as a Service (SaaS).
##### Infrastructure as a Service
In this case the company only provides the physical resources (networking, storage) and the client is responsible of everything else, namely OS, applications and monitoring/patching.
The benefits are that it is less expensive, more flexible and allows for more control, the drawbacks are that it needs internal training and security.

Some such providers are AWS, IBM cloud, Azure, etc.
##### Platform as a Service
The platform is accessible through the internet and provides tools to create applications specific to the client organization’s needs. The provider takes care of updating and monitoring the system. Benefits include less costs, scalability and freedom. Drawbacks include security, intergrations.

Some such providers are Google App Engine, OpenShift.
##### Software as a Service
The provider provides the entire application for it to be used by clients as is. Benefits include less code, cost reduction, ease of use, automatic upgrades and scalability. Drawbacks inclued no control, security.

Some such providers are Salesforce, Dropbox, Slack, etc.
#### Cloud Components
An IoT cloud system usually contains:

- Edge interface for data injection.
- Time-series data stream processing.
- Data aggregation and storage.
- Security and management.
##### Cloud edge gateway
It is the boundary of the cloud, the part closest to the edges.
Incoming data can be sensor readings, Notifications or timestamps and metadata.
Since it is the entry point to the cloud it carries out authentication, access control and filtering.

After being received, data is directed to the correct place in the cloud following either a warm path (through stream processing) or a cold path (through storage).

The outgoing data can be actuations or configurational info.
##### Stream processing
It is applied to data when some kind of operation needs to be carried out as soon as the data is received. It can detect anomalous events or perform transformations and aggregations.
##### Storage
Incoming data can also be stored in batches for a variety of applications, such as ML training, or for long term comparisons or auditing.

Storage can be short-term (hours or days) to get fast access to recent history or long-term in which data is aggregated and downsampled for trend analysis or archieval purposes.

IoT data is fast, huge, heterogeneous, and possibly wrong or missing some parts.

Data is usually stored in a Database and an API queries the database to retrieve results.

Initially RDBMS were used but due to the missing structure of the IoT data, NoSQL databases work better.

One of these types of databases is key-value store where data is saved as pairs of a unique key and a value which can be whatever we want.
Key-value pair databases are easier to define and adapt in case of structure modification, faster and scalable. However, they don’t have a querying language like SQL, they might be too simple for some applications and don’t allow for filters since the data is stored as a block.

Another NoSQL database is Document-oriented database. This is the same as a key-value but the value is a document which is written in metadata (i.e. json) so that a structure can be inferred. They have a querying language and allow for different types of document contents. These databases are very flexible but trying to create connections between documents hinders the performance.

Yet another NoSQL is column databases. These look like normal databases but have a dynamic schema and data is stored in columns and they are optimized for petabytes of information spread across different servers. They are scalable and responsive but the update of columns is slow and so are row-specific queries.

There are also Graph-based databases. They represent well network structures and allow for spotting trends, but are bad for scalability.
### Cloud Analytics
Data can be analyzed for a variety of reasons, namely gaining insight on the system, predicting future behaviour, understanding what is happening at the present time or define a course of action to get to a desired result.

A big part of analysis is showing the results to humans for them to take action. This is done in data visualization and usually shown in a dashboard.

Analytics systems have an endless number of applications, such as monitoring and keeping in line temperatures in buildings. To adress this and many other problems, a combination of handwritten programs and machine learning is used.
### Security
Since IoT devices and networks are more and more present in the world around us, naturally there are security concerns regarding them. These concerns come from different reasons:

- IoT devices are deployed in a distributed fashion and communicate across long distances.
- IoT devices can impact the health and safety of people by actuating something in the real world.
- IoT devices are constrained, which makes it harder to implement security on the device.
- IoT devices are often deployed in unprotected places.
- IoT devices may deal with confidential data.

However, IoT devices also have some useful characteristics in this regard:

- They usually are very simple devices, not whole computers with multi purpose systems.
- We can restrict most of the useless and possibly dangerous things that they can do (close unused ports, allow only to communicate with specific other devices removing os features for remote access).

The things we want to keep an eye on are:

- Security: allow the collected data to be seen only by authorized parties.
- Integrity: secure that the data is untampered. 
- Availability: ensure that data is available whenever needed by authorized parties.

Attcks can roughly be devided into three parts:

- Physical attacks: Substituting or cloning a node, breaking one, tampering with data collection physically.
- Software attacks: Viruses, Worms, Trojans, Code injections, phishing.
- Network attacks: Sniffing or eavesdropping, spoofing, man-in-the-middle attacks, DoS, Sinkhole (a node says it has resources and eats all messages), Wormhole (two nodes say they are close and all other nodes choose to pass through them).

The most important properties an IoT system needs to have are Safety, Reliability and Resilience.

Saltzer and Schroeder outlined general design principles for protection:

- Least privilege: Every user should use the least privileges necessary for a task.
- Separation of privilege: satisfy more than one condition (e.g., 2 keys for a vault).
- Least common mechanism: minimize the number of mechanisms shared among users.
- Economy of mechanism: keep the design simple.
- Complete mediation: every access should be checked for authorization.
- Fail-safe default: the default condition should be lack of access.
- Open design: the design should not be secret (do not rely on the ignorance of attackers).
- User acceptability: the mechanism needs to be user-friendly.
- Work factor: stronger security measures should make attackers work harder.
- Compromise recording: system keeps attack records to discover unauthorized use.

Let’s do some definitions:

>[!definition] Threat
>A threat is any activity or event that causes an unwanted outcome.

>[!definition] Vulnerability
>A vulnerability is a software or hardware bug or misconfiguration that some malicious individual might exploit.

>[!definition] Risk
>The possibility that a threat agent will exploit a vulnerability to damage an asset. The amount of risk that a vulnerability presents depends on:
>- Number of systems afftected.
>- Criticality of the affected systems to the whole systems. 
>- Exposure that the attacked systems present to the organization.

Since risk cannot be avoided completely, there are different approaches to risk mitigation:

- Risk avoidance: Try to eliminate risk by eliminating exposure (for example eliminating nonessential features).
- Risk acceptance: Avoid wasting resources on protecting from very rare attacks or attacks that can be easily managed.
- Risk transferance: Transfer the burden of the risk to someone else (i.e. insurance).

The work flow for risk mitigation includes modeling the system by thinking about its components and how they interact, enumerate threats (from the STRIDE model) and finally mitigating and validating the mitigations.

The known types of threats are summarized by STRIDE:

- Spoofing
- Tampering
- Repudiation
- Information disclosure
- Denial of Service
- Elevation of privilege

and their threat level is measured by DREAD:

- Damage potential
- Reproducibility
- Exploitability
- Affected users
- Discoverability

A common solution to confidentiality, integrity and authentication is cryptography. It can be symmetric (use same key for encoding and decoding, needs to pass keys) or asymmetric (different ancoding and decoding keys, private and public).
The idea is that every node creates a couple of keys, one (public) is used for encrypting and one (provate) for decrypting. Then Sender A decrypts message with its own private key, and encrypts it with Receiver B’s public key, then B decrypts with its own private key and encrypts with A’s public key to get the original message.

IoT devices are constrained so we should use Lightweight Cryptography and only use computationally intense asymmetric cryptography to exchange symmetric keys.

Since one might not want to setup a whole communicating system just to exchange symmetric keys, nodes can compute the commonly agreed upon key using a program that they both have, of which they can each exchange partial outputs across an unsecure connection. Anyone in the middle doesn’t have the algorithm and can’t infer the key just from partial outputs.

The double transformation cryptography has all the properties we want, but some other methods don’t have Authentication, for example. The Message Authentication Code (MAC) is a code that is computed using a shared private key and a common knowledge algorithm by the sender, the computation of the code makes it impossible to change the message without changing the MAC. A common way is to use hashing.

Since IoT networks are very much distributed, a good part of the security is related to endpoints. Endpoint security can be accomplished in many ways, generally divided into:

- Hardware
- Software

Hardware endpoint security can be provided, for example, by Hardware Security Modules (HMS) which are dedicated devices, with their memory and processor which are used to provide high levels of security. They usually provide safe isolated environments to manage security. They can be implemented in various ways:

- Trusted Platform Module: consists in an additional chip which is embedded into devices and provides a secure processor for cryptographic calculations and for storing keys. Can also store good copies of software to check that it is not tampered with by anyone.
- Trusted Execution Environment: Instead of having a different processor, as in TPM, we just divide the original processor into a Normal part and a Secure part.
- Secure Element: It is a tamper resistant hardware component that has its own processor and memory.

Since IoT networks are implemented using a layered design, we can secure communications at different layers, with different pros. If we have CoAp at the application layer, it doesn’t provide security. For this reason we either implement security before CoAp incapsulation or at a lower level.
If security is applied at the application layer, we can guarantee end to end protection and simplify the work for underlying layers.
If instead we implement security at transport/network layer we can reuse it for multiple applications.

Transport Layer Security (TLS) is a way to implement security at the transport layer and is designed to work with TCP. It:

- Authenticates the endpoints and defines keys.
- Exchanges confidential data with symmetric encryption.
- Authenticates messages trough hashing.

The first thing it does is the Handshake which is used to set up secure comunication, then the Record protocol is used that uses the exchanged information during the Handshake to secure the communications.

Datagram Transport Layer Security (DTLS) is another way to implement security at the transport layer, designed to work with UDP. It causes overhead so packet optimization and compression mechanisms are used. It creates point-to-point secure associations not compatible with multicast IP comms and also provides three modes of security:

- PreSharedKey: Devices store pre-shared symmetric keys.
- RawPublicKey: Devices have asymmetric key pairs but not certified.
- Certificate: Devices store certificates.

Internet Protocol Security (IPsec) provides confidentiality, integrity, data-origin authentication and protection against replay attacks. This protocol suite includes two principal protocols: Authentication Header (which provides source authentication and data integrity) and Encapsulation Security Payload (which also provides confidentiality).
Also, IPsec can operate with two different packet forms: 

- Tunnel mode: IP packet is incapsulated into another IP packet so routing nodes only see the info of the outer packet.
- Transport mode: normal, but is less complex than tunneling.

Before communicating with IPsec a Security Association (SA), which is unilateral must be created. This SA contains info about wheter it is an AH or a ESP SA, the type of encryption, the type of integrity check. 

