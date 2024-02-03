### Create two Namespaces and connect them using Linux bridge.

#### Prerequisites
Ensure that your system supports the creation of network namespaces and bridging. You may need to install the bridge-utils package if it's not already installed.

```bash
sudo apt-get install bridge-utils
```

##### 1. Enable IP Forwarding
In the terminal, run the following command to enable IP forwarding:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

##### 2. Create two namespaces
In the terminal, run the following commands to create the namespaces:

```bash
sudo ip netns add ns1
```

```bash
sudo ip netns add ns2
```

##### 3. Create Virtual Ethernet (veth) Pairs
In the terminal, run the following commands to create the veth pairs:

```bash
sudo ip link add veth0 type veth peer name veth-br1
```

```bash
sudo ip link add veth1 type veth peer name veth-br2
```

##### 4. Move veth Interfaces to Respective Namespaces
In the terminal, run the following commands to move the veth interfaces to the respective namespaces:

```bash
sudo ip link set veth0 netns ns1
```

```bash
sudo ip link set veth1 netns ns2
```

##### 5. Configure IP Addresses in the namespaces
In the terminal, run the following commands to configure IP addresses in the namespaces:

```bash
sudo ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth0
```

```bash
sudo ip netns exec ns2 ip addr add 10.0.0.2/24 dev veth1
```

##### 6. Set up Interfaces in Namespaces
In the terminal, run the following commands to set up the interfaces in the namespaces:

```bash
sudo ip netns exec ns1 ip link set veth0 up
```

```bash
sudo ip netns exec ns2 ip link set veth1 up
```

##### 7. Create Bridge and Attach veth Interfaces
In the terminal, run the following commands to create a bridge and attach the veth interfaces to it:

```bash
sudo brctl addbr br0
```

```bash
sudo brctl addif br0 veth-br1
```

```bash
sudo brctl addif br0 veth-br2
```

##### 8. Enable the Bridge and Set IP Address
In the terminal, run the following commands to enable the bridge and set an IP address for it:

```bash
sudo ip link set br0 up
```

##### 9. Test Connectivity
Test the connectivity between the two namespaces by running the following commands in each namespace:
    
 ```bash
sudo ip netns exec ns1 ping -c 3 10.0.0.2
```

```bash
sudo ip netns exec ns2 ping -c 3 10.0.0.1
```

   
