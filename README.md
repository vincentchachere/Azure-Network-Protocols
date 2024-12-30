# Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines

In this lab, we will explore network security groups (NSGs) and inspect network traffic between Azure virtual machines using Wireshark. We will analyze various network protocols, including ICMP, SSH, DHCP, DNS, and RDP, and use command-line tools to observe how these protocols facilitate communication between devices.

<p align="center">
<img width="1000" alt="isolated" src="https://github.com/user-attachments/assets/81f9d94c-a3e4-4a9f-b812-8c425f5ef005" alt="Traffic Examination"/>
</p>

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDP, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)
- PowerShell

## Operating Systems Used

- Windows 10 (21H2)
- Linux Ubuntu Server 20.04

## High-Level Steps

- Create Resources
- Install Wireshark
- Observe Differing Network Protocols::
  - ICMP Traffic (Ping Command)
  - SSH Traffic (tcp.port == 22)
  - DHCP Traffic (UDP port 67 on the server side and UDP port 68 on the client side)
  - DNS Traffic (udp.port == 53)
  - RDP Traffic (tcp.port == 3389)

## Additional Resources

- For more information on Azure Networking Products click [here](https://azure.microsoft.com/en-us/products/category/networking)

## Actions and Observations

<details>

<summary> 
  
### ⚙️ Part 1: Install Resources
  
</summary> 

### 1. ) Create your Resource Group

- Search: `Resource Group`

- Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8817b441-20c7-4402-96ba-93590df8d535"><br>

<br>
<br>
<br>

<ins>Create your Resource Group</ins>:

- Subscription: `Azure Subscription 1`

- Resource Group Name: `RG-LAB-02`

- Region: `(US) West 3`

- Select: `Review + Create`

- Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/5ff75287-da94-4e53-824e-7111f412c7a0"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/6b0736f2-dd6a-4ed3-9e5b-a72570a4a46e"><br>

<br>
<br>
<br>

### 2. ) Create a Windows 10 Virtual Machine (VM1)

- Search: `Virtual Machine`

- Click: `Create` > Select: `Azure Virtual Machine`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/9101c495-f928-4aa1-8e9e-6008ce9e1a57"><br>

<br>
<br>
<br>

<ins>Create a Windows 10 Virtual Machine (VM1)<ins>:

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

- Go To: `Networking` Tab to view your Virtual Network (Vnet)

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/81279c8d-be5a-4e55-a046-9c197e84dbf4"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4ff4a264-ad0c-47f3-9cc3-0ce2877df31b"><br>

<br>
<br>
<br>

<ins>Within your Networking Tab<ins>:

*Take a mental note of the Virtual Network (Vnet) the VM creates. You will need to know this for when you create VM2 in the next step.*

  - The Virtual Network (Vnet) created for this Virtual Machine is: `VM1-vnet`

  - Click: `Review + Create`

  - Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/0ce9eed6-efda-4359-a53c-7e2d1895059b"><br>

<br>
<br>
<br>

### 3. ) Create a Linux [Ubuntu] Virtual Machine (VM2)

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
  - *The username and password can be the same as the VM1 login. You can change it if you want, just remember it.*

- Select Inbound Ports: `SSH (22)`

- Go To: `Networking` Tab to verify it's the same Virtual Network (Vnet) as VM1

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8ebb6247-7180-4317-8c5a-0f16e49a68e2"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/913067ed-149d-459a-a4b1-033ace987c37"><br>

<br>
<br>
<br>

<ins>When you verify it's the same Virtual Network (Vnet) as VM1</ins>:

  - Click: `Review + Create`

  - Click: `Create`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/8908f4bd-35df-492d-9ad2-b2f94e7e925d"><br>

<br>
<br>
<br>

### 4. ) Remote Desktop (RDP) into VM1

- User: `labuser`

- Password: `The one you created in Step 2`

- Select: `Continue`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/14f05798-5905-4e72-827a-4161e5a7dea7"><br>

<br>
<br>
<br>

<ins>Remote Desktop (RDP) into VM1</ins>:

- Uncheck: `All the Boxes`

- Select: `Accept`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/d9869250-3e6c-4780-83dd-bef4993b40d6"><br>

<br>
<br>
<br>

<ins>Once inside continue to Part 2 of this lab, which is Installing Wireshark inside VM1</ins>.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/a97043cf-1987-417f-bee5-0d8e60653de3"><br>

</details>

<details>

<summary>

### ⚙️ Part 2: Install Wireshark

</summary>

### 5. ) Open Microsoft Edge Web Browser and Install Wireshark

*Uncheck & Skip all the prompts it asks you*

- Browse: `www.wireshark.org`

- Select: `Windows x64 Installer`

- Click: `Open file`

- Click: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/919ed2df-f201-4819-8c74-7d0fe76cabfa"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `Noted`

- Click: `Next` until you reach: `USB Capture` (*Next Image*)

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/75d5f591-7934-463a-9397-94f15523699b"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `Install`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/43a1a6f3-28ca-40b8-b96a-70a116c19641"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `I Agree`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/2480642b-9555-4f9a-8fee-178790f07edd"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `Install`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/1c200a7b-e775-4214-909e-2a890ed63eed"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `Next`

*Wait till that loads then..*

- Select: `Finish`

- Click: `Next`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/cdf73159-75c2-4f7f-bb11-f1637393c804"><br>

<br>
<br>
<br>

<ins>Installing Wireshark Instructions</ins>:

- Select: `Finish`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/d87d5845-d493-48e1-9d86-f4850bc98c95"><br>

<br>
<br>
<br>

<ins>Close your Microsoft Web Browser and Open Wireshark</ins>:

- Search: `Wireshark`

*Open Wireshark to full screen*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/eee37f99-4c7a-4789-a14a-6816b2db80fd"><br>

<br>
<br>
<br>

<ins>Inside Wireshark</ins>:

- Select: `Ethernet 2`

- Click: The `Blue Wireshark Icon` in the top left corner under 'File'.

*Continue to Part 3 of this lab where we'll be <ins>Observing Differing Network Protocols</ins>.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/13ccd112-0f5b-4e56-b29c-eda71f099530"><br>

</details>

<details>

<summary>

### ⚙️ Part 3: Observe Differing Network Protocols

</summary>

<details>

<summary>

### 6. ) Observe ICMP Traffic

</summary>

<ins>First, we'll observe and filter by ICMP traffic only.</ins>

<ins>EXPLANATION</ins>: ICMP (Internet Control Message Protocol) is used for reporting network errors and diagnostics. It enables the ping command, where an echo request is sent to a target host, which replies with an echo reply. ICMP messages notify the sender of issues like undelivered data or unreachable hosts.

- On your Windows 10 Virtual Machine (VM1), you may notice it being spammed with traffic before you start any tasks. This is normal due to background processes.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/0c789160-acd6-4157-906f-721689cfebab"><br>

<br>
<br>
<br>

<ins>Observing and Filtering by ICMP Traffic Only</ins>:

- Type in: `icmp` into the Wireshark Filter Bar

- Press: `Enter`

<ins>Notice that no traffic appears because only ICMP packets are displayed</ins>.

To ping VM2, you need its private IP address. So we'll go back to our azure portal and retrieve the private IP from the Linux (Ubuntu) VM2, then ping it from the Windows 10 VM1.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4d18d8f0-f9fe-403e-bae1-71815cbfeff8"><br>

<br>
<br>
<br>

<ins>Retrieving VM2's Private IP Address</ins>:

Go To: `Azure Home Portal` > `Virtual Machine`

*Verify VM1 and VM1 are on the same Vnet to ensure a successful ping.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/3e17fdea-f021-404d-b079-68c22daf9f62"><br>

<br>
<br>
<br>

<ins>Back inside VM1 with Wireshark</ins>:

- Ping VM2's private ip address and observe the traffic being sent between VM1 and VM2.

*As you can see four packets were sent to VM2, and four were received, with no packet loss—indicating a successful ping.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/b89bc760-fbdb-4bc1-8913-9015b80c277b"><br>

<br>
<br>
<br>

<ins>Observing and Filtering by ICMP Traffic Only</ins>:

We can even ping other IP Addresses and domain names, such as: www.google.com.

To test this out yourself just type in: `ping www.google.com -4` (the -4 stands for IPv4)

*As you see, just like you did with pinging the VM2's private ip address, there were four packets sent to Google's domain and four packets recieved by Google-indicating successful ping.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/71cd1264-b413-49a4-83da-42874fcc5414"><br>

<br>
<br>
<br>

<ins>Observing the Eternal Ping</ins>:

<ins>EXPLANATION</ins>: Next we'll refresh our display in Wireshark by clicking the `Green Wireshark Icon` in the top left corner of your Wireshark screen.

  - The Eternal Ping is like a regular ping, but it continues indefinitely until stopped or blocked. To block it, you’ll deny all ICMP traffic to VM2.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f7e93a65-faf8-4dad-9a49-1663ae8166a6"><br>

<br>
<br>
<br>

<ins>Observing the Eternal Ping</ins>:

Just as before you will ping VM2's private ip address, but for this 'eternal ping' you will put a `-t` at the end, making it an 'eternal ping'.

- The Eternal Ping will be: `ping 10.0.0.5 -t` (*or whatever your VM2's Private IP Address is*)

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/16578bd8-fa8b-4675-8fb7-d2f9efe9b815"><br>

<br>
<br>
<br>

<ins>Denying ICMP Traffic</ins>:

As mentioned earlier we will deny all ICMP traffic going to VM2 and observe the changes.

- Go back into your: `Azure Portal Home Screen` to deny all icmp traffic to VM2.

- Search and Select: `Network Security Groups`

- Select: `VM2-nsg`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/a1317929-2ca3-45d6-bd27-8e3699731d18"><br>

<br>
<br>
<br>

<ins>Denying ICMP Traffic</ins>:

- Select: `Inbound Security Rules`

- Source: `*`

- Source Port Ranges: `Any`

- Service: `Custom`

- Destination Port Ranges: `*`

- Protocol: `ICMP`

- Action: `Deny`

- Priority: `200`

- Name: `DENY_ICMP_FROM_ANYWHERE`

- Click: `Add`

*Verify this Inbound Security Rule gets added.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/07472112-e888-44d5-8a68-c4bfef647595"><br>

<br>
<br>
<br>

<ins>Denying ICMP Traffic</ins>:

Once you've verified it actually got added go back inside VM1 and observe the changes.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4d686036-5db7-4755-897b-934d342bb311"><br>

<br>
<br>
<br>

<ins>Denying ICMP Traffic</ins>:

As you see, it almost instantly stops all ICMP traffic to VM2.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/56f671c4-b567-47e7-ac75-51722bd0d7e3"><br>

<br>
<br>
<br>

<ins>Denying ICMP Traffic</ins>:

Once you see the results:

  - Press: `Control + C` to stop the ping.
 
Next, we will observe SSH traffic going to VM2.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f920c09f-500c-47f9-8334-77300842231e"><br>

</details>

<details>

<summary>

### 7. ) Observe SSH Traffic

</summary>

<ins>EXPLANATION</ins>: In Wireshark, filter for SSH traffic by using `tcp.port == 22` (*TCP is the protocol SSH uses and 22 is the port SSH uses*). SSH, unlike Remote Desktop, is a secure network protocol that allows system administrators to access a computer over an unsecured network. Instead of pinging VM2, connect to it from VM1 via SSH.

- Click: `Green Wireshark Icon` in the top left corner

- Select: `Continue without saving`

- Type: `ssh` into the Wireshark Filter Bar

- Press: `Enter`

- Type: `ssh labuser@10.0.0.5` into Powershell

- Press: `Enter`

*Notice your VM2's login username (labuser) and your VM2's Private IP Address being used for this protocol.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/e108d78f-e23f-47a4-a1f4-8fd180f6c496"><br>

<br>
<br>
<br>

<ins>It will ask you inside Powershell</ins>: "Are you sure you want to continue?"
    
  - Type In: `yes `

  - Press: `Enter`

*You will be prompted to enter the password created for VM2. Note that the password will not be visible as you type—this is normal.*

  - Insert: `VM2's password` (*If you mess up press backspace/delete a bunch of times*)

  - Press: `Enter`

*Next, we will test out some commands within the SSH protocol inside Powershell.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/23a14ad1-de46-45f9-9e6a-62015a50eabf">

<br>
<br>
<br>

<ins>Testing out Commands with the SSH protocol inside Powershell</ins>:

- 1st Command: `id`

- 2nd Command: `uname -a`

<ins>You can test Linux commands using the SSH protocol directly in PowerShell, such as</ins>:

- 3rd Linux Command: `pwd`

- 4th Linux Command: `ls`

- 5th Linux Command: `touch`

<ins>You can logout/exit the SSH protocol inside Powershell by</ins>:

- Typing In: `exit` then..
 
- Press: `Enter`

*Notice that each time you enter a command in PowerShell, traffic appears in Wireshark.*

*The `id` command is often used with other tools, like the net command, to view or modify user or group information.*

*The `uname` command displays system information, such as the kernel version running on the IBM Netezza host.The `uname -a` option prints all system information, including kernel name, hostname, kernel release, kernel version, hardware name, platform, and operating system.*

*The `pwd` command (Print Working Directory) outputs the full path of the current working directory.*

*The `ls` command (List) displays files and directories in Unix and Unix-like systems.*

*The `touch` command updates the access and/or modification date of a file or directory.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/6a5b46ec-ca9f-4148-aa89-4e94e570d7d4"><br>

</details>

<details>

<summary>

### 8. ) Observe DHCP Traffic

</summary>

<ins>EXPLANATION</ins>: DHCP (Dynamic Host Configuration Protocol) uses UDP port 67 (server) and UDP port 68 (client). It has two versions, DHCPv4 and DHCPv6, for IPv4 and IPv6, respectively, which are distinct protocols with separate ports. DHCP dynamically assigns IP addresses and network configurations to devices, enabling communication across IP networks. In Azure, an invisible DHCP server handles this process, reissuing IP addresses. This activity can be observed in Wireshark.

<ins>You can force the renewal of an ip address by doing the following</ins>:

- Type: `dhcp` into the Wireshark Filter Bar

- Type: `ipconfig /renew` inside of Powershell

- Press: `Enter`

<ins>Now, VM1 will broadcast on the Virtual Network (VNET) requesting an IP address. This occurs through a four-step process, which is as follows:</ins>:

- Discover: Client broadcasts a request to find a DHCP server.

- Offer: DHCP servers respond with available IP address offers.

- Request: Client selects an offer and requests to use the IP.

- Acknowledge: Server confirms and allocates the IP and options to the client.

*It may disconnect your remote desktop for a moment then it should reconnect. If not, then log back in as normal.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f47eb165-51f5-485c-a509-619d233299a2"><br>

</details>

<details>

<summary>

### 9. ) Observe DNS Traffic

</summary>

<ins>EXPLANATION</ins>: DNS (Domain Name System) converts domain names into IP addresses, enabling browsers to access websites and internet resources. In simple terms, it translates human-readable names (words) into machine-readable numbers (IP addresses) for communication across the internet.

<ins>Observing DNS Traffic in Wireshark</ins>:

- Type: `dns` into the Wireshark Filter Bar

- You can also use: `udp.port == 53` since DNS uses the UDP Protocol and Port 53

*There may be some DNS traffic already within Wireshark. Refresh your Wireshark to start off with a clean screen.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/317eeef0-79e9-413b-95cc-37d148e07654"><br>

<br>
<br>
<br>

<ins>Observing DNS Traffic in Wireshark</ins>:

The first command to test is nslookup (Name Server Lookup), a command-line tool for querying DNS. It maps domain names to IP addresses or retrieves other DNS records by asking the DNS server for the IP address of a given host name.

- Type In: `nslookup www.disney.com`

- Press: `Enter`

*After completing this action, you’ll notice lots of traffic flowing between your VM (as the source) and the DNS server (as the destination).*

- The traffic you see in Wireshark are some of the IP Addresses that Disney uses.

- Close: `Powershell` for the rest of this lab

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/c9e7672e-375a-4359-9e26-186e73303d1d"><br>

</details>

<details>

<summary>

### 10. ) Observe RDP Traffic

</summary>

<ins>EXPLANATION</ins>: RDP (Remote Desktop Protocol) lets users perform remote operations on other computers. It uses port 3389 and ensures secure data exchange through an encrypted communication channel. This protocol is provided by Microsoft.

<ins>Observe RDP Traffic in Wireshark</ins>:

- Type: `tcp.port == 3389` into Wireshark Filter Bar (*This is the protocol and port RDP uses*)

*Now you can observe the constant stream of live traffic happening from RDP and your VM1.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/881b2fbf-a807-4167-9eeb-e7d8beecc38a"><br>

</details>

<details>

<summary>

### 11. ) Delete your Resources

</summary>

<ins>Go back into your Azure Portal</ins>:

  - Go To: Resource Group: `RG-LAB-02`

  - Select: `Delete resource group` > 

  - Copy & Paste your Resource Group Name: `RG-LAB-02` into the Rectangular box towards the bottom right underneath where it says 'Enter resource group name to confirm deletion'.

  - Check: The `Apply force delete for selected Virtual machines and Virtual machine scale sets' Box

  - Click: `Delete`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/user-attachments/assets/5662dc0c-5b67-47da-bbf2-dbd402741cc0"><br>

<br>
<br>
<br>

<ins>Delete your Rsources</ins>:

- Delete: `NetworkWatcherRG`

*Perform the same instructions as you did with RG-LAB-02*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4e0a8677-f71b-4691-9fab-da3465121715"><br>
</p>

</details>

</details>

<h2 align="center">Final Thoughts</h2>

This lab provided hands-on experience with Azure Network Security Groups (NSGs) and network traffic analysis between virtual machines. By leveraging tools like Wireshark and command-line utilities, we explored key network protocols—ICMP, SSH, DHCP, DNS, and RDP—and gained insights into how these protocols facilitate communication. The practical exercises reinforced an understanding of traffic flow, security rules, and the critical role NSGs play in managing access and protecting resources within Azure environments.

For more on network management and security, explore the first lab in my detailed [Active Directory tutorial series](https://github.com/vincentchachere/Active-Directory-and-Azure-Setup).

Thank you for following along with this project. Your time and effort in learning and implementing these concepts are greatly appreciated.

☎️ For questions or to connect you can contact me at: www.linkedin.com/in/vincentchachere
