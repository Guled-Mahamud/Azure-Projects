# **Azure Networking Project: VNet and Network Setup**

## **Introduction**

This document details the step-by-step process of setting up a **custom Azure Virtual Network (VNet)** with both **public and private subnets**, configuring **internet access**, deploying **Azure Virtual Machines (VMs)**, and securing access using **Azure Bastion**.

## **Objective**

The goal of this project is to:

- Create a **Virtual Network (VNet)** with **Public and Private Subnets**.
- Configure **Internet Access** using an **Internet Gateway (default Azure routing) and a NAT Gateway**.
- Deploy **one VM in the public subnet** (with direct internet access) and **one VM in the private subnet** (without direct internet access).
- Secure access to the **private VM using Azure Bastion**.
- Configure **Network Security Groups (NSGs) and Routing Tables** for proper network segmentation.
- Enable **basic monitoring and logging** without Log Analytics.

---

## **Step 1: Create a Virtual Network (VNet) with Public and Private Subnets**

### **Why?**

A **VNet** in Azure allows us to define a network range and subnets, providing segmentation for different workloads.

### **Steps:**

1. **Go to Azure Portal** ([portal.azure.com](https://portal.azure.com/)).
2. Navigate to **Virtual Networks** → Click **Create**.
3. Fill in the details:
    - **Resource Group:** `Networking-RG`
    - **Name:** `MyCustomVNet`
    - **Region:** Select the closest region (e.g., `East US`)
    - **IPv4 Address Space:** `10.0.0.0/16`
4. Click **Next: IP Addresses**.
5. Create **two subnets**:
    - **Public Subnet**: `10.0.1.0/24`
    - **Private Subnet**: `10.0.2.0/24`
6. Click **Review + Create** → Click **Create**.

---

## **Step 2: Setup Internet Access (Public & Private Subnets)**

### **Why?**

- The **Public Subnet** needs direct internet access (**Internet Gateway equivalent in Azure**).
- The **Private Subnet** must be able to reach the internet **without direct access** (via **NAT Gateway**).

### **Steps:**

### **For Public Subnet (Internet Gateway Equivalent)**

1. Go to **Public IP Addresses**.
2. Click **Create** → Name it `PublicIP-for-VM`.
3. Assign it to the **Public VM** (done in Step 3).

### **For Private Subnet (NAT Gateway for Outbound Traffic)**

1. Go to **NAT Gateways** → Click **Create**.
2. Assign it to **Public Subnet**.
3. Attach a **Public IP**.
4. Associate the NAT Gateway with **Private Subnet**.

---

## **Step 3: Deploy Azure Virtual Machines (VMs)**

### **Why?**

- **Public VM**: Can be accessed via the internet.
- **Private VM**: Should not be accessible from the internet but needs outbound access.

### **Steps:**

### **Create the Public VM**

1. Go to **Virtual Machines** → Click **Create**.
2. Select:
    - **Name:** `PublicVM`
    - **Resource Group:** `Networking-RG`
    - **Image:** Ubuntu Server 20.04
    - **Public IP:** Assigned.
3. Click **Review + Create** → Click **Create**.

### **Create the Private VM**

1. Repeat the same steps but:
    - **Name:** `PrivateVM`
    - **Subnet:** `Private-Subnet`
    - **Public IP:** None.

---

## **Step 4: Configure Routing & Security**

### **Why?**

- The **Public VM** should have direct internet access.
- The **Private VM** should be able to reach the internet via **NAT Gateway**.

### **Steps:**

### **Create Route Tables**

1. **Public Route Table**
    - Destination: `0.0.0.0/0`
    - Next Hop: **Internet**.
2. **Private Route Table**
    - Destination: `0.0.0.0/0`
    - Next Hop: **NAT Gateway**.

### **Configure Network Security Groups (NSGs)**

- **Public VM**:
    - Allow **SSH (22) from the internet**.
- **Private VM**:
    - Remove inbound **SSH (22) from any source**.
    - Allow **SSH only from Bastion Host**.

---

## **Step 5: Configure Azure Bastion for Private VM Access**

### **Why?**

- Securely access **Private VM** without exposing SSH to the internet.
- Replaces the need for a traditional jump box.

### **Steps:**

1. Go to **Azure Bastion** → Click **Create**.
2. Assign it to **Public-Subnet**.
3. Assign a **Public IP**.
4. Click **Review + Create** → **Create**.
5. To connect:
    - Go to **PrivateVM** → **Connect** → **Bastion**.
    - Enter credentials → **Access Private VM securely**.

---

## **Step 6: Enable Monitoring Without Log Analytics**

### **Why?**

- Monitor **CPU, Memory, Disk, and Network Usage** without Log Analytics.

### **Steps:**

1. **Go to Azure Monitor** → **Metrics**.
2. Select `PublicVM` or `PrivateVM`.
3. Click **+ Add Metric** and choose **CPU Percentage, Memory Usage, etc.**.
4. To set up alerts:
    - **Monitor** → **Alerts** → **New Alert Rule**.
    - Select condition (e.g., CPU > 80%).
    - Create an email or SMS alert.

---

## **Conclusion**

This project successfully set up a **fully functional Azure networking environment** by:
✅ **Creating a VNet with Public & Private Subnets**.

✅ **Configuring Public Internet Access (Default Azure Routing)**.

✅ **Setting up a NAT Gateway for Private VM Outbound Access**.

✅ **Deploying VMs in Public & Private Subnets**.

✅ **Securing access using Azure Bastion** instead of SSH.

✅ **Configuring Routing & NSGs for segmentation**.

✅ **Enabling monitoring via Azure Monitor**.

This setup ensures **secure, scalable, and well-structured networking** in Azure. 🚀

# Screenshot

The virtual machine setup

![alt text](<Screenshot 2025-03-07 045229-2.png>)


Network Security Groups (NSG)

![alt text](<Screenshot 2025-03-07 045251.png>)

Public IP addresses

![alt text](<Screenshot 2025-03-07 045325.png>)

Route tables 

![alt text](<Screenshot 2025-03-07 045353.png>)

NAT gateways

![alt text](<Screenshot 2025-03-07 045417.png>)

Metric setup for CPU, DISK and available memory 

![alt text](<Screenshot 2025-03-07 045916.png>)

Email Alert Setup - CPU percentage above 80%

![alt text](<Screenshot 2025-03-07 043023.png>)

Connect via Bastion (PrivateVM)

![alt text](<Screenshot 2025-03-07 054439.png>)

