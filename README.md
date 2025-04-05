# 🛠️ VPC Peering Project – AWS Setup Guide

This project demonstrates how to establish VPC Peering between two AWS VPCs to enable private communication between EC2 instances across different VPCs.

---
<p align="center">
  <img src="https://github.com/user-attachments/assets/ca255bd4-fb15-4e44-9136-304615a96312" alt="VPC Peering Diagram" width="700"/>
</p>

## 1. Create Two VPCs
- Navigate to **VPC Dashboard > Your VPCs > Create VPC**.
- **VPC-1**
  - Name: `VPC-1`
  - CIDR block: `192.168.0.0/16`
- **VPC-2**
  - Name: `VPC-2`
  - CIDR block: `172.16.0.0/16`

---

## 2. Create Subnets with Auto-assign Public IP
- **VPC-1 Subnet**
  - CIDR block: `192.168.1.0/24`
  - Enable auto-assign public IPv4
- **VPC-2 Subnet**
  - CIDR block: `172.16.1.0/24`
  - Enable auto-assign public IPv4

---

## 3. Create and Attach Internet Gateways
- Go to **VPC Dashboard > Internet Gateways > Create Internet Gateway**
  - Name: `IGW-VPC-1`, `IGW-VPC-2`
- Attach each IGW to its respective VPC

---

## 4. Create VPC Peering Connection
- Go to **Peering Connections > Create Peering Connection**
  - **Requester VPC:** `VPC-1`
  - **Accepter VPC:** `VPC-2`
- Click **Create Peering**, then go to the connection and click **Accept**

---

## 5. Update Route Tables
### For VPC-1 Route Table:
- Associate it with `192.168.1.0/24`
- Add Routes:
  - `Destination: 172.16.0.0/16` → `Target: Peering Connection`
  - `Destination: 0.0.0.0/0` → `Target: IGW-VPC-1`

### For VPC-2 Route Table:
- Associate it with `172.16.1.0/24`
- Add Routes:
  - `Destination: 192.168.0.0/16` → `Target: Peering Connection`
  - `Destination: 0.0.0.0/0` → `Target: IGW-VPC-2`

---

## 6. Configure Security Groups
### For EC2 in VPC-1:
- Allow **ICMP (ping)** from `172.16.0.0/16`

### For EC2 in VPC-2:
- Allow **ICMP (ping)** from `192.168.0.0/16`

---

## 7. Create IAM Role for SSM
- Go to **IAM > Roles > Create Role**
  - Trusted Entity: `EC2`
  - Policy: `AmazonSSMManagedInstanceCore`
  - Role Name: `SSM-EC2-Role`

---

## 8. Launch EC2 Instances
- Launch **one EC2** instance in each VPC
- Select appropriate **Subnet**, **Security Group**, and **IAM Role**
- Use **Amazon Linux 2 AMI** (recommended)

---

## 9. Test Connectivity
- Go to **EC2 > Instances > Connect** using **Session Manager**
- From EC2-1 (VPC-1), ping EC2-2's private IP
- From EC2-2 (VPC-2), ping EC2-1's private IP

✅ Successful ping confirms proper VPC peering and route setup!
