---
title: Een Azure File Sync | Microsoft Docs
description: Leer hoe u Azure File Sync implementeert, van begin tot eind, met behulp van de Azure Portal, PowerShell of de Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 64b9ce78f05e1c8d14317f33f21758a86baeabd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869181"
---
# <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren
Gebruik Azure File Sync om de bestands shares van uw organisatie te centraliseren in Azure Files, met behoud van de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt over de hele wereld.

U wordt ten zeerste aangeraden Planning voor een [Azure Files-implementatie](../files/storage-files-planning.md) en Planning voor een [Azure File Sync te lezen voordat](file-sync-planning.md) u de stappen in dit artikel voltooit.

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Een Azure-bestands share in dezelfde regio die u wilt implementeren Azure File Sync. Zie voor meer informatie:
    - [Regiobeschikbaarheid](file-sync-planning.md#azure-file-sync-region-availability) voor Azure File Sync.
    - [Maak een bestands share](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) voor een stapsgewijs beschrijving van het maken van een bestands share.
1. Ten minste één ondersteund exemplaar van een Windows Server- of Windows Server-cluster dat moet worden gesynchroniseerd met Azure File Sync. Zie Overwegingen voor Windows-bestandsservers voor meer informatie over ondersteunde versies van Windows Server en aanbevolen [systeembronnen.](file-sync-planning.md#windows-file-server-considerations)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Een Azure-bestands share in dezelfde regio die u wilt implementeren Azure File Sync. Zie voor meer informatie:
    - [Regiobeschikbaarheid](file-sync-planning.md#azure-file-sync-region-availability) voor Azure File Sync.
    - [Maak een bestands share](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) voor een stapsgewijs beschrijving van het maken van een bestands share.
1. Ten minste één ondersteund exemplaar van een Windows Server- of Windows Server-cluster dat moet worden gesynchroniseerd met Azure File Sync. Zie Overwegingen voor Windows-bestandsservers voor meer informatie over ondersteunde versies van Windows Server en aanbevolen [systeembronnen.](file-sync-planning.md#windows-file-server-considerations)

1. De Az PowerShell-module kan worden gebruikt met PowerShell 5.1 of PowerShell 6+. U kunt de Az PowerShell-module voor Azure File Sync gebruiken op elk ondersteund systeem, met inbegrip van niet-Windows-systemen. De cmdlet voor serverregistratie moet echter altijd worden uitgevoerd op het Windows Server-exemplaar dat u registreert (dit kan rechtstreeks of via remoting van PowerShell worden uitgevoerd). In Windows Server 2012 R2 kunt u controleren of u ten minste PowerShell 5.1 gebruikt. \* door te kijken naar de waarde van de **eigenschap PSVersion** van het **$PSVersionTable** object:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Als uw **PSVersion-waarde** kleiner is dan 5.1. , zoals het geval is bij de meeste nieuwe installaties van \* Windows Server 2012 R2, kunt u eenvoudig upgraden door Windows Management Framework [(WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)te downloaden en te installeren. Het juiste pakket voor het downloaden en installeren van Windows Server 2012 R2 is **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu.** 

    PowerShell 6+ kan worden gebruikt met elk ondersteund systeem en kan worden gedownload via de [GitHub-pagina.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Als u van plan bent de gebruikersinterface voor serverregistratie te gebruiken in plaats van rechtstreeks vanuit PowerShell te registreren, moet u PowerShell 5.1 gebruiken.

1. Als u ervoor hebt gekozen om PowerShell 5.1 te gebruiken, moet u ervoor zorgen dat ten minste .NET 4.7.2 is geïnstalleerd. Meer informatie over [.NET Framework en afhankelijkheden van](/dotnet/framework/migration-guide/versions-and-dependencies) uw systeem.

    > [!Important]  
    > Als u .NET 4.7.2+ installeert in Windows Server Core, moet u de vlaggen en installeren, anders mislukt `quiet` `norestart` de installatie. Als u bijvoorbeeld .NET 4.8 installeert, ziet de opdracht er als volgt uit:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. De Az PowerShell-module, die kan worden geïnstalleerd door de instructies hier te volgen: [Installeer en configureer Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > De Az.StorageSync-module wordt nu automatisch geïnstalleerd wanneer u de Az PowerShell-module installeert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Een Azure-bestands share in dezelfde regio die u wilt implementeren Azure File Sync. Zie voor meer informatie:
    - [Beschikbaarheid in](file-sync-planning.md#azure-file-sync-region-availability) regio'Azure File Sync.
    - [Maak een bestands share](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) voor een stapsgewijs beschrijving van het maken van een bestands share.
1. Ten minste één ondersteund exemplaar van een Windows Server- of Windows Server-cluster om te synchroniseren met Azure File Sync. Zie Overwegingen voor Windows-bestandsservers voor meer informatie over ondersteunde versies van Windows Server en aanbevolen [systeembronnen.](file-sync-planning.md#windows-file-server-considerations)

1. [Azure CLI installeren](/cli/azure/install-azure-cli)

   Als u dat liever hebt, kunt u ook Azure Cloud Shell om de stappen in deze zelfstudie uit te voeren.  Azure Cloud Shell is een interactieve shell-omgeving die u via uw browser gebruikt.  Begin Cloud Shell met behulp van een van deze methoden:

   - Selecteer **Nu proberen** in de rechterbovenhoek van een codeblok. **Probeer Het** wordt Azure Cloud Shell geopend, maar de code wordt niet automatisch naar de Cloud Shell.

   - Open Cloud Shell door naar te gaan [https://shell.azure.com](https://shell.azure.com)

   - Selecteer de **Cloud Shell** in de menubalk in de rechterbovenhoek van de [Azure Portal](https://portal.azure.com)

1. Meld u aan.

   Meld u aan met behulp van de opdracht [az login](/cli/azure/reference-index#az_login) als u een lokale installatie van de CLI gebruikt.

   ```azurecli
   az login
   ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

1. Installeer de [Azure CLI-extensie az filesync.](/cli/azure/storagesync)

   ```azurecli
   az extension add --name storagesync
   ```

   Nadat u de verwijzing naar **de storagesync-extensie** hebt geïnstalleerd, ontvangt u de volgende waarschuwing.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server voorbereiden voor gebruik met Azure File Sync
Voor elke server die u wilt gebruiken met Azure File Sync, met inbegrip van elk serverknooppunt in een failovercluster, schakelt u Internet Explorer **verbeterde beveiligingsconfiguratie uit.** Dit is alleen vereist voor de initiële serverregistratie. U kunt de optie opnieuw inschakelen nadat de server is geregistreerd.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> U kunt deze stap overslaan als u een Azure File Sync windows Server Core implementeert.

1. Serverbeheer openen.
2. Klik **op Lokale server:**  
    ![Lokale server aan de linkerkant van de gebruikersinterface van Serverbeheer](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. Selecteer in het deelvenster **Eigenschappen** de koppeling naar **Verbeterde beveiliging van Internet Explorer**.  
    ![Het deelvenster Verbeterde beveiliging van Internet Explorer in de gebruikersinterface van Serverbeheer](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. Selecteer in **Internet Explorer dialoogvenster** Verbeterde beveiliging de optie **Uit** **voor beheerders** en **gebruikers:**  
    ![Het pop-upvenster Verbeterde beveiliging van Internet Explorer met de optie Uit geselecteerd](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u de Internet Explorer verbeterde beveiliging wilt uitschakelen, voert u het volgende uit vanuit een PowerShell-sessie met verhoogde bevoegdheid:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Volg de instructies voor de Azure Portal of PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Opslagsynchronisatieservice implementeren 
De implementatie van Azure File Sync begint met het plaatsen van een **opslagsynchronisatieserviceresource** in een resourcegroep van uw geselecteerde abonnement. U wordt aangeraden slechts enkele van deze in terichten als dat nodig is. U maakt een vertrouwensrelatie tussen uw servers en deze resource en een server kan slechts bij één opslagsynchronisatieservice worden geregistreerd. Daarom is het raadzaam om zoveel opslagsynchronisatieservices te implementeren als u nodig hebt om groepen servers te scheiden. Houd er rekening mee dat servers van verschillende opslagsynchronisatieservices niet met elkaar kunnen worden gesynchroniseerd.

> [!Note]
> De opslagsynchronisatieservice neemt toegangsmachtigingen over van het abonnement en de resourcegroep waar deze in zijn geïmplementeerd. U wordt aangeraden zorgvuldig te controleren wie er toegang tot heeft. Entiteiten met schrijftoegang kunnen beginnen met het synchroniseren van nieuwe sets bestanden van servers die zijn geregistreerd bij deze opslagsynchronisatieservice en ervoor zorgen dat gegevens naar Azure Storage stromen die voor hen toegankelijk zijn.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslagsynchronisatieservice wilt implementeren, gaat u naar [de Azure Portal](https://portal.azure.com/), klikt u op *Een resource maken* en zoekt u naar Azure File Sync. Selecteer in de zoekresultaten Azure File Sync **en** selecteer vervolgens **Maken om** het **tabblad Opslagsynchronisatie implementeren te** openen.

Voer de volgende gegevens in in het deelvenster dat verschijnt:

- **Naam:** een unieke naam (per regio) voor de opslagsynchronisatieservice.
- **Abonnement:** het abonnement waarin u de opslagsynchronisatieservice wilt maken. Afhankelijk van de configuratiestrategie van uw organisatie hebt u mogelijk toegang tot een of meer abonnementen. Een Azure-abonnement is de meest eenvoudige container voor facturering voor elke cloudservice (zoals Azure Files).
- **Resourcegroep:** een resourcegroep is een logische groep Azure-resources, zoals een opslagaccount of een opslagsynchronisatieservice. U kunt een nieuwe resourcegroep maken of een bestaande resourcegroep gebruiken voor Azure File Sync. (We raden u aan resourcegroepen als containers te gebruiken om resources logisch te isoleren voor uw organisatie, zoals het groeperen van HR-resources of resources voor een specifiek project.)
- **Locatie:** de regio waarin u de Azure File Sync. Alleen ondersteunde regio's zijn beschikbaar in deze lijst.

Wanneer u klaar bent, selecteert u **Maken om** de opslagsynchronisatieservice te implementeren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Vervang , en door uw eigen waarden en gebruik vervolgens de volgende opdrachten om een `<Az_Region>` `<RG_Name>` `<my_storage_sync_service>` opslagsynchronisatieservice te maken en te implementeren:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Volg de instructies voor de Azure Portal of PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
De Azure File Sync-agent is een downloadbaar pakket waardoor Windows Server met een Azure-bestandsshare kan worden gesynchroniseerd. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
U kunt de agent downloaden via het [Microsoft Downloadcentrum.](https://go.microsoft.com/fwlink/?linkid=858257) Wanneer het downloaden is voltooid, dubbelklikt u op het MSI-pakket om de installatie van Azure File Sync agent te starten.

> [!Important]  
> Als u van plan bent om Azure File Sync failovercluster te gebruiken, moet Azure File Sync agent op elk knooppunt in het cluster worden geïnstalleerd. Elk knooppunt in het cluster moet zijn geregistreerd om te kunnen werken met Azure File Sync.

U wordt aangeraden het volgende te doen:
- Laat het standaardinstallatiepad (C:\Program Files\Azure\StorageSyncAgent) staan om probleemoplossing en serveronderhoud te vereenvoudigen.
- Schakel Microsoft Update in om uw Azure File Sync up-to-date te houden. Alle updates voor de Azure File Sync agent, inclusief functie-updates en hotfixes, vinden plaats vanaf Microsoft Update. U wordt aangeraden de meest recente update voor Azure File Sync. Zie Beleid bijwerken voor [Azure File Sync meer informatie.](file-sync-planning.md#azure-file-sync-agent-update-policy)

Wanneer de Azure File Sync agent is geïnstalleerd, wordt de gebruikersinterface voor serverregistratie automatisch geopend. U moet een opslagsynchronisatieservice hebben voordat u zich registreert; zie de volgende sectie over het maken van een opslagsynchronisatieservice.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende PowerShell-code uit om de juiste versie van de Azure File Sync agent voor uw besturingssysteem te downloaden en op uw systeem te installeren.

> [!Important]  
> Als u van plan bent om Azure File Sync failovercluster te gebruiken, moet Azure File Sync agent op elk knooppunt in het cluster worden geïnstalleerd. Elk knooppunt in het cluster moet zijn geregistreerd om te kunnen werken met Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Volg de instructies voor de Azure Portal of PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server registreren met opslagsynchronisatieservice
Als u Windows Server registreert voor een opslagsynchronisatieservice, wordt er een vertrouwensrelatie ingesteld tussen uw server (of cluster) en de opslagsynchronisatieservice. Een server kan slechts voor één opslagsynchronisatieservice worden geregistreerd en kan worden gesynchroniseerd met andere servers en Azure-bestandsshares die aan dezelfde opslagsynchronisatieservice zijn gekoppeld.

> [!Note]
> Serverregistratie gebruikt uw Azure-referenties om een vertrouwensrelatie te maken tussen de opslagsynchronisatieservice en uw Windows-server. Vervolgens maakt en gebruikt de server echter een eigen identiteit die geldig is zolang de server geregistreerd blijft en het huidige Shared Access Signature-token (Opslag-SAS) geldig is. Er kan geen nieuw SAS-token aan de server worden uitgegeven zodra de server is uitgeschreven, waardoor de server geen toegang meer heeft tot uw Azure-bestands shares, waardoor synchronisatie wordt gestopt.

De beheerder die de server registreert, moet lid zijn van de beheerrollen **Eigenaar** of **Inzender** voor de opgegeven opslagsynchronisatieservice. Dit kan worden geconfigureerd onder **Access Control (IAM)** in de Azure Portal voor de opslagsynchronisatieservice.

Het is ook mogelijk om onderscheid te maken tussen beheerders die servers kunnen registreren en servers die ook synchronisatie in een opslagsynchronisatieservice mogen configureren. Daarvoor moet u een aangepaste rol maken waarin u de beheerders vermeldt die alleen servers mogen registreren en uw aangepaste rol de volgende machtigingen geven:

* Microsoft.StorageSync/storageSyncServices/registeredServers/write
* Microsoft.StorageSync/storageSyncServices/read
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* Microsoft.StorageSync/storageSyncServices/workflows/operations/read

# <a name="portal"></a>[Portal](#tab/azure-portal)
De gebruikersinterface voor serverregistratie wordt automatisch geopend na de installatie van de Azure File Sync agent. Als dat niet gebeurt, kunt u deze handmatig openen vanuit de bestandslocatie: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Wanneer de gebruikersinterface voor serverregistratie wordt geopend, **selecteert u Aanmelden** om te beginnen.

Nadat u zich hebt aanmelden, wordt u gevraagd om de volgende informatie:

![Schermafbeelding van de gebruikersinterface van de serverregistratie](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-abonnement:** het abonnement dat de opslagsynchronisatieservice bevat (zie [De opslagsynchronisatieservice implementeren).](#deploy-the-storage-sync-service) 
- **Resourcegroep:** de resourcegroep die de opslagsynchronisatieservice bevat.
- **Opslagsynchronisatieservice:** de naam van de opslagsynchronisatieservice waarmee u zich wilt registreren.

Nadat u de juiste informatie hebt geselecteerd, selecteert u **Registreren om** de serverregistratie te voltooien. Als deel van het registratieproces wordt u gevraagd u nogmaals aan te melden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Volg de instructies voor de Azure Portal of PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Synchronisatiegroep en cloudeindpunt maken
Een synchronisatiegroep definieert de synchronisatietopologie voor een verzameling bestanden. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Een synchronisatiegroep moet één cloudeindpunt bevatten, dat een Azure-bestandsshare en een of meer servereindpunten representeert. Een server-eindpunt vertegenwoordigt een pad op een geregistreerde server. Een server kan server-eindpunten in meerdere synchronisatiegroepen hebben. U kunt zoveel synchronisatiegroepen maken als nodig is om de gewenste synchronisatietopologie op de juiste manier te beschrijven.

Een cloud-eindpunt is een aanwijzer naar een Azure-bestands share. Alle server-eindpunten worden gesynchroniseerd met een cloud-eindpunt, waardoor het cloud-eindpunt de hub wordt. Het opslagaccount voor de Azure-bestands share moet zich in dezelfde regio bevinden als de opslagsynchronisatieservice. De volledige Azure-bestands share wordt gesynchroniseerd, met één uitzondering: Er wordt een speciale map ingericht die vergelijkbaar is met de verborgen map Systeemvolumegegevens op een NTFS-volume. Deze map heet '. SystemShareInformation". Het bevat belangrijke synchronisatiemetagegevens die niet worden gesynchroniseerd met andere eindpunten. Gebruik of verwijder deze niet!

> [!Important]  
> U kunt wijzigingen aanbrengen in elk cloud-eindpunt of server-eindpunt in de synchronisatiegroep en uw bestanden laten synchroniseren met de andere eindpunten in de synchronisatiegroep. Als u het cloud-eindpunt (Azure-bestands share) rechtstreeks wijzigt, moeten wijzigingen eerst worden gedetecteerd door een Azure File Sync taak voor wijzigingsdetectie. Een wijzigingsdetectie job wordt slechts eenmaal per 24 uur gestart voor een cloud-eindpunt. Zie veelgestelde vragen [Azure Files meer informatie.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)

De beheerder die het cloud-eindpunt maakt,  moet lid zijn van de beheerrol Eigenaar voor het opslagaccount dat de Azure-bestands share bevat waar het cloud-eindpunt naar verwijst. Dit kan worden geconfigureerd onder **Access Control (IAM)** in de Azure Portal voor het opslagaccount.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een synchronisatiegroep wilt maken, gaat [Azure Portal](https://portal.azure.com/)naar uw opslagsynchronisatieservice en selecteert **u vervolgens + Synchronisatiegroep**:

![Een nieuwe synchronisatiegroep in de Azure-portal maken](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

Voer in het deelvenster dat verschijnt de volgende gegevens in om een synchronisatiegroep met een cloudeindpunt te maken:

- **Naam van synchronisatiegroep:** de naam van de synchronisatiegroep die moet worden gemaakt. Deze naam moet uniek zijn binnen de opslagsynchronisatieservice, maar het mag een willekeurige naam zijn die u makkelijk kunt onthouden.
- **Abonnement:** het abonnement waarin u de opslagsynchronisatieservice hebt geïmplementeerd in [De opslagsynchronisatieservice implementeren.](#deploy-the-storage-sync-service)
- **Opslagaccount:** als u Opslagaccount **selecteren** selecteert, wordt er een ander deelvenster weergegeven waarin u het opslagaccount kunt selecteren met de Azure-bestands share waarmee u wilt synchroniseren.
- **Azure-bestands** share: de naam van de Azure-bestands share waarmee u wilt synchroniseren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende PowerShell uit om de synchronisatiegroep te maken. Vergeet niet om te `<my-sync-group>` vervangen door de gewenste naam van de synchronisatiegroep.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Zodra de synchronisatiegroep is gemaakt, kunt u uw cloud-eindpunt maken. Zorg ervoor dat u `<my-storage-account>` en vervangt door de verwachte `<my-file-share>` waarden.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht az storagesync sync-group](/cli/azure/storagesync/sync-group#az_storagesync_sync_group_create) om een nieuwe synchronisatiegroep te maken.  Gebruik az configure om een standaardresourcegroep te gebruiken voor [alle CLI-opdrachten.](/cli/azure/reference-index#az_configure)

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Gebruik de [opdracht az storagesync sync-group cloud-endpoint](/cli/azure/storagesync/sync-group/cloud-endpoint#az_storagesync_sync_group_cloud_endpoint_create) om een nieuw cloud-eindpunt te maken.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Servereindpunt maken
Een servereindpunt representeert een bepaalde locatie op een geregistreerde server, bijvoorbeeld een map op een servervolume. Een server-eindpunt is onderhevig aan de volgende voorwaarden:

- Een server-eindpunt moet een pad op een geregistreerde server zijn (in plaats van een mounted share). NaS (Network Attached Storage) wordt niet ondersteund.
- Hoewel het server-eindpunt zich op het systeemvolume kan, gebruiken server-eindpunten op het systeemvolume mogelijk geen opslag in cloudlagen.
- Het wijzigen van het pad of de stationletter nadat u een server-eindpunt op een volume hebt ingesteld, wordt niet ondersteund. Zorg ervoor dat u een laatste pad op de geregistreerde server gebruikt.
- Een geregistreerde server kan meerdere server-eindpunten ondersteunen, maar een synchronisatiegroep kan op elk moment slechts één server-eindpunt per geregistreerde server hebben. Andere server-eindpunten binnen de synchronisatiegroep moeten zich op verschillende geregistreerde servers.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een server-eindpunt wilt toevoegen, gaat u naar de zojuist gemaakte synchronisatiegroep en selecteert u **vervolgens Server-eindpunt toevoegen.**

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

Voer in het deelvenster **Servereindpunt toevoegen** de volgende gegevens in om een servereindpunt te maken:

- **Geregistreerde server:** de naam van de server of het cluster waar u het server-eindpunt wilt maken.
- **Pad:** het Windows Server-pad dat moet worden gesynchroniseerd als onderdeel van de synchronisatiegroep.
- **Cloudopslaglagen:** een switch om opslag in cloudlagen in of uit te schakelen. Met opslag in cloudlagen kunnen niet vaak gebruikte of gebruikte bestanden in lagen worden opgeslagen Azure Files.
- **Vrije volumeruimte:** de hoeveelheid vrije ruimte die moet worden reserveren op het volume waarop het server-eindpunt zich bevindt. Als de vrije ruimte van het volume bijvoorbeeld is ingesteld op 50% op een volume met één server-eindpunt, wordt ongeveer de helft van de hoeveelheid gegevens in lagen opgeslagen Azure Files. Ongeacht of opslag in cloudlagen is ingeschakeld, heeft uw Azure-bestands share altijd een volledige kopie van de gegevens in de synchronisatiegroep.
- **Eerste downloadmodus:** dit is een optionele selectie, te beginnen met agentversie 11, die handig kan zijn wanneer er bestanden in de Azure-bestands share zijn, maar niet op de server. Een dergelijke situatie kan bijvoorbeeld bestaan als u een server-eindpunt maakt om een andere filiaalserver toe te voegen aan een synchronisatiegroep of wanneer u een mislukte server herstelt. Als opslag in cloudlagen is ingeschakeld, wordt standaard alleen de naamruimte ingeroepen, in eerste instantie geen bestandsinhoud. Dit is handig als u denkt dat gebruikerstoegangsaanvragen moeten bepalen welke bestandsinhoud wordt teruggeroepen naar de server. Als opslag in cloudlagen is uitgeschakeld, wordt standaard eerst de naamruimte gedownload en worden bestanden teruggeroepen op basis van de laatste gewijzigde tijdstempel totdat de lokale capaciteit is bereikt. U kunt de initiële downloadmodus echter alleen wijzigen in naamruimte. Een derde modus kan alleen worden gebruikt als cloudopslaglagen is uitgeschakeld voor dit server-eindpunt. In deze modus wordt voorkomen dat de naamruimte eerst wordt ingeroepen. Bestanden worden alleen weergegeven op de lokale server als ze de kans hadden om ze volledig te downloaden. Deze modus is handig als een toepassing bijvoorbeeld vereist dat volledige bestanden aanwezig zijn en gelaagde bestanden in de naamruimte van de toepassing niet kunnen tolereren.

Selecteer Maken om het server-eindpunt toe **te voegen.** Uw bestanden worden nu synchroon gehouden in uw Azure-bestands share en Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voer de volgende PowerShell-opdrachten uit om het server-eindpunt te maken. Zorg ervoor dat u en vervangt door de gewenste waarden en controleer de optionele instelling voor het optionele initiële `<your-server-endpoint-path>` `<your-volume-free-space>` downloadbeleid.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de [opdracht az storagesync sync-group server-endpoint](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) om een nieuw server-eindpunt te maken.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Instellingen voor de firewall en het virtuele netwerk configureren

### <a name="portal"></a>Portal
Als u uw Azure File Sync wilt configureren voor gebruik met firewall- en virtuele netwerkinstellingen, gaat u als volgt te werk:

1. Navigeer Azure Portal het opslagaccount dat u wilt beveiligen.
1. Selecteer **Netwerken** in het menu links.
1. Onder **Geselecteerde netwerken** onder Toegang toestaan **vanuit**.
1. Zorg ervoor dat het IP-adres of virtuele netwerk van uw servers wordt vermeld in de **sectie Adresbereik.**
1. Zorg ervoor **dat Vertrouwde Microsoft-services toegang tot dit opslagaccount** is ingeschakeld.
1. Selecteer **Opslaan om** uw instellingen op te slaan.

    ![Firewall- en virtuele netwerkinstellingen configureren voor gebruik met Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding met Azure File Sync
De aanbevolen stappen voor onboarding op Azure File Sync zonder uitvaltijd zonder uitvaltijd, met behoud van de volledige bestandstrouwheid en toegangsbeheerlijst (ACL), zijn als volgt:
 
1. Een opslagsynchronisatieservice implementeren.
1. Maak een synchronisatiegroep.
1. Installeer Azure File Sync agent op de server met de volledige gegevensset.
1. Registreer die server en maak een server-eindpunt op de share. 
1. Synchronisatie: de volledige upload uitvoeren naar de Azure-bestands share (cloud-eindpunt).  
1. Nadat de initiële upload is voltooid, installeert Azure File Sync agent op elk van de resterende servers.
1. Maak nieuwe bestands shares op elk van de resterende servers.
1. Maak server-eindpunten op nieuwe bestands shares met beleid voor cloudopslaglagen, indien gewenst. (Voor deze stap moet er extra opslagruimte beschikbaar zijn voor de eerste installatie.)
1. Laten Azure File Sync agent de volledige naamruimte snel herstellen zonder de werkelijke gegevensoverdracht. Na de volledige synchronisatie van de naamruimte vult de synchronisatie-engine de lokale schijfruimte op basis van het beleid voor cloudopslaglagen voor het server-eindpunt. 
1. Zorg ervoor dat de synchronisatie is voltooid en test uw topologie naar wens. 
1. Gebruikers en toepassingen omleiden naar deze nieuwe share.
1. U kunt eventueel eventuele dubbele shares op de servers verwijderen.
 
Als u geen extra opslagruimte hebt voor de initiële onboarding en u deze wilt koppelen aan de bestaande shares, kunt u de gegevens vooraf in de Azure-bestands shares seeden. Deze aanpak wordt aanbevolen, als en alleen als u downtime kunt accepteren en absoluut geen gegevenswijzigingen op de server-shares kunt garanderen tijdens het eerste onboardingproces. 
 
1. Zorg ervoor dat de gegevens op een van de servers niet kunnen worden gewijzigd tijdens het onboardingproces.
1. Start Azure-bestands shares vooraf met de servergegevens met behulp van een hulpprogramma voor gegevensoverdracht via SMB. Robocopy, bijvoorbeeld. U kunt AzCopy ook gebruiken via REST. Zorg ervoor dat u AzCopy gebruikt met de juiste switches om tijdstempels en kenmerken van ACL's te behouden.
1. Maak Azure File Sync topologie met de gewenste server-eindpunten die naar de bestaande shares wijzen.
1. Synchronisatie moet het afstemmingsproces op alle eindpunten voltooien. 
1. Zodra de afstemming is voltooid, kunt u shares openen voor wijzigingen.
 
Op dit moment kent de pre-seeding-benadering enkele beperkingen: 
- Gegevenswijzigingen op de server voordat de synchronisatietopologie volledig actief is, kunnen conflicten veroorzaken op de server-eindpunten.  
- Nadat het cloud-eindpunt is gemaakt, voert Azure File Sync een proces uit om de bestanden in de cloud te detecteren voordat de initiële synchronisatie wordt uitgevoerd. De tijd die nodig is om dit proces te voltooien, is afhankelijk van de verschillende factoren, zoals de netwerksnelheid, de beschikbare bandbreedte en het aantal bestanden en mappen. Voor de ruwe schatting in de preview-versie wordt het detectieproces ongeveer 10 bestanden per seconde uitgevoerd.  Dus zelfs als pre-seeding snel wordt uitgevoerd, kan de totale tijd voor het krijgen van een volledig werkend systeem aanzienlijk langer zijn wanneer gegevens vooraf in de cloud worden geseed.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Selfservice herstellen via vorige versies en VSS (Volume Shadow Copy Service)

> [!IMPORTANT]
> De volgende informatie kan alleen worden gebruikt met versie 9 (of hoger) van de opslagsynchronisatieagent. Versies lager dan 9 hebben niet de StorageSyncSelfService-cmdlets.

Vorige versies is een Windows-functie waarmee u VSS-momentopnamen aan de serverzijde van een volume kunt gebruiken om restoreerbare versies van een bestand te presenteren aan een SMB-client.
Dit maakt een krachtig scenario mogelijk, ook wel selfserviceherstel genoemd, rechtstreeks voor informatiemedewerkers in plaats van afhankelijk van het herstel door een IT-beheerder.

VSS-momentopnamen en vorige versies werken onafhankelijk van Azure File Sync. Cloudopslaglagen moeten echter worden ingesteld op een compatibele modus. Veel Azure File Sync server-eindpunten kunnen zich op hetzelfde volume. U moet de volgende PowerShell-aanroep maken per volume dat zelfs één server-eindpunt heeft waar u van plan bent of die cloudopslaglagen gebruikt.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-momentopnamen worden gemaakt van een heel volume. Standaard kunnen er maximaal 64 momentopnamen bestaan voor een bepaald volume, omdat er voldoende ruimte is om de momentopnamen op te slaan. VSS verwerkt dit automatisch. Het standaardschema voor momentopnamen maakt twee momentopnamen per dag, maandag tot en met vrijdag. Dit schema kan worden geconfigureerd via een geplande Windows-taak. De bovenstaande PowerShell-cmdlet doet twee dingen:
1. Azure File Syncs configureert cloudopslaglagen op het opgegeven volume zodat deze compatibel zijn met eerdere versies en garandeert dat een bestand kan worden hersteld vanuit een vorige versie, zelfs als het in de cloud op de server is opgeslagen. 
1. Hiermee schakelt u de standaard VSS-planning in. U kunt deze later wijzigen. 

> [!Note]  
> Er zijn twee belangrijke dingen die u moet weten:
>- Als u de parameter -Force gebruikt en VSS momenteel is ingeschakeld, wordt het huidige schema voor VSS-momentopnamen overschreven en vervangen door de standaardplanning. Zorg ervoor dat u uw aangepaste configuratie opgeslagen voordat u de cmdlet wordt uitgevoerd.
> - Als u deze cmdlet op een clusterknooppunt gebruikt, moet u deze ook uitvoeren op alle andere knooppunten in het cluster. 

Als u wilt zien of de compatibiliteit met selfserviceherstel is ingeschakeld, kunt u de volgende cmdlet uitvoeren.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Alle volumes op de server worden weergegeven, evenals het aantal dagen dat compatibel is met cloudopslaglagen. Dit aantal wordt automatisch berekend op basis van de maximale mogelijke momentopnamen per volume en het standaardschema voor momentopnamen. Alle eerdere versies van een informatiemedewerker kunnen dus standaard worden gebruikt om vanuit te herstellen. Hetzelfde geldt als u de standaardplanning wijzigt om meer momentopnamen te maken.
Als u de planning echter op een manier wijzigt die resulteert in een beschikbare momentopname op het volume dat ouder is dan de compatibele dagenwaarde, kunnen gebruikers deze oudere momentopname (vorige versie) niet gebruiken om te herstellen.

> [!Note]
> Het inschakelen van selfserviceherstel kan van invloed zijn op het verbruik en de factuur van uw Azure-opslag. Deze impact is beperkt tot bestanden die momenteel zijn gelaagd op de server. Het inschakelen van deze functie zorgt ervoor dat er een bestandsversie beschikbaar is in de cloud waarnaar kan worden verwezen via een eerdere versie (VSS-momentopname).
>
> Als u de functie uit schakelen, neemt het gebruik van Azure Storage langzaam af totdat het venster compatibele dagen is verstreken. Er is geen manier om dit te versnellen. 

Het standaard maximum aantal VSS-momentopnamen per volume (64) en de standaardplanning om ze te maken, resulteren in maximaal 45 dagen van eerdere versies waaruit een informatiemedewerker kan herstellen, afhankelijk van het aantal VSS-momentopnamen dat u op uw volume kunt opslaan.

Als maximaal 64 VSS-momentopnamen per volume niet de juiste instelling voor u is, kunt u die waarde [wijzigen via een registersleutel](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Om de nieuwe limiet van kracht te laten worden, moet u de cmdlet opnieuw uitvoeren om compatibiliteit met eerdere versies in te stellen op elk volume dat eerder is ingeschakeld, met de vlag -Force om rekening te houden met het nieuwe maximum aantal VSS-momentopnamen per volume. Dit resulteert in een nieuw berekend aantal compatibele dagen. Houd er rekening mee dat deze wijziging alleen van kracht wordt op nieuw gelaagde bestanden en dat eventuele aanpassingen in de VSS-planning die u hebt gemaakt, worden overschreven.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Proactief nieuwe en gewijzigde bestanden terughalen uit een Azure-bestands share

Met agentversie 11 wordt een nieuwe modus beschikbaar op een server-eindpunt. Met deze modus kunnen wereldwijd gedistribueerde bedrijven de servercache in een externe regio vooraf vullen, zelfs voordat lokale gebruikers bestanden openen. Wanneer deze modus is ingeschakeld op een server-eindpunt, zorgt deze modus ervoor dat deze server bestanden inroept die zijn gemaakt of gewijzigd in de Azure-bestands share.

### <a name="scenario"></a>Scenario

Een wereldwijd gedistribueerd bedrijf heeft filialen in de VERENIGDE Staten en India. Informatiemedewerkers maken 's ochtends (Us time) een nieuwe map en nieuwe bestanden voor een geheel nieuw project en werken er de hele dag aan. Azure File Sync synchroniseert de map en bestanden naar de Azure-bestands share (cloud-eindpunt). Informatiemedewerkers in India blijven in hun tijdzone aan het project werken. Wanneer ze 's ochtends aankomt, moet de lokale Azure File Sync-server in India deze nieuwe bestanden lokaal beschikbaar hebben, zodat het Team van India efficiënt kan werken met een lokale cache. Als u deze modus inschakelen, wordt voorkomen dat de initiële toegang tot bestanden langzamer verloopt vanwege terugroepen op aanvraag en kan de server de bestanden proactief terugroepen zodra ze zijn gewijzigd of gemaakt in de Azure-bestands share.

> [!IMPORTANT]
> Het is belangrijk om te realiseren dat het bijhouden van wijzigingen in de Azure-bestands share die nauw op de server worden uitgevoerd, uw verkeer naar en de factuur van Azure kan verhogen. Als bestanden die worden teruggeroepen naar de server niet daadwerkelijk lokaal nodig zijn, kan het onnodig terughalen naar de server negatieve gevolgen hebben. Gebruik deze modus als u weet dat het vooraf vullen van de cache op een server met recente wijzigingen in de cloud een positief effect heeft op gebruikers of toepassingen die de bestanden op die server gebruiken.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Een server-eindpunt proactief laten terughalen wat er is gewijzigd in een Azure-bestands share

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. Ga [in Azure Portal](https://portal.azure.com/)naar uw opslagsynchronisatieservice, selecteer de juiste synchronisatiegroep en identificeer vervolgens het server-eindpunt waarvoor u wijzigingen in de Azure-bestands share (cloud-eindpunt) nauwkeurig wilt bijhouden.
1. Zoek in de sectie Opslag in cloudlagen het onderwerp 'Azure-bestands share downloaden'. U ziet de momenteel geselecteerde modus en kunt deze wijzigen om wijzigingen in De Azure-bestands delen beter bij te houden en deze proactief terug te roepen naar de server.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Een afbeelding van het downloadgedrag van de Azure-bestands share voor een server-eindpunt dat momenteel van kracht is en een knop om een menu te openen waarmee dit kan worden gewijzigd.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

U kunt de eigenschappen van server-eindpunten in PowerShell wijzigen via de [cmdlet Set-AzStorageSyncServerEndpoint.](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint)

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Een implementatie DSF-replicatie (DFS-R) migreren naar Azure File Sync
Een DFS-R-implementatie migreren naar Azure File Sync:

1. Maak een synchronisatiegroep voor de DFS-R-topologie die u vervangt.
1. Start op de server met de volledige set gegevens in de te migreren DFS-R-topologie. Installeer Azure File Sync op die server.
1. Registreer die server en maak een server-eindpunt voor de eerste server die moet worden gemigreerd. Schakel opslag in cloudlagen niet in.
1. Laat alle gegevens synchroniseren met uw Azure-bestands share (cloud-eindpunt).
1. Installeer en registreer de Azure File Sync agent op elk van de resterende DFS-R-servers.
1. Schakel DFS-R uit. 
1. Maak een server-eindpunt op elk van de DFS-R-servers. Schakel opslag in cloudlagen niet in.
1. Zorg ervoor dat de synchronisatie is voltooid en test uw topologie naar wens.
1. Retire DFS-R.
1. Cloudopslaglagen kunnen nu naar wens worden ingeschakeld op elk server-eindpunt.

Zie voor meer informatie [Azure File Sync samenwerking met Distributed File System (DFS)](file-sync-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Volgende stappen
- [Een server-Azure File Sync toevoegen of verwijderen](file-sync-server-endpoint.md)
- [Een server registreren of de registratie van een server bij Azure File Sync](file-sync-server-registration.md)
- [Azure File Sync bewaken](file-sync-monitoring.md)