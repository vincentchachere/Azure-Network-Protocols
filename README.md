<p align="center">
<img width="1000" alt="isolated" src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

# Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

## Operating Systems Used

- Windows 10 (21H2)
- Linux Ubuntu Server 20.04

## High-Level Steps

- Create Resources
- Install Wireshark
- Observe Differing Network Protocols suh as:
  - ICMP Traffic
  - SSH Traffic
  - DHCP Traffic
  - DNS Traffic
  - RDP Traffic

## Additional Resources

- For more information on Azure Networking Products click [here](https://azure.microsoft.com/en-us/products/category/networking)

## Actions and Observations

<details>

<summary> 
  
### Part 1: Install Resources
  
</summary> 

### 1. ) Create your Resource Group

- Search: `Resource Group`

- Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8817b441-20c7-4402-96ba-93590df8d535"><br>

***

- Subscription: `Azure Subscription 1`

- Resource Group Name: `RG-LAB-02`

- Region: `(US) West 3`

- Select: `Review + Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/5ff75287-da94-4e53-824e-7111f412c7a0"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/6b0736f2-dd6a-4ed3-9e5b-a72570a4a46e"><br>

***

- Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/73aa9fd5-42d8-4590-8596-58cd61ddf67d"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/25da2c2f-598f-4bf6-b3e2-9ef5a7a8c773"><br>

***

### 2. ) Create a Windows 10 Virtual Machine (VM1)

- Search: `Virtual Machine`

- Click: `Create > Azure Virtual Machine`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/9101c495-f928-4aa1-8e9e-6008ce9e1a57"><br>

***

- Subscription: `Azure Subscription 1`

- Resource Group Name: `RG-LAB-02`

- Virtual Machine Name: `VM1`

- Region: `(US) West 3`

- Availability Options: `No Infrastructure Redundancy Required`

- Security Type: `Standard`

- Image: `Windows 10 Pro, version 22H2 - x64 Gen2`

- (Disk) Size: `Standard LD45_v3 - 4 vcpus, 16 GiB memory ($140.16/month)`

- User: `labuser`
- Password: *`Something You Can Easily Remember`*

- Select Inbound Ports: `RDP (3389)`

- Check: `Licensing Box`

- *<ins>When you're done typing in all the information</ins>:*

  - Go To: `Networking` Tab
    - Take a mental note of the Virtual Network [VNET] the VM creates. You will need to know this for when you create VM2 in the next step.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/81279c8d-be5a-4e55-a046-9c197e84dbf4"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4ff4a264-ad0c-47f3-9cc3-0ce2877df31b"><br>

***

- The Virtual Network [VNET] created for this Virtual Machine is: `VM-vnet`

- Click: `Review + Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/0ce9eed6-efda-4359-a53c-7e2d1895059b"><br>

***

- *<ins>Review the information you typed in and once you verify it's all correct</ins>:*

  - Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/5757c155-fc86-4cc0-aa29-10b699d26489"><br>

***

### 3. ) Create a Linux [Ubuntu] Virtual Machine (VM2)

- Search: `Virtual Machine`

- Click: `Create > Azure Virtual Machine`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/9101c495-f928-4aa1-8e9e-6008ce9e1a57"><br>

***

- Subscription: `Azure Subscription 1`

- Resource Group Name: `RG-LAB-02`

- Virtual Machine Name: `VM2`

- Region: `(US) West 3`

- Availability Options: `No Infrastructure Redundancy Required`

- Security Type: `Standard`

- Image: `Ubuntu Server 20.04 LTS - x64 Gen2`

- (Disk) Size: `Standard LD45_v3 - 4 vcpus, 16 GiB memory ($140.16/month)`

- Authentication Type: `Password`

- User: `labuser`

- Password: *`Something You Can Easily Remember`*
  - *Yes, the username and password can be the same as the VM1 login. You can change it if you want, but be sure to remember it.*

- Select Inbound Ports: `SSH (22)`

- *<ins>When you're done typing in all the information</ins>:*

  - Go To: `Networking` Tab
    - Make sure the Virtual Network [VNET] is the same as the one you took a mental note of in step 2 when creating VM1.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8ebb6247-7180-4317-8c5a-0f16e49a68e2"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/913067ed-149d-459a-a4b1-033ace987c37"><br>

***

- *<ins>When you confirm the VNET matches the one created when you made VM1</ins>:*

  - Click: `Review + Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8908f4bd-35df-492d-9ad2-b2f94e7e925d"><br>

***

- *<ins>Review the information you typed in and once you verify it's all correct</ins>:*
  - Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/c661b52f-a636-4c7d-8160-52c6f4c5d388"><br>

***

### 4. ) Connect VM1 to RDP

ATTENTION: *For how to connect your virtual machine to Remote Desktop on macOS or Windows go to [my lab](https://github.com/vincentchachere/virtual-machine) on how to create a virtual machine on Step 5 for instructions on how to do that.*

- Copy: `VM1's Public Address`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/2e2b2438-0332-42ad-a731-77d8aa2c548c"><br>

***

- Select: `Add PC`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/6b95ec8d-afc4-4365-b799-f4326250fdd5"><br>

***

- Paste: `VM1's Public IP Address`

- Select: `Add`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/fc7c41f6-4158-4865-97bc-5e20626a66f6"><br>

***

- Double-Click: `VM1's Remote Desktop Account` *(The Blue Rectangle)*

- Select: `Connect`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/6c46f335-7a53-47b6-82a5-a4c6b7e8a696"><br>

***

- User: `labuser`

- Password: `The one you created in Step 2`

- Select: `Continue`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/64e9e366-716c-4076-ae91-40e59fd2cba4"><br>

***

- Select: `Continue`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/47f34c0c-8497-4e91-b64c-3c90d5bb41de"><br>

***

- Uncheck: `All the Boxes`

- Select: `Accept`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/d9869250-3e6c-4780-83dd-bef4993b40d6"><br>

***

- Now we are inside the Virtual Machine (VM1)!

- Continue to Part 2 of this lab, which is Installing Wireshark inside VM1.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/a97043cf-1987-417f-bee5-0d8e60653de3"><br>

</details>

<details>

<summary>

### Part 2: Install Wireshark

</summary>

### 5. ) Open Microsoft Edge Web Browser and Install Wireshark

- *<ins>Uncheck & Skip all the prompts it asks you</ins>.*

- Search: `Install Wireshark`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/bc3d9b64-5654-45f5-8628-b9bf21057d01"><br>

***

- Select: `Wireshark - Download`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/e05430cd-8144-474b-8eb0-a846b4d2f7cc"><br>

***

- Select: `Windows x64 Installer`

- Click: `Open file`

- Click: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/919ed2df-f201-4819-8c74-7d0fe76cabfa"><br>

***

- Select: `Noted`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/75d5f591-7934-463a-9397-94f15523699b"><br>

***

- Select: `Next` 

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/d67cc691-58c7-429e-bc94-de709e30681c"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/9543f800-4e5a-4b76-930d-7e2cad6bb951"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/218e8cbf-5c72-4eba-be38-f5432849016b"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/72418587-57de-46b8-b521-08a8f1e86b3f"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8e03c913-3f18-41e8-8c58-b5b2a0c6b80b"><br>

***

- Select: `Install`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/43a1a6f3-28ca-40b8-b96a-70a116c19641"><br>

***

- Select: `I Agree`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/2480642b-9555-4f9a-8fee-178790f07edd"><br>

***

- Select: `Install`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/1c200a7b-e775-4214-909e-2a890ed63eed"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/cdf73159-75c2-4f7f-bb11-f1637393c804"><br>

***

- Select: `Finish`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/a4d81ffc-39be-42f1-897f-57f1836fdc63"><br>

***

- Select: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/10cca54b-98a7-45db-b2be-97f4fed8b99d"><br>

***

- Select: `Finish`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/d87d5845-d493-48e1-9d86-f4850bc98c95"><br>

***

- Close your Microsoft Web Browser and Search: `Wireshark`

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

</details>

<details>

<summary>

### Part 3: Observe Differing Network Protocols

</summary>

### 

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

### 

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

### 

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

</details>
