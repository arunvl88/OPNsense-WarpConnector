# OPNsense-WarpConnector

This project demonstrates the use of Cloudflare Warp-connector to connect two private networks across different parts of the world. In this homelab setup, a separate LAN network is created using OPNsense, a powerful open-source firewall and routing platform. The OPNsense virtual machine (VM) is hosted on a Proxmox server, providing an efficient and flexible environment for network management. The WAN network is provided by Xfinity, using the 10.x.x.x IP range, while the LAN network is managed by OPNsense, using the 192.168.x.x IP range.

The Cloudflare Warp-connector is utilized to bridge the two networks. This setup allows for seamless connectivity between machines on both the WAN and LAN networks, demonstrating the practical application of Cloudflare Warp-connector in connecting private networks globally.

By following this documentation, you will learn how to set up OPNsense on Proxmox, configure the WAN and LAN networks, and use Cloudflare Warp-connector to ensure connectivity between the networks. This guide provides a step-by-step approach to replicate this environment in your own homelab, showcasing the capabilities and benefits of using Cloudflare Warp-connector for network connectivity.
