---
title: Problemen met Azure Files in Windows oplossen
description: Problemen met Azure Files oplossen in Windows. Zie algemene problemen met betrekking tot Azure Files wanneer u verbinding maakt vanaf Windows-clients en mogelijke oplossingen vindt. Alleen voor SMB-shares
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: aef332e54fa650e1abbebe671560238d7eb318de
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492043"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Problemen met Azure Files oplossen in Windows (SMB)

In dit artikel vindt u algemene problemen die betrekking hebben op Microsoft Azure-bestanden wanneer u verbinding maakt vanaf Windows-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. Naast de stappen voor probleem oplossing in dit artikel, kunt u ook [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) gebruiken om ervoor te zorgen dat de Windows-client omgeving voldoet aan de vereisten. AzFileDiagnostics automatiseert de detectie van de meeste symptomen die in dit artikel worden genoemd en helpt u bij het instellen van uw omgeving om optimaal gebruik te maken van de prestaties.

> [!IMPORTANT]
> De inhoud van dit artikel is alleen van toepassing op SMB-shares. Zie [problemen met Azure NFS-bestands shares oplossen](storage-troubleshooting-files-nfs.md)voor meer informatie over NFS-shares.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Fout 5 bij het koppelen van een Azure-bestands share

Wanneer u een bestands share probeert te koppelen, wordt mogelijk de volgende fout weer gegeven:

- Systeemfout 5 heeft zich voorgedaan. Toegang wordt geweigerd.

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: niet-versleuteld communicatie kanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](../common/storage-require-secure-transfer.md) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Voor Windows 8, Windows Server 2012 en latere versies van elk systeem wordt onderhandeld over aanvragen die SMB 3.0 omvatten, wat ondersteuning biedt voor versleuteling.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Verbinding maken vanaf een client die ondersteuning biedt voor SMB-versleuteling (Windows 8, Windows Server 2012 of hoger) of verbinding maken vanaf een virtuele machine in hetzelfde Data Center als het Azure-opslag account dat wordt gebruikt voor de Azure-bestands share.
2. Controleer of de instelling [beveiligde overdracht vereist](../common/storage-require-secure-transfer.md) is uitgeschakeld op het opslag account als de client geen SMB-versleuteling ondersteunt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Oorzaak 3: machtigingen op share niveau zijn onjuist bij het gebruik van verificatie op basis van identiteiten

Als gebruikers toegang hebben tot de Azure-bestands share met Active Directory (AD) of Azure Active Directory Domain Services-verificatie (Azure AD DS), mislukt de toegang tot de bestands share met de fout ' toegang geweigerd ' als machtigingen op share niveau onjuist zijn. 

### <a name="solution-for-cause-3"></a>Oplossing voor oorzaak 3

Controleer of de machtigingen juist zijn geconfigureerd:

- **Active Directory (AD)** Zie [machtigingen op share niveau toewijzen aan een identiteit](./storage-files-identity-ad-ds-assign-permissions.md).

    Machtigings toewijzingen op share niveau worden ondersteund voor groepen en gebruikers die zijn gesynchroniseerd van de Active Directory (AD) naar Azure Active Directory (Azure AD) met behulp van Azure AD Connect.  Controleer of de groepen en gebruikers toegewezen machtigingen op share niveau worden niet ondersteund in de Cloud groepen.
- Zie **Azure Active Directory Domain Services (Azure AD DS)** [toegangs machtigingen toewijzen aan een identiteit](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Fout 53, fout 67 of fout 87 bij het koppelen of ontkoppelen van een Azure-bestands share

Wanneer u probeert een bestands share te koppelen vanuit een on-premises of vanuit een ander Data Center, kunnen de volgende fouten optreden:

- Systeemfout 53 heeft zich voorgedaan. Het netwerkpad is niet gevonden.
- Systeemfout 67 heeft zich voorgedaan. De naam van het netwerk kan niet worden gevonden.
- Systeemfout 87 heeft zich voorgedaan. De parameter is onjuist.

### <a name="cause-1-port-445-is-blocked"></a>Oorzaak 1: poort 445 is geblokkeerd

Systeem fout 53 of systeem fout 67 kan optreden als poort 445 uitgaande communicatie met een Azure Files Data Center wordt geblokkeerd. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Gebruik het hulp programma [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) of de cmdlet om te controleren of uw firewall of Internet provider poort 445 blokkeert `Test-NetConnection` . 

Als u de `Test-NetConnection` cmdlet wilt gebruiken, moet de module Azure PowerShell zijn geïnstalleerd. zie [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps) voor meer informatie. Vergeet niet om `<your-storage-account-name>` en `<your-resource-group-name>` te vervangen door de betreffende namen van uw opslagaccount.

   
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
Azure File Sync kunt uw on-premises Windows-Server omzetten in een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. Azure File Sync werkt via poort 443 en kan daarom worden gebruikt als tijdelijke oplossing voor toegang tot Azure Files vanaf clients waarbij poort 445 is geblokkeerd. [Meer informatie over het instellen van Azure file sync](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Oplossing 2: VPN gebruiken
Door een VPN-verbinding met uw specifieke opslag account in te stellen, zal het verkeer via internet een beveiligde tunnel passeren. Volg de [instructies voor het instellen van VPN](storage-files-configure-p2s-vpn-windows.md) om toegang te krijgen tot Azure Files vanuit Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Oplossing 3: Poort 445 deblokkeren met behulp van uw internetprovider/IT-beheerder
Werk samen met uw IT-afdeling of provider voor het openen van poort 445 uitgaand verkeer naar [Azure IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Oplossing 4: Hulpprogramma’s op basis van REST API gebruiken, zoals Storage Explorer/PowerShell
Azure Files biedt ook ondersteuning voor REST naast SMB. REST-toegang werkt via poort 443 (standaard TCP). Er zijn verschillende hulpprogramma's die zijn geschreven met REST API die een uitgebreide UI-ervaring bieden. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) is een van beide. [Download en installeer Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met de bestandsshare die door Azure Files wordt ondersteund. U kunt ook [Power shell](./storage-how-to-use-files-powershell.md) gebruiken die ook gebruikers rest API.

### <a name="cause-2-ntlmv1-is-enabled"></a>Oorzaak 2: NTLMv1 is ingeschakeld

Systeem fout 53 of systeem fout 87 kan optreden als NTLMv1-communicatie is ingeschakeld op de client. Azure Files biedt alleen ondersteuning voor NTLMv2-verificatie. Als NTLMv1 is ingeschakeld, is de client minder veilig. Om deze reden wordt communicatie geblokkeerd voor Azure Files. 

Als u wilt vaststellen of dit de oorzaak van de fout is, controleert u of de volgende registersubsleutel is ingesteld op de waarde 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Zie het onderwerp [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) in TechNet voor meer informatie.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Zet de waarde **LmCompatibilityLevel** terug naar de standaardwaarde 3 in de volgende registersubsleutel:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Fout 1816-onvoldoende quotum beschikbaar om deze opdracht te verwerken

### <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer u de bovengrens van gelijktijdige open ingangen bereikt die zijn toegestaan voor een bestand of map op de Azure-bestands share. Zie [Azure Files-schaalbaarheidsdoelen](./storage-files-scale-targets.md#azure-files-scale-targets) voor meer informatie.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige open ingangen door enkele grepen te sluiten en probeer het opnieuw. Zie [Microsoft Azure Storage controle lijst voor prestaties en schaal baarheid](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

Gebruik de Power shell [-cmdlet Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) om de geopende ingangen voor een bestands share, map of bestand weer te geven.  

Gebruik de Power shell [-cmdlet close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) om open ingangen voor een bestands share, map of bestand te sluiten.

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fout ' geen toegang ' wanneer u een Azure-bestands share probeert te openen of verwijderen  
Wanneer u probeert een Azure-bestands share in de portal te openen of verwijderen, wordt mogelijk de volgende fout weer gegeven:

Geen toegang  
Fout code: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 1: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Oorzaak 2: uw gebruikers account heeft geen toegang tot het opslag account

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Blader naar het opslag account waar de Azure-bestands share zich bevindt, klik op **toegangs beheer (IAM)** en controleer of uw gebruikers account toegang heeft tot het opslag account. Zie [uw opslag account beveiligen met Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../blobs/security-recommendations.md#data-protection)voor meer informatie.

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Kan een bestand of map niet wijzigen, verplaatsen/verwijderen of de naam ervan wijzigen
Een van de belangrijkste doel stellingen van een bestands share is dat meerdere gebruikers en toepassingen gelijktijdig kunnen communiceren met bestanden en mappen in de share. Om u te helpen bij deze interactie bieden bestands shares verschillende manieren om toegang tot bestanden en mappen te verkrijgen.

Wanneer u een bestand opent vanuit een gekoppelde Azure-bestands share via SMB, vraagt uw toepassing/besturings systeem een bestands ingang aan. Dit is een verwijzing naar het bestand. Uw toepassing geeft onder andere de modus voor het delen van bestanden op wanneer er een bestands ingang wordt aangevraagd, waarmee het niveau wordt opgegeven van de toegang tot het bestand dat wordt afgedwongen door Azure Files: 

- `None`: u hebt exclusieve toegang. 
- `Read`: anderen kunnen het bestand lezen terwijl u het hebt geopend.
- `Write`: anderen kunnen naar het bestand schrijven terwijl het is geopend. 
- `ReadWrite`: een combi natie van de `Read` `Write` modus en delen.
- `Delete`: anderen kunnen het bestand verwijderen terwijl het geopend is. 

Het FileREST-protocol heeft weliswaar als stateless protocol geen concept van bestands ingangen, maar biedt een vergelijkbaar mechanisme om toegang te krijgen tot bestanden en mappen die het script, de toepassing of de service kunnen gebruiken: bestands leases. Wanneer een bestand wordt geleased, wordt het beschouwd als gelijkwaardig aan een bestands ingang met de modus voor het delen van bestanden van `None` . 

Hoewel bestands ingangen en leases een belang rijk doel hebben, kunnen er soms zwevende bestands ingangen en leases worden gebruikt. Als dit gebeurt, kan dit problemen veroorzaken bij het wijzigen of verwijderen van bestanden. Er kunnen fout berichten worden weer geven, zoals:

- Het proces kan het bestand niet openen omdat het door een ander proces wordt gebruikt.
- De actie kan niet worden voltooid omdat het bestand is geopend in een ander programma.
- Het document is vergrendeld voor bewerking door een andere gebruiker.
- De opgegeven resource is gemarkeerd voor verwijdering door een SMB-client.

De oplossing voor dit probleem is afhankelijk van het feit of dit wordt veroorzaakt door een zwevende bestands ingang of lease. 

### <a name="cause-1"></a>Oorzaak 1
Een bestands ingang voor komt dat een bestand of map wordt gewijzigd of verwijderd. U kunt de Power shell [-cmdlet Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) gebruiken om open ingangen weer te geven. 

Als alle SMB-clients hun open ingangen hebben gesloten op een bestand/map en het probleem blijft optreden, kunt u het sluiten van een bestands ingang afdwingen.

### <a name="solution-1"></a>Oplossing 1
Als u wilt afdwingen dat een bestands ingang wordt gesloten, gebruikt u de Power shell [-cmdlet close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) . 

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

### <a name="cause-2"></a>Oorzaak 2
Een bestands lease voor komt dat een bestand wordt gewijzigd of verwijderd. U kunt controleren of een bestand een bestands lease heeft met de volgende Power shell, vervangen `<resource-group>` ,, `<storage-account>` `<file-share>` en `<path-to-file>` met de juiste waarden voor uw omgeving:

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

Als een bestand een lease heeft, moet het geretourneerde object de volgende eigenschappen bevatten:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Oplossing 2
Als u een lease uit een bestand wilt verwijderen, kunt u de lease vrijgeven of de lease verbreken. Voor het vrijgeven van de lease hebt u de LeaseId van de lease nodig die u instelt wanneer u de lease maakt. U hebt de LeaseId niet nodig om de lease te verstoren.

In het volgende voor beeld ziet u hoe u de lease verbreekt voor het bestand dat is aangegeven in oorzaak 2 (dit voor beeld gaat door met de Power shell-variabelen van oorzaak 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Bestanden worden langzaam gekopieerd van en naar Azure Files in Windows

Mogelijk ziet u trage prestaties wanneer u bestanden probeert over te brengen naar de Azure File-Service.

- Als u geen specifieke minimale I/O-grootte vereiste hebt, raden we u aan om 1 MiB te gebruiken als de I/O-grootte voor optimale prestaties.
-   Als u de uiteindelijke grootte kent van een bestand dat u uitbreidt met schrijf bewerkingen, en uw software geen compatibiliteits problemen heeft wanneer de niet-genoteerde staart van het bestand nullen bevat, moet u de bestands grootte vooraf instellen in plaats van elke schrijf bewerking uit te voeren.
-   Gebruik de juiste Kopieer methode:
    -   Gebruik [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor elke overdracht tussen twee bestands shares.
    -   Gebruik [Robocopy](./storage-files-deployment-guide.md#robocopy) tussen bestands shares op een on-premises computer.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Overwegingen voor Windows 8,1 of Windows Server 2012 R2

Zorg ervoor dat de [KB3114025](https://support.microsoft.com/help/3114025) -hotfix is geïnstalleerd voor clients met Windows 8,1 of windows server 2012 R2. Deze hotfix verbetert de prestaties van het maken en sluiten van ingangen.

U kunt het volgende script uitvoeren om te controleren of de hotfix is geïnstalleerd:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Als de hotfix is geïnstalleerd, wordt de volgende uitvoer weer gegeven:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Voor installatie kopieën van Windows Server 2012 R2 in azure Marketplace is een hotfix-KB3114025 standaard geïnstalleerd, vanaf december 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Geen map met een stationsletter in ' mijn computer ' of ' deze PC '

Als u een Azure-bestands share als beheerder toewijst met behulp van net use, lijkt het alsof de share ontbreekt.

### <a name="cause"></a>Oorzaak

Windows Verkenner wordt standaard niet uitgevoerd als beheerder. Als u net use uitvoert vanaf een opdracht prompt met beheerders rechten, wijst u het netwerk station toe als beheerder. Omdat toegewezen stations gebruikers gericht zijn, worden de stations niet weer gegeven in de gebruikers account die is aangemeld als deze zijn gekoppeld onder een ander gebruikers account.

### <a name="solution"></a>Oplossing
Koppel de share vanuit een niet-beheerders opdracht regel. U kunt ook [Dit TechNet-onderwerp](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) volgen om de register waarde **EnableLinkedConnections** te configureren.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Opdracht net use mislukt als het opslag account een slash (voorwaarts) bevat

### <a name="cause"></a>Oorzaak

De opdracht net use interpreteert een slash (/) als een opdracht regel optie. Als de naam van uw gebruikers account begint met een slash, mislukt de stationstoewijzing.

### <a name="solution"></a>Oplossing

U kunt een van de volgende stappen gebruiken om het probleem te omzeilen:

- Voer de volgende PowerShell-opdracht uit:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Vanuit een batch-bestand kunt u de opdracht op deze manier uitvoeren:

  `Echo new-smbMapping ... | powershell -command –`

- Plaats dubbele aanhalings tekens rond de sleutel om dit probleem te omzeilen, tenzij de slash het eerste teken is. Als dat het geval is, gebruikt u de interactieve modus en voert u uw wacht woord afzonderlijk in of genereert u de sleutels opnieuw om een sleutel op te halen die niet begint met een slash.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Toepassing of service kan geen toegang krijgen tot een gekoppeld Azure Files station

### <a name="cause"></a>Oorzaak

Stations zijn gekoppeld per gebruiker. Als uw toepassing of service wordt uitgevoerd onder een ander gebruikers account dan die waarmee het station is gekoppeld, wordt het station niet weer geven door de toepassing.

### <a name="solution"></a>Oplossing

Gebruik een van de volgende oplossingen:

-   Koppel het station van hetzelfde gebruikers account dat de toepassing bevat. U kunt een hulp programma zoals PsExec gebruiken.
- Geef de naam en sleutel van het opslag account op in de para meters gebruikers naam en wacht woord van de opdracht net use.
- Gebruik de opdracht cmdkey om de referenties toe te voegen aan referentie beheer. Voer dit uit vanaf een opdracht regel in de context van het service account, hetzij via een interactieve aanmelding ofwel met behulp van `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Wijs de share rechtstreeks toe zonder een toegewezen stationsletter te gebruiken. Het is mogelijk dat sommige toepassingen niet opnieuw verbinding maken met de stationsletter, zodat het volledige UNC-pad mogelijk betrouwbaarder is. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Nadat u deze instructies hebt gevolgd, wordt het volgende fout bericht weer gegeven wanneer u net use uitvoert voor het systeem-of netwerk service account: ' systeem fout 1312 is opgetreden. Een opgegeven aanmeldings sessie bestaat niet. Mogelijk is deze al beëindigd. " Als dit het geval is, moet u ervoor zorgen dat de gebruikers naam die wordt door gegeven aan net use, domein gegevens bevat (bijvoorbeeld: [naam van het opslag account]. file. core. Windows. net ").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Fout ' u kopieert een bestand naar een doel dat geen ondersteuning biedt voor versleuteling '

Wanneer een bestand via het netwerk wordt gekopieerd, wordt het bestand ontsleuteld op de bron computer, verzonden als Lees bare tekst en opnieuw versleuteld op de bestemming. Het is echter mogelijk dat u de volgende fout ziet wanneer u een versleuteld bestand probeert te kopiëren: ' u kopieert het bestand naar een bestemming die geen ondersteuning biedt voor versleuteling '.

### <a name="cause"></a>Oorzaak
Dit probleem kan optreden als u Encrypting File System (EFS) gebruikt. Met BitLocker versleutelde bestanden kunnen naar Azure Files worden gekopieerd. Azure Files ondersteunt echter geen NTFS EFS.

### <a name="workaround"></a>Tijdelijke oplossing
Als u een bestand wilt kopiëren via het netwerk, moet u het eerst decoderen. Hanteer één van de volgende methoden:

- Gebruik de opdracht **copy/d** . Hierdoor kunnen de versleutelde bestanden worden opgeslagen als ontsleutelde bestanden op de bestemming.
- Stel de volgende registersleutel in:
  - Pad = HKLM\Software\Policies\Microsoft\Windows\System
  - Waardetype = DWORD
  - Naam = CopyFileAllowDecryptedRemoteDestination
  - Waarde = 1

Houd er rekening mee dat het instellen van de register sleutel van invloed is op alle kopieer bewerkingen die worden uitgevoerd op netwerk shares.

## <a name="slow-enumeration-of-files-and-folders"></a>Trage inventarisatie van bestanden en mappen

### <a name="cause"></a>Oorzaak

Dit probleem kan zich voordoen als er onvoldoende cache op de client computer is voor grote mappen.

### <a name="solution"></a>Oplossing

U kunt dit probleem oplossen door de register waarde **DirectoryCacheEntrySizeMax** aan te passen, zodat er grotere mappen worden opgeslagen in de cache van de client computer:

- Locatie: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Waarde Mane: DirectoryCacheEntrySizeMax 
- Waardetype: DWORD
 
 
U kunt dit bijvoorbeeld instellen op 0x100000 en zien of de prestaties beter worden.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Fout AadDsTenantNotFound bij het inschakelen van de verificatie van Azure Active Directory Domain Service (Azure AD DS) voor Azure Files ' kan geen actieve tenants vinden met de Tenant-ID Aad-Tenant-id '

### <a name="cause"></a>Oorzaak

Fout AadDsTenantNotFound treedt op wanneer u probeert [Azure Active Directory Domain Services-verificatie (azure AD DS) in azure files in te scha kelen](storage-files-identity-auth-active-directory-domain-service-enable.md) op een opslag account waarin Azure [ad Domain Service (Azure AD DS)](../../active-directory-domain-services/overview.md) niet is gemaakt op de Azure AD-Tenant van het gekoppelde abonnement.  

### <a name="solution"></a>Oplossing

Schakel Azure AD DS in op de Azure AD-Tenant van het abonnement waarop uw opslag account is geïmplementeerd. U hebt beheerders bevoegdheden nodig van de Azure AD-Tenant om een beheerd domein te maken. Als u niet de beheerder van de Azure AD-Tenant bent, neemt u contact op met de beheerder en volgt u de stapsgewijze richt lijnen voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein](../../active-directory-domain-services/tutorial-create-instance.md).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Kan Azure Files niet koppelen aan AD-referenties 

### <a name="self-diagnostics-steps"></a>Stappen voor zelf diagnostische gegevens
Zorg er eerst voor dat u alle vier de stappen hebt gevolgd om [Azure files AD-verificatie in te scha kelen](./storage-files-identity-auth-active-directory-enable.md).

Probeer vervolgens de [Azure-bestands share te koppelen met de sleutel van het opslag account](./storage-how-to-use-files-windows.md). Als u de koppeling niet hebt gemaakt, downloadt u [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) om u te helpen bij het valideren van de client omgeving, detecteert u de niet-compatibele client configuratie die toegang zou hebben tot de Azure files, bevat prescriptieve richt lijnen voor zelf herstel en worden de diagnostische traceringen verzameld.

Ten derde kunt u de Debug-AzStorageAccountAuth cmdlet uitvoeren om een set basis controles uit te voeren op uw AD-configuratie met de aangemelde AD-gebruiker. Deze cmdlet wordt ondersteund met [versie AzFilesHybrid v 0.1.2 en hoger](https://github.com/Azure-Samples/azure-files-samples/releases). U moet deze cmdlet uitvoeren met een AD-gebruiker die de machtiging Eigenaar heeft voor het doelopslagaccount.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
De cmdlet voert deze controles hieronder uit, en biedt richt lijnen voor fouten:
1. CheckADObjectPasswordIsCorrect: Zorg ervoor dat het wacht woord dat is geconfigureerd voor de AD-identiteit die het opslag account vertegenwoordigt, overeenkomt met de sleutel kerb1 of kerb2 van het opslag account. Als het wacht woord onjuist is, kunt u [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) uitvoeren om het wacht woord opnieuw in te stellen. 
2. CheckADObject: Bevestig dat er een object in de Active Directory is dat het opslag account vertegenwoordigt en de juiste SPN (Service Principal Name) heeft. Als de SPN niet juist is ingesteld, voert u de set-AD-cmdlet uit die is geretourneerd in de cmdlet debug om de SPN te configureren.
3. CheckDomainJoined: Controleer of de client computer lid is van een domein dat is gekoppeld aan AD. Als uw computer niet is gekoppeld aan AD, raadpleegt u dit [artikel](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) voor instructies over het toevoegen van een domein.
4. CheckPort445Connectivity: Controleer of poort 445 is geopend voor de SMB-verbinding. Als de vereiste poort niet open is, raadpleegt u het hulp programma voor probleem oplossing [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) voor verbindings problemen met Azure files.
5. CheckSidHasAadUser: Controleer of de aangemelde AD-gebruiker is gesynchroniseerd met Azure AD. Als u wilt controleren of een specifieke AD-gebruiker is gesynchroniseerd met Azure AD, kunt u de-gebruikers naam en-domein opgeven in de invoer parameters. 
6. CheckGetKerberosTicket: er wordt geprobeerd een Kerberos-ticket op te halen om verbinding te maken met het opslag account. Als er geen geldig Kerberos-token is, voert u de cmdlet Klist ophalen CIFS/Storage-account-name. file. core. Windows. net uit en controleert u de fout code in het hoofd knooppunt waardoor het ophalen van het ticket mislukt.
7. CheckStorageAccountDomainJoined: Controleer of de AD-verificatie is ingeschakeld en of de AD-eigenschappen van het account zijn ingevuld. Als dat niet het geval is, raadpleegt u de instructie [hier](./storage-files-identity-ad-ds-enable.md) om AD DS verificatie in te scha kelen op Azure files. 

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Kan de machtigingen voor mappen en bestands niveau (Windows-Acl's) niet configureren met Windows bestanden Verkenner

### <a name="symptom"></a>Symptoom

U kunt de volgende symptomen ondervinden bij het configureren van Windows-Acl's met bestanden Verkenner op een gekoppelde bestands share:
- Nadat u op de machtiging bewerken hebt geklikt op het tabblad Beveiliging, wordt de wizard machtiging niet geladen. 
- Wanneer u probeert een nieuwe gebruiker of groep te selecteren, wordt in de domein locatie niet het juiste AD DS domein weer gegeven. 

### <a name="solution"></a>Oplossing

U wordt aangeraden om het [icacls-hulp programma](/windows-server/administration/windows-commands/icacls) te gebruiken om de machtigingen voor het Directory/bestands niveau als tijdelijke oplossing te configureren. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Fouten bij het uitvoeren van Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Fout: de Directory service kan geen relatieve id toewijzen.

Deze fout kan optreden als een domein controller met de FSMO-functie van de RID-master niet beschikbaar is of uit het domein is verwijderd en teruggezet vanuit een back-up.  Bevestig dat alle domein controllers actief zijn en beschikbaar zijn.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Fout: ‘Kan de positieparameters niet binden, omdat er geen namen zijn opgegeven’

Deze fout wordt waarschijnlijk veroorzaakt door een syntaxisfout in de opdracht Join-AzStorageAccountforAuth.  Controleer de opdracht of er fouten zijn opgetreden met de spelling of syntaxis fout en controleer of de meest recente versie van de AzFilesHybrid-module ( https://github.com/Azure-Samples/azure-files-samples/releases) is geïnstalleerd).  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Ondersteuning voor Azure Files on-premises AD DS-verificatie voor AES 256 Kerberos-versleuteling

We hebben ondersteuning voor AES 256 Kerberos-versleuteling geïntroduceerd voor Azure Files on-premises AD DS authenticatie met [AzFilesHybrid module v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Als u AD DS verificatie hebt ingeschakeld met een module versie lager dan v 0.2.2, moet u de meest recente AzFilesHybrid-module (v 0.2.2 +) downloaden en de onderstaande Power shell uitvoeren. Als u AD DS verificatie nog niet hebt ingeschakeld in uw opslag account, kunt u deze [instructies](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) volgen voor de activering. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.