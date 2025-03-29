full project using **Azure Resource Manager (ARM) templates**, including the **code and step-by-step guide** to deploy a **Virtual Network and Virtual Machine** in Azure.

---

Search for ALL Services | ARM 

Tip: Update Networkk Interfacing Card myNIC, myVnet , mySubnet, Region - East US 

## **Project Overview**
### **Objective:**
Deploy an **Azure Virtual Network (VNet) and Virtual Machine (VM)** using an ARM template.

### **Resources Created:**
1. **Resource Group**
2. **Virtual Network (VNet)**
3. **Subnet**
4. **Public IP Address**
5. **Network Security Group (NSG)**
6. **Network Interface (NIC)**
7. **Virtual Machine (VM)**

---

## **Steps to Deploy the ARM Template**
### **Prerequisites:**
1. **Azure Subscription** - Sign up at [Azure Portal](https://portal.azure.com/).
2. **Azure CLI or PowerShell installed**
3. **Editor** (VS Code recommended)

---

## **Step 1: Create the ARM Template**
Create a **JSON** file named **`azure-deploy.json`**.

#### **azure-deploy.json**
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "East US",
      "metadata": {
        "description": "Location for resources"
      }
    },
    "adminUsername": {
      "type": "string",
      "defaultValue": "azureuser",
      "metadata": {
        "description": "Admin username for VM"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password for VM"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2021-05-01",
      "name": "myVNet",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": ["10.0.0.0/16"]
        },
        "subnets": [
          {
            "name": "mySubnet",
            "properties": {
              "addressPrefix": "10.0.1.0/24"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2021-05-01",
      "name": "myPublicIP",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic"
      }
    },
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2021-05-01",
      "name": "myNSG",
      "location": "[parameters('location')]",
      "properties": {
        "securityRules": [
          {
            "name": "AllowRDP",
            "properties": {
              "priority": 1000,
              "protocol": "Tcp",
              "access": "Allow",
              "direction": "Inbound",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2021-05-01",
      "name": "myNIC",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'myVNet', 'mySubnet')]"
              },
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myPublicIP')]"
              }
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2021-07-01",
      "name": "myVM",
      "location": "[parameters('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "Standard_B1s"
        },
        "osProfile": {
          "computerName": "myVM",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter-smalldisk",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', 'myNIC')]"
            }
          ]
        }
      }
    }
  ]
}
```

---

## **Step 2: Deploy the Template**
### **Using Azure CLI**
1. Open **Command Prompt (cmd), PowerShell, or Terminal**.
2. Log in to Azure:
   ```sh
   az login
   ```
3. Create a **Resource Group**:
   ```sh
   az group create --name MyResourceGroup --location eastus
   ```
4. Deploy the ARM template:
   ```sh
   az deployment group create --resource-group MyResourceGroup --template-file azure-deploy.json --parameters adminPassword=<your-password>
   ```

---

### **Using PowerShell**
1. Open **PowerShell**.
2. Log in to Azure:
   ```powershell
   Connect-AzAccount
   ```
3. Create a **Resource Group**:
   ```powershell
   New-AzResourceGroup -Name MyResourceGroup -Location "East US"
   ```
4. Deploy the ARM template:
   ```powershell
   New-AzResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile azure-deploy.json -adminPassword "<your-password>"
   ```

---

## **Step 3: Verify Deployment**
1. Open **Azure Portal**: [https://portal.azure.com](https://portal.azure.com)
2. Navigate to **Resource Groups → MyResourceGroup**.
3. You should see:
   - **Virtual Network (myVNet)**
   - **Subnet (mySubnet)**
   - **Public IP (myPublicIP)**
   - **Network Security Group (myNSG)**
   - **Network Interface (myNIC)**
   - **Virtual Machine (myVM)**

---

## **Step 4: Connect to the VM**
1. In **Azure Portal**, go to **myVM**.
2. Click **Connect → RDP**.
3. Download the **RDP file** and open it.
4. Enter the username (`azureuser`) and password (from the deployment).
5. Now you're connected to your Azure VM!

---

## **Conclusion**
- This project deployed a **Virtual Network, VM, and supporting resources** using an ARM template.
- You learned how to create and deploy an ARM template via **Azure CLI & PowerShell**.
- You successfully **connected to the VM** via **RDP**.

Would you like to extend this with **a Linux VM, extra security rules, or automation**? 🚀
