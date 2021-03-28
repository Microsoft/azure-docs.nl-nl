---
title: Vergelijking Azure Files en Azure NetApp Files | Microsoft Docs
description: Vergelijking van Azure Files en Azure NetApp Files.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 652d362a11e80a488c9278dfeff38e715acee784
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641940"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Vergelijking van Azure Files en Azure NetApp Files

Dit artikel bevat een vergelijking van Azure Files en Azure NetApp Files. 

De meeste werk belastingen waarvoor Cloud bestands opslag is vereist, zijn geschikt voor Azure Files of Azure NetApp Files. Lees de informatie in dit artikel om te bepalen wat het beste past bij uw werk belasting. Zie de documentatie van [Azure files](https://docs.microsoft.com/azure/storage/files/) en [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/) voor meer informatie.

## <a name="features"></a>Functies

| Categorie | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Description | [Azure files](https://azure.microsoft.com/services/storage/files/) is een volledig beheerde, Maxi maal beschik bare service die is geoptimaliseerd voor wille keurige werk belastingen met gegevens updates in de locatie.<br><br> Azure Files is gebaseerd op hetzelfde Azure-opslag platform als andere services, zoals Azure-blobs. | [Azure NetApp files](https://azure.microsoft.com/services/netapp/) is een volledig beheerde NAS-service met hoge Beschik baarheid die de meest veeleisende workloads met lage latentie kan verwerken waarvoor geavanceerde gegevens beheer mogelijkheden zijn vereist. De werk belasting kan worden gemigreerd, die als niet-migreerbaar worden beschouwd.<br><br>  ANF is gebaseerd op de bare metal van NetApp met ONTAP-opslag besturingssysteem dat wordt uitgevoerd in het Azure-Data Center voor een consistente Azure-ervaring en een on-premises soort gelijke prestaties. |
| Protocollen | Premium<br><ul><li>SMB 2,1, 3,0</li><li>NFS 4,1 (preview-versie)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2,1, 3,0</li><li>REST</li></ul><br> Zie [beschik bare bestands share protocollen](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols)voor meer informatie. | Alle lagen<br><ul><li>SMB 1, 2. x, 3. x</li><li>NFS 3,0, 4,1</li><li>Toegang tot twee protocollen (NFSv3/SMB)</li></ul><br> Zie voor meer informatie over het maken van [NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)-, [SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)-of [Dual-protocol](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) volumes. |
| Beschik baarheid van regio | Premium<br><ul><li>30 + regio's</li></ul><br>Standard<br><ul><li>Alle regio's</li></ul><br> Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor meer informatie. | Alle lagen<br><ul><li>25 + regio's</li></ul><br> Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor meer informatie. |
| Redundantie | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Zie [Redundantie](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy)voor meer informatie. | Alle lagen<br><ul><li>Ingebouwde lokale HA</li><li>[Replicatie in meerdere regio's](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level Agreement (SLA)<br><br> Houd er rekening mee dat de Sla's voor Azure Files en Azure NetApp Files op verschillende manieren worden berekend. | [SLA voor Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA voor Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Verificatie en autorisatie Identity-Based | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Houd er rekening mee dat verificatie op basis van een identiteit wordt alleen ondersteund bij het gebruik van het SMB-protocol. Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)voor meer informatie. | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> NFS/SMB Dual Protocol<ul><li>TOEVOEGINGEN/LDAP-integratie</li></ul><br>NFSv3/NFSv 4.1<ul><li>TOEVOEGINGEN/LDAP-integratie (beschikbaar)</li><li>Uitgebreide NFS-groepen (beschikbaar)</li></ul><br> Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs)voor meer informatie. |
| Versleuteling | SMB<br><ul><li>Versleuteling op rest (AES 256) met door de klant of micro soft beheerde sleutels</li><li>Kerberos-versleuteling met AES 256 of RC4-HMAC</li><li>Versleuteling 'in transit'</li></ul><br>NFS<br><ul><li>Versleuteling op rest (AES 256) met door de klant of micro soft beheerde sleutels</li></ul><br>REST<br><ul><li>Versleuteling op rest (AES 256) met door de klant of micro soft beheerde sleutels</li><li>Versleuteling 'in transit'</li></ul><br> Zie [beveiliging](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security)voor meer informatie. | Alle protocollen<br><ul><li>Versleuteling op rest (AES 256) met door micro soft beheerde sleutels </li></ul><br>NFS 4,1<ul><li>Versleuteling in transit met Kerberos met AES 256</li></ul><br> Zie [Veelgestelde vragen over beveiliging](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs)voor meer informatie. |
| Opties voor toegang | <ul><li>Internet</li><li>Beveiligde VNet-toegang</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Zie [netwerk overwegingen](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)voor meer informatie. | <ul><li>Beveiligde VNet-toegang</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Globale bestands cache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Zie [netwerk overwegingen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)voor meer informatie. |
| Gegevensbeveiliging  | <ul><li>Incrementele moment opnamen</li><li>Bestand/Directory gebruiker zelf herstellen</li><li>Herstellen naar nieuwe locatie</li><li>In-place terugzet actie</li><li>Zacht verwijderen op share niveau</li><li>Integratie van Azure Backup</li></ul><br> Zie [Azure files verbeterde mogelijkheden voor gegevens beveiliging](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/)voor meer informatie. | <ul><li>Moment opnamen (255/volume)</li><li>Bestand/Directory gebruiker zelf herstellen</li><li>Herstellen naar nieuw volume</li><li>In-place terugzet actie</li><li>[Replicatie tussen regio's](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (open bare preview)</li></ul><br> Zie [hoe Azure NetApp files moment opnamen werken](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction)voor meer informatie. |
| Migratie Hulpprogramma's  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Opslag migratie service</li><li>AzCopy</li><li>Robocopy</li></ul><br> Zie [migreren naar Azure-bestands shares](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview)voor meer informatie. | <ul><li>[Globale bestands cache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync](https://cloud.netapp.com/cloud-sync-service), [XCP](https://xcp.netapp.com/)</li><li>Opslag migratie service</li><li>AzCopy</li><li>Robocopy</li><li>Op basis van toepassingen (bijvoorbeeld HSR, Data Guard, AOAG)</li></ul> |
| Lagen | <ul><li>Premium</li><li>Trans actie geoptimaliseerd</li><li>Dynamisch</li><li>Statisch</li></ul><br> Zie [opslag lagen](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers)voor meer informatie. | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Alle lagen bieden een minimale latentie van subms.<br><br> Zie [service niveaus](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) en [prestatie overwegingen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations)voor meer informatie. |
| Prijzen | [Azure Files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files prijzen](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Schaalbaarheid en prestaties

| Categorie | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minimale grootte van share/volume | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Alle lagen<br><ul><li>100 GiB (minimum grootte van de capaciteits groep: 4 TiB)</li></ul> |
| Maximale grootte van share/volume | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Alle lagen<br><ul><li>100 TiB (500-limiet voor TiB-capaciteit)</li></ul><br>Maxi maal 12,5 PiB per Azure NetApp-account. |
| Maximum aantal IOPS in share/volume | Premium<br><ul><li>Maxi maal 100.000</li></ul><br>Standard<br><ul><li>Maxi maal 10.000</li></ul> | Ultra en Premium<br><ul><li>Maxi maal 450k </li></ul><br>Standard<br><ul><li>Maxi maal 320k</li></ul> |
| Maximale door Voer van share/volume | Premium<br><ul><li>Maxi maal 10 GiB/s</li></ul><br>Standard<br><ul><li>Maxi maal 300 MiB/s</li></ul> | Ultra en Premium<br><ul><li>Maxi maal 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Maxi maal 3,2 GiB/s</li></ul> |
| Maximale bestandsgrootte | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Alle lagen<br><ul><li>16 TiB</li></ul> |
| Maximum aantal IOPS per bestand | Premium<br><ul><li>Maxi maal 8.000</li></ul><br>Standard<br><ul><li>1000</li></ul> | Alle lagen<br><ul><li>Limiet voor Maxi maal volume</li></ul> |
| Maximale door Voer per bestand | Premium<br><ul><li>300 MiB/s (Maxi maal 1 GiB/s met SMB meerdere kanalen)</li></ul><br>Standard<br><ul><li>60-MiB/s</li></ul> | Alle lagen<br><ul><li>Limiet voor Maxi maal volume</li></ul> |
| SMB Multikanaal | Ja ([Preview-versie](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | Yes |
| Latentie | Minimale latentie van één milliseconde (2ms naar 3ms voor kleine i/o) | Minimale latentie van submilliseconden (<1ms voor wille keurige i/o)<br><br>Zie [benchmarks voor prestaties](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux)voor meer informatie. |

Zie [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) en [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)voor meer informatie over de schaalbaarheids-en prestatie doelen.

## <a name="next-steps"></a>Volgende stappen
* [Documentatie voor Azure Files](https://docs.microsoft.com/azure/storage/files/)
* [Documentatie over Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/)
