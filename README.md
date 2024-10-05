# WEC_Systems_Networks_task

### Network topology
![Network_topo](https://github.com/user-attachments/assets/6fbc03a4-1c02-48f7-a324-623748b67cf4)

### Procedure:
##### Using the ip command provided by the iproute2 package to create namespaces and interfaces 
1. Creating the namespaces client1, client2 and router
   ![Screenshot from 2024-10-05 11-55-00](https://github.com/user-attachments/assets/703d8a60-9691-47ba-b5ec-b4bab7d97184)

2. Creating 2 veth pairs and adding peer0 to client1, peer1 to client2, eth0 and eth1 to router 
   ![Screenshot from 2024-10-05 11-57-59](https://github.com/user-attachments/assets/950e1fb0-e5c0-4de7-b2b8-cf031a0162a8)

3. Setup IP address and loopback address
  ![Screenshot from 2024-10-05 12-09-17](https://github.com/user-attachments/assets/a2b42a59-69a6-4b82-82fe-361781d3e8bd)

4. Add default route and enable IP forwarding
   ![Screenshot from 2024-10-05 12-11-56](https://github.com/user-attachments/assets/e9a4b7a6-c16c-4922-97b2-278d4387c485)


##### Using the iptables command provided by the iproute2 package to set up NAT and IP masquerading 
1. Creating an interface in the router with a public ip to be the external gateway through the router
     
2. Configuring NAT on the router and enabling forwarding between internal and external gateways
