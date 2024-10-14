>[!definition] Network Security
>Network security is the protection of the underlying networking infrastructure from unauthorized access, misuse, or theft. It involves creating a secure infrastructure for devices, applications, users, and applications to work in a secure manner.

#### Sniffing
Sniffing is a technique where the attacker overhears a live communication between devices in a network. Using this the attcker could get useful information to log into the system etc. Access to the transmission medium is required.

![[Pasted image 20241011150824.png]]

Usually a machine is connected to the network via a physical component called Network Interface Card (NIC). When a packet is received the NIC checks if the intended MAC address is the same as the current machine, if it is the message is forwarded into the machine, else it is discarded. If NIC is set in promiscuous mode all packets are forwarded into the machine.
We can get lots of info about the network this way.
#### Spoofing
When sending messages into a network we usually have control only over a small set of fields in the message. For example when sending an IP packet we can only fill in the receiver address and the sender’s is autofilled with the IP of our machine. An attacker could modify those fields and insert fake information in messages.
##### Smurf Attack
A smurf attack is a kind of spoofing where an attacker forges an Internet Control Message Protocol (ICMP) packet using the victim’s address as the sender’s. The packet is sent into a broadcast address and every other machine in the network sends an ICMP packet response to the victim, overwhelming it.
#### Layer 4 attacks
Layer 4 attacks are a series of attacks that is related to layer 4 protocols, used in communication. These group of protocols comprehend:

- Transmission Control Protocol (TCP): A connection based protocol that includes error recovery.
- User Datagram Protocol (UDP): A connectionless protocol, with smaller overhead but not error recovery.

In TCP, for example, we have a 3 way handshake to establish the connection and a Reset (RST) packet to terminate it.

![[Pasted image 20241014144541.png]]

Let’s see some of the attacks that exploit TCP packets:
##### SYN Flooding
It consists in the attacker sending multiple SYN packets to the server using an IP previously spoofed to avoid getting caught. Then the server answers with SYN/ACK but the attacker never responds leaving connections open and blocking others to connect to the server.
##### RST Attack
The attacker sniffs connection port and address of the victim and sends a RST packet to the server disguising himself as the victim. Every packet the victim sends to the server is now discarded.
##### TCP Session Hijacking
This is an attack that works because of the fact that every communication between server and client contains a progressive sequence number. If the attacker can sniff that sequence number it can send a spoofed packet with the next number and cut out the victim from the connection (since the victim is now late on the sequence number update).

To avoid these attacks we should:

- Use random initial sequence numbers to prevent their guessing by the attackers based on the number of exchanged messages.
- Use SSL/TLS instead of TCP.
- Use encryption to avoid packet sniffing.
- Use anomaly intrusion detection to identify intrusions.
