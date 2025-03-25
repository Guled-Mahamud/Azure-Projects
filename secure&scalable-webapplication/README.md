# **Deploying a Secure and Scalable Web Application on Azure**

## **Introduction**

This document provides a step-by-step breakdown of deploying a **secure and scalable web application** using **Azure Application Gateway**, **Virtual Machines (VMs)**, and **Nginx**. The focus is on **security, availability, and scalability**, ensuring that:

- Traffic is only routed through the **Application Gateway**, preventing direct access to backend VMs.
- **HTTPS encryption** is configured using an **SSL certificate from Cloudflare**.
- **Auto-scaling** is implemented with **Azure Virtual Machine Scale Sets (VMSS)**.
- **Network Security Groups (NSGs)** enforce controlled inbound and outbound traffic.

---

## **Project Objectives**

The key objectives of this project include:

- Deploying **two Azure Virtual Machines (VMs)** running **Nginx** across different availability zones.
- Setting up an **Azure Application Gateway** to distribute traffic efficiently.
- Restricting direct access to VMs using **NSGs**.
- Configuring **HTTPS** with an **SSL certificate from Cloudflare**.
- Implementing **VMSS** for automatic scaling.
- Defining **inbound and outbound security rules** to manage traffic flow.
- Configuring **listeners** on the Application Gateway for HTTP and HTTPS traffic.

---

## **Step 1: Deploying Virtual Machines (VMs)**

### **Why?**

Deploying VMs with **Nginx** ensures that our web application can handle HTTP requests effectively. Distributing the VMs across different availability zones improves **fault tolerance** and **high availability**.

### **Steps:**

1. **Create Two Virtual Machines:**
    - Navigate to **Azure Portal** → **Virtual Machines** → **Create**.
    - Select **Ubuntu Server 20.04 LTS**.
    - Assign them to **separate Availability Zones** for redundancy.
    - Ensure each VM is part of the same **Virtual Network (VNet)**.
2. **Install and Configure Nginx:**
    - Run the following command on both VMs to install Nginx and serve a basic webpage:

```
# Update and install Nginx
sudo apt update && sudo apt install -y nginx

# Create a custom index.html file displaying instance details
echo "Welcome to VM $(hostname)" | sudo tee /var/www/html/index.html

# Ensure Nginx service is running
sudo systemctl start nginx
sudo systemctl enable nginx
```

### **Expected Outcome:**

- Each VM runs an **Nginx web server**, displaying a simple page with its hostname.
- The **web service starts automatically** upon reboot.

---

## **Step 2: Configuring Azure Application Gateway**

### **Why?**

An **Application Gateway** provides **load balancing** and **secure routing** of traffic to backend VMs. It also allows us to **configure HTTPS termination** for secure connections.

### **Steps:**

1. **Create an Application Gateway:**
    - Navigate to **Azure Portal** → **Application Gateway** → **Create**.
    - Assign a **Public IP Address**.
    - Select the same **VNet** as the VMs.
    - Set up a **Backend Pool** with both VMs.
2. **Configure Health Probes:**
    - Set up probes to monitor the health of backend VMs.
    - Define the probe path (`/index.html`) to check Nginx availability.
3. **Set Up Routing Rules:**
    - Create an **HTTP Listener** to handle requests.
    - Configure rules to forward **only HTTP traffic** to the backend VMs.

### **Expected Outcome:**

- The **Application Gateway routes traffic efficiently** between the VMs.
- **Health checks** ensure that only functional VMs receive traffic.

---

## **Step 3: Configuring Network Security Groups (NSGs)**

### **Why?**

NSGs **control inbound and outbound traffic**, ensuring security by restricting direct access to the VMs.

### **Steps:**

1. **Create NSG for Application Gateway:**
    - Allow **inbound HTTP and HTTPS traffic** from the internet.
2. **Create NSG for VMs:**
    - Allow **only HTTP traffic** from the **Application Gateway**.
    - Block all other inbound connections.
    - Configure outbound rules to allow VM responses **only to the Gateway**.

### **Expected Outcome:**

- Backend VMs **can’t be accessed directly** from the internet.
- The Application Gateway **is the only allowed traffic source**.

---

## **Step 4: Configuring HTTPS with Cloudflare SSL**

### **Why?**

Using **HTTPS** ensures that traffic is **encrypted**, improving security. Cloudflare SSL offers **easy certificate management**.

### **Steps:**

1. **Generate an SSL Certificate in Cloudflare:**
    - Navigate to **Cloudflare Dashboard** → **SSL/TLS** → **Origin Server**.
    - Create a **certificate for app.guled.co.uk**.
2. **Upload the SSL Certificate to Azure Key Vault:**
    - Navigate to **Azure Key Vault**.
    - Upload the **Cloudflare SSL certificate**.
3. **Configure HTTPS Listener on Application Gateway:**
    - Set up an **HTTPS Listener** with the uploaded SSL certificate.
    - Modify routing rules to **redirect HTTP to HTTPS**.

### **Expected Outcome:**

- The **web application is securely accessible over HTTPS**.
- All HTTP requests **redirect to HTTPS automatically**.

---

## **Step 5: Implementing Auto-Scaling with Azure Virtual Machine Scale Sets (VMSS)**

### **Why?**

VMSS **automatically scales** the number of VMs based on **traffic load**, ensuring high availability and cost efficiency.

### **Steps:**

1. **Create a Virtual Machine Scale Set (VMSS):**
    - Navigate to **Azure Portal** → **Virtual Machine Scale Sets** → **Create**.
    - Select the same **VNet and Subnet** as the existing VMs.
    - Configure **auto-scaling rules** based on CPU usage.
2. **Integrate VMSS with the Application Gateway:**
    - Add **VMSS instances to the backend pool** of the Gateway.

### **Expected Outcome:**

- The infrastructure **scales automatically** based on demand.
- **New VMs join the Application Gateway** without manual intervention.

---

## **Conclusion**

This project successfully implemented a **secure and scalable web application** on Azure. By integrating **Application Gateway, NSGs, HTTPS, and auto-scaling**, the infrastructure achieves:

✅ **Security** – VMs are protected from direct internet access.

✅ **Scalability** – VMSS handles traffic spikes dynamically.

✅ **Reliability** – Load balancing ensures uptime.

### **Key Learnings:**

- **Application Gateway listeners** are essential for managing traffic efficiently.
- **NSGs must be configured properly** to enforce security rules.
- **Cloudflare SSL simplifies HTTPS setup** for Azure applications.
- **VMSS ensures seamless scaling** without manual intervention.

This was a valuable experience in **Azure networking, security, and traffic management**, reinforcing cloud best practices! 🚀

## Screenshots

**Azure Virtual Machines Overview:**

*The list of VMs including `WebVM1`, `WebVM2`, and `AppGatewayVMSS`.*

![alt text](<Screenshot 2025-03-06 064409.png>)

**Azure Application Gateway:**

*Overview of the Azure Application Gateway used for load balancing.*

![alt text](<Screenshot 2025-03-06 064438.png>)

**Network Security Groups:**

*The Network Security Groups (NSGs) protecting the VMs from direct access.*

![alt text](<Screenshot 2025-03-06 064640.png>)

**Azure Key Vault:**

*The Key Vault where SSL certificates and other sensitive data are stored.*

![alt text](<Screenshot 2025-03-06 064720.png>)

**Azure Virtual Machine Scale Set:**

*The Azure VMSS configuration ensuring automatic scaling for the web application.*

![alt text](<Screenshot 2025-03-06 064847.png>)

**Azure Load Balancer Health Check:**

*Azure Application Gateway Backend Health Panel showing both VMs healthy.*

![alt text](<Screenshot 2025-03-06 062344.png>)

**Web Application Page 1:**

*“Welcome to VM WebVM1” hosted on the first VM.*

![alt text](<Screenshot 2025-03-06 062512.png>)

**Web Application Page 2:**

*“Welcome to VM WebVM2” hosted on the second VM.*

![alt text](<Screenshot 2025-03-06 062531.png>)