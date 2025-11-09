# Service Orchestration with Juju on MAAS

This project demonstrates how to install, configure, and use **Juju** to orchestrate services on top of a **MAAS (Metal-as-a-Service)** environment.  

It builds on the [bare-metal-provisioning-maas](https://github.com/MBaranekTech/bare-metal-provisioning-maas) repository, which covers the provisioning of bare-metal infrastructure.

---

## 🚀 Overview

- **MAAS** handles bare-metal provisioning (hardware, PXE booting, OS installation).  
- **Juju** handles service deployment and orchestration (apps, relations, scaling).  

This guide walks through installing Juju on the MAAS controller or admin machine, connecting it to your MAAS API, bootstrapping the Juju controller, and deploying example services.

---

## 🧩 Prerequisites

- Ubuntu 22.04+ with MAAS **region/rack controller** already configured.
- At least one “Ready” node in MAAS (≥4 GiB RAM).
- Your MAAS URL (e.g., `http://192.168.88.X:5240/MAAS`)
- MAAS API key (found under **MAAS → Profile → API Keys**)
- SSH access to your MAAS controller or admin workstation

---

## ⚙️ Step 1: Install Juju on the MAAS Controller (or Admin Node)

Update your system and install Juju:

```bash
sudo apt update && sudo apt upgrade -y
sudo snap install juju --classic
```
Check installation:
```bash
juju version
```
## ⚙️ Step 2: Add MAAS as a Cloud to Juju
```bash
juju add-cloud

You’ll be prompted interactively:
Cloud name: maas-cloud
Cloud type: maas
API endpoint: http://192.168.88.X:5240/MAAS


Then verify:
juju clouds
Output should show maas-cloud listed.
```
## ⚙️ Step 3: Add MAAS Credentials to Juju
```bash
Generate and add credentials using your MAAS API key:
juju add-credential maas-cloud

Example:

Enter credential name: maas-admin
Auth type: oauth
Enter maas-oauth: <PASTE YOUR MAAS API KEY HERE>

You can find your key in:
MAAS Web UI → Profile → API keys

Check stored credentials:
juju credentials
```
## ⚙️ Step 4: Bootstrap the Juju Controller on MAAS
“Bootstrapping” means Juju creates a controller to manage services.
This creates a Juju controller node inside your MAAS cloud.
```bash
juju bootstrap maas-cloud maas-controller

What happens:
Juju requests a “Ready” node from MAAS.
MAAS deploys an OS on it.
Juju installs its controller agent and database.
```
You can confirm that with:
```bash
juju controllers
```
<img width="1188" height="67" alt="image" src="https://github.com/user-attachments/assets/14415762-4ba6-413e-b06a-3a9907e055da" />

## ⚙️ Step 5: Add a new machine - Request one from MAAS and install Nginx
```bash
juju deploy nginx --constraints "arch=amd64"

This will:
Allocate a machine from MAAS (or reuse one if available)
Install Ubuntu
Install and configure Nginx automatically
```
## ⚙️ Step 6: Check deployment status
```bash
juju status
```
<img width="1147" height="339" alt="image" src="https://github.com/user-attachments/assets/18c3d62e-befc-4bbc-9e64-019392d50db0" />

## ⚙️ Step 6: Verify Nginx 
<img width="896" height="317" alt="image" src="https://github.com/user-attachments/assets/93b46aa7-6c2b-406b-9e04-1875a4179621" />


