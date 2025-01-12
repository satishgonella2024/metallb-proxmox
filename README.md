# 🚀 MetalLB for Proxmox Kubernetes Cluster

![MetalLB Version](https://img.shields.io/badge/MetalLB-v0.13.10-blue)
![Kubernetes Version](https://img.shields.io/badge/Kubernetes-1.30.8-green)
![Proxmox Supported](https://img.shields.io/badge/Proxmox-Supported-brightgreen)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

This repository contains configuration files for setting up **MetalLB** in a Kubernetes cluster hosted on Proxmox. MetalLB provides LoadBalancer functionality, enabling services to be exposed with external IP addresses in environments that lack a cloud provider.

---

## 🗂️ Repository Contents

- `metallb-config.yaml`  
   Configuration file defining the IP address pool for MetalLB.

- `l2-advertisement.yaml`  
   File to enable Layer 2 advertisement mode for MetalLB.

---

## 🛠️ Prerequisites

Before you begin, ensure the following:
- A working Kubernetes cluster (e.g., `kubeadm`, `RKE2`) hosted on Proxmox.
- `kubectl` installed and configured to access your cluster.
- `Helm` installed on your local machine.

---

## 📦 Deployment Steps

Follow these steps to set up MetalLB:

### 1️⃣ Install MetalLB
```bash
kubectl create namespace metallb-system
helm repo add metallb https://metallb.github.io/metallb
helm repo update
helm install metallb metallb/metallb --namespace metallb-system
```
2️⃣ Apply the Configuration Files

Clone this repository and apply the configurations:
```bash
git clone https://github.com/satishgonella2024/metallb-proxmox.git
cd metallb-proxmox
kubectl apply -f metallb-config.yaml
kubectl apply -f l2-advertisement.yaml
```

3️⃣ Deploy a Sample Service

Test MetalLB with an nginx service:
```bash

kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=LoadBalancer
kubectl get svc nginx
```

Access the nginx service via the assigned external IP.

⚙️ Configuration Details

metallb-config.yaml

Defines the IP address pool:
```bash
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.6.200-192.168.6.210  # Adjust based on your Proxmox network
```

l2-advertisement.yaml

Enables Layer 2 advertisement:
```bash
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - first-pool
```
🧪 Verification
1.	Check the status of MetalLB pods:

kubectl get pods -n metallb-system


2.	Verify the external IP assignment:

kubectl get svc nginx


3.	Access the service using the assigned IP in your browser or via curl:
```bash
curl http://192.168.6.200
```
🛡️ Troubleshooting
	1.	No External IP Assigned:
Verify the metallb-config.yaml IP range is within your network’s subnet and does not conflict with DHCP.
	2.	MetalLB Pods Not Running:
Check the logs for troubleshooting:
```bash
kubectl logs -n metallb-system -l app=metallb
```

3.	Web Service Unreachable:
Ensure firewalls or security rules allow traffic to the IP range.

🌐 Learn More
	•	MetalLB Documentation
	•	Kubernetes Official Docs

👏 Contributors
	•	Satish Gonella - GitHub Profile

📝 License

This project is licensed under the MIT License. See the LICENSE file for details.

---
