### Create two Namespaces and connect them using Linux bridge.

1. In the terminal, run the following commands to create the namespaces:

    ```bash
    sudo ip netns add ns1
    ```

    ```bash
    sudo ip netns add ns2
    ```

2. Create Linux Bridge:

    ```bash
   sudo brctl addbr mybridge
    ```
   2.1 If brctl is not installed, install it using the following command:

    ```bash
    sudo apt-get install bridge-utils
    ```

3. Create Virtual Ethernet (veth) pairs:

    ```bash
    sudo ip link add veth-ns1 type veth peer name veth-br1
    ```

    ```bash
    sudo ip link add veth-ns2 type veth peer name veth-br2
    ```

4. Connect Namespaces to Bridge:

    ```bash
    sudo ip link set veth-ns1 netns ns1
    ```

    ```bash
    sudo ip link set veth-ns2 netns ns2
    ```

5. Configure Interfaces inside Namespaces:

    ```bash 
    sudo ip netns exec ns1 ip link set dev lo up
    ```

    ```bash
    sudo ip netns exec ns1 ip link set dev veth-ns1 up
    ```

    ```bash
    sudo ip netns exec ns1 ip addr add 192.168.1.1/24 dev veth-ns1
    ```
   <br>

    ```bash
    sudo ip netns exec ns2 ip link set dev lo up
    ```

    ```bash
    sudo ip netns exec ns2 ip link set dev veth-ns2 up
    ```

    ```bash
    sudo ip netns exec ns2 ip addr add 192.168.1.2/24 dev veth-ns2
    ```

6. Connect veth pairs to Bridge

    ```bash
    sudo brctl addif mybridge veth-br1
    ```

    ```bash
    sudo brctl addif mybridge veth-br2
    ```

7. Bring up the Bridge:

    ```bash
    sudo ip link set dev mybridge up
    ```
   
8. Test the connectivity between the two namespaces, open two terminals and run the following commands in each terminal:
   
    ###### In the first terminal, run the following command to ping ns2 from ns1:
          
     ```bash
     sudo ip netns exec ns1 ping 192.168.1.2
     ```
    ######  In the second terminal, run the following command to ping ns1 from ns2:

     ```bash
    sudo ip netns exec ns2 ping 192.168.1.1
     ```
   
