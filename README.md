## Lab Outline: Azure Vulnerability Management

### Prerequisites
- Computer with the Internet
- Free Azure Account - Possible to do with Free Subscription

### Create our Free Azure Account

<img width="1384" alt="1" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/de96dfb4-7d79-41f8-8cf8-9a591d140a87">

1. Sign up: [Azure Free Account](https://azure.microsoft.com/en-us/free/)
2. Login: [Azure Portal](https://portal.azure.com)

### Prepare Vulnerability Management Scanner
<img width="1544" alt="2" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/52897735-b4fa-4a2d-839c-7c53d6a6e459">


1. Go to [Azure Portal](https://portal.azure.com)
2. Navigate to the Marketplace and search for "OpenVAS secured and supported by HOSSTED"
3. Choose the "Start with a pre-set configuration" option and select the weakest configuration.
4. Click "Continue to Create VM"
5. Configure the VM:
   - Resource Group: Vulnerability-Management
   - VM Name: OpenVAS (Take note of the region and Vnet–consider East US 2)
   - Authentication: Username → azureuser / Cyberlab123!
   - Monitoring: Disable Boot Diagnostic
6. Click "Create" to create the VM.
7. Once the VM is created, SSH into it using PowerShell (Windows) or Terminal (MacOS) with the provided credentials.
8. Wait until the deployment of OpenVAS is complete.

### Create Client Virtual Machine and Make it Vulnerable
<img width="862" alt="file type" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/a797d493-1cc7-4694-b14a-3fe9c007c283">


1. Go to [Azure Portal](https://portal.azure.com)
2. Search for Virtual Machines and create a new Virtual Machine.
3. Configure the VM:
   - Resource Group: Vulnerability-Management
   - VM Name: Win10-Vulnerable
   - Region: Same as the OpenVAS VM (East US 2)
   - Virtual Network: Same as OpenVAS
   - Image: Windows 10 Pro
   - Size: Any size with 2 vCPUs
   - Username: azureuser / Cyberlab123!
   - Networking: Same Vnet as OpenVAS
4. Create the VM.
5. Once the VM is created, ensure you can RDP into it with the provided credentials.
6. After logging in, make the VM vulnerable:
   - Disable the Windows Firewall
   - Gather up some [Old Software](https://drive.google.com/drive/folders/1n83ilCjZWZulbDdYnUe9wQPK2buY47_U?usp=sharing)
   - Install an Old Version of FireFox: Firefox Setup 97.0b5
   - Install an Old Version of VLC Player: vlc-1.1.7-win32
   - Install an Old Version of Adobe Reader: 10.0_AdbeRdr1000_en_US_1_
   - Restart the VM.

### Configure OpenVAS to Perform First Unauthenticated Scan against our Vulnerable VM
<img width="1116" alt="3" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/dba0e0ae-ab6a-400a-befa-1e709bcfc184">


1. Login to OpenVAS and navigate to Assets > Hosts > New Host.
2. Add the Client VM PRIVATE IP Address.
3. Create a New Target from the Host, name it "Azure Vulnerable VMs".
4. Take note of the credentials. We will add SMB credentials later.
5. Create a new Task:
   - Name & Comment: "Scan - Azure Vulnerable VMs"
   - Scan Targets: "Azure Vulnerable VMs"
6. Save the Task.
7. Start the "Scan - Azure Vulnerable VMs" Task.
8. Once the scan is finished, click the date under "Last Report" to see the results.
9. Take note of the Tabs, especially the "Results" tab.

### Make Configurations for Credentialed Scans (Within VM)
<img width="1012" alt="Screenshot 2023-10-21 at 7 26 22 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/854f1931-877f-4ee5-b90a-ca8805677a29">


1. Disable Windows Firewall.
2. Disable User Account Control.
3. Enable Remote Registry.
4. Set Registry Key:
   - Launch Registry Editor (regedit.exe) in "Run as administrator" mode.
   - Navigate to HKEY_LOCAL_MACHINE hive.
   - Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key.
   - Create a new DWORD (32-bit) value with the following properties:
     - Name: LocalAccountTokenFilterPolicy
     - Value: 1
   - Close Registry Editor.
5. Restart the VM.

### Make Configurations for Credentialed Scans (OpenVAS)
<img width="1443" alt="Screenshot 2023-10-21 at 7 28 12 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/1ec6257f-2227-44c3-8e69-cab39d0832b8">


1. Go to Configuration > Credentials > New Credential.
2. Name / Comment: "Azure VM Credentials".
3. Allow Insecure Use: Yes.
4. Username: azureuser.
5. Password: Cyberlab123!
6. Save.
7. Go to Configuration > Targets > CLONE the Target we made before.
8. NEW Name / Comment: "Azure Vulnerable VMs - Credentialed Scan".
9. Ensure the Private IP is still accurate.
10. Credentials > SMB > Select the Credentials we just made: Azure VM Credentials.
11. Save.

### Execute Credentialed Scan against our Vulnerable Windows VM
<img width="861" alt="Screenshot 2023-10-21 at 7 31 05 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/2b018c1c-6d0f-4740-beb1-87281ae419b9">


<img width="861" alt="Screenshot 2023-10-21 at 7 31 51 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/74bc1c68-6bb7-4bae-b813-cfd8322a3e8a">


1. Within Greenbone / OpenVAS, go to Scans > Tasks.
2. CLONE the "Scan - Azure Vulnerable VMs" Task and Edit it.
3. Name / Comment: "Scan - Azure Vulnerable VMs - Credentialed".
4. Targets: Azure Vulnerable VMs - Credentialed Scan.
5. Save.
6. Click the Play button to launch the new Credentialed Scan and wait for it to finish.

### Remediate Vulnerabilities
<img width="1574" alt="Screenshot 2023-10-21 at 7 32 53 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/4f1aa45d-e190-4d79-85d4-c3f47db210b7">


1. Log back into your Win10-Vulnerable VM.
2. Uninstall Adobe Reader, VLC Player, and Firefox.
3. Restart the VM.
4. Re-initiate the "Scan - Azure Vulnerable VMs - Credentialed" scan and observe the results.

### Verify Remediations
<img width="1676" alt="Screenshot 2023-10-21 at 7 34 27 PM" src="https://github.com/Hugoatlist444/Open-Vas-Lab/assets/95655080/a6689b74-bd03-4975-9b7e-0c73df3e815d">


1. Note that there are no longer Vulnerabilities for FireFox, VLC Player, or Adobe Reader!
