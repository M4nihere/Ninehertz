# Creating Private Network on Proxmox VE

Creating a private network on Proxmox VE involves configuring a virtual bridge and creating virtual machines (VMs) connected to that bridge. Here's a step-by-step guide to set it up:

### **Step 1: Access Proxmox Web Interface**

1. Open a web browser and navigate to the Proxmox VE web interface.
2. Log in with your username and password.

### **Step 2: Create a Bridge Interface**

1. In the Proxmox web interface, navigate to the "Datacenter" view.
2. Select the "Network" tab.
3. Click on "Create" and choose "Linux Bridge".
4. Enter a name for the bridge (e.g., "privatenet").
5. Select the physical network interface you want to bridge to. This interface should be connected to the private network.
6. Click "Create."

Network configuration

```bash
auto vmbr1
iface vmbr1 inet static
        address 10.0.0.1/24
        bridge-ports enp130s0
        bridge-stp off
        bridge-fd 0
        
        
  # or you can used instead
  
auto vmbr1
iface vmbr1 inet static
        address 10.0.0.1
        netmask 255.255.255.0
        bridge_ports enp130s0
        bridge_stp off
        bridge_fd 0

```

### **Step 3: Configure IP Address (Optional)**

1. If you want to assign an IP address to the bridge interface:
    - Go to the "System" view.
    - Select the "Network" tab.
    - Click on the bridge you created ("privatenet").
    - Click "Edit" and configure the IP address settings according to your network setup.

### **Step 4: Create Virtual Machines**

1. Navigate to the "VM" view in the Proxmox web interface.
2. Click "Create VM" and go through the VM creation wizard.
3. In the "OS" tab, select the OS you want to install on the VM.
4. In the "Hard Disk" tab, select storage and configure disk settings.
5. In the "CPU" and "Memory" tabs, configure CPU and memory settings.
6. In the "Network" tab:
    - Select the "Bridge" mode.
    - Choose the bridge interface you created ("privatenet") from the dropdown.
    - Optionally, assign a static MAC address.
7. Complete the wizard, then start the VM.

### **Step 5: Repeat for Additional VMs**

Repeat Step 4 to create additional VMs that you want to connect to the private network. Ensure that you select the same bridge interface ("privatenet") for each VM's network configuration.

### **Step 6: Verify Connectivity**

1. Start the VMs.
2. Log in to each VM and configure networking if necessary (e.g., set static IP addresses).
3. Test connectivity between the VMs on the private network to ensure they can communicate with each other.

### **Step 7: Firewall Configuration (Optional)**

If you want to restrict access to the private network, configure firewall rules on each VM to allow/deny traffic as needed. Proxmox VE supports both iptables and nftables, so you can use your preferred firewall tool.

## Steps to enable NAT (Network Address Translation)

### **Step 1: Enable IP Forwarding**

First, you need to enable IP forwarding on the host machine to allow it to forward packets between interfaces.

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

To make this change persistent across reboots, edit the **`/etc/sysctl.conf`** file and uncomment or add the following line:

```bash
net.ipv4.ip_forward=1
```

### **Step 2: Set up NAT using iptables**

Next, you'll configure NAT using iptables. Replace **`eth0`** with the interface connected to the internet (if it's different from **`privatew`**).

```bash
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o eth0 -j MASQUERADE

```

This command configures iptables to perform NAT for packets coming from the **`10.0.0.0/24`** network and going out through the interface connected to the internet (**`eth0`**).

### **Step 3: Set Virtual Machine Gateway**

Make sure the virtual machine **`privatew`** is using the host (10.0.0.1) as its gateway. You can check and set the default gateway on the virtual machine with:

```bash

route -n
route add default gw 10.0.0.1

```

These steps should enable NAT on your host machine and allow internet access for your virtual machine **`privatew`**. Make sure to replace interface names (**`eth0`**, **`privatew`**, etc.) with your actual interface names and IP addresses as per your network configuration.