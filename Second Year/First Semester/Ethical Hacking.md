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