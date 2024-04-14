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
- Password: *Something You Can Easily Remember`

- Select Inbound Ports: `RDP (3389)

- Check: `Licensing Box`

- *<ins>When you're done typing in all the information</ins>:*

  - Go To: `Networking` Tab (So that you can see what Virtual Network [VNET] the VM creates, which you will need to know for when you create VM2 in the next step)

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

- Select the Resoucre Group you created in step 1: `RG-LAB-02`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/9101c495-f928-4aa1-8e9e-6008ce9e1a57"><br>

***

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8ebb6247-7180-4317-8c5a-0f16e49a68e2"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/913067ed-149d-459a-a4b1-033ace987c37"><br>

***

<p align="center">
<img width="800" alt="isolated" src=""><br>
<p align="center">
<img width="800" alt="isolated" src=""><br>
<p align="center">
<img width="800" alt="isolated" src=""><br>
<p align="center">
<img width="800" alt="isolated" src=""><br>
<p align="center">
<img width="800" alt="isolated" src=""><br>
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

***

### 

- 

<p align="center">
<img width="800" alt="isolated" src=""><br>

***

</details>

<details>

<summary>

### Part 2: Install Wireshark

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
