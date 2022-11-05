# Networking Refresher
\begin{minipage}{\linewidth}
    \centering      
    \includegraphics[width=\linewidth]{Figures/2\_net\_components.PNG} 
\end{minipage}

* **Autonomous System (AS):** Internet is a network of networks. ASes only exchange information at edges, internal topology hidden. (Advantages: internal infrastructure and protocols are independent, more efficient routing)
    * Internet service providers (ISP)
    * Global backbone networks (CenturyLink, Verizon)
    * Universities, large companies (Google, Cloudflare)

## Layering / Encapsulation

* **Protocols and Layers**: main structuring method used to divide up network functionality
    * Each instance of a protocol uses only the services of the lower layer but should not look   at data from higher protocols (violated in e.g. NAT)

* **Encapsulation**: mechanism used to effect protocol layering. Lower layer ("envelope") wraps   higher layer ("letter") content. Each layer adds its own header.
    * Advantages: Higher layers do not need to worry about low-level details. Possibility to     connect different systems.
    * Disadvantages: Some optimization potential is lost. Often Layering not strictly followed.

\begin{minipage}{\linewidth}
    \centering      
    \includegraphics[width=\linewidth]{Figures/2\_Layers.PNG} 
\end{minipage}

* **Physical/Link Layer**: less relevant for this lecture.
* **Internet Layer**:
    * ***Internet Protocol***: Internet Layer, IPv4/IPv6 64/128 bit addresses
        * End hosts specify source and destination.
        * No control over paths.
        * Internet Control Message Protocol (ICMP) for error messages.
        * Allocation and Ownership of IP addresses handled by Authorities (IANA)
    * ***Border Gateway Protocol (BGP)***: Internet Layer
        * Border Gateway Protocol (BGP) for global routing (interdomain) and IP for intradomain routing. Glues the internet together.
        * Business relationships shape topology: An AS only forwards traffic where it can earn money from someone.
    * **Reminder: NAT** (Network Address Translation) connects an internal network to an external network. Many internal hosts are connected to same external IP address. NAT keeps track of which internal host is connected to which of the NATs external ports.

* **Transport Layer**: 
    * ***Transmission Control Protocol (TCP)***:
        * Deliver data to correct application
        * Split data into packets (segmentation), reassemble at destination (reassembly)
        * Make sure data is unchanged and arrives in order (ACK's, checksums, sequence nrs)
        * Do not overload receiver (flow control with sliding window sl. 66)
        * Do not overload the network (congestion control sl 68.)
        * TCP Three-way handshake and connection release (sl. 62)
    * ***User Datagram Protocol (UDP)***
        * Only delivers packets to correct application.
        * Checksum for error detection
        * No segmentation, no retransmission, no flow control, no congestion control.
        * No connection establishment, very little overhead but no reliability.

* **Application Layer**: 
    * **Domain Name System**:
        * Translates between human-readable host names and IP addresses.
        * Hierarchical name space 
        * Distributed operation
        * Recursive Resolvers: Resolve the domain name recursively and make extensive use of caching at every step.

## Routing / Forwarding

* **Routing**: (part of Control Plane) is the process of discovering best  routes in network. (network-wide, expensive, performed ahead of time)
* **Forwarding**: (Data Plane) is the process of sending packet on its way (local computation for node, very fast)
* **Rules of Routing Algorithms**:
    * All nodes are alike; no controller.
    * Node can exchange messages concurrently with neighbors to gain knowledge of topology.
    * There may be node/link failures

* ***Distance-Vector (DV) Routing***:
    * Simple early routing approach
    * Distributed version of Bellman-Ford algorithm
    * Works but very slow convergence after some failures.

* ***Link-State (LS) Routing***:
    * 1. Nodes flood topology in the form of link-state packets
    * 2. Each node computes its own forwarding table (Dijkstra's alg.)
    * Faster convergence, supports multipath, but more state and computation required.

* ***Path-Vector (PV) Routing***: 
    * Extenstion of DV routing, where full paths are included. Overcomes issues of DV routing.
    * E.g: BGP

## Reliability and Security

* **Reliability**: Checksums and error-correcting codes can detect accidental errors. Can be corrected through error correction or retransmission.
*  **Security**: Additional mechanism are necessary to prevent malicious modifications (MACs, Signatures). Most original networking protocols only had reliability but not security.

