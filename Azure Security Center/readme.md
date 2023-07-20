## Azure Security Center

Microsoft Azure Security Center is a set of tools for monitoring and managing the security of virtual machines and other cloud computing resources within the Microsoft Azure public cloud.

Administrators access the Azure Security Center through the Azure management portal. Within the Security Center, they can find a number of security tools intended to prevent, detect and respond to potential security threats to their Azure deployment. Organizations must have an Azure subscription to access the Security Center.

The main features of Azure Security Center include:

    Policy configuration -- allows admins to establish a set of security-related controls for a specific Azure subscription or resource group. An Azure resource group refers to the collection of Azure resources, such as a VM, storage, database or virtual network, required to run an application.
    Data collection -- gathers data about Azure resources to ensure policies are enforced. The service also enables daily scanning of VMs for potential security threats. Admins can choose the Azure storage account in which collected VM data is stored.
    Recommendations -- provides a list of suggestions for creating Azure security policies based on the security needs of your specific Azure resources. Potential recommendations include deploying a missing system update, provisioning antimalware and using network security groups to control VM traffic.
    Alerts -- issues an alert when potential security threats, such as compromised VMs or malware, are detected. Azure Security Center automatically collects and integrates log data about Azure resources to produce alerts.

#### Here is the screenshot of the Security Score of all the resources I have created in Azure

![](https://github.com/AncyThomas-dev/Cybersecurity/blob/main/Azure%20Security%20Center/Images/AzureSecurityScore.png)

#### Recommendations to improve Security Score

![](https://github.com/AncyThomas-dev/Cybersecurity/blob/main/Azure%20Security%20Center/Images/Recommendations.png)

#### Remediations I tried to improve the Security Score

![](https://github.com/AncyThomas-dev/Cybersecurity/blob/main/Azure%20Security%20Center/Images/Remediations.png)

I was not able to improve the securtiy score of due to the limitations with my subscription.

From what I have read, to improve your secure score, you have to implement the security recommendations for your environment. 

