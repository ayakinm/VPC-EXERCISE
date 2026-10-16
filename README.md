# VPC-EXERCISE
AWS VPC Infrastructure &amp; Connectivity Validation for a Standard 3-Tier Architecture
# Designing and Implementing a Highly Available Three-Tier VPC Architecture on AWS

## 🚀 Project Overview
As part of the **Borderless Tech Academy** DevOps program, this project involves building a secure, production-grade, highly available Three-Tier Virtual Private Cloud (VPC) architecture from scratch in AWS (`us-east-1`). 

The focus of this exercise is purely on core networking infrastructure, security isolation, high availability, and connectivity validation—without deploying any sample applications.

---

## 🏗️ Architecture Design

- **VPC CIDR:** `10.0.0.0/16`
- **Region:** `us-east-1` (Availability Zones: `us-east-1a` and `us-east-1b`)
- **Internet Gateway (IGW):** Provides public internet access.
- **NAT Gateways:** 2 NAT Gateways (one per AZ) deployed in public subnets to ensure high availability for private subnets.
- **Subnets:**
  - **Web Tier (Public):** `Web-Public-A` (10.0.1.0/24), `Web-Public-B` (10.0.2.0/24)
  - **App Tier (Private):** `App-Private-A` (10.0.11.0/24), `App-Private-B` (10.0.12.0/24)
  - **DB Tier (Private):** `DB-Private-A` (10.0.21.0/24), `DB-Private-B` (10.0.22.0/24)

---

## 🛠️ Step-by-Step Implementation

1. **VPC Creation:** Created `Creativeflow-ThreeTier-VPC` with CIDR `10.0.0.0/16`.
2. **Subnets Provisioning:** Configured 6 subnets across `us-east-1a` and `us-east-1b`, enabling auto-assign public IPv4 for the public subnets.
3. **Internet & NAT Gateways:** Attached an Internet Gateway (`ThreeTier-IGW`) to the VPC and provisioned dual NAT Gateways for fault tolerance.
4. **Route Tables:** 
   - Configured a **Public Route Table** associated with public subnets pointing to the IGW (`0.0.0.0/0`).
   - Configured a **Private Route Table** associated with private subnets pointing to the NAT Gateways.
5. **Security Groups & Tier Isolation:**
   - **Web-SG:** Allowed SSH (22), HTTP (80), and ICMP from anywhere (`0.0.0.0/0`).
   - **App-SG:** Restricted inbound SSH, ICMP, and port 8080 strictly to `Web-SG`.
   - **DB-SG:** Restricted inbound SSH, ICMP, and MySQL/Aurora (3306) strictly to `App-SG`.
6. **EC2 Instance Deployment:**
   - Launched 4 minimal placeholder VMs (`Bastion-Host`, `Web-VM`, `App-VM`, `DB-VM`) to simulate the tiers.

---

## 📸 Infrastructure Screenshots

### 1. EC2 Instances Dashboard
*Showing running instances across public and private subnets with respective security and networking states.*

### 2. VPC Resource Map & Subnet Architecture
*Visualizing the 6 subnets mapped across us-east-1a and us-east-1b connected to route tables and gateways.*

### 3. Route Tables Configuration
*Configured Public and Private route tables showing proper routing definitions.*
---

## 🧪 Connectivity Validation & Testing

- **Internet Access:** Verified via `curl ifconfig.me` and `ping 8.8.8.8` from public and private VMs (leveraging the NAT Gateways).
- **Tier-to-Tier Isolation:** Tested secure internal communication from Web to App and App to DB tiers.
- **Bastion Jump Validation:** Successfully used the Bastion Host to securely tunnel/SSH into private application and database instances.
- **Security Enforcement:** Confirmed that direct external public access/SSH attempts to private tier VMs correctly time out and fail.

---

## 📝 Short Report

### What Worked?
The deployment of subnets, internet gateway attachment, and routing configurations went smoothly. Setting up explicit security group references.

### What Was the Hardest Part?
The hardest part for me was knowing how to SSH into the different servers because I was getting Permission denied (publickey) errors at first. Figuring out how to properly manage the key pair permissions (chmod 400), jumping through the bastion host, and making sure the private key was correctly referenced on the intermediate servers took some troubleshooting to get right.

### Why Do We Use Separate Private Subnets for App and DB?
Separating the Application Tier from the Database Tier adds a vital layer of defense-in-depth. Even if the application tier is somehow compromised, attackers cannot directly access the database tier because network routing and security groups completely block direct traffic from the web/internet layer to the database.

---