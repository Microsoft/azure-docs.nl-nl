---
title: Veelgestelde vragen (FAQ) voor Azure Files | Microsoft Docs
description: Krijg antwoorden op Azure Files veelgestelde vragen. U kunt Azure-bestands shares gelijktijdig aan cloud- of on-premises Windows-, Linux- of macOS-implementaties toevoegen.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785303"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Lees de veelgestelde vragen (FAQ) over Azure Files
[Azure Files](storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het [industriestandaard Server Message Block-protocol (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) en het [NFS-protocol (Network File System)](https://en.wikipedia.org/wiki/Network_File_System) (preview). U kunt Azure-bestands shares gelijktijdig aan de cloud of on-premises implementaties van Windows, Linux en macOS toevoegen. U kunt Azure-bestands shares ook in de cache opslaan op Windows Server-computers met behulp van Azure File Sync voor snelle toegang dicht bij de plek waar de gegevens worden gebruikt.

In dit artikel vindt u antwoorden op veelvoorkomende vragen Azure Files functies en functionaliteit, waaronder het gebruik van Azure File Sync met Azure Files. Als u het antwoord op uw vraag niet ziet, kunt u contact met ons opnemen via de volgende kanalen (in escalerende volgorde):

1. De sectie opmerkingen van dit artikel.
2. [Microsoft Q&A question page for Azure Storage](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice.](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft-ondersteuning. Als u een nieuwe ondersteuningsaanvraag wilt maken, selecteert u in Azure Portal tabblad **Help** de knop **Help en** ondersteuning en selecteert u vervolgens **Nieuwe ondersteuningsaanvraag**.

## <a name="general"></a>Algemeen
* <a id="why-files-useful"></a>
  **Hoe is Azure Files nuttig?**  
   U kunt een Azure Files om bestands shares te maken in de cloud, zonder dat u verantwoordelijk bent voor het beheren van de overhead van een fysieke server, apparaat of apparaat. We doen het monotone werk voor u, waaronder het toepassen van updates van het besturingssysteem en het vervangen van slechte schijven. Zie Waarom Azure Files nuttig is Azure Files voor meer informatie over de [scenario's](storage-files-introduction.md#why-azure-files-is-useful)Azure Files u kunt helpen.

* <a id="file-access-options"></a>
  **Wat zijn verschillende manieren om toegang te krijgen tot bestanden in Azure Files?**  
    SMB-bestands shares kunnen op uw lokale computer worden bevestigd met behulp van het SMB 3.0-protocol, of u kunt hulpprogramma's zoals [Storage Explorer](https://storageexplorer.com/) gebruiken om toegang te krijgen tot bestanden in uw bestands share. NFS-bestands shares kunnen worden bevestigd op uw lokale computer door het script dat is opgegeven door de Azure Portal. Vanuit uw toepassing kunt u opslagclientbibliotheken, REST API's, PowerShell of Azure CLI gebruiken om toegang te krijgen tot uw bestanden in de Azure-bestands share.

* <a id="what-is-afs"></a>
  **Wat is Azure File Sync?**  
    U kunt Azure File Sync gebruiken om de bestands shares van uw organisatie te centraliseren in Azure Files, met behoud van de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Azure File Sync transformeert uw Windows Server-machines naar een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, Network File System (NFS) en File Transfer Protocol Service (FTPS). U kunt zoveel caches hebben als u nodig hebt over de hele wereld.

* <a id="files-versus-blobs"></a>
  **Waarom zou ik een Azure-bestands share gebruiken in plaats van Azure Blob Storage voor mijn gegevens?**  
    Azure Files en Azure Blob Storage bieden beide manieren om grote hoeveelheden gegevens op te slaan in de cloud, maar ze zijn nuttig voor iets andere doeleinden. 
    
    Azure Blob Storage is handig voor grootschalige cloudtoepassingen die ongestructureerde gegevens moeten opslaan. Azure Blob Storage is een eenvoudigere opslagabstring dan een echt bestandssysteem om de prestaties en schaal te maximaliseren. U hebt alleen toegang tot Azure Blob-opslag via op REST gebaseerde clientbibliotheken (of rechtstreeks via het OP REST gebaseerde protocol).

    Azure Files is specifiek een bestandssysteem. Azure Files bevat alle bestandsabstringen die u kent en die u graag kent van jaren werken met on-premises besturingssystemen. Net als Azure Blob Storage biedt Azure Files een REST-interface en op REST gebaseerde clientbibliotheken. In tegenstelling tot Azure Blob Storage biedt Azure Files SMB- of NFS-toegang tot Azure-bestands shares. Bestands shares kunnen rechtstreeks in Windows, Linux of macOS worden gekoppeld, on-premises of in cloud-VM's, zonder code te schrijven of speciale stuurprogramma's aan het bestandssysteem te koppelen. U kunt Azure SMB-bestands shares ook in de cache opslaan op on-premises bestandsservers met behulp van Azure File Sync voor snelle toegang, dicht bij de locatie waar de gegevens worden gebruikt. 
   
    Zie Introduction to the core Azure Storage services (Inleiding tot de belangrijkste [Azure Storage-services)](../common/storage-introduction.md)voor een gedetailleerde beschrijving van de verschillen tussen Azure Files en Azure Blob Storage. Zie Inleiding tot Blob Storage voor meer informatie [over Azure Blob Storage.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Waarom zou ik een Azure-bestands share gebruiken in plaats van Azure Disks?**  
    Een schijf in Azure Disks is gewoon een schijf. Als u waarde wilt op halen uit Azure-schijven, moet u een schijf koppelen aan een virtuele machine die wordt uitgevoerd in Azure. Azure Disks kan worden gebruikt voor alles waar u een schijf voor op een on-premises server zou gebruiken. U kunt deze gebruiken als een besturingssysteemschijf, als wisselruimte voor een besturingssysteem of als toegewezen opslag voor een toepassing. Een interessant gebruik voor Azure Disks is het maken van een bestandsserver in de cloud voor gebruik op dezelfde plaatsen waar u een Azure-bestands share kunt gebruiken. Het implementeren van een bestandsserver in Azure Virtual Machines is een krachtige manier om bestandsopslag in Azure op te halen wanneer u implementatieopties nodig hebt die momenteel niet worden ondersteund door Azure Files. 

    Het uitvoeren van een bestandsserver met Azure Disks als back-endopslag is om een aantal redenen echter veel duurder dan het gebruik van een Azure-bestands share. Naast het betalen voor schijfopslag moet u ook betalen voor de kosten van het uitvoeren van een of meer Virtuele Azure-VM's. Ten tweede moet u ook de VM's beheren die worden gebruikt om de bestandsserver uit te voeren. U bent bijvoorbeeld verantwoordelijk voor upgrades van het besturingssysteem. Als u uiteindelijk wilt dat gegevens on-premises in de cache worden opgeslagen, is het aan u om replicatietechnologieën, zoals Distributed File System Replication (DFSR), in te stellen en te beheren om dat te realiseren.

    U kunt het beste van zowel Azure Files als een bestandsserver die wordt gehost in Azure Virtual Machines (naast het gebruik van Azure Disks als back-endopslag) het installeren van Azure File Sync op een bestandsserver die wordt gehost op een cloud-VM. Als de Azure-bestands delen zich in dezelfde regio als uw bestandsserver, kunt u opslag in cloudlagen inschakelen en het volume van de vrije ruimte instellen op maximaal (99%). Dit zorgt voor minimale duplicatie van gegevens. U kunt ook alle toepassingen gebruiken die u wilt gebruiken met uw bestandsservers, zoals toepassingen waarvoor NFS-protocolondersteuning is vereist.

    Zie [Deploying IaaS VM guest clusters in Microsoft Azure (IaaS VM-gastclusters](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)implementeren in azure) voor informatie over een optie voor het instellen van een bestandsserver met hoge prestaties en hoge Microsoft Azure. Zie Introduction to the core Azure Storage services (Inleiding tot de belangrijkste Azure Storage services) voor een gedetailleerde beschrijving van de verschillen tussen Azure Files en Azure [Disks.](../common/storage-introduction.md) Zie Overzicht van Azure Managed Disks voor [meer informatie over Azure Disks.](../../virtual-machines/managed-disks-overview.md)

* <a id="get-started"></a>
  **Hoe kan ik aan de slag met Azure Files?**  
   Aan de slag met Azure Files is eenvoudig. Maak eerst een [SMB-bestands share](storage-how-to-create-file-share.md) of een [NFS-share](storage-files-how-to-create-nfs-shares.md)maken en bevestig deze vervolgens in het besturingssysteem van uw voorkeur: 

  * [Een SMB-share in Windows installeren](storage-how-to-use-files-windows.md)
  * [Een SMB-share in Linux installeren](storage-how-to-use-files-linux.md)
  * [Een SMB-share in macOS installeren](storage-how-to-use-files-mac.md)
  * [Een NFS-bestands share toevoegen](storage-files-how-to-mount-nfs-shares.md)

    Zie Planning for an Azure Files deployment (Planning for an Azure Files deployment )voor een uitgebreidere handleiding over het implementeren van een [Azure-bestands](storage-files-planning.md)share ter vervanging van productiebestands shares in uw organisatie.

* <a id="redundancy-options"></a>
  **Welke opties voor opslag redundantie worden ondersteund door Azure Files?**  
    Momenteel biedt Azure Files ondersteuning voor lokaal redundante opslag (LRS), zone-redundante opslag (ZRS), geografisch redundante opslag (GRS) en geografisch zone-redundante opslag (GZRS). Azure Files Premium-laag ondersteunt momenteel alleen LRS en ZRS.

* <a id="tier-options"></a>
  **Welke opslaglagen worden ondersteund in Azure Files?**  
    Azure Files ondersteunt twee opslaglagen: Premium en Standard. Standaardbestands shares worden gemaakt in opslagaccounts voor algemeen gebruik (GPv1 of GPv2) en Premium-bestands shares worden gemaakt in FileStorage-opslagaccounts. Meer informatie over het maken van [standaardbestands shares](storage-how-to-create-file-share.md) en [Premium-bestands shares.](./storage-how-to-create-file-share.md) 
    
    > [!NOTE]
    > U kunt geen Azure-bestands  shares maken van Blob Storage-accounts of Premium-opslagaccounts voor algemeen gebruik (GPv1 of GPv2). Standaard Azure-bestands shares moeten alleen *worden* gemaakt in standaard accounts voor algemeen gebruik en Premium Azure-bestands shares moeten alleen worden gemaakt in FileStorage-opslagaccounts.  Premium-opslagaccounts voor algemeen gebruik (GPv1 en GPv2) zijn alleen voor Premium-pagina-blobs. 

* <a id="file-locking"></a>
  **Biedt Azure Files ondersteuning voor bestandsvergrendeling?**  
    Ja, Azure Files ondersteuning voor bestandsvergrendeling in SMB-/Windows-stijl, [zie details.](/rest/api/storageservices/managing-file-locks)

* <a id="give-us-feedback"></a>
  **Ik wil graag dat er een specifieke functie wordt toegevoegd aan Azure Files. Kunt u deze toevoegen?**  
    Het Azure Files is geïnteresseerd in alle feedback die u over onze service hebt. Stem op functieaanvragen via [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! We kijken ernaar uit om u te voorzien van veel nieuwe functies.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Welke regio's worden ondersteund voor Azure File Sync?**  
    De lijst met beschikbare regio's vindt u in de [sectie Regiobeschikbaarheid](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) van de Azure File Sync planningshandleiding. We voegen continu ondersteuning toe voor aanvullende regio's, waaronder niet-openbare regio's.

* <a id="cross-domain-sync"></a>
  **Kan ik servers die lid zijn van een domein en niet lid zijn van een domein, in dezelfde synchronisatiegroep hebben?**  
    Ja. Een synchronisatiegroep kan server-eindpunten bevatten die verschillende Active Directory-lidmaatschappen hebben, zelfs als ze niet lid zijn van een domein. Hoewel deze configuratie technisch werkt, raden we dit niet aan als een typische configuratie, omdat toegangsbeheerlijsten (ACL's) die zijn gedefinieerd voor bestanden en mappen op de ene server mogelijk niet kunnen worden afgedwongen door andere servers in de synchronisatiegroep. Voor de beste resultaten raden we u aan om te synchroniseren tussen servers die zich in hetzelfde Active Directory-forest, tussen servers in verschillende Active Directory-forests, maar die vertrouwensrelaties hebben ingesteld of tussen servers die zich niet in een domein. U wordt aangeraden een combinatie van deze configuraties te vermijden.

* <a id="afs-change-detection"></a>
  **Ik heb een bestand rechtstreeks in mijn Azure-bestands share gemaakt met behulp van SMB of in de portal. Hoe lang duurt het voordat het bestand is gesynchroniseerd met de servers in de synchronisatiegroep?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Hoe lang duurt het voordat Azure File Sync 1TiB aan gegevens hebt geüpload?**
  
    De prestaties variëren afhankelijk van uw omgevingsinstellingen, configuratie en of dit een initiële synchronisatie of een doorlopende synchronisatie is. Zie Metrische prestatiegegevens Azure File Sync [meer informatie](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Als hetzelfde bestand op ongeveer hetzelfde moment op twee servers wordt gewijzigd, wat gebeurt er dan?**  
    Azure File Sync maakt gebruik van een eenvoudige strategie voor conflictoplossing: we behouden beide wijzigingen in bestanden die op hetzelfde moment worden gewijzigd in twee eindpunten. De laatst geschreven wijziging behoudt de oorspronkelijke bestandsnaam. Het oudere bestand (bepaald door LastWriteTime) heeft de eindpuntnaam en het conflictnummer toegevoegd aan de bestandsnaam. Voor server-eindpunten is de naam van het eindpunt de naam van de server. Voor cloud-eindpunten is de naam van het eindpunt **Cloud**. De naam volgt deze taxonomie: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Het eerste conflict van een CompanyReport.docx wordt bijvoorbeeld CompanyReport-CentralServer.docx centralserver is waar de oudere schrijfing is opgetreden. Het tweede conflict heeft de naam CompanyReport-CentralServer-1.docx. Azure File Sync ondersteunt 100 conflictbestanden per bestand. Zodra het maximum aantal conflictbestanden is bereikt, kan het bestand pas worden gesynchroniseerd als het aantal conflictbestanden kleiner is dan 100.

* <a id="afs-storage-redundancy"></a>
  **Wordt geografisch redundante opslag ondersteund voor Azure File Sync?**  
    Ja, Azure Files ondersteunt zowel lokaal redundante opslag (LRS) als geografisch redundante opslag (GRS). Als u een failover van een opslagaccount tussen gekoppelde regio's start vanuit een account dat is geconfigureerd voor GRS, wordt u aangeraden de nieuwe regio alleen te behandelen als een back-up van gegevens. Azure File Sync wordt niet automatisch gesynchroniseerd met de nieuwe primaire regio. 

* <a id="sizeondisk-versus-size"></a>
  **Waarom komt de eigenschap *Grootte op schijf voor* een bestand niet overeen met de eigenschap *Grootte* nadat u Azure File Sync?**  
  Zie [Inzicht in Azure File Sync opslag in cloudlagen.](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)

* <a id="is-my-file-tiered"></a>
  **Hoe kan ik zien of een bestand gelaagd is?**  
  Zie [Informatie over cloudopslaglagen.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)

* <a id="afs-recall-file"></a>**Een bestand dat ik wil gebruiken, is gelaagd. Hoe kan ik het bestand terugroepen naar de schijf om het lokaal te gebruiken?**  
  Zie [Informatie over cloudopslaglagen.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

* <a id="afs-force-tiering"></a>
  **Hoe kan ik een bestand of map worden gelaagd?**  
  Zie [Informatie over cloudopslaglagen.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)

* <a id="afs-effective-vfs"></a>
  **Hoe wordt *de vrije ruimte van* het volume geïnterpreteerd wanneer ik meerdere server eindpunten op een volume heb?**  
  Zie [Informatie over cloudopslaglagen.](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Ik heb opslag in cloudlagen uitgeschakeld. Waarom zijn er gelaagde bestanden op de locatie van het server-eindpunt?**  
    Er zijn twee redenen waarom gelaagde bestanden kunnen bestaan op de locatie van het server-eindpunt:

    - Wanneer u een nieuw server-eindpunt toevoegt aan een bestaande synchronisatiegroep en u ofwel de eerste optie voor de relevante naamruimte of alleen de naamruimte voor de initiële downloadmodus kiest, worden bestanden als gelaagde bestanden weer gegeven totdat ze lokaal worden gedownload. Om dit te voorkomen, selecteert u de optie Gelaagde bestanden voorkomen voor de eerste downloadmodus. Als u bestanden handmatig wilt terugroepen, gebruikt u de cmdlet [Invoke-StorageSyncFileRecall.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

    - Als opslag in cloudlagen is ingeschakeld op het server-eindpunt en vervolgens is uitgeschakeld, blijven bestanden gelaagd totdat ze worden gebruikt.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Waarom worden in mijn gelaagde bestanden geen miniaturen of previews weergegeven in Windows Verkenner?**  
    Voor gelaagde bestanden zijn miniaturen en previews niet zichtbaar op het server-eindpunt. Dit gedrag wordt verwacht omdat de functie miniatuurcache in Windows opzettelijk het lezen van bestanden met het kenmerk offline overslaat. Als opslag in cloudlagen is ingeschakeld, zou het lezen van gelaagde bestanden ertoe leiden dat ze worden gedownload (teruggeroepen).

    Dit gedrag is niet specifiek voor Azure File Sync, Windows Verkenner geeft een 'grijze X' weer voor bestanden waar het offlinekenmerk is ingesteld. U ziet het X-pictogram bij het openen van bestanden via SMB. Raadpleeg voor een gedetailleerde uitleg van dit gedrag [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Zie Gelaagde bestanden beheren voor vragen over het beheren van [gelaagde bestanden.](../file-sync/file-sync-how-to-manage-tiered-files.md)

* <a id="afs-files-excluded"></a>
  **Welke bestanden of mappen worden automatisch uitgesloten door Azure File Sync?**  
  Zie [Bestanden overgeslagen.](../file-sync/file-sync-planning.md#files-skipped)

* <a id="afs-os-support"></a>
  **Kan ik Azure File Sync gebruiken met Windows Server 2008 R2, Linux of mijn network-attached storage (NAS)-apparaat?**  
    Momenteel ondersteunt Azure File Sync alleen Windows Server 2019, Windows Server 2016 en Windows Server 2012 R2. Op dit moment hebben we geen andere plannen die we kunnen delen, maar we staan open voor het ondersteunen van extra platformen op basis van de vraag van klanten. Laat ons via [Azure Files UserVoice weten](https://feedback.azure.com/forums/217298-storage/category/180670-files) welke platformen u graag wilt ondersteunen.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Waarom bestaan gelaagde bestanden buiten de naamruimte van het server-eindpunt?**  
    Vóór Azure File Sync agentversie 3 Azure File Sync het verplaatsen van gelaagde bestanden buiten het server-eindpunt geblokkeerd, maar op hetzelfde volume als het server-eindpunt. Kopieerbewerkingen, het verplaatst van niet-gelaagde bestanden en het opslaan van gelaagde bestanden naar andere volumes zijn niet beïnvloed. De reden voor dit gedrag is de impliciete aanname dat Verkenner en andere Windows-API's hebben dat verplaatsen van bewerkingen op hetzelfde volume (bijna) directe hernoembewerkingen zijn. Dit betekent dat de bestandenverkenner of andere methoden voor verplaatsen (zoals de opdrachtregel of PowerShell) niet meer reageren wanneer Azure File Sync gegevens uit de cloud terugroept. Vanaf [Azure File Sync agentversie 3.0.12.0](../file-sync/file-sync-release-notes.md#supported-versions)kunt u met Azure File Sync een gelaagd bestand buiten het server-eindpunt verplaatsen. We vermijden de negatieve effecten die eerder zijn genoemd door toe te staan dat het gelaagde bestand bestaat als een gelaagd bestand buiten het server-eindpunt en vervolgens het bestand op de achtergrond terugroept. Dit betekent dat wordt verplaatst op hetzelfde volume onmiddellijk en we doen al het werk om het bestand terug te roepen naar schijf nadat de verplaatsen is voltooid. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Ik heb een probleem met Azure File Sync op mijn server (synchronisatie, opslag in cloudlagen, enzovoort). Moet ik mijn server-eindpunt verwijderen en opnieuw maken?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan ik de opslagsynchronisatieservice en/of het opslagaccount verplaatsen naar een andere resourcegroep, abonnement of Azure AD-tenant?**  
   Ja, de opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere resourcegroep, abonnement of Azure AD-tenant. Nadat de opslagsynchronisatieservice of het opslagaccount is verplaatst, moet u de toepassing Microsoft.StorageSync toegang geven tot het opslagaccount (zie Ervoor zorgen dat Azure File Sync toegang heeft tot het [opslagaccount).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Bij het maken van het cloud-eindpunt moeten de opslagsynchronisatieservice en het opslagaccount zich in dezelfde Azure AD-tenant hebben. Zodra het cloud-eindpunt is gemaakt, kunnen de opslagsynchronisatieservice en het opslagaccount worden verplaatst naar verschillende Azure AD-tenants.
    
* <a id="afs-ntfs-acls"></a>
  **Blijven Azure File Sync NTFS-ACL's op map-/bestandsniveau behouden, samen met gegevens die zijn opgeslagen in Azure Files?**

    Vanaf 24 februari 2020 worden nieuwe en bestaande ACL's die zijn gelaagd door Azure File Sync, persistent gemaakt in NTFS-indeling en worden wijzigingen in de ACL rechtstreeks in de Azure-bestands share gesynchroniseerd met alle servers in de synchronisatiegroep. Alle wijzigingen in ACL's die zijn Azure Files worden gesynchroniseerd via Azure File Sync. Wanneer u gegevens kopieert naar Azure Files, moet u een kopieerprogramma gebruiken dat ondersteuning biedt voor de benodigde 'betrouwbaarheid' om kenmerken, tijdstempels en ACL's te kopiëren naar een Azure-bestands share, via SMB of REST. Wanneer u azure-kopieerhulpprogramma's gebruikt, zoals AzCopy, is het belangrijk om de nieuwste versie te gebruiken. Raadpleeg de [tabel met hulpprogramma's](storage-files-migration-overview.md#file-copy-tools) voor bestandskopieer voor een overzicht van De hulpprogramma's voor kopiëren van Azure om ervoor te zorgen dat u alle belangrijke metagegevens van een bestand kunt kopiëren.

    Als u de Azure Backup bestandssynchronisatie hebt ingeschakeld voor beheerde bestands shares, kunnen bestands-ACL's nog steeds worden hersteld als onderdeel van de werkstroom voor het herstellen van back-ups. Dit werkt voor de volledige share of voor afzonderlijke bestanden/mappen.

    Als u momentopnamen gebruikt als onderdeel van de zelf-beheerde back-upoplossing voor bestands shares die worden beheerd door bestandssynchronisatie, worden uw ACL's mogelijk niet correct hersteld naar NTFS ACL's als de momentopnamen zijn gemaakt vóór 24 februari 2020. Als dit het geval is, kunt u contact opnemen met Ondersteuning voor Azure.

* <a id="afs-lastwritetime"></a>
  **Synchroniseert Azure File Sync LastWriteTime voor -directories?**  
    Nee, Azure File Sync synchroniseert de LastWriteTime niet voor -directories. Dit is standaard.
    
## <a name="security-authentication-and-access-control"></a>Beveiliging, verificatie en toegangsbeheer
* <a id="ad-support"></a>
**Wordt verificatie op basis van identiteit en toegangsbeheer ondersteund door Azure Files?**  
    
    Ja, Azure Files ondersteuning voor verificatie op basis van identiteit en toegangsbeheer. U kunt kiezen uit twee manieren om op identiteit gebaseerd toegangsbeheer te gebruiken: on-premises Active Directory Domain Services of Azure Active Directory Domain Services (Azure AD DS). On-premises Active Directory Domain Services (AD DS) ondersteunt verificatie met behulp van AD DS computers die lid zijn van een domein, on-premises of in Azure, voor toegang tot Azure-bestands shares via SMB. Azure AD DS via SMB voor Azure Files kunnen Azure AD DS windows-VM's die lid zijn van een domein toegang krijgen tot shares, mappen en bestanden met behulp van Azure AD-referenties. Zie Overview of Azure Files identity-based authentication support for SMB access (Overzicht van verificatie op basis van identiteit voor [SMB-toegang) voor meer informatie.](storage-files-active-directory-overview.md) 

    Azure Files biedt twee extra manieren om toegangsbeheer te beheren:

    - U kunt Shared Access Signatures (SAS) gebruiken om tokens te genereren die specifieke machtigingen hebben en die geldig zijn voor een opgegeven tijdsinterval. U kunt bijvoorbeeld een token genereren met alleen-lezentoegang tot een specifiek bestand met een verloopdatum van 10 minuten. Iedereen die het token heeft terwijl het token geldig is, heeft die 10 minuten alleen-lezentoegang tot dat bestand. Handtekeningsleutels voor gedeelde toegang worden alleen ondersteund via de REST API of in clientbibliotheken. U moet de Azure-bestands delen via SMB met behulp van de opslagaccountsleutels.

    - Azure File Sync behoudt en repliceert alle discretionaire ACL's (of DACL's, ongeacht of deze zijn gebaseerd op Active Directory of lokaal) naar alle server-eindpunten die worden gesynchroniseerd. 
    
    Raadpleeg Toegang verlenen tot Azure Storage voor [een uitgebreide](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) weergave van alle protocollen die worden ondersteund op Azure Storage services. 
    
* <a id="encryption-at-rest"></a>
**Hoe kan ik ervoor zorgen dat mijn Azure-bestands share at-rest is versleuteld?**  

    Ja. Zie serviceversleuteling [Azure Storage meer informatie.](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="access-via-browser"></a>
**Hoe kan ik toegang bieden tot een specifiek bestand met behulp van een webbrowser?**  

    U kunt handtekeningen voor gedeelde toegang gebruiken om tokens te genereren die specifieke machtigingen hebben en die geldig zijn voor een opgegeven tijdsinterval. U kunt bijvoorbeeld voor een bepaalde periode een token genereren dat alleen-lezentoegang biedt tot een specifiek bestand. Iedereen die de URL heeft, heeft rechtstreeks vanuit elke webbrowser toegang tot het bestand terwijl het token geldig is. U kunt eenvoudig een shared access signature-sleutel genereren vanuit een gebruikersinterface zoals Storage Explorer.

* <a id="file-level-permissions"></a>
**Is het mogelijk om alleen-lezen- of alleen-schrijvenmachtigingen op te geven voor mappen in de share?**  

    Als u de bestands share met behulp van SMB aan elkaar kunt toevoegen, hebt u geen controle op mapniveau over machtigingen. Als u echter een shared access signature maakt met behulp van de REST API- of clientbibliotheken, kunt u alleen-lezen- of alleen-schrijvenmachtigingen opgeven voor mappen in de share.

* <a id="ip-restrictions"></a>
**Kan ik IP-beperkingen implementeren voor een Azure-bestands share?**  

    Ja. Toegang tot uw Azure-bestands share kan worden beperkt op het niveau van het opslagaccount. Zie Configure [Azure Storage Firewalls and Virtual Networks (Firewalls en virtuele netwerken configureren) voor meer informatie.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="data-compliance-policies"></a>
**Welk nalevingsbeleid voor gegevens wordt Azure Files ondersteund?**  

   Azure Files wordt uitgevoerd op dezelfde opslagarchitectuur die wordt gebruikt in andere opslagservices in Azure Storage. Azure Files hetzelfde nalevingsbeleid voor gegevens toegepast dat wordt gebruikt in andere Azure-opslagservices. Voor meer informatie over Azure Storage naleving van gegevens raadpleegt u Azure Storage [nalevingsaanbiedingen](../common/storage-compliance-offerings.md)en gaat u naar [het Vertrouwenscentrum van Microsoft.](https://microsoft.com/trustcenter/default.aspx)

* <a id="file-auditing"></a>
**Hoe kan ik de toegang tot bestanden en wijzigingen in de Azure Files?**

  Er zijn twee opties die controlefunctionaliteit bieden voor Azure Files:
  - Als gebruikers rechtstreeks toegang hebben tot de Azure-bestands share, [kunnen Azure Storage logboeken (preview)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) worden gebruikt om bestandswijzigingen en gebruikerstoegang bij te houden. Deze logboeken kunnen worden gebruikt voor het oplossen van problemen en de aanvragen worden op basis van een best-effort vastgelegd.
  - Als gebruikers toegang hebben tot de Azure-bestands share via een Windows-server waar de Azure File Sync-agent is geïnstalleerd, gebruikt u een controlebeleid of een product van derden om bestandswijzigingen en gebruikerstoegang op de Windows Server bij te houden. [](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS verificatie
* <a id="ad-support-devices"></a>
**Ondersteunt Azure Files Azure Active Directory Domain Services (Azure AD DS) SMB-toegang met behulp van Azure AD-referenties van apparaten die zijn verbonden met of zijn geregistreerd bij Azure AD?**

    Nee, dit scenario wordt niet ondersteund.

* <a id="ad-vm-subscription"></a>
**Heb ik toegang tot Azure-bestands shares met Azure AD-referenties vanaf een VM onder een ander abonnement?**

    Als het abonnement waaronder de bestands share is geïmplementeerd, is gekoppeld aan dezelfde Azure AD-tenant als de Azure AD DS-implementatie waaraan de VM lid is van een domein, kunt u vervolgens toegang krijgen tot Azure-bestands shares met dezelfde Azure AD-referenties. De beperking wordt niet opgelegd aan het abonnement, maar aan de gekoppelde Azure AD-tenant.
    
* <a id="ad-support-subscription"></a>
**Kan ik Azure AD DS- of on-premises AD DS-verificatie voor Azure-bestands shares inschakelen met behulp van een Azure AD-tenant die verschilt van de primaire tenant van de Azure-bestands share?**

    Nee, Azure Files ondersteunt alleen Azure AD DS of on-premises AD DS-integratie met een Azure AD-tenant die zich in hetzelfde abonnement bevindt als de bestands share. Er kan slechts één abonnement worden gekoppeld aan een Azure AD-tenant. Deze beperking geldt voor zowel Azure AD DS als on-premises AD DS verificatiemethoden. Wanneer u on-premises AD DS gebruikt voor verificatie, moet de AD DS-referentie worden gesynchroniseerd met de [Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) waar het opslagaccount aan is gekoppeld.

* <a id="ad-linux-vms"></a>
**Ondersteunt Azure AD DS of on-premises AD DS verificatie voor Azure-bestands shares Linux-VM's?**

    Nee, verificatie van Linux-VM's wordt niet ondersteund.

* <a id="ad-aad-smb-afs"></a>
**Bieden bestands shares die worden beheerd Azure File Sync ondersteuning voor Azure AD DS of on-premises AD DS verificatie?**

    Ja, u kunt verificatie Azure AD DS on-premises AD DS inschakelen op een bestands share die wordt beheerd door Azure File Sync. Wijzigingen in de map/bestand NTFS ACL's op lokale bestandsservers worden gelaagd naar Azure Files en vice versa.

* <a id="ad-aad-smb-files"></a>
**Hoe kan ik controleren of ik verificatie voor AD DS opslagaccount heb ingeschakeld en de domeingegevens heb opgehaald?**

    Zie hier voor [instructies.](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled)

* <a id=""></a>
**Ondersteunt Azure Files Azure AD-verificatie Linux-VM's?**

    Nee, verificatie van Linux-VM's wordt niet ondersteund.

* <a id="ad-multiple-forest"></a>
**Ondersteunt on-premises AD DS verificatie voor Azure-bestands shares integratie met een AD DS omgeving met behulp van meerdere forests?**    

    Azure Files on-premises AD DS kan alleen worden geïntegreerd met het forest van de domeinservice waar het opslagaccount is geregistreerd. Ter ondersteuning van verificatie vanuit een ander forest moet in uw omgeving een forestvertrouwensrelatie correct zijn geconfigureerd. De manier Azure Files registreren in AD DS vrijwel hetzelfde als een gewone bestandsserver, waar een identiteit (computer- of servicelogoaccount) wordt gemaakt in AD DS voor verificatie. Het enige verschil is dat de geregistreerde SPN van het opslagaccount eindigt op 'file.core.windows.net', wat niet met het domeinachtervoegsel komt. Neem contact op met uw domeinbeheerder om te zien of er een update van uw achtervoegselrouteringsbeleid is vereist voor het inschakelen van meervoudige forestverificatie vanwege het andere domeinachtervoegsel. Hieronder ziet u een voorbeeld voor het configureren van het routeringsbeleid voor achtervoegsels.
    
    Voorbeeld: wanneer gebruikers in forest A-domein een bestands share willen bereiken met het opslagaccount dat is geregistreerd voor een domein in forest B, werkt dit niet automatisch omdat de service-principal van het opslagaccount geen achtervoegsel heeft dat overeenkomt met het achtervoegsel van een domein in forest A. We kunnen dit probleem oplossen door handmatig een regel voor doorsturen van het achtervoegsel van forest A naar forest B te configureren voor een aangepast achtervoegsel 'file.core.windows.net'.
    Eerst moet u een nieuw aangepast achtervoegsel toevoegen aan forest B. Zorg ervoor dat u de juiste beheerdersmachtigingen hebt om de configuratie te wijzigen en volg deze stappen:   
    1. Aanmelding bij een computerdomein dat is verbonden met forest B
    2.  Open de console Active Directory-domeinen en -vertrouwensrelatie
    3.  Klik met de rechtermuisknop op Active Directory-domeinen en -vertrouwensrelatie
    4.  Klik op Eigenschappen
    5.  Klik op Toevoegen
    6.  Voeg 'file.core.windows.net' toe als upn-achtervoegsels
    7.  Klik op Toepassen en vervolgens op OK om de wizard te sluiten
    
    Voeg vervolgens de routeringsregel voor het achtervoegsel toe aan forest A, zodat deze wordt omgeleid naar forest B.
    1.  Aanmelding bij een computerdomein dat is verbonden met forest A
    2.  Open de console Active Directory-domeinen en -vertrouwensrelatie
    3.  Klik met de rechtermuisknop op het domein dat u toegang wilt geven tot de bestands share, klik vervolgens op het tabblad Vertrouwensrelatie en selecteer forest B-domein uit uitgaande vertrouwensrelaties. Als u geen vertrouwensrelatie tussen de twee forests hebt geconfigureerd, moet u eerst de vertrouwensrelatie instellen
    4.  Klik op Eigenschappen... vervolgens 'Routering van naamachtervoegsel'
    5.  Controleer of het achtervoegsel '*.file.core.windows.net' wordt weer te zien. Als dat niet het is, klikt u op Vernieuwen
    6.  Selecteer *.file.core.windows.net en klik vervolgens op Inschakelen en Toepassen

* <a id=""></a>
**Welke regio's zijn beschikbaar voor Azure Files AD DS verificatie?**

    Raadpleeg regionale [AD DS voor](storage-files-identity-auth-active-directory-enable.md#regional-availability) meer informatie.
    
* <a id="ad-aad-smb-afs"></a>
**Kan ik gebruikmaken van Azure Files Active Directory-verificatie (AD) op bestands shares die worden beheerd door Azure File Sync?**

    Ja, u kunt AD-verificatie inschakelen op een bestands share die wordt beheerd door Azure File Sync. Wijzigingen in de map/bestand NTFS ACL's op lokale bestandsservers worden gelaagd naar Azure Files en vice versa.

* <a id="ad-aad-smb-files"></a>
**Is er een verschil in het maken van een computeraccount of servicelogoaccount om mijn opslagaccount in AD weer te geven?**

    Het maken van [een computeraccount](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standaard) of een [aanmeldingsaccount](/windows/win32/ad/about-service-logon-accounts) voor de service heeft geen verschil in de manier waarop de verificatie zou werken met Azure Files. U kunt zelf kiezen hoe u een opslagaccount vertegenwoordigt als een identiteit in uw AD-omgeving. De standaardinstelling DomainAccountType in Join-AzStorageAccountForAuth cmdlet is computeraccount. De vervaldatum van het wachtwoord die is geconfigureerd in uw AD-omgeving kan echter anders zijn voor een computer- of servicelogoaccount. U moet hiermee rekening houden bij het bijwerken van het wachtwoord van uw opslagaccount-id [in AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Zijn er REST API's ter ondersteuning van Get/Set/Copy directory/file Windows ACL's?**

    Ja, we ondersteunen REST API's die NTFS-ACL's voor mappen of bestanden op halen, instellen of kopiëren wanneer de ACL's [van 2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (of hoger) REST API. We ondersteunen ook persistente Windows ACL's in op REST gebaseerde hulpprogramma's: [AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Hoe verwijder ik referenties in de cache met een opslagaccountsleutel en verwijder ik bestaande SMB-verbindingen voordat u een nieuwe verbinding initialiseert met Azure AD- of AD-referenties?**

    U kunt het onderstaande proces in twee stappen volgen om de opgeslagen referentie te verwijderen die is gekoppeld aan de sleutel van het opslagaccount en de SMB-verbinding te verwijderen: 
    1. Voer de onderstaande cmdlet uit in Windows Cmd.exe de referentie te verwijderen. Als u er geen kunt vinden, betekent dit dat u de referentie niet hebt opgeslagen en deze stap kunt overslaan.
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. Verwijder de bestaande verbinding met de bestands share. U kunt het pad voor het bevestigingspad opgeven als de bevestigingsletter of het storage-account-name.file.core.windows.net pad.
    
       net use <drive-letter/share-path> /delete

## <a name="network-file-system"></a>Netwerkbestandssysteem

* <a id="when-to-use-nfs"></a>
**Wanneer moet ik Azure Files NFS gebruiken?**

    Zie [NFS-shares (preview)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Hoe kan ik back-upgegevens opgeslagen in NFS-shares?**

    Het maken van een back-up van uw gegevens op NFS-shares kan worden orkestreerd met vertrouwde hulpprogramma's zoals rsync of producten van een van onze externe back-uppartners. Meerdere back-uppartners, waaronder [Commvault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Moetenam](https://www.veeam.com/blog/?p=123438)en [Veritas,](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) maakten deel uit van onze eerste preview en hebben hun oplossingen uitgebreid voor zowel SMB 3.0 als NFS 4.1 voor Azure Files.

* <a id="migrate-nfs-data"></a>
**Kan ik bestaande gegevens migreren naar een NFS-share?**

    Binnen een regio kunt u standaardhulpprogramma's zoals scp, rsync of SSHFS gebruiken om gegevens te verplaatsen. Omdat Azure Files NFS gelijktijdig kan worden gebruikt vanuit meerdere rekenexemplaars, kunt u de kopieersnelheden verbeteren met parallelle uploads. Als u gegevens van buiten een regio wilt halen, gebruikt u een VPN of een Expressroute om vanuit uw on-premises datacenter aan uw bestandssysteem te worden toegevoegd.

## <a name="on-premises-access"></a>Lokale toegang

* <a id="port-445-blocked"></a>
**Mijn ISP of IT blokkeert poort 445 die niet kan worden Azure Files aan. Wat moet ik doen?**

    U kunt hier meer [informatie vinden over verschillende manieren om geblokkeerde poort 445 op te vragen.](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) Azure Files alleen verbindingen met SMB 3.0 (met ondersteuning voor versleuteling) van buiten de regio of het datacenter toe. Het SMB 3.0-protocol heeft veel beveiligingsfuncties geïntroduceerd, waaronder kanaalversleuteling, wat zeer veilig is voor gebruik via internet. Het is echter mogelijk dat poort 445 is geblokkeerd vanwege historische redenen van beveiligingsproblemen in lagere SMB-versies. In het ideale geval moet de poort alleen worden geblokkeerd voor SMB 1.0-verkeer en moet SMB 1.0 worden uitgeschakeld op alle clients.

* <a id="expressroute-not-required"></a>
**Moet ik de Azure ExpressRoute gebruiken om verbinding te maken met Azure Files of om Azure File Sync on-premises te gebruiken?**  

    Nee. ExpressRoute is niet vereist voor toegang tot een Azure-bestands share. Als u een Azure-bestands share rechtstreeks on-premises wilt toevoegen, hoeft u alleen maar poort 445 (TCP uitgaand) open te hebben voor internettoegang (dit is de poort die SMB gebruikt om te communiceren). Als u een Azure File Sync, is alleen poort 443 (TCP uitgaand) voor HTTPS-toegang vereist (geen SMB vereist). U kunt *ExpressRoute* echter gebruiken met een van deze toegangsopties.

* <a id="mount-locally"></a>
**Hoe kan ik een Azure-bestands share aan mijn lokale computer toevoegen?**  

    U kunt de bestands delen met behulp van het SMB-protocol als poort 445 (TCP uitgaand) is geopend en uw client het SMB 3.0-protocol ondersteunt (bijvoorbeeld als u Windows 10 of Windows Server 2016 gebruikt). Als poort 445 wordt geblokkeerd door het beleid van uw organisatie of door uw internetprovider, kunt u Azure File Sync toegang krijgen tot uw Azure-bestands share.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hoe kan ik back-up maken van mijn Azure-bestands share?**  
    U kunt periodieke [sharemomentopnamen gebruiken](storage-snapshots-files.md) voor beveiliging tegen onbedoelde verwijderingen. U kunt ook AzCopy, Robocopy of een back-uphulpprogramma van derden gebruiken waarmee u een back-up kunt maken van een gemonteerde bestands share. Azure Backup biedt back-ups van Azure Files. Meer informatie over [het maken van een back-up van Azure-bestands shares Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Momentopnamen van shares

### <a name="share-snapshots-general"></a>Momentopnamen delen: Algemeen
* <a id="what-are-snaphots"></a>
**Wat zijn momentopnamen van bestands delen?**  
    U kunt momentopnamen van Azure-bestands share gebruiken om een alleen-lezen versie van uw bestands shares te maken. U kunt ook Azure Files een eerdere versie van uw inhoud terug te kopiëren naar dezelfde share, naar een alternatieve locatie in Azure of on-premises voor meer wijzigingen. Zie Overzicht van momentopnamen delen voor meer informatie over het delen [van momentopnamen.](storage-snapshots-files.md)

* <a id="where-are-snapshots-stored"></a>
**Waar worden momentopnamen van mijn share opgeslagen?**  
    Momentopnamen van delen worden opgeslagen in hetzelfde opslagaccount als de bestands share.

* <a id="snapshot-consistency"></a>
**Zijn share-momentopnamen toepassings consistent?**  
    Nee, share-momentopnamen zijn niet toepassings-consistent. De gebruiker moet de schrijfgegevens van de toepassing naar de share leeg maken voordat de momentopname van de share wordt genomen.

* <a id="snapshot-limits"></a>
**Zijn er limieten voor het aantal momentopnamen van share dat ik kan gebruiken?**  
    Ja. Azure Files kunnen maximaal 200 momentopnamen van delen behouden. Momentopnamen van delen tellen niet mee voor het sharequotum, dus er is geen limiet per share voor de totale ruimte die wordt gebruikt door alle momentopnamen van de share. De limieten voor opslagaccounts zijn nog steeds van toepassing. Na 200 momentopnamen van shares moet u oudere momentopnamen verwijderen om nieuwe momentopnamen van de share te maken.

* <a id="snapshot-cost"></a>
**Wat zijn de kosten voor momentopnamen van een share?**  
    Standaardtransactie- en standaardopslagkosten zijn van toepassing op momentopnamen. Momentopnamen zijn incrementeel van aard. De basismomentopname is de share zelf. Alle volgende momentopnamen zijn incrementeel en slaan alleen het verschil van de vorige momentopname op. Dit betekent dat de deltawijzigingen die in de factuur worden gezien, minimaal zijn als uw werkbelastingsverloop minimaal is. Zie [de pagina met](https://azure.microsoft.com/pricing/details/storage/files/) prijzen voor informatie Azure Files Standard-prijzen. Vandaag de dag kunt u kijken naar de grootte die door de momentopname van een share wordt verbruikt door de gefactureerde capaciteit te vergelijken met de gebruikte capaciteit. We werken aan hulpprogramma's om de rapportage te verbeteren.

* <a id="ntfs-acls-snaphsots"></a>
**Blijven NTFS-ACL's voor mappen en bestanden opgeslagen in momentopnamen van share?**  
    NTFS ACL's voor mappen en bestanden worden persistent in momentopnamen van de share.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken
* <a id="file-snaphsots"></a>
**Kan ik een momentopname van afzonderlijke bestanden maken?**  
    Momentopnamen van de share worden gemaakt op bestands shareniveau. U kunt afzonderlijke bestanden herstellen vanuit de momentopname van de bestands share, maar u kunt geen momentopnamen van share op bestandsniveau maken. Als u echter **een** momentopname van een share op shareniveau hebt gemaakt en u momentopnamen van shares wilt bekijken waarin een specifiek bestand is gewijzigd, kunt u dit doen onder Vorige versies op een share die aan Windows is bevestigd. 
    
    Als u een functie voor het maken van een momentopname van een bestand nodig hebt, laat ons dit dan weten [Azure Files UserVoice.](https://feedback.azure.com/forums/217298-storage/category/180670-files)

* <a id="encrypted-snapshots"></a>
**Kan ik momentopnamen van een versleutelde bestands share maken?**  
    U kunt een momentopname van een share maken van Azure-bestands shares waarop versleuteling-at-rest is ingeschakeld. U kunt bestanden herstellen van een momentopname van een share naar een versleutelde bestands share. Als uw share is versleuteld, wordt de momentopname van uw share ook versleuteld.

* <a id="geo-redundant-snaphsots"></a>
**Zijn mijn share-momentopnamen geografisch redundant?**  
    Momentopnamen van delen hebben dezelfde redundantie als de Azure-bestands share waarvoor ze zijn gemaakt. Als u geografisch redundante opslag voor uw account hebt geselecteerd, wordt uw momentopname van de share ook redundant opgeslagen in de gekoppelde regio.

### <a name="manage-share-snapshots"></a>Momentopnamen van share beheren
* <a id="browse-snapshots-linux"></a>
**Kan ik door momentopnamen van mijn share bladeren vanuit Linux?**  
    U kunt Azure CLI gebruiken om momentopnamen van share in Linux te maken, weer te brengen, weer te zoeken en te herstellen.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan ik de momentopnamen van de share kopiëren naar een ander opslagaccount?**  
    U kunt bestanden kopiëren van momentopnamen van een share naar een andere locatie, maar u kunt de momentopnamen van de share zelf niet kopiëren.

### <a name="restore-data-from-share-snapshots"></a>Gegevens herstellen vanuit momentopnamen van share
* <a id="promote-share-snapshot"></a>
**Kan ik een momentopname van een share promoveren naar de basis share?**  
    U kunt gegevens kopiëren van een momentopname van een share naar een andere bestemming. U kunt een momentopname van een share niet promoveren naar de basis share.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan ik gegevens herstellen vanuit de momentopname van mijn share naar een ander opslagaccount?**  
    Ja. Bestanden van een momentopname van een share kunnen worden gekopieerd naar de oorspronkelijke locatie of naar een alternatieve locatie met hetzelfde opslagaccount of een ander opslagaccount, in dezelfde regio of in verschillende regio's. U kunt ook bestanden kopiëren naar een on-premises locatie of naar een andere cloud.    
  
### <a name="clean-up-share-snapshots"></a>Momentopnamen van share ops schonen
* <a id="delete-share-keep-snapshots"></a>
**Kan ik mijn share verwijderen, maar geen momentopnamen van mijn share?**  
    Als u actieve share-momentopnamen op uw share hebt, kunt u uw share niet verwijderen. U kunt een API gebruiken om momentopnamen van delen te verwijderen, samen met de share. U kunt ook zowel de momentopnamen van de share als de share in de Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Wat gebeurt er met mijn momentopnamen van share als ik mijn opslagaccount verwijder?**  
    Als u uw opslagaccount verwijdert, worden de momentopnamen van de share ook verwijderd.

## <a name="billing-and-pricing"></a>Facturering en prijzen
* <a id="vm-file-share-network-traffic"></a>
**Telt het netwerkverkeer tussen een Azure-VM en een Azure-bestands share mee als externe bandbreedte die in rekening wordt gebracht voor het abonnement?**  
    Als de bestands share en de VM zich in dezelfde Azure-regio, zijn er geen extra kosten voor het verkeer tussen de bestands share en de VM. Als de bestands share en de VM zich in verschillende regio's hebben, wordt het verkeer ertussen in rekening gebracht als externe bandbreedte.

* <a id="share-snapshot-price"></a>
**Wat zijn de kosten voor momentopnamen van een share?**  
     Tijdens de preview worden er geen kosten in rekening brengen voor de capaciteit van de share-momentopname. Standaardkosten voor opslaguitding en transactie zijn van toepassing. Na de algemene beschikbaarheid worden er kosten in rekening gebracht voor capaciteit en transacties op momentopnamen van share.
     
     Momentopnamen van delen zijn incrementeel van aard. De momentopname van de basis share is de share zelf. Alle volgende share-momentopnamen zijn incrementeel en slaan alleen het verschil op van de voorgaande share-momentopname. U wordt alleen gefactureerd voor de gewijzigde inhoud. Als u een share hebt met 100 GiB aan gegevens, maar slechts 5 GiB is gewijzigd sinds uw laatste momentopname van de share, verbruikt de momentopname van de share slechts 5 extra GiB en wordt u gefactureerd voor 105 GiB. Zie de pagina Prijzen voor meer informatie over transactie- en standaardkosten voor [egress.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="scale-and-performance"></a>Schaal en prestaties
* <a id="files-scale-limits"></a>
**Wat zijn de schaallimieten van Azure Files?**  
    Zie schaalbaarheids- en prestatiedoelen voor Azure Files informatie over schaalbaarheids- en [prestatiedoelen voor Azure Files.](storage-files-scale-targets.md)

* <a id="need-larger-share"></a>
**Welke grootten zijn beschikbaar voor Azure-bestands shares?**  
    Azure-bestands sharegrootten (Premium en Standard) kunnen tot 100 TiB worden geschaald. Zie de [sectie Onboarding naar grotere bestands shares (Standard-laag)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) van de planningshandleiding voor onboarding-instructies voor de grotere bestands shares voor de Standard-laag.

* <a id="lfs-performance-impact"></a>
**Is het uitbreiden van mijn quotum voor bestandsdeelingen van invloed op mijn workloads of Azure File Sync?**
    
    Nee. Het uitbreiden van het quotum heeft geen invloed op uw workloads of Azure File Sync.

* <a id="open-handles-quota"></a>
**Hoeveel clients hebben gelijktijdig toegang tot hetzelfde bestand?**   
    Er is een quotum van 2000 geopende grepen voor één bestand. Wanneer u 2000 geopende grepen hebt, wordt er een foutbericht weergegeven met de melding dat het quotum is bereikt.

* <a id="zip-slow-performance"></a>
**Mijn prestaties zijn traag wanneer ik bestanden uit een Azure Files. Wat moet ik doen?**  
    Als u grote aantallen bestanden wilt overdragen naar Azure Files, raden we u aan AzCopy (voor Windows, in preview voor Linux en UNIX) of Azure PowerShell. Deze hulpprogramma's zijn geoptimaliseerd voor netwerkoverdracht.

* <a id="slow-perf-windows-81-2012r2"></a>
**Waarom zijn mijn prestaties traag nadat ik mijn Azure-bestands share heb bevestigd op Windows Server 2012 R2 of Windows 8.1?**  
    Er is een bekend probleem bij het installeren van een Azure-bestands share op Windows Server 2012 R2 en Windows 8.1. Het probleem is opgelost in de cumulatieve update van april 2014 voor Windows 8.1 en Windows Server 2012 R2. Voor optimale prestaties moet u ervoor zorgen dat deze patch op alle exemplaren van Windows Server 2012 R2 en Windows 8.1 is toegepast. (U moet altijd Windows-patches ontvangen via Windows Update.) Zie het bijbehorende artikel Trage Microsoft Knowledge Base wanneer u toegang hebt tot Azure Files van [Windows 8.1 of Server 2012 R2](https://support.microsoft.com/kb/3114025)voor meer informatie.

## <a name="features-and-interoperability-with-other-services"></a>Functies en interoperabiliteit met andere services
* <a id="cluster-witness"></a>
**Kan ik mijn Azure-bestands share gebruiken als *bestandsdeel witness* voor mijn Windows Server-failovercluster?**  
    Deze configuratie wordt momenteel niet ondersteund voor een Azure-bestands share. Zie Deploy a Cloud Witness for a Failover Cluster (Een cloud-witness implementeren voor een [failovercluster)](/windows-server/failover-clustering/deploy-cloud-witness)voor meer informatie over het instellen van dit voor Azure Blob-opslag.

* <a id="containers"></a>
**Kan ik een Azure-bestands share aan een Azure Container-exemplaar toevoegen?**  
    Ja, Azure-bestands shares zijn een goede optie als u informatie wilt opslaan buiten de levensduur van een container-instantie. Zie Een [Azure-bestands share aan Azure Container Instances toevoegen voor meer informatie.](../../container-instances/container-instances-volume-azure-files.md)

* <a id="rest-rename"></a>
**Is er een hernoemingsbewerking in de REST API?**  
    Momenteel niet.

* <a id="nested-shares"></a>
**Kan ik geneste shares instellen? Met andere woorden, een share onder een share?**  
    Nee. De bestands *share is* het virtuele stuurprogramma dat u kunt toevoegen, zodat geneste shares niet worden ondersteund.

* <a id="ibm-mq"></a>
**Hoe kan ik een Azure Files IBM MQ gebruiken?**  
    IBM heeft een document uitgebracht waarmee IBM MQ-klanten hun Azure Files met de IBM-service. Zie How [to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service (Een wachtrijbeheer](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)met meerdere exemplaren van IBM MQ instellen met Microsoft Azure Files-service) voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen oplossen met Azure File Sync](../file-sync/file-sync-troubleshoot.md)
