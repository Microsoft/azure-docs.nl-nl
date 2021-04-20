---
title: Veelgestelde vragen over Azure NetApp Files | Microsoft Docs
description: Bekijk veelgestelde vragen over Azure NetApp Files, zoals netwerken, beveiliging, prestaties, capaciteitsbeheer en gegevensmigratie/-beveiliging.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: a8c06b25b923d663e982e940100be7b9a2a009e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726840"
---
# <a name="faqs-about-azure-netapp-files"></a>Veelgestelde vragen over Azure NetApp Files

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure NetApp Files. 

## <a name="networking-faqs"></a>Veelgestelde vragen over netwerken

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Loopt het gegevenspad voor NFS of SMB via internet?  

Nee. Het gegevenspad voor NFS of SMB gaat niet via internet. Azure NetApp Files is een native Azure-service die wordt geïmplementeerd in de Azure Virtual Network (VNet) waar de service beschikbaar is. Azure NetApp Files een gedelegeerd subnet gebruikt en een netwerkinterface rechtstreeks op het VNet inmaakt. 

Zie [Richtlijnen voor Azure NetApp Files netwerkplanning](./azure-netapp-files-network-topologies.md) voor meer informatie.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kan ik een VNet dat ik al heb gemaakt, verbinden met de Azure NetApp Files service?

Ja, u kunt VNets die u hebt gemaakt, verbinden met de service. 

Zie [Richtlijnen voor Azure NetApp Files netwerkplanning](./azure-netapp-files-network-topologies.md) voor meer informatie.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kan ik een NFS-volume van een Azure NetApp Files dns FQDN-naam gebruiken?

Ja, dat kan, als u de vereiste DNS-vermeldingen maakt. Azure NetApp Files levert het service-IP-adres voor het inrichtende volume. 

> [!NOTE] 
> Azure NetApp Files kunt zo nodig extra IP's voor de service implementeren.  DNS-vermeldingen moeten mogelijk periodiek worden bijgewerkt.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kan ik mijn eigen IP-adres instellen of selecteren voor een Azure NetApp Files volume?  

Nee. IP-toewijzing aan Azure NetApp Files volumes is dynamisch. Statische IP-toewijzing wordt niet ondersteund. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Ondersteunt Azure NetApp Files VNet met dubbele stack (IPv4 en IPv6)?

Nee, Azure NetApp Files ondersteunt momenteel geen VNet met dubbele stack (IPv4 en IPv6).  
 
## <a name="security-faqs"></a>Veelgestelde vragen over beveiliging

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kan het netwerkverkeer tussen de azure-VM en de opslag worden versleuteld?

Gegevensverkeer tussen NFSv4.1-clients en Azure NetApp Files-volumes kan worden versleuteld met Behulp van Kerberos met AES-256-versleuteling. Zie [NFSv4.1 Kerberos-versleuteling](configure-kerberos-encryption.md) configureren voor Azure NetApp Files voor meer informatie.   

Gegevensverkeer tussen NFSv3- of SMB3-clients naar Azure NetApp Files volumes wordt niet versleuteld. Het verkeer van een Azure-VM (met een NFS- of SMB-client) naar Azure NetApp Files is echter net zo veilig als ander verkeer van Azure-VM naar VM. Dit verkeer is lokaal in het Azure-datacenternetwerk. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kan de opslag at-rest worden versleuteld?

Alle Azure NetApp Files worden versleuteld met behulp van de FIPS 140-2-standaard. Alle sleutels worden beheerd door de Azure NetApp Files service. 

### <a name="how-are-encryption-keys-managed"></a>Hoe worden versleutelingssleutels beheerd? 

Sleutelbeheer voor Azure NetApp Files wordt verwerkt door de service. Voor elk volume wordt een unieke XTS-AES-256-gegevensversleutelingssleutel gegenereerd. Een versleutelingssleutelhiërarchie wordt gebruikt om alle volumesleutels te versleutelen en te beveiligen. Deze versleutelingssleutels worden nooit weergegeven of gerapporteerd in een niet-versleutelde indeling. Versleutelingssleutels worden onmiddellijk verwijderd wanneer een volume wordt verwijderd.

Ondersteuning voor door de klant beheerde sleutels (Bring Your Own Key) met behulp van Azure Dedicated HSM is op gecontroleerde basis beschikbaar in de regio's VS - oost, VS - zuid-centraal, VS - west 2 en US Gov - Virginia regio's. U kunt toegang aanvragen via [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Zodra de capaciteit beschikbaar komt, worden aanvragen goedgekeurd.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kan ik de NFS-exportbeleidsregels configureren om de toegang tot het Azure NetApp Files te bepalen?

Ja, u kunt maximaal vijf regels configureren in één NFS-exportbeleid.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Ondersteunt Azure NetApp Files netwerkbeveiligingsgroepen?

Nee, momenteel kunt u geen netwerkbeveiligingsgroepen toepassen op het gedelegeerde subnet van Azure NetApp Files of de netwerkinterfaces die door de service zijn gemaakt.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kan ik Azure RBAC gebruiken met Azure NetApp Files?

Ja, Azure NetApp Files ondersteuning voor Azure RBAC-functies. Naast de ingebouwde Azure-rollen kunt u [aangepaste rollen maken](../role-based-access-control/custom-roles.md) voor Azure NetApp Files. 

Zie Bewerkingen voor Azure-resourceproviders Azure NetApp Files volledige lijst met [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) machtigingen.

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Worden Azure-activiteitenlogboeken ondersteund op Azure NetApp Files?

Azure NetApp Files is een native Azure-service. Alle PUT-, POST- en DELETE-API's voor Azure NetApp Files worden geregistreerd. De logboeken tonen bijvoorbeeld activiteiten zoals wie de momentopname heeft gemaakt, wie het volume heeft gewijzigd, en meer.

Zie voor de volledige lijst met API-bewerkingen [Azure NetApp Files REST API.](/rest/api/netapp/)

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Kan ik Azure-beleid gebruiken met Azure NetApp Files?

Ja, u kunt aangepaste [Azure-beleidsregels maken.](../governance/policy/tutorials/create-custom-policy-definition.md) 

U kunt echter geen Azure-beleid (aangepast naamgevingsbeleid) maken op de Azure NetApp Files interface. Zie [Richtlijnen voor Azure NetApp Files netwerkplanning.](azure-netapp-files-network-topologies.md#considerations)

## <a name="performance-faqs"></a>Veelgestelde vragen over prestaties

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Wat moet ik doen om de prestaties van Azure NetApp Files optimaliseren of afstemmen?

U kunt de volgende acties uitvoeren volgens de prestatievereisten: 
- Zorg ervoor dat de grootte van de virtuele machine op de juiste wijze is aangepast.
- Schakel Versneld netwerken in voor de VM.
- Selecteer het gewenste serviceniveau en de gewenste grootte voor de capaciteitspool.
- Maak een volume met de gewenste quotumgrootte voor de capaciteit en prestaties.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Hoe kan ik op doorvoer gebaseerde serviceniveaus van Azure NetApp Files naar IOPS?

U kunt MB/s converteren naar IOPS met behulp van de volgende formule:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Hoe kan ik het serviceniveau van een volume wijzigen?

U kunt het serviceniveau van een bestaand volume wijzigen door het volume te verplaatsen naar een andere capaciteitspool die gebruikmaakt van het [serviceniveau](azure-netapp-files-service-levels.md) dat u voor het volume wilt gebruiken. Zie [Het serviceniveau van een volume dynamisch wijzigen.](dynamic-change-volume-service-level.md) 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Hoe kan ik prestaties Azure NetApp Files bewaken?

Azure NetApp Files biedt metrische gegevens over volumeprestaties. U kunt ook Azure Monitor voor het bewaken van metrische gegevens over gebruik voor Azure NetApp Files.  Zie [Metrische gegevens Azure NetApp Files](azure-netapp-files-metrics.md) voor de lijst met metrische prestatiegegevens voor Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Wat is de invloed op de prestaties van Kerberos op NFSv4.1?

Zie Impact op de prestaties van [Kerberos op NFSv4.1-volumes](performance-impact-kerberos.md) voor informatie over beveiligingsopties voor NFSv4.1, de geteste prestatievectoren en de verwachte invloed op de prestaties. 

## <a name="nfs-faqs"></a>Veelgestelde vragen over NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ik wil dat een volume automatisch wordt bevestigd wanneer een Azure-VM wordt gestart of opnieuw wordt opgestart.  Hoe kan ik mijn host configureren voor permanente NFS-volumes?

Als u wilt dat een NFS-volume automatisch wordt toegevoegd bij het starten of opnieuw opstarten van de VM, voegt u een vermelding toe aan het `/etc/fstab` bestand op de host. 

Zie Een volume voor virtuele Windows- of [Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) installeren of ontkoppelen voor meer informatie.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Waarom geeft de DF-opdracht op de NFS-client niet de inrichtende volumegrootte weer?

De volumegrootte die in DF wordt vermeld, is de maximale grootte Azure NetApp Files volume kan groeien. De grootte van het Azure NetApp Files volume in DF opdracht is niet weerspiegelend van het quotum of de grootte van het volume.  U kunt de Azure NetApp Files volumegrootte of het quotum via de Azure Portal of de API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welke NFS-versie wordt Azure NetApp Files ondersteund?

Azure NetApp Files biedt ondersteuning voor NFSv3 en NFSv4.1. U kunt [een volume maken met](azure-netapp-files-create-volumes.md) behulp van een van beide NFS-versies. 

### <a name="how-do-i-enable-root-squashing"></a>Hoe kan ik hoofdstleten inschakelen?

U kunt opgeven of het hoofdaccount al dan niet toegang heeft tot het volume met behulp van het exportbeleid van het volume. Zie [Exportbeleid configureren voor een NFS-volume](azure-netapp-files-configure-export-policy.md) voor meer informatie.

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kan ik hetzelfde bestandspad (token voor het maken van volumes) gebruiken voor meerdere volumes?

Ja, dat kunt u. Het bestandspad moet echter worden gebruikt in een ander abonnement of in een andere regio.   

U maakt bijvoorbeeld een volume met de naam `vol1` . Vervolgens maakt u een ander volume met de naam `vol1` in een andere capaciteitspool, maar in hetzelfde abonnement en dezelfde regio. In dit geval veroorzaakt het gebruik van dezelfde volumenaam `vol1` een fout. Als u hetzelfde bestandspad wilt gebruiken, moet de naam zich in een andere regio of een ander abonnement hebben.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Wanneer ik via een Windows-client toegang probeer te krijgen tot NFS-volumes, waarom duurt het dan lang om mappen en submappen te zoeken?

Zorg ervoor dat is ingeschakeld op de Windows-client om het zoeken van mappen en `CaseSensitiveLookup` submappen te versnellen:

1. Gebruik de volgende PowerShell-opdracht om CaseSensitiveLookup in teschakelen:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Bevestig het volume op de Windows-server.   
    Voorbeeld:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Hoe ondersteunt Azure NetApp Files NFSv4.1-bestandsvergrendeling? 

Voor NFSv4.1-clients ondersteunt Azure NetApp Files het NFSv4.1-bestandsvergrendelingsmechanisme dat de status van alle bestandsvergrendelingen onderhoudt onder een op lease gebaseerd model. 

Volgens RFC 3530 definieert Azure NetApp Files één leaseperiode voor alle statussen die worden gehouden door een NFS-client. Als de client de lease niet binnen de gedefinieerde periode vernieuwt, worden alle staten die zijn gekoppeld aan de lease van de client vrijgegeven door de server.  

Als een client die een volume mountt bijvoorbeeld niet meer reageert of vast loopt na de time-outs, worden de vergrendelingen vrijgegeven. De client kan de lease expliciet of impliciet vernieuwen door bewerkingen uit te voeren zoals het lezen van een bestand.   

Een respijtperiode definieert een periode van speciale verwerking waarin clients hun vergrendelingstoestand kunnen claimen tijdens het herstellen van een server. De standaard time-out voor de leases is 30 seconden met een respijtperiode van 45 seconden. Na die tijd wordt de lease van de client vrijgegeven.   

## <a name="smb-faqs"></a>Veelgestelde vragen over SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Welke SMB-versies worden ondersteund door Azure NetApp Files?

Azure NetApp Files ondersteunt SMB 2.1 en SMB 3.1 (met ondersteuning voor SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Is een Active Directory-verbinding vereist voor SMB-toegang? 

Ja, u moet een Active Directory-verbinding maken voordat u een SMB-volume implementeert. De opgegeven domeincontrollers moeten toegankelijk zijn voor het gedelegeerde subnet van Azure NetApp Files voor een geslaagde verbinding.  Zie [Een SMB-volume maken](./azure-netapp-files-create-volumes-smb.md) voor meer informatie. 

### <a name="how-many-active-directory-connections-are-supported"></a>Hoeveel Active Directory-verbindingen worden ondersteund?

Azure NetApp Files biedt geen ondersteuning voor meerdere Ad-verbindingen (Active Directory) in één *regio,* zelfs niet als de AD-verbindingen zich in verschillende NetApp-accounts hebben. U kunt echter meerdere AD-verbindingen in één abonnement *hebben,* zolang de AD-verbindingen zich in verschillende regio's hebben. Als u meerdere AD-verbindingen in één regio nodig hebt, kunt u afzonderlijke abonnementen gebruiken om dit te doen. 

Er wordt een AD-verbinding geconfigureerd per NetApp-account; de AD-verbinding is alleen zichtbaar via het NetApp-account waarin deze is gemaakt.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Biedt Azure NetApp Files ondersteuning Azure Active Directory? 

Zowel [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) als Active Directory Domain Services [(AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) worden ondersteund. U kunt bestaande Active Directory-domeincontrollers gebruiken met Azure NetApp Files. Domeincontrollers kunnen zich in Azure bevinden als virtuele machines of on-premises via ExpressRoute of S2S VPN. Azure NetApp Files biedt op dit moment [](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) geen ondersteuning voor AD Azure Active Directory-join.

Als u een Azure NetApp Files met Azure Active Directory Domain Services, is het pad naar de organisatie-eenheid wanneer u `OU=AADDC Computers` Active Directory configureert voor uw NetApp-account.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welke versies van Windows Server Active Directory worden ondersteund?

Azure NetApp Files windows Server 2008r2SP1-2019-versies van Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Waarom geeft de beschikbare ruimte op mijn SMB-client niet de inrichtende grootte weer?

De volumegrootte die door de SMB-client wordt gerapporteerd, is de maximale grootte Azure NetApp Files het volume kan groeien. De grootte van het Azure NetApp Files volume, zoals weergegeven op de SMB-client, is niet afhankelijk van het quotum of de grootte van het volume. U kunt de Azure NetApp Files volumegrootte of het quotum via de Azure Portal of de API.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Ik heb problemen met het maken van verbinding met mijn SMB-share. Wat moet ik doen?

Stel als best practice maximumtolerantie voor synchronisatie van computerklok in op vijf minuten. Zie voor meer informatie [Maximale tolerantie voor synchronisatie van de computerklok.](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)) 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Kan ik `SMB Shares` , en beheren via `Sessions` `Open Files` computerbeheerconsole (MMC)?

Beheer van `SMB Shares` , en via Computer Management Console `Sessions` `Open Files` (MMC) wordt momenteel niet ondersteund.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Hoe kan ik het IP-adres van een SMB-volume verkrijgen via de portal?

Gebruik de **koppeling JSON-weergave** in het volumeoverzichtsvenster en zoek naar de **startIp-id** onder   ->  **de eigenschappen mountTargets**.

### <a name="smb-encryption-faqs"></a>Veelgestelde vragen over SMB-versleuteling

In deze sectie vindt u antwoorden op veelgestelde vragen over SMB-versleuteling (SMB 3.0 en SMB 3.1.1).

#### <a name="what-is-smb-encryption"></a>Wat is SMB-versleuteling?  

[SMB-versleuteling](/windows-server/storage/file-server/smb-security) biedt end-to-end versleuteling van SMB-gegevens en beschermt gegevens tegen meeluisterende gebeurtenissen in niet-vertrouwde netwerken. SMB-versleuteling wordt ondersteund op SMB 3.0 en hoger. 

#### <a name="how-does-smb-encryption-work"></a>Hoe werkt SMB-versleuteling?

Bij het verzenden van een aanvraag naar de opslag versleutelt de client de aanvraag, die vervolgens door de opslag wordt ontsleuteld. Antwoorden worden op vergelijkbare wijze versleuteld door de server en ontsleuteld door de client.

#### <a name="which-clients-support-smb-encryption"></a>Welke clients ondersteunen SMB-versleuteling?

Windows 10, Windows 2012 en latere versies ondersteunen SMB-versleuteling.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>Op Azure NetApp Files welke laag is SMB-versleuteling ingeschakeld?  

SMB-versleuteling is ingeschakeld op shareniveau.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Welke vormen van SMB-versleuteling worden gebruikt door Azure NetApp Files?

SMB 3.0 maakt gebruik van het AES-CCM-algoritme, terwijl SMB 3.1.1 het AES-GCM-algoritme gebruikt

#### <a name="is-smb-encryption-required"></a>Is SMB-versleuteling vereist?

SMB-versleuteling is niet vereist. Als zodanig is deze alleen ingeschakeld voor een bepaalde share als de gebruiker vraagt om Azure NetApp Files in te stellen. Azure NetApp Files shares zijn nooit blootgesteld aan internet. Ze zijn alleen toegankelijk vanuit een bepaald VNet, via VPN of expressroute, zodat Azure NetApp Files shares inherent veilig zijn. De keuze voor het inschakelen van SMB-versleuteling is geheel aan de gebruiker. Let op de verwachte prestatiestraf voordat u deze functie inschakelen.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Wat is de verwachte impact van SMB-versleuteling op clientworkloads?

Hoewel SMB-versleuteling van invloed is op zowel de client (CPU-overhead voor het versleutelen en ontsleutelen van berichten) als de opslag (afname van de doorvoer), markeert de volgende tabel alleen de impact op de opslag. U moet de impact van de versleutelingsprestaties op uw eigen toepassingen testen voordat u workloads in productie implementeert.

|     I/O-profiel       |     Impact        |
|-  |-  |
|     Workloads lezen en schrijven      |     10% tot 15%        |
|     Metagegevensintensief        |     5%    |

## <a name="capacity-management-faqs"></a>Veelgestelde vragen over capaciteitsbeheer

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Hoe kan ik het gebruik van capaciteitspools en volume van Azure NetApp Files? 

Azure NetApp Files biedt metrische gegevens over capaciteitspools en volumegebruik. U kunt ook Azure Monitor gebruiken om het gebruik voor uw Azure NetApp Files. Zie [Metrische gegevens Azure NetApp Files](azure-netapp-files-metrics.md) voor meer informatie. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kan ik de Azure NetApp Files beheren via Azure Storage Explorer?

Nee. Azure NetApp Files wordt niet ondersteund door Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Hoe kan ik bepalen of een map de limietgrootte nadert?

U kunt de opdracht van een client gebruiken om te zien of een map de maximale groottelimiet voor mapmetagegevens `stat` (320 MB) nadert.   

Voor een map van 320 MB is het aantal blokken 655360, met elke blokgrootte 512 bytes.  (Dat wil zeggen 320x1024x1024/512.)  Dit aantal wordt omgezet in ongeveer 4 miljoen bestanden voor een map van 320 MB. Het werkelijke aantal bestanden kan echter lager zijn, afhankelijk van factoren zoals het aantal bestanden met niet-ASCII-tekens in de map. Als zodanig moet u de opdracht als volgt gebruiken om te bepalen of `stat` uw directory de limiet nadert.  

Voorbeelden:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Veelgestelde vragen over gegevensmigratie en -beveiliging

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Hoe kan ik gegevens migreren naar Azure NetApp Files?
Azure NetApp Files biedt NFS- en SMB-volumes.  U kunt elk hulpprogramma voor het kopiëren op basis van bestanden gebruiken om gegevens naar de service te migreren. 

NetApp biedt een SaaS-oplossing, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Met de oplossing kunt u NFS- of SMB-gegevens repliceren naar Azure NetApp Files NFS-exports of SMB-shares. 

U kunt ook een breed scala aan gratis hulpprogramma's gebruiken om gegevens te kopiëren. Voor NFS kunt u hulpprogramma's voor werkbelastingen zoals [rsync](https://rsync.samba.org/examples.html) gebruiken om brongegevens naar een Azure NetApp Files synchroniseren. Voor SMB kunt u workloads [Robocopy](/windows-server/administration/windows-commands/robocopy) op dezelfde manier gebruiken.  Met deze hulpprogramma's kunnen ook machtigingen voor bestanden of mappen worden gerepliceerd. 

De vereisten voor gegevensmigratie van on-premises naar Azure NetApp Files zijn als volgt: 

- Zorg Azure NetApp Files beschikbaar is in de Azure-doelregio.
- Valideer de netwerkverbinding tussen de bron en Azure NetApp Files ip-adres van het doelvolume. Gegevensoverdracht tussen on-premises en Azure NetApp Files service wordt ondersteund via ExpressRoute.
- Maak het doel Azure NetApp Files volume.
- Breng de brongegevens over naar het doelvolume met behulp van het hulpprogramma voor het kopiëren van bestanden van uw voorkeur.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Hoe kan ik een kopie maken van een Azure NetApp Files volume in een andere Azure-regio?
    
Azure NetApp Files biedt NFS- en SMB-volumes.  Elk hulpprogramma voor het kopiëren op basis van bestanden kan worden gebruikt om gegevens tussen Azure-regio's te repliceren. 

NetApp biedt een SaaS-oplossing, [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Met de oplossing kunt u NFS- of SMB-gegevens repliceren naar Azure NetApp Files NFS-exports of SMB-shares. 

U kunt ook een breed scala aan gratis hulpprogramma's gebruiken om gegevens te kopiëren. Voor NFS kunt u hulpprogramma's voor werkbelastingen zoals [rsync](https://rsync.samba.org/examples.html) gebruiken om brongegevens naar een Azure NetApp Files synchroniseren. Voor SMB kunt u workloads [Robocopy](/windows-server/administration/windows-commands/robocopy) op dezelfde manier gebruiken.  Met deze hulpprogramma's kunnen ook machtigingen voor bestanden of mappen worden gerepliceerd. 

De vereisten voor het repliceren Azure NetApp Files volume naar een andere Azure-regio zijn als volgt: 
- Zorg Azure NetApp Files beschikbaar is in de Azure-doelregio.
- Valideer de netwerkverbinding tussen VNets in elke regio. Op dit moment wordt wereldwijde peering tussen VNets niet ondersteund.  U kunt connectiviteit tussen VNets tot stand brengen door een koppeling te maken met een ExpressRoute-circuit of door een S2S VPN-verbinding te gebruiken. 
- Maak het doel Azure NetApp Files volume.
- Breng de brongegevens over naar het doelvolume met behulp van het hulpprogramma voor het kopiëren van bestanden van uw voorkeur.

### <a name="is-migration-with-azure-data-box-supported"></a>Wordt migratie met Azure Data Box ondersteund?

Nee. Azure Data Box biedt momenteel geen Azure NetApp Files ondersteuning. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Wordt migratie met de Azure Import/Export-service ondersteund?

Nee. De Azure Import/Export-service biedt momenteel Azure NetApp Files ondersteuning.

## <a name="product-faqs"></a>Veelgestelde vragen over producten

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Kan ik Azure NetApp Files NFS- of SMB-volumes gebruiken met Azure VMware Solution (AVS)?

U kunt de Azure NetApp Files NFS-volumes op AVS Windows-VM's of Linux-VM's. U kunt de Azure NetApp Files SMB-shares op AVS Windows-VM's. Zie voor meer informatie [Azure NetApp Files met Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Welke regio's worden ondersteund voor Azure NetApp Files NFS- of SMB-volumes met Azure VMware Solution (AVS)?

Het Azure NetApp Files NFS- of SMB-volumes met AVS wordt ondersteund in de volgende regio's: VS - oost, VS - west, Europa - west en Australië - oost.

## <a name="next-steps"></a>Volgende stappen  

- [Microsoft Azure Veelgestelde vragen over ExpressRoute](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network veelgestelde vragen](../virtual-network/virtual-networks-faq.md)
- [Een ondersteuningsaanvraag maken voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Veelgestelde vragen over SMB-prestaties voor Azure NetApp Files](azure-netapp-files-smb-performance.md)
