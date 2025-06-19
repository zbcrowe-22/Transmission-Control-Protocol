# Analyzing a TCP (Transmission Control Protocol) 3‑Way Handshake & Connection Teardown with Wireshark
Author: Zachary Crowe
Date: June 19, 2025

# Table of Contents

1. Introduction
2. Installing Wireshark
3. Capturing a TCP Session
4. Filtering Your Capture
5. Refresher: TCP/IP & OSI Models
6. The TCP 3‑Way Handshake
7. Connection Teardown
8. Annotated Packet Analysis


## Introduction

This guide walks through capturing and explaining a full TCP (Transmission Control Protocol) 3‑way handshake and graceful teardown using Wireshark. Whether you are refreshing your knowledge of the OSI (Open Systems Interconnection) layers or just learning how to read packet captures, the step‑by‑step instructions and annotated screenshots will help you master the fundamentals.

## Installing Wireshark

### 1. Download

| Operating System                   | Command / Action                                                                                                                                                                                                                                                                                  |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Windows**                        | Download the latest stable installer from [https://www.wireshark.org/download.html](https://www.wireshark.org/download.html). When prompted, install **Npcap** (Network Packet Capture) driver—Wireshark needs it for packet sniffing.                                                            |
| **macOS**                          | With Homebrew (package manager) installed, run:<br>`brew install --cask wireshark`<br>Grant packet-capture permissions when macOS prompts for them.                                                                                                                                               |
| **Linux (Debian / Ubuntu)**        | `bash<br>sudo apt update && sudo apt install wireshark<br>`<br>During installation choose **“Yes”** to allow non-root packet capturing, then add yourself to the *wireshark* group:<br>`bash<br>sudo usermod -aG wireshark $USER<br>`<br>Log out and back in for the group change to take effect. |
| **Linux (Fedora / RHEL / CentOS)** | `bash<br>sudo dnf install wireshark wireshark-qt<br>`                                                                                                                                                                                                                                             |
### Set Up

1. Launch Wireshark.

2. Verify that your primary network interface (e.g., en0, eth0, Wi‑Fi) appears in the capture list.

3. If no interfaces are listed, confirm Npcap or dumpcap permissions (Linux) are configured.


## Capturing a TCP Session

1. Select the correct interface (usually the one with the highest packets/s count) and click the blue shark‑fin icon to start capture.

2. Generate a quick TCP exchange in another window (pick one):

    * Terminal: curl -I https://example.com (fetches headers only)

    * Browser: Load any HTTPS site.

    * Command‑line: telnet example.com 80 then CTRL+] & quit.

3. Stop the capture after a few seconds (red square button) to keep the file small.

4. Save the capture as tcp_handshake.pcapng.

## Filtering your Capture

1. In the Display Filter bar enter:

    * tcp.stream eq 0

    * This isolates the first TCP conversation Wireshark observed.

3. Alternatively, filter by SYN packets:

    * tcp.flags.syn == 1 && tcp.flags.ack == 0

4. Right‑click any packet in your stream → Follow ▸ TCP Stream to view only that conversation and its teardown.

## Refresher: TCP/IP & OSI Models 

| # | Layer        | Common Protocols                                                                                   |
| - | ------------ | -------------------------------------------------------------------------------------------------- |
| 7 | Application  | HTTP (Hypertext Transfer Protocol), DNS (Domain Name System), SMTP (Simple Mail Transfer Protocol) |
| 6 | Presentation | TLS (Transport Layer Security), SSL (Secure Sockets Layer)                                         |
| 5 | Session      | NetBIOS (Network Basic Input/Output System), RPC (Remote Procedure Call)                           |
| 4 | Transport    | TCP (Transmission Control Protocol), UDP (User Datagram Protocol)                                  |
| 3 | Network      | IP (Internet Protocol), ICMP (Internet Control Message Protocol)                                   |
| 2 | Data Link    | Ethernet, 802.11 Wi-Fi                                                                             |
| 1 | Physical     | Cables, radio waves, fiber                                                                         |


## The TCP 3‑Way Handshake

Client                         Server
   | --- SYN (Synchronize) ---> |
   | <-- SYN-ACK (Synchronize-Acknowledge) -- |
   | --- ACK (Acknowledge) ---> |


1. SYN: Client sends a synchronization request, proposing an initial sequence number (ISN).

2. SYN‑ACK: Server acknowledges the SYN and proposes its own ISN.

3. ACK: Client acknowledges server’s ISN, establishing the connection (ESTABLISHED state).

After this handshake, data transfer can begin via PSH (Push) and ACK packets.

## Connection Teardown

A graceful close typically performs a 4‑way FIN (Finish) handshake:

1. FIN from the sender.

2. ACK by the receiver.

3. FIN from the receiver.

4. ACK by the original sender.

RST (Reset) can appear when a party aborts the connection abruptly.

## Annotated Packet Analysis  

Screenshot 1: Packet #1 – Client SYNExplain sequence number, flags, source/destination ports, MSS (Maximum Segment Size) option.

Screenshot 2: Packet #2 – Server SYN‑ACKHighlight acknowledgment number and window size.

Screenshot 3: Packet #3 – Client ACKNote transition to ESTABLISHED state.

Screenshot 4: FIN/ACK sequenceShow orderly teardown.


