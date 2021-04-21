---
title: Problemen met Azure Files in Windows oplossen
description: Problemen met Azure Files in Windows oplossen. Zie veelvoorkomende problemen met betrekking tot Azure Files wanneer u verbinding maakt vanaf Windows-clients en bekijk mogelijke oplossingen. Alleen voor SMB-shares
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 115c083a75adab96e416fc200bf7db287a99ff4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788417"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Problemen Azure Files in Windows (SMB) oplossen

In dit artikel vindt u veelvoorkomende problemen met betrekking tot Microsoft Azure-bestanden wanneer u verbinding maakt vanaf Windows-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. Naast de stappen voor probleemoplossing in dit artikel kunt u [ook AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) gebruiken om ervoor te zorgen dat de Windows-clientomgeving over de juiste vereisten beschikt. AzFileDiagnostics automatiseert de detectie van de meeste van de symptomen die in dit artikel worden vermeld en helpt bij het instellen van uw omgeving voor optimale prestaties.

> [!IMPORTANT]
> De inhoud van dit artikel is alleen van toepassing op SMB-shares. Zie Problemen met Azure NFS-bestands shares oplossen voor meer informatie over [NFS-shares.](storage-troubleshooting-files-nfs.md)

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fout 5 bij het toevoegen van een Azure-bestands share

Wanneer u probeert een bestands share te maken, wordt mogelijk de volgende fout weergegeven:

- Systeemfout 5 heeft zich voorgedaan. Toegang wordt geweigerd.

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: niet-versleuteld communicatiekanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](../common/storage-require-secure-transfer.md) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Voor Windows 8, Windows Server 2012 en latere versies van elk systeem wordt onderhandeld over aanvragen die SMB 3.0 omvatten, wat ondersteuning biedt voor versleuteling.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Maak verbinding vanaf een client die ondersteuning biedt voor SMB-versleuteling (Windows 8, Windows Server 2012 of hoger) of maak verbinding vanaf een virtuele machine in hetzelfde datacenter als het Azure-opslagaccount dat wordt gebruikt voor de Azure-bestands share.
2. Controleer of [de instelling Veilige overdracht](../common/storage-require-secure-transfer.md) vereist is uitgeschakeld voor het opslagaccount als de client geen ondersteuning biedt voor SMB-versleuteling.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: er zijn regels voor het virtuele netwerk of de firewall ingeschakeld in het opslagaccount 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Oorzaak 3: Machtigingen op shareniveau zijn onjuist bij het gebruik van verificatie op basis van identiteit

Als gebruikers toegang hebben tot de Azure-bestands share via Active Directory (AD) of Azure Active Directory Domain Services-verificatie (Azure AD DS), mislukt de toegang tot de bestands share met de fout 'Toegang wordt geweigerd' als de machtigingen op shareniveau onjuist zijn. 

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3

Controleer of de machtigingen correct zijn geconfigureerd:

- **Zie Machtigingen op shareniveau** toewijzen aan een identiteit in Active Directory (AD). [](./storage-files-identity-ad-ds-assign-permissions.md)

    Machtigingstoewijzingen op shareniveau worden ondersteund voor groepen en gebruikers die zijn gesynchroniseerd vanuit Active Directory (AD) naar Azure Active Directory (Azure AD) met behulp van Azure AD Connect.  Controleer of groepen en gebruikers die machtigingen op shareniveau krijgen toegewezen niet-ondersteunde 'alleen-cloud'-groepen zijn.
- **Azure Active Directory Domain Services (Azure AD DS) zie** [Toegangsmachtigingen toewijzen aan een identiteit](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fout 53, fout 67 of fout 87 bij het toevoegen of ontkoppelen van een Azure-bestands share

Wanneer u probeert een bestands share te maken vanaf on-premises of vanuit een ander datacenter, kunnen de volgende fouten optreden:

- Systeemfout 53 heeft zich voorgedaan. Het netwerkpad is niet gevonden.
- Systeemfout 67 heeft zich voorgedaan. De naam van het netwerk kan niet worden gevonden.
- Systeemfout 87 heeft zich voorgedaan. De parameter is onjuist.

### <a name="cause-1-port-445-is-blocked"></a>Oorzaak 1: Poort 445 is geblokkeerd

Systeemfout 53 of systeemfout 67 kan optreden als uitgaande communicatie via poort 445 naar een Azure Files datacenter is geblokkeerd. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Gebruik het hulpprogramma of de cmdlet [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) om te controleren of poort 445 door uw firewall of internetprovider `Test-NetConnection` wordt geblokkeerd. 

Als u de cmdlet wilt gebruiken, moet Azure PowerShell module zijn geïnstalleerd. Zie Install Azure PowerShell module (Een `Test-NetConnection` [module installeren) voor](/powershell/azure/install-Az-ps) meer informatie. Vergeet niet om `<your-storage-account-name>` en `<your-resource-group-name>` te vervangen door de betreffende namen van uw opslagaccount.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Als de verbinding is geslaagd, hoort u de volgende uitvoer te zien:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> De bovenstaande opdracht retourneert het huidige IP-adres van het opslagaccount. Dit IP-adres blijft niet noodzakelijkerwijs hetzelfde en kan op elk moment veranderen. Neem dit IP-adres niet op in scripts of in de firewallconfiguratie.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

#### <a name="solution-1---use-azure-file-sync"></a>Oplossing 1: Azure File Sync gebruiken
Azure File Sync kunt uw on-premises Windows Server transformeren naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. Azure File Sync werkt via poort 443 en kan daarom worden gebruikt als tijdelijke oplossing voor toegang tot Azure Files vanaf clients waarbij poort 445 is geblokkeerd. [Meer informatie over het instellen van Azure File Sync](../file-sync/file-sync-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Oplossing 2: VPN gebruiken
Door een VPN voor uw specifieke opslagaccount in te stellen, loopt het verkeer via een beveiligde tunnel in plaats van via internet. Volg de [instructies voor het instellen van VPN](storage-files-configure-p2s-vpn-windows.md) om toegang te krijgen tot Azure Files vanuit Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Oplossing 3: Poort 445 deblokkeren met behulp van uw internetprovider/IT-beheerder
Werk samen met uw IT-afdeling of ISP om poort 445 uitgaand naar [Azure IP-adresbereiken te openen.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Oplossing 4: Hulpprogramma’s op basis van REST API gebruiken, zoals Storage Explorer/PowerShell
Azure Files biedt naast SMB ook ondersteuning voor REST. REST-toegang werkt via poort 443 (standaard TCP). Er zijn verschillende hulpprogramma's die zijn geschreven met REST API die een uitgebreide UI-ervaring bieden. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) is er een van. [Download en installeer Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met de bestandsshare die door Azure Files wordt ondersteund. U kunt ook [PowerShell gebruiken die](./storage-how-to-use-files-powershell.md) ook gebruikersaccounts REST API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Oorzaak 2: NTLMv1 is ingeschakeld

Systeemfout 53 of systeemfout 87 kan optreden als NTLMv1-communicatie is ingeschakeld op de client. Azure Files biedt alleen ondersteuning voor NTLMv2-verificatie. Als NTLMv1 is ingeschakeld, is de client minder veilig. Om deze reden wordt communicatie geblokkeerd voor Azure Files. 

Controleer of de volgende registersubsleutel niet is ingesteld op een waarde die kleiner is dan 3 om te bepalen of dit de oorzaak van de fout is:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Zie het onderwerp [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) in TechNet voor meer informatie.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Zet de waarde **LmCompatibilityLevel** terug naar de standaardwaarde 3 in de volgende registersubsleutel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Fout 1816: Er is onvoldoende quotum beschikbaar om deze opdracht te verwerken

### <a name="cause"></a>Oorzaak

Fout 1816 teert wanneer u de bovengrens bereikt van gelijktijdige geopende grepen die zijn toegestaan voor een bestand of map op de Azure-bestands share. Zie [Azure Files-schaalbaarheidsdoelen](./storage-files-scale-targets.md#azure-files-scale-targets) voor meer informatie.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige geopende grepen door enkele grepen te sluiten en het vervolgens opnieuw te proberen. Zie voor meer informatie Microsoft Azure Storage [controlelijst voor prestaties en schaalbaarheid.](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Gebruik de PowerShell-cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) om geopende grepen voor een bestands share, map of bestand weer te geven.  

Gebruik de PowerShell-cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) om de geopende grepen voor een bestands share, map of bestand te sluiten.

> [!Note]  
> De Get-AzStorageFileHandle- en Close-AzStorageFileHandle-cmdlets zijn opgenomen in Az PowerShell-module versie 2.4 of hoger. Zie Install the Azure PowerShell module (De module Azure PowerShell installeren) [om de nieuwste Az PowerShell-module te installeren.](/powershell/azure/install-az-ps)

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fout 'Geen toegang' wanneer u probeert een Azure-bestands share te openen of te verwijderen  
Wanneer u een Azure-bestands share probeert te openen of verwijderen in de portal, wordt mogelijk de volgende fout weergegeven:

Geen toegang  
Foutcode: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 1: Regels voor virtuele netwerken of firewalls zijn ingeschakeld voor het opslagaccount

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Oorzaak 2: Uw gebruikersaccount heeft geen toegang tot het opslagaccount

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Blader naar het opslagaccount waar de Azure-bestands share zich bevindt, klik op Toegangsbeheer **(IAM)** en controleer of uw gebruikersaccount toegang heeft tot het opslagaccount. Zie Uw opslagaccount beveiligen met op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../blobs/security-recommendations.md#data-protection)voor meer informatie.

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Kan een bestand of map niet wijzigen, verplaatsen/hernoemen of verwijderen
Een van de belangrijkste doeleinden van een bestands share is dat meerdere gebruikers en toepassingen tegelijkertijd kunnen communiceren met bestanden en mappen in de share. Om te helpen bij deze interactie bieden bestands shares verschillende manieren om de toegang tot bestanden en mappen te mediagen.

Wanneer u een bestand opent vanuit een gemonteerde Azure-bestands share via SMB, vraagt uw toepassing/besturingssysteem een bestandsing handle aan. Dit is een verwijzing naar het bestand. Uw toepassing geeft onder andere een modus voor het delen van bestanden op wanneer een bestandsingingsing wordt gevraagd, waarmee het niveau van exclusiviteit wordt opgegeven van uw toegang tot het bestand dat wordt afgedwongen door Azure Files: 

- `None`: u hebt exclusieve toegang. 
- `Read`: anderen kunnen het bestand lezen terwijl u het geopend hebt.
- `Write`: andere kunnen naar het bestand schrijven terwijl u het geopend hebt. 
- `ReadWrite`: een combinatie van de modi `Read` en `Write` voor delen.
- `Delete`: anderen kunnen het bestand verwijderen terwijl u het geopend hebt. 

Hoewel het FileREST-protocol een staatloos protocol is, heeft het geen concept van bestandsing handles, maar het biedt wel een vergelijkbaar mechanisme voor het mediaaleren van toegang tot bestanden en mappen die uw script, toepassing of service kan gebruiken: bestandsleases. Wanneer een bestand wordt geleased, wordt dit beschouwd als een bestandsingingsingal met de modus voor bestandsdeling van `None` . 

Hoewel bestandsing handles en leases een belangrijk doel hebben, kunnen bestandsing handles en leases soms zwevend zijn. Als dit gebeurt, kan dit problemen veroorzaken bij het wijzigen of verwijderen van bestanden. Mogelijk ziet u foutberichten zoals:

- Het proces kan het bestand niet openen omdat het door een ander proces wordt gebruikt.
- De actie kan niet worden voltooid omdat het bestand is geopend in een ander programma.
- Het document is vergrendeld voor bewerking door een andere gebruiker.
- De opgegeven resource is gemarkeerd voor verwijdering door een SMB-client.

De oplossing voor dit probleem is afhankelijk van of dit wordt veroorzaakt door een zwevende bestandsing handle of lease. 

### <a name="cause-1"></a>Oorzaak 1
Een bestandsing handle voorkomt dat een bestand/map wordt gewijzigd of verwijderd. U kunt de PowerShell-cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) gebruiken om geopende grepen weer te geven. 

Als alle SMB-clients hun geopende grepen voor een bestand/map hebben gesloten en het probleem zich blijft voordoen, kunt u een bestandsing handle geforceerde sluiten.

### <a name="solution-1"></a>Oplossing 1
Gebruik de PowerShell-cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) om af te dwingen dat een bestandsing handle wordt gesloten. 

> [!Note]  
> De Get-AzStorageFileHandle- en Close-AzStorageFileHandle-cmdlets zijn opgenomen in Az PowerShell-moduleversie 2.4 of hoger. Zie Install the Azure PowerShell module (De module Azure PowerShell installeren) om de meest recente [Az PowerShell-module te installeren.](/powershell/azure/install-az-ps)

### <a name="cause-2"></a>Oorzaak 2
Een bestandslease voorkomt dat een bestand kan worden gewijzigd of verwijderd. U kunt controleren of een bestand een bestandslease heeft met de volgende PowerShell, en vervangen door de juiste waarden `<resource-group>` `<storage-account>` voor uw `<file-share>` `<path-to-file>` omgeving:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Als een bestand een lease heeft, moet het geretourneerde object de volgende eigenschappen hebben:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Oplossing 2
Als u een lease uit een bestand wilt verwijderen, kunt u de lease vrijgeven of de lease onderbreken. Voor het vrijgeven van de lease hebt u de LeaseId van de lease nodig. Deze hebt u ingesteld bij het maken van de lease. U hebt de LeaseId niet nodig om de lease te onderbreken.

In het volgende voorbeeld ziet u hoe u de lease voor het bestand dat wordt aangegeven bij oorzaak 2, verbreekt (dit voorbeeld gaat verder met de PowerShell-variabelen van oorzaak 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Bestanden worden langzaam gekopieerd van en naar Azure Files in Windows

Mogelijk ziet u trage prestaties wanneer u bestanden probeert over te dragen naar de Azure File-service.

- Als u geen specifieke minimale I/O-grootte nodig hebt, raden we u aan 1 MiB te gebruiken als de I/O-grootte voor optimale prestaties.
-   Als u weet wat de uiteindelijke grootte is van een bestand dat u uitbreidt met schrijf- en schrijfproblemen, en uw software geen compatibiliteitsproblemen heeft wanneer de niet-geschreven staart op het bestand nullen bevat, stelt u de bestandsgrootte vooraf in in plaats van elke schrijf-uitbreiding te maken.
-   Gebruik de juiste kopieermethode:
    -   Gebruik [AzCopy voor](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) elke overdracht tussen twee bestands shares.
    -   [Robocopy gebruiken tussen](./storage-how-to-create-file-share.md) bestands shares op een on-premises computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Overwegingen voor Windows 8.1 of Windows Server 2012 R2

Zorg ervoor dat voor clients met Windows 8.1 of Windows Server 2012 R2 de hotfix [KB3114025](https://support.microsoft.com/help/3114025) is geïnstalleerd. Deze hotfix verbetert de prestaties van het maken en sluiten van grepen.

U kunt het volgende script uitvoeren om te controleren of de hotfix is geïnstalleerd:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Als hotfix is geïnstalleerd, wordt de volgende uitvoer weergegeven:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Op windows Server 2012 R2-installatie Azure Marketplace is hotfix KB3114025 standaard geïnstalleerd vanaf december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Geen map met een stationletter in 'Mijn computer' of 'Deze pc'

Als u een Azure-bestands share als beheerder toekent met behulp van net use, lijkt de share te ontbreken.

### <a name="cause"></a>Oorzaak

Windows Verkenner wordt standaard niet uitgevoerd als beheerder. Als u net use vanaf een opdrachtprompt met beheerdersaccounts, kunt u het netwerkstation als beheerder. Omdat de stations die zijn toegeschreven op de gebruiker zijn gericht, worden de stations niet weergegeven in het gebruikersaccount dat is aangemeld als ze zijn bevestigd onder een ander gebruikersaccount.

### <a name="solution"></a>Oplossing
De share vanaf een niet-beheerder opdrachtregel. U kunt ook dit [TechNet-onderwerp volgen om](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) de registerwaarde **EnableLinkedConnections** te configureren.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>De opdracht Net use mislukt als het opslagaccount een slash bevat

### <a name="cause"></a>Oorzaak

De opdracht net use interpreteert een slash (/) als een opdrachtregeloptie. Als de naam van uw gebruikersaccount begint met een slash, mislukt de toewijzing van het station.

### <a name="solution"></a>Oplossing

U kunt een van de volgende stappen gebruiken om het probleem op te lossen:

- Voer de volgende PowerShell-opdracht uit:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Vanuit een batchbestand kunt u de opdracht op deze manier uitvoeren:

  `Echo new-smbMapping ... | powershell -command –`

- Plaats dubbele aanhalingstekens rond de sleutel om dit probleem op te lossen, tenzij de slash het eerste teken is. Als dat zo is, gebruikt u de interactieve modus en voert u uw wachtwoord afzonderlijk in of voert u uw sleutels opnieuw in om een sleutel op te halen die niet met een slash begint.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Toepassing of service heeft geen toegang tot een Azure Files station

### <a name="cause"></a>Oorzaak

Stations worden per gebruiker bevestigd. Als uw toepassing of service wordt uitgevoerd onder een ander gebruikersaccount dan het account dat het station heeft bevestigd, ziet de toepassing het station niet.

### <a name="solution"></a>Oplossing

Gebruik een van de volgende oplossingen:

-   Bevestig het station vanuit hetzelfde gebruikersaccount dat de toepassing bevat. U kunt een hulpprogramma zoals PsExec gebruiken.
- Geef de naam en sleutel van het opslagaccount door in de gebruikersnaam- en wachtwoordparameters van de opdracht net use.
- Gebruik de opdracht cmdkey om de referenties toe te voegen aan Aanmeldingsgegevensbeheer. Voer dit uit vanaf een opdrachtregel onder de context van het serviceaccount, via een interactieve aanmelding of met behulp van `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Wijs de share rechtstreeks toe zonder gebruik te maken van een kaart met een stationletter. Sommige toepassingen maken mogelijk niet opnieuw verbinding met de letter van het station, zodat het volledige UNC-pad betrouwbaarder kan zijn. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Nadat u deze instructies hebt gevolgd, ontvangt u mogelijk het volgende foutbericht wanneer u net use voor het systeem-/netwerkserviceaccount hebt uitgevoerd: 'Systeemfout 1312 is opgetreden. Er bestaat geen opgegeven aanmeldingssessie. Deze is mogelijk al beëindigd. Als dit het geval is, moet u ervoor zorgen dat de gebruikersnaam die wordt doorgegeven aan net use domeingegevens bevat (bijvoorbeeld[naam opslagaccount].file.core.windows.net).

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fout 'U kopieert een bestand naar een bestemming die geen ondersteuning biedt voor versleuteling'

Wanneer een bestand via het netwerk wordt gekopieerd, wordt het bestand ontsleuteld op de broncomputer, verzonden als tekst zonder tekst en opnieuw versleuteld op de bestemming. Mogelijk ziet u echter de volgende fout wanneer u een versleuteld bestand probeert te kopiëren: 'U kopieert het bestand naar een bestemming die geen ondersteuning biedt voor versleuteling.'

### <a name="cause"></a>Oorzaak
Dit probleem kan optreden als u EFS (Encrypting File System gebruikt). Met BitLocker versleutelde bestanden kunnen worden gekopieerd naar Azure Files. De Azure Files echter geen ondersteuning voor NTFS EFS.

### <a name="workaround"></a>Tijdelijke oplossing
Als u een bestand wilt kopiëren via het netwerk, moet u het eerst ontsleutelen. Hanteer één van de volgende methoden:

- Gebruik de **kopiëren /d** opdracht. Hiermee kunnen de versleutelde bestanden worden opgeslagen als ontsleutelde bestanden op de bestemming.
- Stel de volgende registersleutel in:
  - Pad = HKLM\Software\Policies\Microsoft\Windows\System
  - Waardetype = DWORD
  - Naam = CopyFileAllowDecryptedRemoteDestination
  - Waarde = 1

Let erop dat het instellen van de registersleutel van invloed is op alle kopieerbewerkingen die naar netwerk shares worden gemaakt.

## <a name="slow-enumeration-of-files-and-folders"></a>Langzame enumeratie van bestanden en mappen

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als er onvoldoende cache op de clientmachine is voor grote directories.

### <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de registerwaarde **DirectoryCacheEntrySizeMax** aan te passen zodat grotere mapvermeldingen op de clientmachine kunnen worden opgeslagen in de caching:

- Locatie: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Waarde mane: DirectoryCacheEntrySizeMax 
- Waardetype:DWORD
 
 
U kunt deze bijvoorbeeld instellen op 0x100000 om te zien of de prestaties beter worden.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fout AadDsTenantNotFound bij het inschakelen van Azure Active Directory Domain Service-verificatie (Azure AD DS) voor Azure Files 'Kan actieve tenants niet vinden met tenant-id aad-tenant-id'

### <a name="cause"></a>Oorzaak

Fout AadDsTenantNotFound teert wanneer u [Azure Active Directory Domain Services-verificatie (Azure AD DS)](storage-files-identity-auth-active-directory-domain-service-enable.md) probeert in te schakelen op Azure Files in een opslagaccount waarin [Azure AD Domain Service (Azure AD DS)](../../active-directory-domain-services/overview.md) niet is gemaakt in de Azure AD-tenant van het gekoppelde abonnement.  

### <a name="solution"></a>Oplossing

Schakel Azure AD DS in op de Azure AD-tenant van het abonnement waar uw opslagaccount op is geïmplementeerd. U hebt beheerdersbevoegdheden van de Azure AD-tenant nodig om een beheerd domein te maken. Als u niet de beheerder van de Azure AD-tenant bent, neem dan contact op met de beheerder en volg de stapsgewijs richtlijnen voor het maken en configureren van een Azure Active Directory Domain Services [beheerd domein.](../../active-directory-domain-services/tutorial-create-instance.md)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Kan geen Azure Files met AD-referenties 

### <a name="self-diagnostics-steps"></a>Stappen voor zelfdiagnose
Zorg er eerst voor dat u alle vier de stappen hebt gevolgd om [ad-Azure Files in te schakelen.](./storage-files-identity-auth-active-directory-enable.md)

Probeer ten tweede een [Azure-bestands share te maken met de opslagaccountsleutel](./storage-how-to-use-files-windows.md). Als het niet lukt om te worden bevestigd, downloadt u [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) om de clientomgeving te valideren, de incompatibele clientconfiguratie te detecteren die toegangsfout zou veroorzaken voor Azure Files, geeft u prescriptieve richtlijnen voor het zelf oplossen van problemen en verzamelt u de diagnostische traceringen.

Ten derde kunt u de cmdlet Debug-AzStorageAccountAuth uitvoeren om een set basiscontroles uit te voeren op uw AD-configuratie met de aangemelde AD-gebruiker. Deze cmdlet wordt ondersteund met [versie AzFilesHybrid v 0.1.2 en hoger](https://github.com/Azure-Samples/azure-files-samples/releases). U moet deze cmdlet uitvoeren met een AD-gebruiker die de machtiging Eigenaar heeft voor het doelopslagaccount.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
De cmdlet voert de onderstaande controles op volgorde uit en biedt richtlijnen voor fouten:
1. CheckADObjectPasswordIsCorrect: zorg ervoor dat het wachtwoord dat is geconfigureerd voor de AD-identiteit die het opslagaccount vertegenwoordigt, overeenkomt met dat van de kerb1- of kerb2-sleutel van het opslagaccount. Als het wachtwoord onjuist is, kunt u [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) uitvoeren om het wachtwoord opnieuw in te stellen. 
2. CheckADObject: controleer of er een object in de Active Directory is dat het opslagaccount vertegenwoordigt en de juiste SPN (service-principal name) heeft. Als de SPN niet correct is ingesteld, moet u de Set-AD-cmdlet uitvoeren die in de foutopsporings-cmdlet is geretourneerd om de SPN te configureren.
3. CheckDomainJoined: controleer of de clientmachine lid is van een domein in AD. Als uw computer geen lid is van een domein aan AD, raadpleegt u dit [artikel](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) voor instructies voor domein-join.
4. CheckPort445Connectivity: controleer of poort 445 is geopend voor de SMB-verbinding. Als de vereiste poort niet is geopend, raadpleegt u het hulpprogramma voor probleemoplossing voor [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) connectiviteitsproblemen met Azure Files.
5. CheckSidHasAadUser: controleer of de aangemelde AD-gebruiker is gesynchroniseerd met Azure AD. Als u wilt controleren of een specifieke AD-gebruiker is gesynchroniseerd met Azure AD, kunt u de -UserName en -Domain opgeven in de invoerparameters. 
6. CheckGetKerberosTicket: probeer een Kerberos-ticket te verkrijgen om verbinding te maken met het opslagaccount. Als er geen geldig Kerberos-token is, voer dan de cmdlet klist get cifs/storage-account-name.file.core.windows.net uit en bekijk de foutcode om de fout bij het ophalen van het ticket te veroorzaken.
7. CheckStorageAccountDomainJoined: Controleer of de AD-verificatie is ingeschakeld en of de AD-eigenschappen van het account zijn ingevuld. Als dat niet het beste is, [raadpleegt](./storage-files-identity-ad-ds-enable.md) u deze instructie om verificatie AD DS in te schakelen op Azure Files. 
8. CheckUserRbacAssignment: Controleer of de AD-gebruiker de juiste RBAC-roltoewijzing heeft om machtigingen op shareniveau te bieden voor toegang tot Azure Files. Zo niet, raadpleegt u de instructie [hier om](./storage-files-identity-ad-ds-assign-permissions.md) de machtiging op shareniveau te configureren. (Ondersteund op AzFilesHybrid v0.2.3+ versie)
9. CheckUserFileAccess: Controleer of de AD-gebruiker de juiste machtiging voor directory/bestand (Windows ACL's) heeft voor toegang tot Azure Files. Zo niet, raadpleegt u de instructie [hier om](./storage-files-identity-ad-ds-configure-permissions.md) de machtiging op map-/bestandsniveau te configureren. (Ondersteund op AzFilesHybrid v0.2.3+ versie)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Kan geen machtigingen op map-/bestandsniveau (Windows ACL's) configureren met Windows Verkenner

### <a name="symptom"></a>Symptoom

U kunt een van de onderstaande symptomen ervaren bij het configureren van Windows ACL's met Verkenner op een bevestigingsbestands share:
- Nadat u op de machtiging Bewerken onder tabblad Beveiliging klikt, wordt de wizard Machtiging niet geladen. 
- Wanneer u probeert een nieuwe gebruiker of groep te selecteren, wordt op de domeinlocatie niet de juiste AD DS weergegeven. 

### <a name="solution"></a>Oplossing

U wordt aangeraden het [hulpprogramma icacls](/windows-server/administration/windows-commands/icacls) te gebruiken om machtigingen op map-/bestandsniveau te configureren als tijdelijke oplossing. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Fouten bij het uitvoeren Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Fout: 'De adreslijstservice kan geen relatieve id toewijzen'

Deze fout kan optreden als een domeincontroller met de FSMO-rol RID-master niet beschikbaar is of is verwijderd uit het domein en is hersteld vanuit de back-up.  Controleer of alle domeincontrollers actief en beschikbaar zijn.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Fout: ‘Kan de positieparameters niet binden, omdat er geen namen zijn opgegeven’

Deze fout wordt waarschijnlijk veroorzaakt door een syntaxisfout in de opdracht Join-AzStorageAccountforAuth.  Controleer de opdracht op spelfouten of syntaxisfouten en controleer of de nieuwste versie van de AzFilesHybrid-module https://github.com/Azure-Samples/azure-files-samples/releases) ( is geïnstalleerd.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files on-premises AD DS ondersteuning voor AES 256 Kerberos-versleuteling

We hebben ondersteuning voor AES 256 Kerberos-versleuteling geïntroduceerd voor Azure Files on-AD DS-verificatie met [AzFilesHybrid-module v0.2.2.](https://github.com/Azure-Samples/azure-files-samples/releases) Als u AD DS hebt ingeschakeld met een moduleversie lager dan v0.2.2, moet u de nieuwste AzFilesHybrid-module (v0.2.2+) downloaden en de onderstaande PowerShell uitvoeren. Als u de verificatie AD DS uw opslagaccount nog niet hebt ingeschakeld, kunt u deze [richtlijnen](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) volgen voor inschakelen. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, [neem dan contact op met de](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ondersteuning om uw probleem snel op te lossen.
