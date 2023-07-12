## Azure Cloud Infrastructure

I used the following steps to set up cloud resources in Azure

---

#### Setting up the Resource Group

Used the Azure portal to create a resource group that will contain everything the Purple Team needs in the cloud.

- On the home screen, search for "resource."

- Click on the **+ Create** button or the **Create resource group** button.

- Click on **Review + create**.

- Azure will alert you if there are any errors. Click on **Create** to finalize your settings and create the group.

    ![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/AzureResourceGroup.png)

---

#### Setting up the VNet

Before you can deploy servers and services, there must be a network where these items can be accessed.

- This network should have the capacity to hold any resource that the Purple Team needs, now and in the future.

- Return to the home screen and search for "net." Choose the search result for **Virtual networks**.

- Click on the **+ Create** button on the top-left of the page or the **Create virtual network** button on the bottom of the page.


Fill in the network settings:

- Subscription

- Resource group: This should be the resource group you created in step two.

- Name: A descriptive name so it will not get confused with other cloud networks in the same account.

- Region: Make sure to choose the same region you chose for your resource group. 

    - Carefully configuring the region of your resources is important for ensuring low latency and high availability. Resources should be located as close as possible to those who will be consuming them.

- IP Addresses: Azure requires you to define a network and subnet.
    - Use the defaults on this tab.

- Security: Leave the default settings.

- Tags: No tags are needed.

Click **Create**.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/AzureVirtualNetwork.png)

Once you have created your resource group and VNet, return to the home screen and choose the resource group option. 
- This provides a list of all resource groups in your account. 
- Choose the group that you created and you should see your VNet listed as a resource. 

![](1/Activities/04_Virtual_Networking/Solved/Images/virtual_net/final_resource_group.png)

You now have a resource group and VNet that you can use to create the rest of the cloud infrastructure.
---

#### Setting up a Network Security Group:

- On your Azure portal home screen, search "net" and choose **Network security groups**. 

- Create a new security group.

- Add this security group to your resource group.

- Give the group a recognizable name that is easy to remember.

- Make sure the security group is in the same region that you chose during the previous activity.

To create an inbound rule to block all traffic:

- Once the security group is created, click on the group to configure it.

- Choose **Inbound security rules** on the left.

- Click on the **+ Add** button to add a rule.


Configure the inbound rule as follows:

- Source: Choose **Any** source to block all traffic.

- Source port ranges: Source ports are always random, even with common services like HTTP. Therefore,keep the wildcard (*) to match all source ports.

- Destination: Select **Any** to block any and all traffic associated with this security group.

- Service: Select **Custom**

- Destination port ranges: Usually, you would specify a specific port or a range of ports for the destination. In this case, you can use the wildcard (*) to block all destination ports. You can also block all ports using a range like `0-65535`.

- Protocol: Block **Any** protocol that is used.

- Action: Use the **Block** action to stop all of the traffic that matches this rule.

- Priority: This rule will always be the last rule, so it should have the highest possible number for the priority. Other rules will always come before this rule. The highest number Azure allows is 4,096.

- Name: Give your rule a name like "Default-Deny."

![](1/Images/inbound_rule_settings1.png)

- Description: "Deny all inbound traffic."

- Save the rule.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/PurpleTeamSG.png)

I now have a VNet protected by a network security group that blocks all traffic.

#### Setting up Virtual Machines

The goal of the previous step was to set up the first virtual machines inside the cloud network, which is protected by the network security group. I will use this machine as a jump box to access your cloud network and any other machines inside the VNet.

---

SSH Key Generation

Open the command line and run `ssh-keygen` to create a new SSH key pair.
    - DO NOT CREATE A PASSPHRASE, just press enter twice.
Run `cat ~/.ssh/id_rsa.pub` to display your `id_rsa.pub` key:

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSH.png)


- Highlight and copy the SSH key string to your clipboard. 

---

#### VM 1 - Jump-Box

Open the Azure portal and search for "virtual machines."

- Use the **+ Add** button or the **Create virtual machine** button to create a new VM.


Use the following settings for this VM: 

- Resource group: Choose the same resource group that you created for the Purple Team.

- Virtual machine name: Use the name "Jump Box Provisioner."

- Region: Use the same region that you used for your other resources.
	- Note that availability of VM's in Azure could cause you to change the region where your VM's are created.

- Availability options: We will use this setting for other machines. For our jump box, we will leave this on the default setting.

- Image: Chose the Ubuntu Server 18.04 option.

- Chose the VM option that has:
  - Whose offering is **Standard - B1s**
  - 1 CPU
  - 1 RAM

For SSH, use the following settings: 

- Authentication type: SSH public key.

- Username: Create any username you like.

- SSH public key: Paste the public key string that you copied earlier.

- Public inbound ports: Ignore this setting. It will be overwritten when you choose your security group.

- Select inbound ports: Ignore this setting. It will be overwritten when you choose your security group.

Move to the **Networking** tab and set the following settings:

- Virtual network: Choose the VNet you created for the Purple Team.

- Subnet: Choose the subnet that you created earlier.

- Public IP: This can be kept as default. 

- NIC network security group: Choose the Advanced option so we can specify our custom security group.

- Configure network security group: Choose your Purple Team network security group.

- Accelerated networking: Keep as the default setting (Off).


- Load balancing: Keep as the default setting (None).

    ![](1/Images/VM/VMNetworking-b.png)

- Click on **Review + create**.

    ![](1/Images/VM/FinalizeVM.png)

- Finalize all your settings and create the VM by clicking on the **Create** button.

#### VM's 2 and 3 - Web VM's

Create 2 more new VMs. Keep the following in mind when configuring these VM's:
- Each VM should be named "Web-1" and "Web-2"

- These VM's need to be in the same resource group you are using for all other resources.

- The VM's should be located in the same region as your resource group and security group.
	- Note that availability of VM's in Azure could cause you to change the region where your VM's are created.
	- The goal is to create 3 machines in the same resource group attached to the same security group. If you cannot add 3 machines to the resource group and security group that you have, a new resource group and security group may need to be created in another region.

- The administrative username should make sense for this scenario. You should use the same admin name for all 3 machines. Make sure to take a note of this name as you will need it to login later.

- **SSH Key:** Later in the lab setup, we will overwrite these SSH keys. For now, use the SSH key that you created in the first VM setup.
    - Run: `cat ~/.ssh/id_rsa.pub` and copy the key.

- Choose the VM option that has:
  - Whose offering is **Standard - B1ms**
  - 1 CPU
  - 2 RAM

**Note:** These web machines should have _2 GB_ of RAM and the Jump-Box only needs _1 GB_. All 3 machines should only have _1 vCPU_ because the free Azure account only allows _4 vCPU's_ in total per region.

**VERY IMPORTANT:** Make sure both of these VM's are in the same availability Set. Machines that are not in the same availability set cannot be added to the same load balancer later, and will have to be deleted and recreated in the same availability set.  
- Under Availability Options, select 'Availability Set'. Click on 'Create New' under the Availability set. Give it an appropriate name. After creating it on the first VM, choose it for the second VM.

![](1/Images/Avail_Set/Avail-Set.png)

In the **Networking** tab and set the following settings:

- Virtual network: Choose the VNet you created for the Purple Team.

- Subnet: Choose the subnet that you created earlier.

- Public IP: NONE! Make sure these web VM's do not have a public IP address.

![](1/Images/Avail_Set/No-Ip.png)

- NIC network security group: Choose the Advanced option so we can specify our custom security group.

- Configure network security group: Choose your Purple Team network security group.

- Load balancing: Keep as the default setting (No).

**NOTE:** Notice that these machines will not be accessible at this time because our security group is blocking all traffic. We will configure access to these machines later.

Final Web VM-1

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/VM's.png)

#### Setting up your Jump Box Administration

The goal of this step is to create a security group rule to allow SSH connections only from your current IP address, and to connect to your new virtual machine for management.

---


1. Create a security group rule to allow SSH connections from your current IP address.

2. Create a rule allowing SSH connections from your IP address. 

    - Choose **Inbound security rules** on the left.

    - Click **+ Add** to add a rule.

        - Source: Use the **IP Addresses** setting, with your IP address in the field.

        - Source port ranges: Set to **Any** or '*' here.
            - Destination: This can be set **VirtualNetwork** but a better setting is to specify the internal IP of your jump box to really limit this traffic.

        - Service: This can be set to **SSH**

        - Destination port ranges: Since we chose SSH, it will default to `22`.

        - Protocol: This will default to **TCP**.

        - Action: Set to **Allow** traffic.

        - Priority: less than 4096

        - Name: Name this rule anything you like

        - Description: Write a short description similar to: "Allow SSH from my IP." 


	![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSHInboundRule.png)


4. Used the command line to SSH to the VM for administration. Windows users should use GitBash.

    - The command to connect is `ssh admin-username@VM-public-IP`.

    
   ![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/SSHKey.png)

---

#### Docker Container Setup

The goal of this step is to configure the jump box to run Docker containers and to install a container.

1. Start by installing `docker.io` on your Jump box.

    - Run `sudo apt update` then `sudo apt install docker.io`
    
2. Verify that the Docker service is running.

    - Run `sudo systemctl status docker`

    ![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/DockerInstall.png)

3. Once Docker is installed, pulled the container `cyberxsecurity/ansible`.

    - Run `sudo docker pull cyberxsecurity/ansible`.

![]( https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/Ansible.png)

  
   
![](2/Images/Docker_Ansible/Container_Connected.png)

4. Create a new security group rule that allows your jump box machine full access to your VNet.

    - Get the private IP address of your jump box.

    - Go to your security group settings and create an inbound rule. Create rules allowing SSH connections from your IP address.

       - Source: Use the **IP Addresses** setting with your jump box's internal IP address in the field.

        - Source port ranges: **Any** or * can be listed here.

        - Destination: Set to **VirtualNetwork**.

        - Service: Select **SSH**

        - Destination port ranges: This will default to port `22`.

        - Protocol: This will default to **TCP**.

        - Action: Set to **Allow** traffic from your jump box.

        - Priority: Priority must be a lower number than your rule to deny all traffic.

        - Name: Name this rule anything you like.
        - Description: Write a short description similar to: "Allow SSH from the jump box IP."


---

#### Setup your Provisioner

In this step, I launched a new VM from the Azure portal that could only be accessed using a new SSH key from the container running inside the jump box.

1. Connect to the Ansible container. Once connected, I created a new SSH key and copy the public key.

    - Ran `docker run -it cyberxsecurity/ansible /bin/bash` to start the container and connect to it. 
     ```

    - Ran `ls .ssh/` to view the keys.

    - Ran `cat .ssh/id_rsa.pub` to display the public key.

    - Copy the public key string.

2. Returned to the Azure portal and located one of the web-vm's details page.

		- Reset the Vm's password and use the container's new public key for the SSH user.

    - Got the internal IP for the new VM from the Details page.

3. After the VM launches, tested your connection using `ssh` from the jump box Ansible container. 

![](https://github.com/AncyThomas-dev/WilldanInterview/blob/main/Azure/Images/Azure%20Resource%20Group/sshintoweb1.png)


4. Modified the Ansible config file and hosts file.

   ```
     - Added the web VM internal IP addresses to the Ansible hosts file.

    - Open the file with `nano /etc/ansible/hosts`.
    - Uncomment the `[webservers]` header line.
    - Add the internal IP address under the `[webservers]` header.
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



