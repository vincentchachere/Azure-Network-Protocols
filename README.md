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
- Observe Differing Network Protocols such as:
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

### 6. ) First we will Observe and Filter by ICMP Traffic Only

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

<ins>Back inside VM1 with Wireshark</ins:

- Okay, now that you are back in Wireshark with Powershell open go ahead and ping VM2's private ip address and observe the traffic being sent between the two virtual machines (VM1 and VM2).

  - *As you can see there were 4 packets sent to VM2 and 4 packets recieved by VM2 resulting in 0 packets lost, equally a succesful ping. Congrats!*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/b89bc760-fbdb-4bc1-8913-9015b80c277b"><br>

<br>
<br>
<br>

- We can even ping other ip addresses and domain names, such as www.google.com.

  - To test this out yourself just type in: `ping www.google.com -4` (the '-4' stands for IPv4)
 
  - As you see, just like you did with pinging the VM2's private ip address, there were 4 packets sent to Google's domain and 4 packets recieved by Google.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/71cd1264-b413-49a4-83da-42874fcc5414"><br>

<br>
<br>
<br>

- <ins>EXPLANATION</ins>: So next, we will refresh our display in Wireshark by clicking the 'Green Wireshark Icon' in the top left corner of your Wireshark screen under 'Edit'.

  - Next we will do what is called an 'eternal ping'. This is basically what we just did, but instead of the ping stopping it will go on 'forever' until it is stopped and or blocked. How you will block this 'eternal ping' will be by denying all icmp traffic from getting to VM2. *(Go to the next image for instrutions.)*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f7e93a65-faf8-4dad-9a49-1663ae8166a6"><br>

<ins>EXPLANATION</ins>: So, just like before you will ping VM2's private ip address, but for this 'eternal ping' you will put a `-t` at the end, making it an 'eternal ping'.

- So the 'eternal ping' will be: `ping 10.0.0.5 -t`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/16578bd8-fa8b-4675-8fb7-d2f9efe9b815"><br>

<br>
<br>
<br>

<ins>EXPLANATION</ins>: Now, as mentioned earlier we will deny all ICMP traffic going to VM2 and observe the changes.

- Go back into your: `Azure Portal Home Screen` to deny all icmp traffic to VM2.

- Search: `Network Security Groups`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/e36dd85a-b229-4b76-a59c-be80642e79ef"><br>

<br>
<br>
<br>

- Select: `VM2-nsg`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/a1317929-2ca3-45d6-bd27-8e3699731d18"><br>

<br>
<br>
<br>

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

*<ins>Make sure it actually gets added. See the 2nd image to verify what it should look like. Then go back inside VM1 and observe what this did.</ins>* 

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/07472112-e888-44d5-8a68-c4bfef647595"><br>
<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4d686036-5db7-4755-897b-934d342bb311"><br>

<br>
<br>
<br>

- As you see, it almost instantly stops all ICMP traffic to VM2.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/56f671c4-b567-47e7-ac75-51722bd0d7e3"><br>

<br>
<br>
<br>

- Once you see the results:

  - Press: `Control + C` to stop ping.
 
*<ins>Next, we will observe SSH traffic going to VM2.</ins>*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f920c09f-500c-47f9-8334-77300842231e"><br>

<br>
<br>
<br>

### 7. ) Observe SSH Traffic

<ins>EXPLANATION</ins>: Back in Wireshark, filter for SSH (Secure Shell or Secure Socket Shell) traffic only, or since ssh uses port 22 you can also use: `tcp.port == 22` (this is the more direct way to use SSH). SSH is similar to remote desktop, except that it is a network protocol that gives users, particularly system administrators, a secure way to access a computer over an unsecured network. So, instead of pinging VM2 you will SSH into VM2 from VM1. In other words, connect from VM1 into VM2 via SSH.

- Click: `Green Wireshark Icon` in the top left corner of your Wireshark screen under 'Edit'.

- Select: `Continue without saving`

- Type in: `ssh` into the Wireshark Filter Bar

- Press: `Enter`

- Type in: `ssh labuser@10.0.0.5` into Powershell
  - *As you see, this is your VM2's login username (labuser) and your VM2's Private IP Address being used for this protocol.*

- Press: `Enter` when done doing the above action.
  - *<ins>It will ask you inside Powershell "Are you sure you want to continue?"</ins>*
    - Type in: `yes `

    - Press: `Enter`
 
- It will then prompt you to type in the password you created when making VM2, but you will not be able to see what you're typing as you type it in, this is normal.

  - *<ins>Just insert your VM2's password as normal and</ins>*:

    - Press: `Enter`

    - *(If you mess up just press the backspace/delete a bunch of times)*

><ins>NOTE</ins>: Notice, everytime you type in a command on Powershell you can see the traffic show up on Wireshark.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/0161a9d1-05cb-4d28-90d1-8d272750ae9b"><br>

***

<ins>Now you can test out some commands within the SSH protocol inside Powershell, such as</ins>:

- 1st Command: `id`

  - *This command is typically used in conjunction with other command-line tools, such as the net command, to display or modify user or group information.*

- 2nd Command: `uname -a`

  - *The `uname` command is used to learn the kernel version that the IBM Netezza host is running, which displays system information.*

  - *The `-a` option in `uname` command in Linux prints all the system information in the following order: Kernel name, network node hostname, kernel release date, kernel version, machine hardware name, hardware platform, operating system.*

<ins>You can also try out a few Linux commands within the SSH protocol inside Powershell, such as</ins>:

- 3rd Linux Command: `pwd`

  - *This stands for `Print Working Directory`, this command writes the full pathname of the current working directory to the standard output.*

- 4th Linux Command: `ls`

  - *This stands for `List`, this is a command that lists computer files and directories in Unix and Unix-like operating systems.*

- 5th Linux Command: `touch`

  - *This command is used to update the access date and/or modification date of a computer file or directory.*

- You can Exit the SSH protocol inside Powershell by:

  - Typing in: `exit`
 
  - Press: `Enter`
 
><ins>NOTE</ins>: Notice, everytime you typed in a command on Powershell you seen traffic populate within Wireshark.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/febe95ba-fd39-4670-b1e1-36f8e130393f"><br>

***

### 8. ) Observe DHCP Traffic

<ins>EXPLANATION</ins>: DHCP stands for Dynamic Host Configuration Protocol. DHCP uses UDP port 67 on the server side and UDP port 68 on the client side. DHCP also has two versions: DHCPv4 and DHCPv6 to support IPv4 and IPv6, respectively. These two versions, much like the two versions of IP, are very different and are therefore considered separate protocols and use separate ports. The DHCP protocol dynamically assigns an IP address and other network configuration parameters to each device on a network, so they can communicate with other IP networks. This is all done in the background where there is a DHCP server inside of Azure that is 'invisible'. This is where the DHCP server will reissue our IP Address and we can then observe the traffic within Wireshark.

<ins>You can force the renewal of an ip address by doing the following</ins>:

- Type in: `dhcp` into the Wireshark Filter Bar

- Type in: `ipconfig /renew` inside of Powershell

- Press: `Enter`

  - *<ins>So now, VM1 will broadcast on your Virtual Network (VNET) to say "Hey, give me and ip address.". This is a four-step process, which as follows</ins>*:

    - Discover - Client broadcasts a message to discover a DHCP server.

    - Offer - DHCP servers offer an IP address.

    - Request - Client selects an offer and formally requests to use the IP.

    - Acknowledge - The Server formally allocates the IP (and options) to the client.

>NOTE: It might also disconnect your remote desktop for a moment and then it should reconnect. If not, then just log back in as normal.

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/f47eb165-51f5-485c-a509-619d233299a2"><br>

***

### 9. ) Observe DNS Traffic

<ins>EXPLANATION</ins>: DNS stands for Domain Name System. DNS turns domain website names into IP addresses, which allow browsers to get to websites and other internet resources. Web browsers interact through Internet Protocol (IP) addresses, so every device on the internet has an IP address, which other devices can use to locate the device. So, in laymans terms, it turns human language (words) into computer language (numbers), so that it can communicate with other computers and devices on the internet.

<ins>So to begin, you can carry out the following</ins>:

- Type in: `dns` into the Wireshark Filter Bar

  - You can also use: `udp.port == 53` since DNS uses port 53

  - *As you see, there may be some DNS traffic already within Wireshark. Refresh your Wireshark to start with a clean screen.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/317eeef0-79e9-413b-95cc-37d148e07654"><br>

***

The first command we will test out is 'nslookup'; this stands for 'Name Serve Lookup'. This is a network administration command-line tool for querying the Domain Name System (DNS) to obtain the mapping between domain name and IP address, or other DNS records. To rephrase, it lets users enter a host name and find out the corresponding IP address or domain name system (DNS) records (It asks the DNS Server what the IP Address is for any given host name).

- Type in: `nslookup www.disney.com`

- Press: `Enter`

  - *When you complete this action you will see a bunch of 'source' and 'destination' traffic between your VM and the DNS Server.*

- The traffic you see in Wireshark are some of the IP Addresses that Disney uses.

- The area in green is the breakdown of the packet that was sent.

<ins>You can now close Powershell for the rest of this lab.</ins>

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/c9e7672e-375a-4359-9e26-186e73303d1d"><br>

***

### 10. ) Observe RDP Traffic

<ins>EXPLANATION</ins>: RDP allows users to execute remote operations on other computers. RDP uses port 3389 It facilitates secure information exchange between remotely connected machines over an encrypted communication channel. This is a secure network communication protocol offered by Microsoft.

<ins>To view RDP Traffic you will need to</ins>:

- Type in: `tcp.port == 3389` into Wireshark Filter Bar

  - *Now you can observe the constant stream of live traffic happening from RDP and your VM1.*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/881b2fbf-a807-4167-9eeb-e7d8beecc38a"><br>

***

### 11. ) Delete your Rsources

- <ins>Go back into your Azure Portal</ins>:

  - Go To: `RG-LAB-02`
 
  - Select: `Delete resource group`
 
  - Copy & Paste: `RG-LAB-02` into the Rectangular box towards the bottom right underneath where it says 'Enter resource group name to confirm deletion'.

  - Check: `The Box` where it says 'Apply force delete for selected Virtual machines and Virtual machine scale sets'.

  - Click: `Delete`

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/7354cf59-43cb-4e92-9f8f-c9ff800a3ef9"><br>

***

- Delete: `NetworkWatcherRG`

  - *(Perform the same instructions as you did with RG-LAB-02)*

<p align="center">
<img width="800" alt="isolated" src="https://github.com/vincentchachere/azure-network-protocols/assets/161680745/4e0a8677-f71b-4691-9fab-da3465121715"><br>

🎉 Congratulations! Complete this lab as many times as needed to solidify your understanding and create a skill that will pay dividends if you choose to put it to use.

☎️ For any questions, advice (for me or for you), constructive criticism, or just to connect, you can contact me at:

📲 LinkedIn: www.linkedin.com/in/vincentchachere

📬 Email: vincent.chachere@gmail.com

(Please no soliticing, only real connections, please and thank you.😁👍🏾)

</details>
