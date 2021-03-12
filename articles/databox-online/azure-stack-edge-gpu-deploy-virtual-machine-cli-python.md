---
title: VM's implementeren op uw GPU-apparaat voor Azure Stack Edge Pro via Azure CLI en Python
description: Hierin wordt beschreven hoe u virtuele machines (Vm's) maakt en beheert op een Azure Stack Edge Pro GPU-apparaat met behulp van Azure CLI en python.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 43853f2eba05794540a72fe64e748fcafd9073d2
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637895"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>VM's implementeren op uw GPU-apparaat voor Azure Stack Edge Pro met behulp van Azure CLI en Python

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

In deze zelfstudie wordt beschreven hoe u een virtuele machine op uw Azure Stack Edge Pro-apparaat maakt en beheert met behulp van de Azure-opdrachtregelinterface (CLI) en Python.

## <a name="vm-deployment-workflow"></a>VM-implementatiewerkstroom

De implementatiewerkstroom wordt afgebeeld in het volgende diagram.

![VM-implementatiewerkstroom](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

De samen vatting van het hoge niveau van de implementatie werk stroom is als volgt:

1. Verbinding maken met Azure Resource Manager
2. Een resourcegroep maken
3. Create a storage account
4. Blob-URI toevoegen aan het hosts-bestand
5. Certificaten installeren
6. Een VHD uploaden
7. Beheerde schijven maken op basis van de VHD
8. Een VM-installatiekopie maken op basis van de installatiekopie van de beheerde schijf
9. Een VM maken met eerder gemaakte resources
10. Een VNet maken
11. Een VNIC maken met de VNet-subnet-ID

Zie [VM's op uw Azure Stack Edge Pro-apparaat implementeren met behulp van Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md) voor een gedetailleerde uitleg van het werkstroomdiagram. Voor informatie over verbinding maken met Azure Resource Manager raadpleegt u [Verbinding maken met Azure Resource Manager met behulp van Azure PowerShell](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Vereisten

Voordat u een virtuele machine op uw Azure Stack Edge Pro-apparaat gaat maken en beheren met behulp van Azure CLI en Python, moet u ervoor zorgen dat u voldoet aan de vereisten die worden genoemd in de volgende stappen:

1. U hebt de netwerkinstellingen op uw Azure Stack Edge Pro-apparaat geconfigureerd, zoals wordt beschreven in [Stap 1: Een Azure Stack Edge Pro-apparaat configureren](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. U hebt een netwerkinterface ingeschakeld voor berekeningen. Het IP-adres van de netwerkinterface wordt gebruikt om een virtuele switch te maken voor de VM-implementatie. Met de volgende stappen doorloopt u dit proces:

    1. Ga naar **Compute**. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken.

        > [!IMPORTANT] 
        > U kunt slechts één poort configureren voor berekeningen.

    2. Schakel Compute in op de netwerkinterface. Azure Stack Edge Pro maakt en beheert een virtuele switch die overeenkomt met die netwerkinterface.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. U hebt alle certificaten gemaakt en geïnstalleerd op uw Azure Stack Edge Pro-apparaat en in het vertrouwensarchief van uw client. Volg de procedure die wordt beschreven in [Stap 2: Certificaten maken en installeren](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

4. U hebt een met Base-64 versleuteld *.CER*-certificaat (PEM-indeling) gemaakt voor uw Azure Stack Edge Pro-apparaat. Dat certificaat is al geüpload als een handtekening keten op het apparaat en is geïnstalleerd in het vertrouwde basis archief op uw client. Dit certificaat is ook vereist in *PEM*-indeling om Python te laten werken op deze client.

    Converteer dit certificaat naar `pem` een indeling met behulp van de `certutil` opdracht. U moet deze opdracht uitvoeren in de map met uw certificaat.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Hieronder ziet u een voorbeeld van hoe u deze opdracht gebruikt:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    U kunt deze ook `pem` later toevoegen aan de python-opslag.

5. U hebt het IP-adres van het apparaat toegewezen in uw **netwerkpagina** in de lokale web-UI van het apparaat. Dit IP-adres toevoegen aan:

    - Het hostbestand op de client, OF
    - De DNS-serverconfiguratie
    
    > [!IMPORTANT]
    > We raden aan de DNS-serverconfiguratie aan te passen voor de eindpuntnaamomzetting.

    1. Start **Kladblok** als beheerder (er zijn beheerdersrechten vereist om het bestand op te slaan) en open vervolgens het bestand **hosts** dat zich bevindt op `C:\Windows\System32\Drivers\etc`.
    
        ![Hosts-bestand in Windows Verkenner](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Voeg de volgende vermeldingen toe aan uw **hosts**-bestand, waarbij u de juiste waarden voor uw apparaat invoert:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Gebruik de volgende afbeelding ter referentie. Sla het bestand met **hosts** op.

        ![Hosts-bestand in Kladblok](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Download het Python-script](https://aka.ms/ase-vm-python) dat in deze procedure wordt gebruikt.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Stap 1: Azure CLI/Python instellen op de client

### <a name="verify-profile-and-install-azure-cli"></a>Profiel controleren en Azure CLI installeren

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Installeer Azure CLI op uw clientcomputer. In dit voorbeeld is Azure CLI 2.0.80 geïnstalleerd. Als u de versie van Azure CLI wilt controleren, voert u de opdracht `az --version` uit.

    Hier volgt een voor beeld van uitvoer van de bovenstaande opdracht:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Als u Azure CLI nog niet hebt, kunt u Azure CLI downloaden en [installeren in Windows](/cli/azure/install-azure-cli-windows). U kunt Azure CLI uitvoeren via de Windows-opdrachtprompt of via Windows PowerShell.

2. Noteer de locatie van Python voor de CLI. U hebt de python-locatie nodig om de locatie van het vertrouwde basis certificaat Archief voor Azure CLI te bepalen.

3. Om het voorbeeldscript uit te voeren dat in dit artikel wordt gebruikt, hebt u de volgende Python-bibliotheekversies nodig:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Voer de volgende opdracht uit om de versies te installeren:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    In de volgende voorbeelduitvoer ziet u de installatie van Haikunator:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    In de volgende voorbeelduitvoer ziet u de installatie van pip voor `msrestazure`: 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Het CA-basiscertificaat van Azure Stack Edge Pro vertrouwen

1. Zoek de locatie van het certificaat op de computer. De locatie kan variëren, afhankelijk van waar u `az cli` hebt geïnstalleerd. Voer Windows PowerShell uit als beheerder. Schakel over naar het pad waar `az cli` Python heeft geïnstalleerd: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe`.

    Als u de certificaat locatie wilt ophalen, typt u de volgende opdracht:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    De cmdlet retourneert de certificaatlocatie, zoals hieronder wordt weergegeven:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Noteer deze locatie als u deze later gaat gebruiken: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Vertrouw het CA-basiscertificaat van Azure Stack Edge Pro door het toe te voegen aan het bestaande Python-certificaat. U geeft het pad op naar de locatie waar u het PEM-certificaat eerder hebt opgeslagen.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Een voorbeeld van dit pad is 'C:\VM-scripts\rootteam3device.pem'
    
    Typ vervolgens de volgende reeks opdrachten in Windows PowerShell:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Verbinding maken met Azure Stack Edge Pro

1. Registreer uw Azure Stack Edge Pro-omgeving door de opdracht `az cloud register` uit te voeren.

    In sommige gevallen worden directe uitgaande Internet-verbindingen gerouteerd via een proxy of firewall, waarmee SSL-onderschepping wordt afgedwongen. In dergelijke gevallen kan de opdracht az cloud register mislukken met een fout als \"Kan geen eindpunten ophalen uit de cloud.\" U kunt deze fout omzeilen door de volgende omgevingsvariabelen in te stellen in Windows PowerShell:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Stel omgevingsvariabelen in voor het script voor het Azure Resource Manager-eindpunt, de locatie waar de resources zijn gemaakt en het pad naar de locatie van de bron-VHD. De locatie van de resources is hetzelfde op alle Azure Stack Edge Pro-apparaten en is ingesteld op `dbelocal`. U moet ook de adresvoorvoegsels en het privé-IP-adres opgeven. De volgende omgevings variabelen zijn waarden die zijn gebaseerd op uw waarden, met uitzonde ring van `AZURE_RESOURCE_LOCATION` , die moeten worden ingesteld op `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Registreer uw omgeving. Gebruik de volgende parameters bij het uitvoeren van az cloud register:

    | Waarde | Beschrijving | Voorbeeld |
    | --- | --- | --- |
    | Omgevingsnaam | De naam van de omgeving waarmee u verbinding wilt maken | Geef een naam op, zoals `aze-environ` |
    | Resource Manager-eindpunt | Deze URL is `https://Management.<appliancename><dnsdomain>`. <br> Als u deze URL wilt ophalen, gaat u naar pagina **Apparaten** in de lokale webgebruikersinterface van uw apparaat. |Bijvoorbeeld `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Hieronder ziet u een voorbeeld van het gebruik van de bovenstaande opdracht:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Gebruik de volgende opdrachten om de actieve omgeving in te stellen:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Hieronder ziet u een voorbeeld van het gebruik van de bovenstaande opdracht:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Meld u aan bij uw Azure Stack Edge Pro-omgeving door de opdracht `az login` uit te voeren. U kunt zich aanmelden bij de Azure Stack Edge Pro-omgeving als een gebruiker of als een [service-principal](../active-directory/develop/app-objects-and-service-principals.md).

   Voer de volgende stappen uit om u aan te melden als een *gebruiker*:

   U kunt de gebruikersnaam en het wachtwoord rechtstreeks opgeven met de opdracht `az login` of verifiëren met behulp van een browser. U moet dit laatste doen als voor uw account meervoudige verificatie is ingeschakeld.

   Hieronder ziet u een voorbeeld van hoe u `az login` gebruikt:
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Nadat u de aanmeldings opdracht hebt gebruikt, wordt u gevraagd een wacht woord op te vragen. Voer het Azure Resource Manager-wachtwoord in.

   Hier volgt een voor beeld van de uitvoer van een geslaagde aanmelding na het opgeven van het wacht woord:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Noteer de `id` `tenantId` waarden en als deze waarden overeenkomen met uw Azure Resource Manager-abonnement-id en Azure Resource Manager Tenant-id respectievelijk en worden gebruikt in de volgende stap.
       
   De volgende omgevingsvariabelen moeten worden ingesteld voor correcte werking als *service-principal*:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   De Azure Resource Manager-client-id is vastgelegd in code. De Azure Resource Manager-tenant-id en Azure Resource Manager-abonnements-id zijn beide aanwezig in de uitvoer van opdracht `az login` die u eerder hebt uitgevoerd. Het Azure Resource Manager-clientgeheim is het Azure Resource Manager-wachtwoord dat u hebt ingesteld.

   Zie [Azure Resource Manager-wachtwoord](azure-stack-edge-j-series-set-azure-resource-manager-password.md) voor meer informatie.

5. Wijzig het profiel naar versie 2019-03-01-hybrid. Als u de profielversie wilt wijzigen, voert u de volgende opdracht uit:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Hieronder ziet u een voorbeeld van hoe u `az cloud update` gebruikt:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Stap 2: Een virtuele machine maken

Er wordt een Python-script beschikbaar gemaakt waarmee u een virtuele machine kunt maken. Afhankelijk van of u bent aangemeld als gebruiker of als service-principal, gebruikt het script de relevante invoer om een VM te maken.

1. Voer het Python-script uit in dezelfde map waarin Python is geïnstalleerd.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Wanneer het script wordt uitgevoerd, duurt het uploaden van de VHD 20-30 minuten. Als u de voortgang van de uploadbewerking wilt weergeven, kunt u Azure Storage Explorer of AzCopy gebruiken.

    Hier volgt een voorbeeld van de uitvoer van een geslaagde uitvoering van het script. Met het script worden alle resources in een resourcegroep gemaakt, worden deze resources gebruikt om een VM te maken en wordt de resourcegroep verwijderd, inclusief alle resources die zijn gemaakt.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Volgende stappen

[Algemene Az CLI-opdrachten voor virtuele Linux-machines](../virtual-machines/linux/cli-manage.md)