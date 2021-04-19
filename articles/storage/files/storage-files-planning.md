---
title: Planning voor een Azure Files implementatie | Microsoft Docs
description: Inzicht in het plannen van Azure Files implementatie. U kunt een Azure-bestands share direct aan een account toevoegen of de Azure-bestands share on-premises in de cache opslaan met Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2cb3bee770653173f1a40b209c27d2dc92c7df11
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718031"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planning voor de implementatie van Azure Files
[Azure Files](storage-files-introduction.md) kunnen op twee belangrijke manieren worden geïmplementeerd: door de serverloze Azure-bestands shares rechtstreeks te mounten of door Azure-bestands shares on-premises op te Azure File Sync. Met welke implementatieoptie u kiest, wijzigt u de zaken die u moet overwegen bij het plannen van uw implementatie. 

- **Directe** bevestiging van een Azure-bestands share: omdat Azure Files toegang biedt tot Server Message Block (SMB) of Network File System (NFS), kunt u Azure-bestands shares on-premises of in de cloud aan elkaar toevoegen met behulp van de standaard-SMB- of NFS-clients die beschikbaar zijn in uw besturingssysteem. Omdat Azure-bestands shares serverloos zijn, is voor implementatie voor productiescenario's geen beheer van een bestandsserver of NAS-apparaat vereist. Dit betekent dat u geen softwarepatches hoeft toe te passen of fysieke schijven hoeft te verwisselen. 

- **Azure-bestands share on-premises** cachen met Azure File Sync: met Azure File Sync kunt u de bestands shares van uw organisatie centraliseren in Azure Files, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver be behouden. Azure File Sync transformeert een on-premises (of cloud) Windows Server naar een snelle cache van uw Azure SMB-bestands share. 

In dit artikel worden met name overwegingen voor de implementatie van een Azure-bestands share beschreven die rechtstreeks door een on-premises client of cloudclient moeten worden bevestigd. Zie Planning for an Azure File Sync deployment (Planning voor een Azure File Sync [implementatie) als u](storage-sync-files-planning.md)een Azure File Sync wilt plannen.

## <a name="available-protocols"></a>Beschikbare protocollen

Azure Files biedt twee protocollen die kunnen worden gebruikt bij het toevoegen van uw bestands shares, SMB en Network File System (NFS). Zie Protocollen voor Azure-bestands delen voor [meer informatie over deze protocollen.](storage-files-compare-protocols.md)

> [!IMPORTANT]
> De meeste inhoud van dit artikel is alleen van toepassing op SMB-shares. Alles wat van toepassing is op NFS-shares geeft aan dat het van toepassing is.

## <a name="management-concepts"></a>Beheerconcepten
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Wanneer u Azure-bestands shares implementeert in opslagaccounts, raden we het volgende aan:

- Alleen Azure-bestands shares implementeren in opslagaccounts met andere Azure-bestands shares. Hoewel u met GPv2-opslagaccounts opslagaccounts voor gemengde doeleinden kunt hebben, kan het, omdat opslagresources zoals Azure-bestands shares en blobcontainers de limieten van het opslagaccount delen, het combineren van resources het moeilijker maken om later prestatieproblemen op te lossen. 

- Let op de IOPS-beperkingen van een opslagaccount bij het implementeren van Azure-bestands shares. Idealiter zou u bestands shares 1:1 koppelen aan opslagaccounts, maar dit is mogelijk niet altijd mogelijk vanwege verschillende limieten en beperkingen, zowel vanuit uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestands share te hebben geïmplementeerd in één opslagaccount, moet u overwegen welke shares zeer actief zijn en welke shares minder actief zijn om ervoor te zorgen dat de meest populaire bestands shares niet samen in hetzelfde opslagaccount worden geplaatst.

- Implementeer alleen GPv2- en FileStorage-accounts en upgrade GPv1- en klassieke opslagaccounts wanneer u ze in uw omgeving vindt. 

## <a name="identity"></a>Identiteit
Voor toegang tot een Azure-bestands share moet de gebruiker van de bestands share worden geverifieerd en zijn autorisatie hebben voor toegang tot de share. Dit wordt gedaan op basis van de identiteit van de gebruiker die toegang heeft tot de bestands share. Azure Files kan worden geïntegreerd met drie hoofdidentiteitsproviders:
- **On-premises Active Directory Domain Services (AD DS of on-premises AD DS)**: Azure-opslagaccounts kunnen net als een Windows Server-bestandsserver of NAS-apparaat lid worden van een domein dat eigendom is van de klant, Active Directory Domain Services. U kunt een domeincontroller on-premises, in een Azure-VM of zelfs als een VM in een andere cloudprovider implementeren; Azure Files is onafhankelijk van waar uw domeincontroller wordt gehost. Zodra een opslagaccount lid is van een domein, kan de eindgebruiker een bestands share aan het gebruikersaccount toevoegen dat hij/zij heeft aangemeld bij zijn pc. Verificatie op basis van AD maakt gebruik van het Kerberos-verificatieprotocol.
- **Azure Active Directory Domain Services (Azure AD DS)**: Azure AD DS biedt een door Microsoft beheerde domeincontroller die kan worden gebruikt voor Azure-resources. Domein dat uw opslagaccount aan Azure AD DS biedt vergelijkbare voordelen als domein toevoegen aan een Active Directory die eigendom is van de klant. Deze implementatieoptie is het handigst voor lift-and-shift-scenario's voor toepassingen waarvoor AD-machtigingen zijn vereist. Omdat Azure AD DS ad-verificatie biedt, maakt deze optie ook gebruik van het Kerberos-verificatieprotocol.
- **Azure-opslagaccountsleutel:** Azure-bestands shares kunnen ook worden bevestigd met een Azure-opslagaccountsleutel. Als u een bestands share op deze manier wilt toevoegen, wordt de naam van het opslagaccount gebruikt als de gebruikersnaam en wordt de sleutel van het opslagaccount gebruikt als wachtwoord. Het gebruik van de sleutel van het opslagaccount om de Azure-bestands share te mounten is in de meeste plaats een beheerdersbewerking, omdat de aan de share geplaatste bestands share volledige machtigingen heeft voor alle bestanden en mappen op de share, zelfs als deze ACL's hebben. Wanneer u de sleutel van het opslagaccount gebruikt om te worden bevestigd via SMB, wordt het NTLMv2-verificatieprotocol gebruikt.

Voor klanten die migreren van on-premises bestandsservers of voor het maken van nieuwe bestands shares in Azure Files bedoeld om zich te gedragen als Windows-bestandsservers of NAS-apparaten, is domein dat uw opslagaccount lid maakt van **Active Directory van** de klant de aanbevolen optie. Zie Active Directory-overzicht voor meer informatie over het toevoegen van uw opslagaccount aan een Active Directory van de klant [Azure Files active directory.](storage-files-active-directory-overview.md)

Als u van plan bent om de sleutel van het opslagaccount te gebruiken voor toegang tot uw Azure-bestands shares, raden we u aan service-eindpunten te gebruiken, zoals beschreven in de [sectie](#networking) Netwerken.

## <a name="networking"></a>Netwerken
Azure-bestands shares zijn overal toegankelijk via het openbare eindpunt van het opslagaccount. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldingsidentiteit van een gebruiker, veilig kunnen zijn van binnen of buiten Azure. In veel klantomgevingen kan een initiële koppeling van de Azure-bestandsshare op uw on-premises werkstation mislukken, zelfs als het wel lukt om Azure-VM's te koppelen. De reden hiervoor is dat veel organisaties en internetproviders (ISP's) de poort blokkeren die door SMB wordt gebruikt voor communicatie: poort 445. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Als u de toegang tot uw Azure-bestands share wilt deblokkeren, hebt u twee hoofdopties:

- Deblokker poort 445 voor het on-premises netwerk van uw organisatie. Azure-bestands shares kunnen alleen extern worden gebruikt via het openbare eindpunt met behulp van internetveilige protocollen zoals SMB 3.0 en de FileREST-API. Dit is de eenvoudigste manier om on-premises toegang te krijgen tot uw Azure-bestands share, omdat er geen geavanceerde netwerkconfiguratie nodig is naast het wijzigen van de regels voor uitgaande poort van uw organisatie. U wordt echter aangeraden verouderde en afgeschafte versies van het SMB-protocol te verwijderen, namelijk SMB 1.0. Zie [Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) beveiligen en Linux beveiligen voor meer [informatie.](storage-how-to-use-files-linux.md#securing-linux)

- Toegang tot Azure-bestands shares via een ExpressRoute- of VPN-verbinding. Wanneer u toegang hebt tot uw Azure-bestands share via een netwerktunnel, kunt u uw Azure-bestands share als een on-premises bestands delen, omdat SMB-verkeer uw organisatiegrens niet passeert.   

Hoewel het vanuit technisch oogpunt aanzienlijk eenvoudiger is om uw Azure-bestands shares te verbinden via het openbare eindpunt, verwachten we dat de meeste klanten ervoor kiezen om hun Azure-bestands shares te verbinden via een ExpressRoute- of VPN-verbinding. Met deze opties kunt u zowel SMB- als NFS-shares gebruiken. Hiervoor moet u het volgende configureren voor uw omgeving:  

- Netwerktunneling met Behulp van **ExpressRoute, site-naar-site of punt-naar-site-VPN:** Tunneling naar een virtueel netwerk maakt toegang tot Azure-bestands shares vanaf on-premises mogelijk, zelfs als poort 445 is geblokkeerd.
- **Privé-eindpunten:** privé-eindpunten geven uw opslagaccount een toegewezen IP-adres vanuit de adresruimte van het virtuele netwerk. Dit maakt netwerktunneling mogelijk zonder dat on-premises netwerken hoeven te worden geopend voor alle IP-adresbereiken die eigendom zijn van de Azure-opslagclusters. 
- **Doorsturen via DNS:** configureer uw on-premises DNS om de naam van uw opslagaccount (voor de openbare cloudregio's) om te zetten in het IP-adres van uw `storageaccount.file.core.windows.net` privé-eindpunten.

Als u de netwerken wilt plannen die zijn gekoppeld aan het implementeren van een Azure-bestands share, Azure Files [aandachtspunten voor netwerken.](storage-files-networking-overview.md)

## <a name="encryption"></a>Versleuteling
Azure Files ondersteunt twee verschillende soorten versleuteling: versleuteling 'in transit', die betrekking heeft op de versleuteling die wordt gebruikt bij het mounteren/openen van de Azure-bestands share, en versleuteling-at-rest, die betrekking heeft op de manier waarop de gegevens worden versleuteld wanneer ze op schijf worden opgeslagen. 

### <a name="encryption-in-transit"></a>Versleuteling tijdens overdracht

> [!IMPORTANT]
> Deze sectie bevat informatie over versleuteling tijdens overdracht voor SMB-shares. Zie [Beveiliging](storage-files-compare-protocols.md#security) voor meer informatie over versleuteling tijdens overdracht voor NFS-shares.

Standaard is versleuteling in-transit ingeschakeld voor alle Azure-opslagaccounts. Dit betekent dat wanneer u een bestandsshare koppelt via SMB of de bestandsshare opent via het FileREST-protocol (bijvoorbeeld via de Azure-portal, PowerShell/CLI of Azure-SDK's), de verbinding alleen wordt toegestaan als deze wordt gemaakt met SMB 3.0+ met versleuteling of HTTPS. Op clients die SMB 3.0 niet ondersteunen of op clients die SMB 3.0 wel ondersteunen maar SMB-versleuteling niet, kan de Azure-bestandsshare niet worden gekoppeld als versleuteling in transit is ingeschakeld. Zie onze gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) en [Linux-](storage-how-to-use-files-linux.md) voor meer informatie over besturingssystemen waarin SMB 3.0 met versleuteling wordt ondersteund. Alle huidige versies van PowerShell, CLI en SDK's ondersteunen HTTPS.  

U kunt versleuteling in transit uitschakelen voor een Azure-opslagaccount. Wanneer versleuteling is uitgeschakeld, Azure Files ook SMB 2.1, SMB 3.0 zonder versleuteling en niet-versleutelde FileREST API-aanroepen via HTTP. De belangrijkste reden om versleuteling in transit uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. In Azure Files worden SMB 2.1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestandsshare. SMB 2.1-clients buiten de Azure-regio van de Azure-bestandsshare, zoals on-premises clients of clients in een andere Azure-regio, hebben geen toegang tot de bestandsshare.

We raden u ten zeerste aan ervoor te zorgen dat versleuteling van gegevens die onderweg zijn is ingeschakeld.

Zie [Veilige overdracht vereisen in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over versleuteling in transit.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Gegevensbeveiliging
Azure Files een aanpak met meerdere lagen om ervoor te zorgen dat er een back-up van uw gegevens wordt gemaakt, herstelbaar is en wordt beschermd tegen beveiligingsrisico's.

### <a name="soft-delete"></a>Voorlopig verwijderen
Soft Delete voor bestands shares (preview) is een instelling op opslagaccountniveau waarmee u de bestands share kunt herstellen wanneer deze per ongeluk wordt verwijderd. Wanneer een bestands share wordt verwijderd, wordt deze overgeslagen naar een voorlopig verwijderde status in plaats van permanent te worden gewist. U kunt configureren hoe lang tijdelijke verwijderde gegevens kunnen worden hersteld voordat ze permanent worden verwijderd en de share op elk gewenst moment verwijderen tijdens deze bewaarperiode. 

We raden u aan om voor de meeste bestands shares de knop Voor het verwijderen van bestanden in te laten. Als u een werkstroom hebt waarin het verwijderen van een share gebruikelijk en verwacht is, kunt u besluiten een korte retentieperiode in te stellen of helemaal geen zachte verwijdering in te stellen.

Zie Onopzettelijk verwijderen van gegevens voorkomen [voor meer informatie over het verwijderen van gegevens.](./storage-files-prevent-file-share-deletion.md)

### <a name="backup"></a>Backup
U kunt een back-up maken van uw Azure-bestands share via [share-momentopnamen.](./storage-snapshots-files.md)Dit zijn alleen-lezen, point-in-time kopieën van uw share. Momentopnamen zijn incrementeel, wat betekent dat ze alleen zoveel gegevens bevatten als sinds de vorige momentopname is gewijzigd. U kunt maximaal 200 momentopnamen per bestands share hebben en deze maximaal tien jaar bewaren. U kunt deze momentopnamen handmatig maken in de Azure Portal, via PowerShell of via de opdrachtregelinterface (CLI), of u kunt [Azure Backup.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) Momentopnamen worden opgeslagen in uw bestands share, wat betekent dat als u uw bestands share verwijdert, uw momentopnamen ook worden verwijderd. Als u de back-ups van momentopnamen wilt beveiligen tegen onbedoeld verwijderen, moet u ervoor zorgen dat het verwijderen van de momentopname is ingeschakeld voor uw share.

[Azure Backup voor Azure-bestands shares](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) worden de planning en retentie van momentopnamen verwerkt. De mogelijkheden van zijn vader-vader-zoon (GFS) betekenen dat u dagelijkse, wekelijkse, maandelijkse en jaarlijkse momentopnamen kunt maken, elk met hun eigen afzonderlijke retentieperiode. Azure Backup ook de inschakelen van de functie voor soft delete in en wordt een verwijdervergrendeling voor een opslagaccount ingeschakeld zodra een bestandsdeling in het account is geconfigureerd voor back-up. Ten laatste biedt Azure Backup bepaalde mogelijkheden voor sleutelbewaking en waarschuwingen waarmee klanten een geconsolideerde weergave van hun back-up kunnen hebben.

U kunt herstel op item- en shareniveau in de Azure Portal met behulp Azure Backup. U hoeft alleen maar het herstelpunt (een bepaalde momentopname), het betreffende bestand of de betreffende map te kiezen, indien relevant, en vervolgens de locatie (oorspronkelijk of alternatief) waar u naar wilt herstellen. De back-upservice verwerkt het kopiëren van de momentopnamegegevens en toont de voortgang van het herstellen in de portal.

Zie Over back-ups van Azure-bestands delen voor meer informatie over [back-ups.](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-defender-for-azure-files"></a>Azure Defender voor Azure Files 
Azure Defender for Azure Storage (voorheen Advanced Threat Protection voor Azure Storage) biedt een extra laag beveiligingsinformatie die waarschuwingen biedt wanneer afwijkende activiteiten in uw opslagaccount worden gedetecteerd, bijvoorbeeld ongebruikelijke toegangspogingen. Er wordt ook een malware-hashreputatieanalyse uitgevoerd en er wordt een waarschuwing over bekende malware gegenereerd. U kunt een Azure Defender op abonnements- of opslagaccountniveau configureren via Azure Security Center. 

Zie Inleiding tot Azure Defender [voor Storage voor meer informatie.](../../security-center/defender-for-storage-introduction.md)

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standaardbestands shares inschakelen voor een bereik van maximaal 100 TiB
Standaard kunnen standaardbestands shares maximaal 5 TiB bespannen, maar u kunt de sharelimiet verhogen naar 100 TiB. Zie Enable and create large file shares (Grote bestands shares inschakelen en maken) voor meer informatie over het [verhogen van de sharelimiet.](storage-files-how-to-create-large-file-share.md)


#### <a name="limitations"></a>Beperkingen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migratie
In veel gevallen maakt u geen nieuwe netbestands share voor uw organisatie, maar migreert u in plaats daarvan een bestaande bestands share van een on-premises bestandsserver of NAS-apparaat naar Azure Files. Het kiezen van de juiste migratiestrategie en het juiste hulpprogramma voor uw scenario is belangrijk voor het slagen van uw migratie. 

Het [overzichtsartikel over migratie](storage-files-migration-overview.md) bevat kort de basisbeginselen en bevat een tabel die u naar migratiehandleidingen leidt die waarschijnlijk betrekking hebben op uw scenario.

## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure File Sync implementatie](storage-sync-files-planning.md)
* [Implementatie Azure Files](./storage-how-to-create-file-share.md)
* [Implementatie van Azure File Sync](storage-sync-files-deployment-guide.md)
* [Raadpleeg het artikel Over migratieoverzicht om de migratiehandleiding voor uw scenario te vinden](storage-files-migration-overview.md)