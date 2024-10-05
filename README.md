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
1. Creating an interface in the router with a public IP to be the external gateway through the router
   ![Screenshot from 2024-10-05 12-16-28](https://github.com/user-attachments/assets/5deada88-4fc2-4d6e-b62b-316a7f00bdd5)
     
2. Configuring NAT on the router and enabling forwarding between internal and external gateways
   ![Screenshot from 2024-10-05 12-25-56](https://github.com/user-attachments/assets/2c9e78da-dddc-4dad-bb60-3b0b66b651ef)

3. Testing connectivity to external network's interface 
   ![Screenshot from 2024-10-05 13-21-26](https://github.com/user-attachments/assets/a2f27285-ff3d-49b9-9e36-7d259815e9d4)

4. Checking firewall rules
   ![Screenshot from 2024-10-05 13-22-51](https://github.com/user-attachments/assets/107832d1-2981-4802-8a76-3b6e83639fbd)
      


### Challenges faced:
1. No command found to check the IP translation table
2. Not able to ping from client2 to eth1 but able to ping from eth1 to client2
3. While enabling port forwarding, figuring out the forwarding rules to the router was hard because there are no proper resources


### Bonus Tasks 

####  Port Forwarding

Web server network details:

    Private IP Address: 192.168.10.3
    Private Interface: peer0

Firewall network details:

    Public IP Address: 203.0.113.1
    Private IP Address: 192.168.10.1
    Public Interface: eth-ext
    Private Interface: eth0

(Prerequisite: install nginx)

1. Set the default server interface as client1
   ```
      ip netns exec client1 nano /etc/nginx/sites-enabled/default
   ```

   Inside the listen directive, change this :
   ```
   server {
           listen 80 default_server;
           listen [::]:80 default_server ipv6only=on;

           . . .
       }
   ```
   to :
   ```
   server {
           listen 192.168.10.3:80 default_server;

           . . .
       }
   ```
   
2.   Host a simple web server using **python** using the command and to check if you can access the server from the client1 namespace:

   ```
     ip netns exec client1 python3 -m http.server 8080
     ip netns exec client1 curl http://192.168.10.3:8080 
   ```

   ![Screenshot from 2024-10-05 23-19-26](https://github.com/user-attachments/assets/4ea46365-ce92-44db-9630-a6b690c76e01)

   ![Screenshot from 2024-10-05 23-34-00](https://github.com/user-attachments/assets/473a6d13-36ea-42c1-a509-d2ae3ac717ad)   


3. To access your web server from your router, use the following command:
   Since the router is directly connected to the client1's private interface we can directly access it through client's private IP

   ```
     ip netns exec router curl http://192.168.10.3:8080 
   ```

   ![Screenshot from 2024-10-05 23-33-38](https://github.com/user-attachments/assets/bb62337c-af9a-4798-b480-8e56220e590c)


4. Enable ip forwarding

   ```
    ip netns exec router echo 1 | tee /proc/sys/net/ipv4/ip_forward
       ip netns exec router nano /etc/sysctl.conf
       sudo sysctl -p
   ```
       
   ![Screenshot from 2024-10-05 23-40-10](https://github.com/user-attachments/assets/e58d704a-8f13-4783-ae19-8ef4f67f76f3)

5. To turn on port forwarding permanently run the following command:

   ```
     sysctl --system 
   ```

   ![Screenshot from 2024-10-05 23-43-42](https://github.com/user-attachments/assets/cfd5aad0-5a46-49df-88cb-89b60171b4aa)


6. Add forwarding rules to your router

   ```
       ip netns exec router iptables -t nat -A PREROUTING -p tcp -d 203.0.113.1 --dport 80 -j DNAT --to-destination 192.168.10.3:8080
       ip netns exec router iptables -A FORWARD -p tcp -d 192.168.10.3 --dport 8080 -j ACCEPT
       ip netns exec router iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT
   ```

7. To verify if the rules are set correctly

   ```
       ip netns exec router iptables -t nat -L -v
       ip netns exec router iptables -L FORWARD -v
   ```

   ![Screenshot from 2024-10-05 23-49-16](https://github.com/user-attachments/assets/9b25765c-6593-4c23-935b-d680a401ab13)

8. To check if we're able to forward requests from "internet" to the web server through router's public IP
   Using client1's private IP:

   ```
      curl http://192.168.10.3:8080
   ```

   ![image](https://github.com/user-attachments/assets/417c0a72-fce4-493a-a871-132496c7642a)

   Using router's public IP:

   ```
      curl http://203.0.113.1:8080
   ```

   ![image](https://github.com/user-attachments/assets/4d0e3b0b-7631-4279-a705-00f062e6c1aa)
   ![image](https://github.com/user-attachments/assets/c85ae51d-c76c-4329-9177-a0f4b469f680)


   



   
   

  

   


