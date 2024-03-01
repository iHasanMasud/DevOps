
### Create Two Network Namespaces
```bash
sudo ip netns add red-ns
sudo ip netns add green-ns
```

### Check the Network Namespaces
```bash
sudo ip netns list
```

### Verify that inside the Network Namespaces, loop back interfaces are created
```bash
sudo ip netns exec red-ns ip address show
sudo ip netns exec green-ns ip address show
```

### Enable loopback interfaces
```bash
sudo ip netns exec red-ns ip link set dev lo up
sudo ip netns exec red-ns ip link
sudo ip netns exec green-ns ip link set dev lo up
sudo ip netns exec green-ns ip link
```

### Create a veth pair
```bash
sudo ip link add red-veth type veth peer name br-veth1
```
```bash
sudo ip link add green-veth type veth peer name br-veth2
```

### Associate the non `br-` side with corresponding network namespace
```bash
sudo ip link set red-veth netns red-ns
sudo ip link set green-veth netns green-ns
```

### Assign the addresses to the veth interfaces
```bash
sudo ip netns exec red-ns ip addr add 192.168.1.11/24 dev red-veth ip route ping -c 2 192.168.1.1
sudo ip netns exec green-ns ip addr add 192.168.1.12/24 dev green-veth ip route ping -c 2 192.168.1.1
```

### Create the bridge device naming it `br1`
```bash
sudo ip link add name br1 type bridge
```
```bash
sudo ip link set br1 up
```

### Check that the device has been created
```bash
sudo ip link | grep br1
```

### Configure IP for the Bridge
```bash
sudo ip addr add 192.168.1.1/24 dev br1
```
### Set the bridge veths from the default
```bash
sudo ip link set br-veth1 up
sudo ip link set br-veth2 up
```

### Set the veths from the namespaces up
```bash
sudo ip netns exec red-ns ip link set red-veth up
sudo ip netns exec green-ns ip link set green-veth up
```

### Add the veths to the bridge
```bash
sudo ip link set br-veth1 master br1
sudo ip link set br-veth2 master br1
```

### Verify the bridge
```bash
sudo bridge link show br1
```

### Connect namespaces to the internet
```bash
sudo ip netns exec red-ns ip route add default via 192.168.1.1
```
```bash
sudo ip netns exec red-ns route -n
```
```bash
sudo ip netns exec green-ns ip route add default via 192.168.1.1
```
```bash
sudo ip netns exec green-ns route -n
```

### Ping from namespace to the host IP
```bash
ip addr | grep eth0
```

### Analyzing Traffic with tcpdump

Terminal-1
```bash
sudo ip netns exec red-ns ping 8.8.8.8
```

Terminal-2: Observe traffic
```bash
sudo tcpdump -i eth0 icmp
```
If no packets are captured, try capturing on br1
```bash
sudo tcpdump -i br1 icmp
```

### IP forward if not enabled
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

Terminal-2
```bash
sudo tcpdump -i eth0 icmp
```

### Setting up NAT
```bash
sudo iptables -t nat -A POSTROUTING -s 192.168.1.0/24 ! -o br1 -j MASQUERADE
```

### Ping from namespace to outside
```bash
sudo ip netns exec red-ns ping -c 2 8.8.8.8
sudo ip netns exec green-ns ping -c 2 8.8.8.8
```

