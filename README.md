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

## Installing OPNsense on Proxmox

This guide will walk you through the steps to install and configure OPNsense on a Proxmox virtual environment. Follow these steps to set up your OPNsense firewall and routing platform.

### Prerequisites

- Proxmox VE installed and running
- ISO image of OPNsense downloaded from the official website
- Create a new network bridge (vmbr1) for the OPNsense LAN network

### Creating a New Network Bridge (vmbr1)

1. Log in to the Proxmox web interface.
2. Navigate to **Datacenter > Your Node > Network**.
3. Click on **Create > Linux Bridge**.
4. Configure the bridge:
    - Name: `vmbr1`
    - Leave other settings as default or configure as needed.
5. Click **Create** and then **Apply Changes**.

### Steps

### 1. Upload OPNsense ISO to Proxmox

1. Log in to the Proxmox web interface.
2. Navigate to **Datacenter > Your Node > local (storage) > ISO Images**.
3. Click on **Upload**.
4. Select the OPNsense ISO file and upload it to the Proxmox server.

### 2. Create a New Virtual Machine

1. In the Proxmox web interface, navigate to **Datacenter > Your Node**.
2. Click on **Create VM**.
3. In the **General** tab:
    - Enter the VM ID and Name.
    - Click **Next**.
4. In the **OS** tab:
    - Select **Do not use any media**.
    - Click **Next**.
5. In the **CD/DVD** tab:
    - Select the OPNsense ISO image.
    - Click **Next**.
6. In the **Hard Disk** tab:
    - Select **SCSI** as the Bus/Device type.
    - Set the disk size (e.g., 16 GB).
    - Click **Next**.
7. In the **CPU** tab:
    - Allocate CPU cores (e.g., 2 cores).
    - Click **Next**.
8. In the **Memory** tab:
    - Allocate RAM (e.g., 2 GB).
    - Click **Next**.
9. In the **Network** tab:
    - Select **VirtIO** as the model.
    - Click **Next**.
10. Review the configuration and click **Finish**.

### 3. Configure VM Hardware

1. Select the newly created VM from the Proxmox interface.
2. Navigate to the **Hardware** tab.
3. Click on **Add** and select **Network Device**.
    - Set the model to **VirtIO** for both WAN and LAN interfaces.
    - Assign appropriate bridges:
        - WAN: `vmbr0` (connected to Xfinity)
        - LAN: `vmbr1` (created earlier)

### 4. Install OPNsense

1. Select the VM and click on **Start**.
2. Open the console by clicking on **Console**.
3. Follow the on-screen instructions to install OPNsense:
    - Choose the installation type (usually, a standard installation is sufficient).
    - Partition the disk (select Auto (UFS) for simplicity).
    - Complete the installation and reboot the VM.

### 5. Configure OPNsense Interfaces

1. **Start the OPNsense VM:**
    - Boot the VM and open the console.
2. **Assign Interfaces:**
    - Log in as `root`.
    - Select option **`1) Assign interfaces`** in the OPNsense console menu.
    - Assign **`vtnet0`** to **`WAN`** (connected to **`vmbr0`**).
    - Assign **`vtnet1`** to **`LAN`** (connected to **`vmbr1`**).
3. **Set Interface IP Addresses:**
    - Select option **`2) Set interface IP address`** to configure the LAN interface to **`192.168.1.1/24`**.
  
<img width="1089" alt="Untitled" src="https://github.com/arunvl88/OPNsense-WarpConnector/assets/7003647/bdbdebe3-8392-4d24-a744-0b140503cc61">


### 6. Initial Configuration of OPNsense

1. After the reboot, access the OPNsense web interface:
    - Open a web browser and navigate to the LAN IP address (default is 192.168.1.1).
    - You can perform any necessary configuration if required.

<img width="715" alt="Untitled (1)" src="https://github.com/arunvl88/OPNsense-WarpConnector/assets/7003647/96d15c7c-8d19-4e98-a1f9-81dfc9a66563">


### Conclusion

You have successfully installed and configured OPNsense on your Proxmox server. Your OPNsense VM is now ready to manage your network traffic and provide firewall and routing capabilities.
