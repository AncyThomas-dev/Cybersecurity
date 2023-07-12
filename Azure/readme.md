## Azure Cloud Infrastructure

I used the following steps to set up cloud resources in Azure

---

#### Setting up the Resource Group

Used the Azure portal to create a resource group that will contain everything the Purple Team needs in the cloud.

- On the home screen, search for "resource."

- Click on the **+ Create** button or the **Create resource group** button.

- Click on **Review + create**.

    ![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/AzureResourceGroup.png)

---

#### Setting up the VNet

I setup a virtual network where the rsource group items can be accessed.

- This network should have the capacity to hold any resource that the Purple Team needs, now and in the future.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/AzureVirtualNetwork.png)

![](1/Activities/04_Virtual_Networking/Solved/Images/virtual_net/final_resource_group.png)

I now have a resource group and VNet that I can use to create the rest of the cloud infrastructure.
---

#### Setting up a Network Security Group:

Steps:

- Created a new security group.

- Added this security group to the resource group.

Created an inbound rule to block all traffic:


Configured the inbound rule as follows:

- Source: Chose **Any** source to block all traffic.

- Source port ranges: Source ports are always random, even with common services like HTTP. Therefore,kept the wildcard (*) to match all source ports.

- Destination: Select **Any** to block any and all traffic associated with this security group.

- Service: Select **Custom**

- Destination port ranges: I used the wildcard (*) to block all destination ports. 

- Protocol: Block **Any** protocol that is used.

- Action: Used the **Block** action to stop all of the traffic that matches this rule.

- Priority: This rule will always be the last rule, so I gave it the highest possible number for the priority. Other rules will always come before this rule. The highest number Azure allows is 4,096.


![](1/Images/inbound_rule_settings1.png)


![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/PurpleTeamSG.png)

I now have a VNet protected by a network security group that blocks all traffic.

#### Setting up Virtual Machines

The goal of the previous step was to set up the first virtual machines inside the cloud network, which is protected by the network security group. I will use this machine as a jump box to access my cloud network and any other machines inside the VNet.

---

SSH Key Generation

Opened the command line and run `ssh-keygen` to create a new SSH key pair.
    
Ran `cat ~/.ssh/id_rsa.pub` to display my `id_rsa.pub` key:

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSH.png)


#### VM 1 - Jump-Box


Use the following settings for this VM: 

- Resource group: Chose the same resource group that you created for the Purple Team.

- Virtual machine name: "Jump-Box-Provisioner."

- Region: Used the same region that you used for your other resources.

- Image: Chose the Ubuntu Server 18.04 option.

- Chose the VM option that has:
  -  **Standard - B1s**
  - 1 CPU
  - 1 RAM

For SSH, I used the following settings: 

- Authentication type: SSH public key.

- Username: PurpleTeamUser.

- SSH public key: Pasted the public key string that you copied earlier.


 **Networking** tab:

- Virtual network: Chose the VNet I created for the Purple Team.

- Subnet: Choose the subnet that I created earlier.

- Public IP:  default. 

- NIC network security group: Chose the Advanced option so I could specify my custom security group.

- Configure network security group: Chose my Purple Team network security group.

    ![](1/Images/VM/VMNetworking-b.png)

- Click on **Review + create**.

    ![](1/Images/VM/FinalizeVM.png)


#### VM's 2 and 3 - Web VM's

Create 2 more new VMs named - "Web-1" and "Web-2".

- These VM's are in the same resource group I used for all other resources.

- The VM's are located in the same region as my resource group and security group.

- Chose the VM option that has:
  - Whose offering is **Standard - B1ms**
  - 1 CPU
  - 2 RAM


Both of these VM's are in the same availability Set. 

![](1/Images/Avail_Set/Avail-Set.png)

In the **Networking** tab I set the following settings:

- Virtual network: Chose the VNet that I created for the Purple Team.

- Subnet: Chose the subnet that I created earlier.

- Public IP: NONE! 

![](1/Images/Avail_Set/No-Ip.png)

- NIC network security group: Chose the Advanced option so that I can specify my custom security group.

- Configure network security group: Chose the Purple Team network security group.

- Load balancing: No.

Final Web VM-1

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/VM's.png)

#### Setting up Jump Box Administration

The goal of this step is to create a security group rule to allow SSH connections only from my current IP address, and to connect to the new virtual machine for management.

---


1. Created a security group rule to allow SSH connections from my current IP address.

2. Create a rule allowing SSH connections from my IP address. 

	![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSHInboundRule.png)


4. Used the command line to SSH to the VM for administration. 

    - The command to connect is `ssh admin-username@VM-public-IP`.

       ![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSHKey.png)

---

#### Docker Container Setup

The goal of this step is to configure the jump box to run Docker containers and to install a container.

1. Started by installing `docker.io` on your Jump box.

    - Ran `sudo apt update` then `sudo apt install docker.io`
    
2. Verified that the Docker service is running.

    - Ran `sudo systemctl status docker`

    ![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/DockerInstall.png)

3. Once Docker is installed, I pulled the container `cyberxsecurity/ansible`.

    - Ran `sudo docker pull cyberxsecurity/ansible`.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/Ansible.png)

  
   
![](2/Images/Docker_Ansible/Container_Connected.png)

4. Created a new security group rule that allows the jump box machine full access to the VNet.

---

#### Setup the Provisioner

In this step, I launched a new VM from the Azure portal that could only be accessed using a new SSH key from the container running inside the jump box.

1. Connected to the Ansible container. Once connected, I created a new SSH key and copied the public key.

    - Ran `docker run -it cyberxsecurity/ansible /bin/bash` to start the container and connect to it. 
     ```
    - Ran `ls .ssh/` to view the keys.

    - Ran `cat .ssh/id_rsa.pub` to display the public key.

    - Copied the public key string.

2. Returned to the Azure portal and located one of the web-vm's details page.

		- Reset the Vm's password and use the container's new public key for the SSH user.

    - Got the internal IP for the new VM from the Details page.

3. After the VM launched, tested my connection using `ssh` from the jump box Ansible container. 

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/sshintoweb1.png)


4. Modified the Ansible config file and hosts file.

   ```
     - Added the web VM internal IP addresses to the Ansible hosts file.

    - Opened the file with `nano /etc/ansible/hosts`.
    - Uncommented the `[webservers]` header line.
    - Added the internal IP address under the `[webservers]` header.
		- Add the python line: `ansible_python_interpreter=/usr/bin/python3` besides each IP.

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/editwebservers.png)


5. Changed the Ansible configuration file to use the administrator account for SSH connections.

    - Opened the file with `nano /etc/ansible/ansible.cfg` and scrolled down to the `remote_user` option.
    
    - Uncommented the `remote_user` line and replace `root` with my admin username: `remote_user = PurpleTeamUser`

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/hosts.png)


6. Tested an Ansible connection:

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/ansibleping.png) 

---

#### Setup Ansible Playbooks

The goal here is to create an Ansible playbook that installed Docker and configure a VM with the DVWA web app.

---

1. Connected to the jump box, and connected to the Ansible container in the box. 

    
    ```

2. Created a YAML playbook file that you will use for your configuration. 

    ```YAML
    ---
    - name: Config Web VM with Docker
      hosts: webservers
      become: true
      tasks:
      - name: docker.io
        apt:
          force_apt_get: yes
          update_cache: yes
          name: docker.io
          state: present

      - name: Install pip3
        apt:
          force_apt_get: yes
          name: python3-pip
          state: present

      - name: Install Docker python module
        pip:
          name: docker
          state: present

      - name: download and launch a docker web container
        docker_container:
          name: dvwa
          image: cyberxsecurity/dvwa
          state: started
          published_ports: 80:80

      - name: Enable docker service
        systemd:
          name: docker
          enabled: yes
    ```

  - Ran the playbook using the command: ansible-playbook /etc/ansible/pentest.yml

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/Installingapps.png)

    ```

4. To test that DVWA is running on the new VM, I SSH’ed to the new VM from the Ansible container.

    ```

    - Ran `curl localhost/setup.php` to test the connection. 
![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/setuphp.png)


#### Setting up the Load Balancer

I installed a load balancer in front of the VM to distribute the traffic among more than one VM.

---

1. Created a new load balancer and assigned it a static IP address.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/loadbalancer.png)

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/lbbackend.png)
---

#### Setting up the NSG to expose port 80

 I configured the load balancer and security group to work together to expose port `80` of the VM to the internet.

1. Created a load balancing rule to forward port `80` from the load balancer to the Purple Team VNet.


2. Create a new security group rule to allow port `80` traffic from the internet to your internal VNet.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/LBSecurityRules.png)

3. Removed the security group rule that blocks _all_ traffic on the vnet to allow traffic from the load balancer through.


4. Verified that I could reach the DVWA app from my browser over the internet.
![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/dvwa.png)

## Built, Hosted, and Designed a Web Application Using Azure Free Domain

Steps:

  - (1) **Created an Azure web app**. 
  - (2) **Deployed a container on the web app**.
  - (3) **Designed my custom web application**.


### Step 1: Created an Azure Web App

In this step, I created a Azure web application 

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/CreateWebApp.png)

	
### Part 2: Deployed a Container on the Web App

In this step, I used the Azure Cloud Shell to deploy a Docker container on the web application. This container contains the framework for my cyber blog webpage.

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/webappconfig.png)
 
The following screenshot verifies that the container has been successfully deployed. 
![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/webpage.png)

### Part 3: Designed my Custom Web Application

I customized the following elements in the blog:
- My name
- My email
- My LinkedIn profile link
- My picture

### Protected my Web Application with Azure's Security Features

Steps:

  - (1) **Created a front door instance**.
  - (2) **Configured custom WAF rules**.

### Part 1: Created a Front Door Instance

In this step, I created an Azure Front Door instance. 

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/frontdoor.png)

### Part 3: Configured Custom WAF Rules

In this part, I configured a custom WAF rule to protect against a potential security attack.

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/WAFRule.png)


---
#### END



