# OPNsense-WarpConnector

This project demonstrates the use of Cloudflare Warp-connector to connect two private networks across different parts of the world. In this homelab setup, a separate LAN network is created using OPNsense, a powerful open-source firewall and routing platform. The OPNsense virtual machine (VM) is hosted on a Proxmox server, providing an efficient and flexible environment for network management. The WAN network is provided by Xfinity, using the 10.x.x.x IP range, while the LAN network is managed by OPNsense, using the 192.168.x.x IP range.

The Cloudflare Warp-connector is utilized to bridge the two networks. This setup allows for seamless connectivity between machines on both the WAN and LAN networks, demonstrating the practical application of Cloudflare Warp-connector in connecting private networks globally.

By following this documentation, you will learn how to set up OPNsense on Proxmox, configure the WAN and LAN networks, and use Cloudflare Warp-connector to ensure connectivity between the networks. This guide provides a step-by-step approach to replicate this environment in your own homelab, showcasing the capabilities and benefits of using Cloudflare Warp-connector for network connectivity.

![opnsense](https://github.com/arunvl88/OPNsense-WarpConnector/assets/7003647/f87297bc-a5e9-4cf2-bd17-6bff767a09eb)

### **Network Diagram Description with Symbols**

1. **Xfinity Modem/Router:**
    - **Symbol:** A router symbol connected to the internet (cloud symbol).
    - **Connections:**
        - **WAN:** Internet (cloud symbol)
        - **LAN:** 10.x.x.x network, connected to Proxmox and the OPNsense WAN interface.
2. **Proxmox Server:**
    - **Symbol:** A server symbol representing the Proxmox server.
    - **Connections:**
        - **Internal Virtual Switch/Bridge:** Connected to VMs on both 10.x.x.x and 192.168.x.x networks.
        - **OPNsense VM:** Two network interfaces (WAN and LAN).
3. **OPNsense VM (Ubuntu VM with OPNsense):**
    - **Symbol:** A server symbol representing the OPNsense VM.
    - **Connections:**
        - **WAN Interface:** Connected to the Xfinity modem/router.
        - **LAN Interface:** Connected to the internal virtual switch/bridge in Proxmox.
4. **VMs on 10.x.x.x Network:**
    - **Symbol:** Multiple VM symbols connected directly to the Xfinity modem/router.
5. **VMs on 192.168.x.x Network:**
    - **Symbol:** Multiple VM symbols connected to the internal virtual switch/bridge in Proxmox, receiving IPs from the OPNsense DHCP server.

