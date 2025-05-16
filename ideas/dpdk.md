---
title: Ideas - dpdk
date: May 02, 2025
tags: [dpdk, SRIOV]
---

Glossary

DPDK, PMD, DRIVER, rte_mbuff, sw_ring, hw_ring, SRIOV, virtio, MAC LAYER, queue, PHY, TX, RX, lCore, physical address,
virtual address, producer-consumer patterns, run-2-completion, QEMU, vfio-pci, VF ( virtual function), PF ( physical function), KVM, modprobe, lspci, Hugepages, Ingress, Egress

How is sw-ring and hw-ring buffers have same address ?
mbuf->buf_iova (IO Virtual Address = physical address, on x86) is stored in the hw_ring descriptor.
hw_ring has DMA'able memeory region mapped from memzone. All the allocated are maintained in the cache.

Packet Reception Flow
---------------------
NIC writes received data to the buffer at the physical address from hw_ring.
Application calls rte_eth_rx_burst().

The driver:
Checks status bits in the descriptor ring.
Uses the sw_ring[i] to retrieve the rte_mbuf (VA) corresponding to the data just received.
Sets data_len, pkt_len, and other fields in the rte_mbuf.
Returns this mbuf to the application.
✅ This allows zero-copy: the NIC wrote directly to the mbuf buffer, and the app gets the pointer immediately.


mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge
echo 1024 > /proc/sys/vm/nr_hugepages


SRIOV

# Create 2 VFs on PF
echo 2 > /sys/class/net/enp3s0f0/device/sriov_numvfs

# Bind VF to vfio-pci
dpdk-devbind.py -b vfio-pci 0000:03:10.0


VHOST 
======================
#vhost-user: A user-space interface that allows a user-space application (like DPDK or QEMU) to interact with the vhost framework in the kernel. This is useful for fast packet I/O in virtual environments like KVM and QEMU.

#vhost-net: The kernel-side implementation of the vhost framework, providing networking functionality (e.g., for virtual NICs) to virtual machines using virtio.


TAP interface
-------------

Why Use a TAP Interface with DPDK?

Interop with Linux stack	

If a DPDK app needs to exchange packets with Linux applications, you can use a TAP interface to bridge DPDK and the kernel stack (e.g., for ssh, ping, iptables, etc.).

Testing / Debugging	

Useful to generate or inspect traffic using tcpdump, ping, or iperf via the Linux stack, while still using DPDK.
Hybrid I/O model	In some setups, you might combine fast-path DPDK I/O with slow-path kernel processing (e.g., control plane packets go via TAP).

Virtual Machine Integration	

If DPDK is running on the host, it can connect to a VM’s tap-backed interface to send/receive traffic into a guest.

Traffic Redirection	

You can redirect specific traffic from DPDK to Linux for routing, NAT, or firewalling (e.g., via tc, iptables, netfilter).

------------------------------------------------------------------------------------------------------------------------
How It Works

A TAP interface acts like a virtual NIC that appears in the Linux kernel and passes packets to/from user space.

With DPDK, you can create and bind to a TAP interface using the rte_eth_tap PMD (Poll Mode Driver), which allows the DPDK app to send and receive packets via that TAP device.

Egress
[Linux Application] ⇄ [Linux Kernel] ⇄ [TAP Interface] ⇄ [DPDK App]

Ingress
[DPDK App] → [TAP PMD / eth_tap device] → [TAP Interface] → [Linux Kernel Stack] → [Linux App / Routing / Firewall]

Step-by-Step Flow
=================
1) DPDK receives packet from a real NIC:

The packet enters the DPDK app via a physical port (e.g., using rte_eth_rx_burst()).

2) DPDK sends packet to TAP interface:

The TAP interface is treated like a regular DPDK port (created using the TAP PMD).

The DPDK app calls rte_eth_tx_burst(tap_port_id, ...) to transmit the packet to the TAP device.

3. TAP PMD writes the packet to the TAP file descriptor:

Internally, this results in a write() syscall to the Linux TAP interface.

4. Linux kernel receives the packet:

From the kernel's perspective, it’s as if the packet just arrived from a virtual NIC.

It’s now subject to routing, netfilter rules, etc.

5. Linux applications can receive the packet:

E.g., via a raw socket, TCP socket, or forwarded elsewhere.

===============================================================================

Inband INTERFACE 

inband interface is a tap interface with the IP address. Thus, user application will be able to get the packet if the socket is opened for this IP. 
TCPDUMP uses raw socket to get the packet from the TAP interface which does not have the IP address.

===============================================================================
