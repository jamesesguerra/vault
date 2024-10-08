If the network layer is for delivering a packet from one host to a destination host ie host-to-host communication, the **link-layer** has a much more localized task of moving packets between nodes in the same network / LAN. In other words, the network layer decides where to forward packets, and the link-layer is responsible for getting the packet over a communication link to another physical adjacent node on the network.

You can then say that the link-layer is what connects interfaces, whether using some wired / wireless technology. It allows devices on a network to talk to each other **without** going through a L3 switch.

**Analogy:**
A travel agent plans a trip for a tourist from PH to JP. They make reservations for a car, bus, and a plane. Even though the transportation modes are different, they all have the basic responsibility of moving the tourist from one location to an adjacent location.
- person - L3 datagram
- transport segment (PH to JP) - communication link
- transportation mode (car, bus, plane) - link-layer protocol

#### Services
- move a datagram through a link from one node to an adjacent node
- framing / encapsulation
- channel access when medium is shared
- reliable data transfer mainly for links with high bit errors
- error detection and correction using parity bits, CRC (error-detection bits)

Each device that implements the link-layer can be called a **node**, and each path that connects one node to another adjacent node is called a **link**. Therefore, the job of the link-layer is to move a datagram from source to destination across all the individual links in the end-to-end path. Each node encapsulates a datagram in the PDU of L2 which is a **link-layer frame**.

#### Where is L2 implemented?

L2 is mainly implemented in hardware in a **network adapter** or **network interface card (NIC)**. At the heart of a NIC is the **link-layer controller**, usually in a single, special-purpose chip that implements may of the L2 services. It connects to the computer via a PCI bus and looks much like any other I/O device.

On the sending side, the link-layer controller takes a datagram stored in host memory by higher layers of the stack, encapsulates it in a frame, and transmits the frame into the communication link. 

The software side of L2 is implemented in the host CPU, which performs higher-level L2 tasks such as assembling link-layer addressing information and activating the controller hardware. On the receiving side, the L2 software is responsible for responding to controller interrupts (controller telling the CPU to alter its sequence of instructions in response to a frame arrival), and passing the frame up the protocol stack.

![[nic.png]]



