The Network design is as follows:

I divided the endpoints into 6 different subnets with each department contained within its own subnet.

	The Sales and Management departments are able to access sensitive data contained in the Customer Info server.
	
	The remaining office departments, Human Resources and Supply Chain, are unable to access the data. 
	
	The Guest endpoint is only allowed to access the Internet. 
	
	Every department is able to access the Supply Database server. 
	
	I configured an access control list on the Office Switch to drop any traffic intended for the Customer Info server.
	
      Sales - VLAN 10 192.168.10.0 /24
      Management - VLAN 20 192.168.20.0 /24
      HR - VLAN 30 192.168.30.0 /24
      Supply Chain - VLAN 40 192.168.40.0 /24
      Customer Info - VLAN 50 192.168.50.0 /24
      Guest - VLAN 60 192.168.60.0 /24
			
There are 3 switches 
	
	1. Root
	
	2. Office
	
	3. Secondary
	
Router 0 is the Edge Router, connnecting to the ASA, Router 1 simulates the public connection to the remote salesman.
	
Both of the server groups have static IP addresses. The endpoints in each department utilize DHCP, originating from the Office Switch.

I enabled an instance of Rapid-PVST+ per VLAN in order to prevent broadcast storms and loops. The main core switch, designated as "Root Switch," is the Root for the Spanning Tree Protocol. 

I set up an ACL on the Firewall that blocks unwanted traffic into the network. The default security level for "inside" is 100 and the default security for "outside" is 0. 

The topology contains 3 switches, allowing outside access to the Customer Info server if the Office Switch were to fail. This hybrid design provides fault tolerance.

I frequently tested functionality by send PDU's from one resource to another, catching issues early on.


**Problems**
	There were several times during the development of my network that I had issues with DHCP failing to work. When I tried to send a PDU from one of the departments to another, it would immediately fail. I checked the IP configuration and it would show an IP outisde of the intended subnet. I would re-initialize the DHCP process and it would fail. I then checked the DHCP pools to verify the pools were enabled and applied to the correct interfaces. The configurations were correct. I then checked the ACL applied to the Office Switch and found there was no exception for the UDP 67/68 ports that DHCP uses. I added a rule to allow access to those ports and started the DHCP process over on the effected endpoints. The DHCP then applied correctly.  

