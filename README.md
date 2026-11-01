# AWS EC2 Instance Lab
End-to-end process of launching, configuring and connecting to an EC2 instance in AWS

**Author:** Andre Patterson  
**Environment:** AWS Management Console - eu-west-2 (London)  
**Service:** Amazon EC2 (Elastic Cloud Compute)  
**Date:** October 2026

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Overview

This lab documents the end-to-end process of launching, configuring and connecting to an EC2 instance in AWS. EC2 is the core compute service in AWS - it provides virtual servers (instances) that you provision on demand and pay for by usage. Understanding how to launch, secure and connect to an EC2 instance is foundational to cloud infrastructure work.

This lab covers: AMI selection, instance type configuration, key pair management, security group rules and remote access via RDP.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## What is EC2?

**Elastic Cloud Compute (EC2)** is AWS's virtual server service. Rather than buying and maintaining physical hardware, you rent compute capacity in the cloud — selecting the operating system, CPU, memory and storage you need, paying only for what you use, and stopping or terminating the instance when you're done.

Key concepts covered in this lab:

|         Concept            |                       What it is                                 |
|----------------------------|------------------------------------------------------------------|
| AMI (Amazon Machine Image) | The template that defines the OS and software on the instance    |
| Instance type              | The hardware spec - CPU cores and memory (e.g. t2.micro)         |
| Key pair                   | A public/private key used to authenticate access to the instance |
| Security group             | A virtual firewall controlling inbound and outbound traffic      |
| RDP                        | Remote Desktop Protocol - how you connect to a Windows instance  |

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Lab Steps

### Step 1 - Open the EC2 Console

From the AWS Management Console, navigate to **EC2** under Services. The EC2 dashboard shows running instances, costs, and resource summaries for your region.

Also confirmed I was working in **eu-west-2 (London)** - always worth checking the region before launching resources, since instances, storage and networking are all region-specific.
![AWS Management Console EC2 dashboard](Screenshots/image1.png)



--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 2 - Configure the Instance

I clicked **Launch Instance** and worked through the configuration:

**AMI selection:** I selected **Windows Server 2025** as the Amazon Machine Image. The AMI defines the operating system and base software stack — in this case a clean Windows Server environment.

**Instance type:** I selected **t3.micro** (1 vCPU, 1 GB RAM), which sits within the AWS free tier. Instance types define the compute resources - more demanding workloads would need a larger type.

**Instance name:** I named the instance **Dre Demo Server** to make it identifiable in the console.

![AMI and instance type selection](Screenshots/image2.png)


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


### Step 3 - Create a Key Pair

To authenticate access to the instance, I created a new key pair named **Dre Demo Server Key**.

![Key pair creation](Screenshots/image3.png)

AWS generated a `.pem` private key file, which I downloaded and stored securely. This is the only way to retrieve the Windows administrator password later — if you lose this file, you cannot decrypt the password and cannot access the instance.

![Downloaded .pem key file](Screenshots/image4.png)

**Why this matters:** Key pairs replace password-based authentication for initial access. The public key is stored by AWS on the instance; the private key stays with you. Only someone holding the private key can decrypt the administrator password — this is a core AWS identity and access control principle.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 4 - Configure the Security Group (Firewall)

I reviewed and configured the inbound security group rules. Security groups act as a virtual firewall — controlling which traffic is allowed to reach the instance and on which ports.

For this lab, I configured the security group to allow **RDP (port 3389)** inbound, which is required to connect to a Windows instance remotely.

![Firewall configuration](Screenshots/image5.png)


**Why this matters:** In a production environment, you would restrict RDP access to a specific IP range rather than open it to all sources. Opening port 3389 to `0.0.0.0/0` (anywhere) is a well-known security risk - noted here as a deliberate lab simplification, not a recommended practice.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


### Step 5 - Review and Launch

Before launching, I reviewed the full instance summary: AMI, instance type, key pair, network settings and security group.

![Instance launch summary](Screenshots/image6.png)

I clicked **Launch Instance**. The EC2 console confirmed the instance was initialising.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 6 - Confirm the Instance is Running

In the EC2 dashboard under **Instances**, I confirmed the instance **Dre Demo Server** showed a status of **Running** with a public IP address assigned.

![Running instance in EC2 dashboard](Screenshots/image7.png)

The dashboard shows the instance name, state, type and availabilty zone - key operational details you'd monitor in a live environment.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 7 - Retrieve the Administrator Password

To connect via RDP, I needed the Windows administrator password. I selected the instance, clicked **Connect → RDP Client → Get Password**, and uploaded my `.pem` private key file.

![Get Windows password screen](images/image8.png)

AWS used the private key to decrypt and display the administrator password. This confirms the key pair mechanism is working correctly: only the holder of the private key can decrypt the password.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 8 - Connect via RDP

With the decrypted password in hand, I downloaded the **RDP file** from the Connect screen and opened it.

![dowloaded perm key file](images/image9.png)

I entered the administrator credentials in the Windows Security prompt.

![Windows credentials prompt](images/image10.png)

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Step 9 - Confirm Access

The RDP session connected successfully and I reached the **Windows Server 2025 desktop** on the running EC2 instance.

![Connected Windows Server 2025 desktop via RDP](images/image11.png)

This confirms end-to-end that the instance is running, the security group is correctly configured to allow RDP, the key pair decryption worked, and the credentials are valid.

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Summary

|       Step         |                  What was done                           |
|--------------------|----------------------------------------------------------|
| AMI selection      | Windows Server 2025 chosen as the operating system       |
| Instance type      | t2.micro (free tier) — 1 vCPU, 1 GB RAM                  |
| Key pair           | Created and downloaded .pem file for password decryption |
| Security group     | Inbound RDP (port 3389) rule configured                  |
| Launch             | Instance confirmed as running in eu-west-2 (London)      |
| Password retrieval | Private key used to decrypt administrator password       |
| RDP access         | Remote desktop session connected successfully            |

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Key Takeaways

- **EC2 is on-demand compute.** You choose the OS, hardware spec and configuration, pay for what you use, and terminate when done - no physical hardware required.
- **Key pairs are the authentication mechanism.** The private key is generated once at launch and must be stored securely - losing it means losing access to the initial administrator credentials.
- **Security groups are the first line of defence.** Every instance sits behind a security group by default; only explicitly permitted traffic can reach it. In production, RDP should be restricted to known IP ranges, never open to the internet.
- **Region matters.** All AWS resources are region-specific. Always confirm you're working in the intended region before launching anything.
