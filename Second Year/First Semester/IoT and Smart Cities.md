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

