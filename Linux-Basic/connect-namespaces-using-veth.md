### Create two namespaces and connect them using veth pair.

1. In the terminal, run the following commands to create the namespaces:

    ```bash
    sudo ip netns add ns1
    ```
   
    ```bash
    sudo ip netns add ns2
    ```

2. Create a veth pair (veth0 and veth1):

    ```bash
   sudo ip link add veth0 type veth peer name veth1
    ```
   
3. Connect the veth0 end to ns1 and the veth1 end to ns2:

    ```bash
    sudo ip link set veth0 netns ns1
    ```
   
    ```bash
    sudo ip link set veth1 netns ns2
    ```
   
4. Assign IP addresses to the veth0 and veth1 interfaces:

    ```bash
    sudo ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth0
    ```
   
    ```bash
    sudo ip netns exec ns2 ip addr add 10.0.0.2/24 dev veth1
    ```
  
5. Bring up(enable) the veth0 and veth1 interfaces:

    ```bash 
    sudo ip netns exec ns1 ip link set veth0 up
    ```
   
    ```bash
    sudo ip netns exec ns2 ip link set veth1 up
    ```
   
6. Enable loopback for the namespaces:

    ```bash
    sudo ip netns exec ns1 ip link set lo up
    ```
   
    ```bash
    sudo ip netns exec ns2 ip link set lo up
    ```
   
7. Test the connectivity between the two namespaces, open two terminals and run the following commands in each terminal:
    In windows virtualbox, you can open two terminals by pressing right ctrl + F2 and right ctrl + F3 OR Windows + F2 and Windows + F3.

   ###### In the first terminal, run the following command to ping ns2 from ns1:
        
    ```bash
    sudo ip netns exec ns1 ping -c 4 10.0.0.2
    ```
   ######  In the second terminal, run the following command to ping ns1 from ns2:

    ```bash
    sudo ip netns exec ns2 ping -c 4 10.0.0.1
    ```
   
8. Delete the namespaces:

    ```bash
    sudo ip netns delete ns1
    ```
   
    ```bash
    sudo ip netns delete ns2
    ```
   
9. Verify that the namespaces are deleted:

    ```bash
    sudo ip netns list
    ```
