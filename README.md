# Project 2: Secure Enterprise Storage with Network Isolation
Topics/technologies Covered: Virtual Networks, NSGs, Private Endpoints, Storage Firewalls, Encryption, ARM Templates, Security Monitoring

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

3. Create Storage Account with Security-First Configuration

<img width="1730" height="864" alt="Screenshot 2025-11-16 100603" src="https://github.com/user-attachments/assets/eb1b5f5d-12ba-45c2-9397-dde12d0ed48f" />


_Critical configurations_: (**Shown in the screenshots below**)
+   Network routing: Premium (Microsoft network routing) for optimal security
+   Public access: Disabled from the start
+   Network access: Enabled from selected virtual networks only
+   TLS version: Require TLS 1.2

<img width="1329" height="855" alt="Screenshot 2025-11-16 102324" src="https://github.com/user-attachments/assets/630ae248-10f2-4917-80f2-0113c9bef0be" />

_Disabled public access at creation to enforce private-only connectivity, preventing accidental exposure._

<img width="1337" height="864" alt="image" src="https://github.com/user-attachments/assets/786e30fb-aa33-4f09-8752-4ca5506fe79d" />


<img width="1260" height="328" alt="Screenshot 2025-11-16 103438" src="https://github.com/user-attachments/assets/14915cf0-8056-41d0-8d03-37d52516c231" />

Under Data Protection tab, I enabled "Enable soft delete for blobs", "Enable soft delete for containers", "Enable soft delete for file shares" and left them at 7 days. I did not enable any of the Tracking options as I don't think it was necessary for this project.

<img width="1081" height="865" alt="Screenshot 2025-11-16 104637" src="https://github.com/user-attachments/assets/952acf75-8000-4ea1-9a7d-22ca0bc3f6d5" />

I opted for 'Microsoft-managed keys(MMK)' for the "Encryption type". I allowed 'Blobs and files only' at the "Enable support for customer-managed keys" section, and ticked the option to "Enable infrastructure encryption" to add another layer of security to my deployment.

<img width="1081" height="865" alt="Screenshot 2025-11-16 104637" src="https://github.com/user-attachments/assets/484a805f-9ae2-4e85-a38c-2bcef8c06ebb" />

Deployed storage account screenshot below.

<img width="1498" height="811" alt="Screenshot 2025-11-16 105500" src="https://github.com/user-attachments/assets/494eac8e-bf02-4761-a052-672b386db106" />

4. Create a Private Endpoint with DNS integration

<img width="1067" height="858" alt="Screenshot 2025-11-16 110104" src="https://github.com/user-attachments/assets/c4cd2f56-45c5-41f4-841b-1df5fcf44439" />

I used 'Microsoft.Storage/storageAccounts' because I am implementing a private endpoint for a Storage Account. DNS integration during setup, ensures private name resolution without public DNS exposure

<img width="1505" height="802" alt="Screenshot 2025-11-16 202436" src="https://github.com/user-attachments/assets/04dda7d0-eaeb-485d-afc7-d0b02cae3716" />

Screenshot of successfully deployed private endpoint.

5. Enable Advanced Encryption & Security Features
+ I created a key vault
+ Then created managed identity to access the storage account with customer-managed key (CMK) instead of Microsoft managed key. Used customer-managed keys to maintain control over encryption keys and enabled advanced threat protection for suspicious activity monitoring.
+ Successfully encrypted the storage account.

<img width="576" height="117" alt="Screenshot 2025-11-16 215716" src="https://github.com/user-attachments/assets/09449dc1-0e0a-403a-9e28-80a3e1c4811a" />









