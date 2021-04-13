---
title: Azure Files Azure NetApp Files | Microsoft Docs
description: Vergelijking van Azure Files en Azure NetApp Files.
author: jeffpatt24
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/19/2021
ms.author: jeffpatt
ms.openlocfilehash: 381fe00e835f0e359a2347372cbc2544a2b211ca
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364223"
---
# <a name="azure-files-and-azure-netapp-files-comparison"></a>Azure Files en Azure NetApp Files vergelijking

Dit artikel bevat een vergelijking van Azure Files en Azure NetApp Files. 

De meeste workloads waarvoor opslag van cloudbestandsopslag is vereist, werken goed op Azure Files of Azure NetApp Files. Bekijk de informatie in dit artikel om te bepalen wat het beste past bij uw workload. Zie de documentatie over Azure Files [en](https://docs.microsoft.com/azure/storage/files/) Azure NetApp Files voor [meer](https://docs.microsoft.com/azure/azure-netapp-files/) informatie.

## <a name="features"></a>Functies

| Categorie | Azure Files | Azure NetApp Files |
|---------|-------------------------|---------|
| Description | [Azure Files](https://azure.microsoft.com/services/storage/files/) is een volledig beheerde, zeer beschikbare service van ondernemingsklasse die is geoptimaliseerd voor workloads voor willekeurige toegang met in-place gegevensupdates.<br><br> Azure Files is gebouwd op hetzelfde Azure-opslagplatform als andere services zoals Azure Blobs. | [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) is een volledig beheerde, zeer beschikbare NAS-service van ondernemingsklasse die de meest veeleisende workloads met hoge prestaties en lage latentie kan verwerken waarvoor geavanceerde mogelijkheden voor gegevensbeheer nodig zijn. Het maakt de migratie mogelijk van workloads, die als 'niet-migratable' worden beschouwd zonder.<br><br>  ANF is gebouwd op de bare-metal van NetApp met het ONTAP-opslagbesturingssysteem dat wordt uitgevoerd in het Azure-datacenter voor een consistente Azure-ervaring en een on-premises, zoals prestaties. |
| Protocollen | Premium<br><ul><li>SMB 2.1, 3.0</li><li>NFS 4.1 (preview)</li><li>REST</li></ul><br>Standard<br><ul><li>SMB 2.1, 3.0</li><li>REST</li></ul><br> Zie beschikbare protocollen voor [bestands delen voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols) | Alle lagen<br><ul><li>SMB 1, 2.x, 3.x</li><li>NFS 3.0, 4.1</li><li>Toegang tot dubbele protocollen (NFSv3/SMB)</li></ul><br> Zie [NFS-,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) [SMB-](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb)of [dual-protocolvolumes](https://docs.microsoft.com/azure/azure-netapp-files/create-volumes-dual-protocol) maken voor meer informatie. |
| Beschikbaarheid in regio's | Premium<br><ul><li>Meer dan 30 regio's</li></ul><br>Standard<br><ul><li>Alle regio's</li></ul><br> Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor meer informatie. | Alle lagen<br><ul><li>Meer dan 25 regio's</li></ul><br> Zie [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor meer informatie. |
| Redundantie | Premium<br><ul><li>LRS</li><li>ZRS</li></ul><br>Standard<br><ul><li>LRS</li><li>ZRS</li><li>GRS</li><li>GZRS</li></ul><br> Zie redundantie [voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy) | Alle lagen<br><ul><li>Ingebouwde lokale ha</li><li>[Replicatie in meerdere regio's](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction)</li></ul> |
| Service-Level Agreement (SLA)<br><br> Sla's voor Azure Files en Azure NetApp Files worden anders berekend. | [SLA voor Azure Files](https://azure.microsoft.com/support/legal/sla/storage/) | [SLA voor Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp) |  
| Identity-Based verificatie en autorisatie | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Houd er rekening mee dat verificatie op basis van identiteit alleen wordt ondersteund bij het gebruik van het SMB-protocol. Zie Veelgestelde vragen voor [meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) | SMB<br><ul><li>Active Directory Domain Services (AD DS)</li><li>Azure Active Directory Domain Services (Azure AD DS)</li></ul><br> Dubbel NFS/SMB-protocol<ul><li>ADDS/LDAP-integratie</li></ul><br>NFSv3/NFSv4.1<ul><li>ADDS/LDAP-integratie (binnenkort)</li><li>Uitgebreide NFS-groepen (binnenkort)</li></ul><br> Zie Veelgestelde vragen voor [meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs) |
| Versleuteling | SMB<br><ul><li>Versleuteling in rust (AES 256) met door de klant of Microsoft beheerde sleutels</li><li>Kerberos-versleuteling met AES 256 of RC4-HMAC</li><li>Versleuteling 'in transit'</li></ul><br>NFS<br><ul><li>Versleuteling-at-rest (AES 256) met door de klant of Microsoft beheerde sleutels</li></ul><br>REST<br><ul><li>Versleuteling in rust (AES 256) met door de klant of Microsoft beheerde sleutels</li><li>Versleuteling 'in transit'</li></ul><br> Zie beveiliging voor [meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-compare-protocols#security) | Alle protocollen<br><ul><li>Versleuteling in rust (AES 256) met door Microsoft beheerde sleutels </li></ul><br>NFS 4.1<ul><li>Versleuteling 'in transit' met Behulp van Kerberos met AES 256</li></ul><br> Zie Veelgestelde vragen over [beveiliging voor meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#security-faqs) |
| Opties voor toegang | <ul><li>Internet</li><li>VNet-toegang beveiligen</li><li>VPN Gateway</li><li>ExpressRoute</li><li>Azure File Sync</li></ul><br> Zie Netwerkoverwegingen [voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) | <ul><li>VNet-toegang beveiligen</li><li>VPN Gateway</li><li>ExpressRoute</li><li>[Globale bestandscache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[HPC Cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-overview)</li></ul><br> Zie Netwerkoverwegingen [voor meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) |
| Gegevensbeveiliging  | <ul><li>Incrementele momentopnamen</li><li>Bestands-/directorygebruikers herstellen zelf</li><li>Herstellen naar nieuwe locatie</li><li>In-place terugdraaien</li><li>Zacht verwijderen op shareniveau</li><li>Azure Backup integratie</li></ul><br> Zie voor meer informatie [Azure Files mogelijkheden voor gegevensbeveiliging verbetert.](https://azure.microsoft.com/blog/azure-files-enhances-data-protection-capabilities/) | <ul><li>Momentopnamen (255/volume)</li><li>Bestands-/mapgebruiker zelf herstellen</li><li>Herstellen naar nieuw volume</li><li>In-place terugdraaien</li><li>[Replicatie tussen regio's](https://docs.microsoft.com/azure/azure-netapp-files/cross-region-replication-introduction) (openbare preview)</li></ul><br> Zie How Azure NetApp Files snapshots work (Hoe [Azure NetApp Files werken) voor meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/snapshots-introduction) |
| Hulpprogramma's voor migratie  | <ul><li>Azure Data Box</li><li>Azure File Sync</li><li>Storage Migration Service</li><li>AzCopy</li><li>Robocopy</li></ul><br> Zie Migreren naar Azure-bestands [shares voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-migration-overview) | <ul><li>[Globale bestandscache](https://cloud.netapp.com/global-file-cache/azure)</li><li>[CloudSync,](https://cloud.netapp.com/cloud-sync-service) [XCP](https://xcp.netapp.com/)</li><li>Storage Migration Service</li><li>AzCopy</li><li>Robocopy</li><li>Op basis van toepassingen (bijvoorbeeld HSR, Data Guard, AOAG)</li></ul> |
| Lagen | <ul><li>Premium</li><li>Geoptimaliseerd voor transacties</li><li>Dynamisch</li><li>Statisch</li></ul><br> Zie Opslaglagen [voor meer informatie.](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers) | <ul><li>Ultra</li><li>Premium</li><li>Standard</li></ul><br> Alle lagen bieden een minimale latentie van minder dan ms.<br><br> Zie Serviceniveaus [](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) en Prestatieoverwegingen [voor meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-considerations) |
| Prijzen | [Azure Files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) | [Azure NetApp Files prijzen](https://azure.microsoft.com/pricing/details/netapp/) |

## <a name="scalability-and-performance"></a>Schaalbaarheid en prestaties

| Categorie | Azure Files | Azure NetApp Files |
|---------|---------|---------|
| Minimale share/volumegrootte | Premium<br><ul><li>100 GiB</li></ul><br>Standard<br><ul><li>1 GiB</li></ul> | Alle lagen<br><ul><li>100 GiB (minimale capaciteitspoolgrootte: 4 TiB)</li></ul> |
| Maximale share/volumegrootte | Premium<br><ul><li>100 TiB</li></ul><br>Standard<br><ul><li>100 TiB</li></ul> | Alle lagen<br><ul><li>100 TiB (limiet van 500 TiB-capaciteitspools)</li></ul><br>Maximaal 12,5 PiB per Azure NetApp-account. |
| Maximale IOPS voor share/volume | Premium<br><ul><li>Maximaal 100.000</li></ul><br>Standard<br><ul><li>Maximaal 10.000</li></ul> | Ultra en Premium<br><ul><li>Maximaal 450.000 </li></ul><br>Standard<br><ul><li>Maximaal 320.000</li></ul> |
| Maximale share-/volumedoorvoer | Premium<br><ul><li>Maximaal 10 GiB/s</li></ul><br>Standard<br><ul><li>Maximaal 300 MiB/s</li></ul> | Ultra en Premium<br><ul><li>Maximaal 4,5 GiB/s</li></ul><br>Standard<br><ul><li>Maximaal 3,2GiB/s</li></ul> |
| Maximale bestandsgrootte | Premium<br><ul><li>4 TiB</li></ul><br>Standard<br><ul><li>1 TiB</li></ul> | Alle lagen<br><ul><li>16 TiB</li></ul> |
| Maximum aantal IOP's per bestand | Premium<br><ul><li>Maximaal 8000</li></ul><br>Standard<br><ul><li>1000</li></ul> | Alle lagen<br><ul><li>Maximaal volumelimiet</li></ul> |
| Maximale doorvoer per bestand | Premium<br><ul><li>300 MiB/s (maximaal 1 GiB/s met SMB meerdere kanalen)</li></ul><br>Standard<br><ul><li>60 MiB/s</li></ul> | Alle lagen<br><ul><li>Maximaal volumelimiet</li></ul> |
| SMB Multikanaal | Ja ([Preview](https://docs.microsoft.com/azure/storage/files/storage-files-smb-multichannel-performance)) | Yes |
| Latentie | Minimale latentie van één milliseconde (2 ms tot 3 ms voor kleine IO) | Minimale latentie van minder dan milliseconden (<1 ms voor willekeurige I/O)<br><br>Zie [prestatiebenchmarks voor meer informatie.](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) |

Zie voor meer informatie over schaalbaarheids- en prestatiedoelen [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) en [Azure NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)

## <a name="next-steps"></a>Volgende stappen
* [Documentatie voor Azure Files](https://docs.microsoft.com/azure/storage/files/)
* [Azure NetApp Files documentatie](https://docs.microsoft.com/azure/azure-netapp-files/)
