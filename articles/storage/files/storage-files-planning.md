---
title: Een Azure Files-implementatie plannen | Microsoft Docs
description: Meer informatie over het plannen van een Azure Files-implementatie. U kunt een Azure-bestands share direct koppelen of de Azure-bestands share on-premises opslaan in de cache met Azure File Sync.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 267b68fbdae6d894acc3222a8d74a8e15e865dbc
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023517"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planning voor de implementatie van Azure Files
[Azure files](storage-files-introduction.md) kunnen op twee manieren worden geïmplementeerd: door de Serverloze Azure-bestands shares rechtstreeks te koppelen of door Azure-bestands shares on-premises in de cache op te maken met behulp van Azure file sync. Welke implementatie optie u kiest, wijzigt de dingen die u moet overwegen bij het plannen van uw implementatie. 

- **Directe koppeling van een Azure-bestands share**: omdat Azure files Server Message Block (SMB) of NFS-toegang (Network File System) hebt, kunt u Azure-bestands shares on-premises of in de Cloud koppelen met behulp van de standaard SMB-of NFS-clients die beschikbaar zijn in uw besturings systeem. Omdat Azure-bestands shares serverloos zijn, is voor het implementeren van productie scenario's geen bestands server of een NAS-apparaat nodig. Dit betekent dat u geen software patches hoeft toe te passen of fysieke schijven hoeft uit te wisselen. 

- **Azure-bestands share op locatie opslaan in de cache met Azure file sync**: Azure File Sync kunt u de bestands shares van uw organisatie in azure files centraliseren en tegelijkertijd de flexibiliteit, prestaties en compatibiliteit van een on-premises Bestands server behouden. Azure File Sync transformeert een on-premises Windows-Server (of Cloud) naar een snelle cache van uw Azure SMB-bestands share. 

Dit artikel heeft voornamelijk betrekking op implementatie overwegingen voor het implementeren van een Azure-bestands share om rechtstreeks te worden gekoppeld door een on-premises of cloud-client. Zie [planning voor een implementatie van een Azure file sync](storage-sync-files-planning.md)om een Azure file sync implementatie te plannen.

## <a name="available-protocols"></a>Beschik bare protocollen

Azure Files biedt twee protocollen die kunnen worden gebruikt bij het koppelen van uw bestands shares, SMB en Network File System (NFS). Zie [Azure file share-protocollen](storage-files-compare-protocols.md)voor meer informatie over deze protocollen.

> [!IMPORTANT]
> De meeste inhoud van dit artikel is alleen van toepassing op SMB-shares. Alle items die van toepassing zijn op NFS-shares, worden in het bijzonder vermeld.

## <a name="management-concepts"></a>Beheer concepten
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Wanneer u Azure-bestands shares in opslag accounts implementeert, kunt u het volgende doen:

- Implementeer alleen Azure-bestands shares in opslag accounts met andere Azure-bestands shares. Hoewel u met GPv2-opslag accounts gebruik kunt maken van gemengde opslag accounts, omdat opslag resources zoals Azure-bestands shares en BLOB-containers de limieten van het opslag account delen, kan het lastiger zijn om sneller problemen op te lossen. 

- Let op de limieten voor de IOPS van een opslag account bij het implementeren van Azure-bestands shares. In het ideale geval wijst u bestands shares 1:1 met opslag accounts toe, maar dit is mogelijk niet altijd mogelijk als gevolg van verschillende limieten en beperkingen, zowel vanuit uw organisatie als vanuit Azure. Wanneer het niet mogelijk is om slechts één bestands share in één opslag account te implementeren, moet u overwegen welke shares Maxi maal actief zullen zijn en welke shares minder actief zijn om ervoor te zorgen dat de meest uitgebreide bestands shares niet samen in hetzelfde opslag account worden geplaatst.

- Implementeer alleen GPv2-en FileStorage-accounts en werk GPv1 en klassieke opslag accounts bij wanneer u deze in uw omgeving vindt. 

## <a name="identity"></a>Identiteit
Om toegang te krijgen tot een Azure-bestands share, moet de gebruiker van de bestands share worden geverifieerd en moeten ze toegang hebben tot de share. Dit wordt gedaan op basis van de identiteit van de gebruiker die toegang heeft tot de bestands share. Azure Files integreert met drie belangrijkste id-providers:
- **On-premises Active Directory Domain Services (AD DS, of on-premises AD DS)**: Azure Storage-accounts kunnen lid zijn van een domein dat eigendom is van een klant Active Directory Domain Services, net als een Windows Server-Bestands server of een NAS-apparaat. U kunt een domein controller on-premises, in een Azure-VM of zelfs als een virtuele machine in een andere Cloud provider implementeren. Azure Files is neutraal waar uw domein controller wordt gehost. Als een opslag account is toegevoegd aan een domein, kan de eind gebruiker een bestands share koppelen met het gebruikers account waarmee ze zijn aangemeld bij hun PC. Op AD gebaseerde verificatie maakt gebruik van het Kerberos-verificatie protocol.
- **Azure Active Directory Domain Services (azure AD DS)**: Azure AD DS biedt een door micro soft beheerde domein controller die kan worden gebruikt voor Azure-resources. Een domein dat wordt toegevoegd aan uw opslag account voor Azure AD DS biedt vergelijk bare voor delen als het domein wordt toegevoegd aan een Active Directory van een klant. Deze implementatie optie is het nuttigst voor toepassingen die zijn gebaseerd op AD-machtigingen. Omdat Azure AD DS op AD gebaseerde verificatie biedt, gebruikt deze optie ook het Kerberos-verificatie protocol.
- **Azure Storage-account sleutel**: Azure-bestands shares kunnen ook worden gekoppeld met een Azure Storage-account sleutel. Als u een bestands share op deze manier wilt koppelen, wordt de naam van het opslag account gebruikt als de gebruikers naam en de sleutel voor het opslag account als wacht woord. Het gebruik van de sleutel van het opslag account voor het koppelen van de Azure-bestands share is in feite een beheerders bewerking, omdat de gekoppelde bestands share volledige machtigingen heeft voor alle bestanden en mappen op de share, zelfs als deze Acl's hebben. Wanneer u de sleutel voor het opslag account gebruikt om te koppelen via SMB, wordt het NTLMv2-verificatie protocol gebruikt.

Voor klanten die migreren van on-premises bestands servers of het maken van nieuwe bestands shares in Azure Files bedoeld om te fungeren als Windows-bestands servers of NAS-apparaten, is het domein dat wordt toegevoegd aan de door de **klant Active Directory** de aanbevolen optie. Zie [Azure Files Active Directory Overview](storage-files-active-directory-overview.md)voor meer informatie over het toevoegen van uw opslag account aan een Active Directory van de klant.

Als u van plan bent om de sleutel van het opslag account te gebruiken voor toegang tot uw Azure-bestands shares, kunt u het beste service-eind punten gebruiken zoals beschreven in de sectie [netwerken](#networking) .

## <a name="networking"></a>Netwerken
Azure-bestands shares zijn overal toegankelijk via het open bare eind punt van het opslag account. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldingsidentiteit van een gebruiker, veilig kunnen zijn van binnen of buiten Azure. In veel klantomgevingen kan een initiële koppeling van de Azure-bestandsshare op uw on-premises werkstation mislukken, zelfs als het wel lukt om Azure-VM's te koppelen. De reden hiervoor is dat veel organisaties en internetproviders (ISP's) de poort blokkeren die door SMB wordt gebruikt voor communicatie: poort 445. Ga naar [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) voor een overzicht van welke internetproviders toegang via poort 445 toestaan en welke niet.

Als u de toegang tot uw Azure-bestands share wilt blok keren, hebt u twee belang rijke opties:

- Deblokkeren van poort 445 voor het on-premises netwerk van uw organisatie. Azure-bestands shares zijn mogelijk alleen extern toegankelijk via het open bare eind punt met behulp van veilige protocollen voor Internet, zoals SMB 3,0 en de FileREST-API. Dit is de eenvoudigste manier om toegang te krijgen tot uw Azure-bestands share vanaf een on-premises omdat er geen geavanceerde netwerk configuratie nodig is dan het wijzigen van de uitgaande poort regels van uw organisatie, maar we raden u echter aan verouderde en verouderde versies van het SMB-protocol te verwijderen, namelijk SMB 1,0. Zie [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server) en [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)voor meer informatie over hoe u dit doet.

- Toegang tot Azure-bestands shares via een ExpressRoute of een VPN-verbinding. Wanneer u toegang hebt tot uw Azure-bestands share via een netwerk tunnel, kunt u uw Azure-bestands share koppelen zoals een on-premises bestands share omdat het SMB-verkeer uw organisatie grens niet overschrijdt.   

Hoewel het vanuit een technisch perspectief aanzienlijk eenvoudiger is om uw Azure-bestands shares te koppelen via het open bare eind punt, zullen de meeste klanten ervoor kiezen hun Azure-bestands shares te koppelen via een ExpressRoute of een VPN-verbinding. Het koppelen met deze opties is mogelijk met zowel SMB-als NFS-shares. Hiervoor moet u het volgende configureren voor uw omgeving:  

- **Netwerk tunneling met behulp van ExpressRoute, site-naar-site-of punt-naar-site-VPN**: tunneling naar een virtueel netwerk maakt toegang tot Azure-bestands shares van on-premises mogelijk, zelfs als poort 445 wordt geblokkeerd.
- **Persoonlijke eind punten**: persoonlijke eind punten geven uw opslag account een toegewezen IP-adres binnen de adres ruimte van het virtuele netwerk. Hierdoor kan netwerk tunneling worden ingeschakeld zonder dat u on-premises netwerken hoeft te openen tot alle IP-adresbereiken die eigendom zijn van de Azure Storage-clusters. 
- **Door sturen via DNS**: Configureer uw on-premises DNS om de naam van uw opslag account ( `storageaccount.file.core.windows.net` voor de open bare-Cloud regio's) om te zetten in het IP-adres van uw privé-eind punten.

Zie [Azure files netwerk overwegingen](storage-files-networking-overview.md)voor het plannen van de netwerken die zijn gekoppeld aan de implementatie van een Azure-bestands share.

## <a name="encryption"></a>Versleuteling
Azure Files ondersteunt twee verschillende soorten versleuteling: versleuteling in transit, dat betrekking heeft op de versleuteling die wordt gebruikt bij het koppelen/openen van de Azure-bestands share, en versleuteling op rest, die betrekking heeft op hoe de gegevens worden versleuteld wanneer deze op schijf worden opgeslagen. 

### <a name="encryption-in-transit"></a>Versleuteling tijdens overdracht

> [!IMPORTANT]
> Deze sectie bevat informatie over versleuteling tijdens overdracht voor SMB-shares. Zie [Beveiliging](storage-files-compare-protocols.md#security) voor meer informatie over versleuteling tijdens overdracht voor NFS-shares.

Standaard is versleuteling in-transit ingeschakeld voor alle Azure-opslagaccounts. Dit betekent dat wanneer u een bestandsshare koppelt via SMB of de bestandsshare opent via het FileREST-protocol (bijvoorbeeld via de Azure-portal, PowerShell/CLI of Azure-SDK's), de verbinding alleen wordt toegestaan als deze wordt gemaakt met SMB 3.0+ met versleuteling of HTTPS. Op clients die SMB 3.0 niet ondersteunen of op clients die SMB 3.0 wel ondersteunen maar SMB-versleuteling niet, kan de Azure-bestandsshare niet worden gekoppeld als versleuteling in transit is ingeschakeld. Zie onze gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) en [Linux-](storage-how-to-use-files-linux.md) voor meer informatie over besturingssystemen waarin SMB 3.0 met versleuteling wordt ondersteund. Alle huidige versies van PowerShell, CLI en SDK's ondersteunen HTTPS.  

U kunt versleuteling in transit uitschakelen voor een Azure-opslagaccount. Wanneer versleuteling is uitgeschakeld, staat Azure Files ook toe dat SMB 2,1, SMB 3,0 zonder versleuteling en niet-versleutelde API-aanroepen van FileREST via HTTP. De belangrijkste reden om versleuteling in transit uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. In Azure Files worden SMB 2.1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestandsshare. SMB 2.1-clients buiten de Azure-regio van de Azure-bestandsshare, zoals on-premises clients of clients in een andere Azure-regio, hebben geen toegang tot de bestandsshare.

We raden u ten zeerste aan te zorgen dat de versleuteling van gegevens in-transit is ingeschakeld.

Zie [Veilige overdracht vereisen in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over versleuteling in transit.

### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Gegevensbeveiliging
Azure Files beschikt over een aanpak met meerdere lagen om ervoor te zorgen dat er een back-up wordt gemaakt van uw gegevens, herstel bare en beschermd tegen beveiligings Risico's.

### <a name="soft-delete"></a>Voorlopig verwijderen
Zacht verwijderen voor bestands shares (preview) is een instelling voor het niveau van een opslag account waarmee u uw bestands share kunt herstellen wanneer deze per ongeluk wordt verwijderd. Wanneer een bestands share wordt verwijderd, wordt deze overgezet naar een voorlopig verwijderde status in plaats van permanent te wissen. U kunt de hoeveelheid tijd waarvoor tijdelijke verwijderde gegevens kunnen worden hersteld, configureren voordat deze definitief wordt verwijderd en de share tijdens deze Bewaar periode op elk gewenst moment verwijderen. 

U kunt het beste verwijderen voor de meeste bestands shares inschakelen. Als u een werk stroom hebt waarbij het verwijderen van shares gemeen schappelijk en verwacht is, kunt u ervoor kiezen om een korte Bewaar periode te hebben of dat u helemaal geen tijdelijke verwijdering hebt ingeschakeld.

Voor meer informatie over zacht verwijderen, Zie [voor komen dat onbedoelde gegevens worden verwijderd](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
U kunt een back-up maken van uw Azure-bestands share via [moment opnamen van shares](./storage-snapshots-files.md), die alleen-lezen zijn, Point-in-time-kopieën van uw share. Moment opnamen zijn incrementeel, wat betekent dat ze slechts zoveel gegevens bevatten als is gewijzigd sinds de vorige moment opname. U kunt Maxi maal 200 moment opnamen per bestands share hebben en deze Maxi maal tien jaar bewaren. U kunt deze moment opnamen hand matig maken in de Azure Portal, via Power shell of via de opdracht regel interface (CLI), maar u kunt ook [Azure backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)gebruiken. Moment opnamen worden opgeslagen in uw bestands share, wat betekent dat als u de bestands share verwijdert, uw moment opnamen ook worden verwijderd. Als u back-ups van uw moment opname wilt beveiligen tegen onbedoeld verwijderen, moet u ervoor zorgen dat zacht verwijderen is ingeschakeld voor uw share.

[Azure backup voor Azure-bestands shares zorgt voor](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) de planning en retentie van moment opnamen. De mogelijkheden van de groot vader-vader-zoon (GFS) betekenen dat u dagelijkse, wekelijkse, maandelijkse en jaarlijkse moment opnamen kunt maken, elk met een eigen Bewaar periode. Azure Backup wordt ook de activering van zacht verwijderen in de vorm van een opslag account in de vorm van een bestands share die is geconfigureerd voor back-up. Ten slotte biedt Azure Backup bepaalde belang rijke functies voor het bewaken en waarschuwen waarmee klanten een geconsolideerde weer gave van hun back-ups kunnen maken.

U kunt op item niveau en op share niveau terugzetten in de Azure Portal met behulp van Azure Backup uitvoeren. Het enige wat u hoeft te doen, is het herstel punt (een bepaalde moment opname), het specifieke bestand of de betreffende map, indien van toepassing, en vervolgens de locatie (oorspronkelijk of alternatief) kiezen waarnaar u wilt herstellen. De back-upservice zorgt voor het kopiëren van de momentopname gegevens en toont de voortgang van de herstel bewerking in de portal.

Zie [about Azure file share backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)(Engelstalig) voor meer informatie over back-ups.

### <a name="azure-defender-for-azure-files"></a>Azure Defender voor Azure Files 
Azure Defender voor Azure Storage (voorheen Advanced Threat Protection voor Azure Storage) biedt een extra beveiligingslaag met waarschuwingen wanneer er afwijkende activiteiten in uw opslag account worden gedetecteerd, bijvoorbeeld ongebruikelijke toegangs pogingen. Er wordt ook malware-hash-reputatie analyse uitgevoerd en er wordt gewaarschuwd voor bekende malware. U kunt Azure Defender op een abonnement of opslag account niveau configureren via Azure Security Center. 

Zie [Inleiding tot Azure Defender voor opslag](../../security-center/defender-for-storage-introduction.md)voor meer informatie.

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Schakel standaard bestands shares in om Maxi maal 100 TiB te beslaan
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Beperkingen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migratie
In veel gevallen is het niet mogelijk om een net nieuwe bestands share te maken voor uw organisatie, maar om een bestaande bestands share te migreren van een on-premises Bestands server of een NAS-apparaat naar Azure Files. Het is belang rijk dat u de juiste migratie strategie en het hulp programma voor uw scenario kiest voor het slagen van de migratie. 

In het [artikel migratie overzicht](storage-files-migration-overview.md) vindt u een korte beschrijving van de basis beginselen en een tabel die u leidt naar migratie handleidingen die waarschijnlijk uw scenario beslaan.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
* [Azure Files implementeren](./storage-how-to-create-file-share.md)
* [Azure File Sync implementeren](storage-sync-files-deployment-guide.md)
* [Bekijk het artikel over migratie overzicht om de migratie handleiding voor uw scenario te vinden](storage-files-migration-overview.md)