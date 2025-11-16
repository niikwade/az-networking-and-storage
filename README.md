# Project 2: Azure Networking and Storage
Topics/technologies Covered: Virtual Networks, NSGs, Storage Accounts, Encryption, Protocols, ARM Templates, Security Monitoring

### **Summary**

This project demonstrates how to deploy a secure, enterprise-grade storage solution with network isolation, private connectivity, and automating resource deployment using ARM templates. It demonstrates networking and storage skills and knowledge.

### **Scenario**

A company needs to deploy a storage account for sensitive financial data that must be:
+  Isolated from public internet
+  Accessible only from specific subnets
+  Encrypted with customer-managed keys
+  Monitored for suspicious access
+  Deployed via automated, repeatable templates

### Steps
1. Create VNet
   
<img width="1251" height="853" alt="Screenshot 2025-11-15 225723" src="https://github.com/user-attachments/assets/20972523-3664-4c70-8093-540efe6c2586" />

<img width="1757" height="866" alt="Screenshot 2025-11-15 225453" src="https://github.com/user-attachments/assets/c1179315-eed3-4a1d-99f6-e7509e971e44" />

Under the "IP addresses" tab in the Azure portal, I added 2 subnets - *web* and *db*. Azure created a default subnet, which I renamed to *privateendpoint*. I renamed as such to it would serve as a dedicated subnet for private endpoints to simplify NSG management and follow Azure best practices for service isolation. I created these subnets for network isolation.

<img width="1745" height="861" alt="Screenshot 2025-11-15 225546" src="https://github.com/user-attachments/assets/fedbb875-4784-45a7-a892-b090b1750894" />

A screenshot of the successfully deployed VNet is below.

<img width="1517" height="849" alt="Screenshot 2025-11-15 231216" src="https://github.com/user-attachments/assets/007ac830-f8e0-4805-a7c1-59e638842bd3" />

2. Deploy a Network Security Group (NSG)

<img width="1528" height="868" alt="Screenshot 2025-11-15 231525" src="https://github.com/user-attachments/assets/52b87c14-2aaa-4f2f-b78a-701d8e122645" />


<img width="1894" height="850" alt="Screenshot 2025-11-16 000544" src="https://github.com/user-attachments/assets/7faaa02f-609a-4c5e-ae47-aac3c2844a55" />

The rules in the web NSG are displayed in the screenshot above. The 'AllowVnetInBound', 'AllowAzureLoadBalancerInBound', 'DenyAllInBound' rules are Azure default rules that cannot be deleted. I added another rule 'allowHTTPS' to allow HTTPS traffic from the internet. I assigned it a priority of 100, a lower number than the other defaults, as I wanted that rule to be evalusted first before higher priority rules (custom rules > default rules). I added another rule 'allowStorageOutbound', with Priority 110, a Service Tag Destination --> Storage. This allows for dynamic IP management and supports secure web access and cloud-native storage integration.


NSG for *db* 

<img width="1908" height="804" alt="Screenshot 2025-11-16 001621" src="https://github.com/user-attachments/assets/ed276ee6-71af-47f2-8f10-e1e9ec95f9ce" />

I added another rule to allow connection from the web subnet. 







