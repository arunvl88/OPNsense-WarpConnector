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

## Creating a Warp Connector

After successfully setting up OPNsense, the next step is to create a Warp Connector to connect two private networks using Cloudflare Warp. Follow the steps below to configure the Warp Connector.

<img width="1079" alt="image" src="https://github.com/arunvl88/OPNsense-WarpConnector/assets/7003647/e0be807d-ce97-4403-a686-ec0906d12886">


### Steps to Create a Warp Connector

1. **Prepare the Environment:**
    - Ensure you have two private networks you want to connect. In this setup, one network is managed by the OPNsense VM (192.168.x.x), and the other is the WAN network provided by Xfinity (10.x.x.x).
2. **Configure Warp Connector:**
    - Follow the detailed instructions provided by Cloudflare to set up the Warp Connector. The documentation can be found here: [Cloudflare Warp Connector Documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/private-net/warp-connector/).

I have used the following option in my home lab setup:
[option-3-intermediate-gateway](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/private-net/warp-connector/#option-3-intermediate-gateway)

If you do not have access to the router, you will need to configure each device on the subnet to egress through the WARP Connector machine instead of the default gateway.

<img width="743" alt="image" src="https://github.com/arunvl88/OPNsense-WarpConnector/assets/7003647/3a3c48a1-3bd6-430e-8d7b-2ba2fff3dbfb">

### Adding Routes for Network Connectivity

To enable seamless connectivity between the two private networks (192.168.x.x and 10.x.x.x), specific routes need to be added on the machines located in each network. This is achieved using the `ip route add` command, which adds a static route to the routing table. Below is an explanation of the commands used and the role of the Warp Connectors.

### Commands Used to Add Routes

### On a Machine in the 192.168.x.x Network

```
sudo ip route add 10.0.0.0/16 via 192.168.1.104 dev ens18
```

- **sudo**: Runs the command with administrative privileges.
- **ip route add**: Command to add a new route to the routing table.
- **10.0.0.0/16**: Specifies the destination network (10.0.0.0) with a subnet mask of 255.255.0.0.
- **via 192.168.1.104**: Specifies the next-hop IP address through which packets should be routed. This is the IP address of the machine with the Warp Connector installed in the 192.168.x.x network.
- **dev ens18**: Specifies the network interface to use for the route. Replace `ens18` with the appropriate interface name for your machine.

### On a Machine in the 10.x.x.x Network

```
sudo ip route add 192.168.0.0/16 via 10.0.0.235 dev eth0
```

- **sudo**: Runs the command with administrative privileges.
- **ip route add**: Command to add a new route to the routing table.
- **192.168.0.0/16**: Specifies the destination network (192.168.0.0) with a subnet mask of 255.255.0.0.
- **via 10.0.0.235**: Specifies the next-hop IP address through which packets should be routed. This is the IP address of the machine with the Warp Connector installed in the 10.x.x.x network.
- **dev eth0**: Specifies the network interface to use for the route. Replace `eth0` with the appropriate interface name for your machine.

### Steps to Make Routes Persistent

1. **Open the Netplan Configuration File:**
    - Edit the `/etc/netplan/cloud-init.yml` configuration file.
2. **Add the Network Configuration:**
    - Add the static routes to the configuration file under the appropriate network interface.

### Example Configuration for `/etc/netplan/cloud-init.yml`

### For a Machine in the 192.168.x.x Network

```yaml
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: true
      routes:
        - to: 10.0.0.0/16
          via: 192.168.1.104
          on-link: true

```

### For a Machine in the 10.x.x.x Network

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
      routes:
        - to: 192.168.0.0/16
          via: 10.0.0.235
          on-link: true

```

1. **Apply the Changes:**
    - After editing the file, apply the changes by using the `netplan apply` command.
  
### Steps for Verifying the Routes

1. **Verify Routes on the 10.x.x.x Network Machine**
    - Open a terminal on a machine in the 10.x.x.x network.
    - Use the `ip route get` command to verify the route to a machine in the 192.168.x.x network. For example:
        
        ```
        ip route get 192.168.1.10
        ```
        
    - Expected output:
        
        ```css
        192.168.1.101 via 10.0.0.235 dev eth0 src 10.0.0.8 uid 1000
            cache
        
        ```
        
    - This output indicates that the machine knows to route traffic to the 192.168.x.x network via the 10.0.0.235 gateway on the `eth0` interface.
2. **Verify Routes on the 192.168.x.x Network Machine**
    - Open a terminal on a machine in the 192.168.x.x network.
    - Use the `ip route get` command to verify the route to a machine in the 10.x.x.x network. For example:
        
        ```
        ip route get 10.0.0.8
        ```
        
    - Expected output:
        
        ```css
        10.0.0.8 via 192.168.1.104 dev ens18 src 192.168.1.101 uid 1000
            cache
        ```
        
    - This output indicates that the machine knows to route traffic to the 10.x.x.x network via the 192.168.1.104 gateway on the `ens18` interface.
