---
title: Inleiding tot Azure File Sync | Microsoft Docs
description: Een overzicht van Azure File Sync, een service waarmee u netwerkbestands shares in de cloud kunt maken en gebruiken met behulp van het industriestandaard SMB-protocol.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797658"
---
# <a name="what-is-azure-file-sync"></a>Wat is Azure File Sync?
Azure File Sync kunt u de bestands shares van uw organisatie centraliseren in Azure Files, terwijl de flexibiliteit, prestaties en compatibiliteit van een Windows-bestandsserver behouden blijven. Hoewel sommige gebruikers ervoor kiezen om een volledige kopie van hun gegevens lokaal te bewaren, heeft Azure File Sync ook de mogelijkheid om Windows Server te transformeren in een snelle cache van uw Azure-bestands share. U kunt elk protocol dat beschikbaar is in Windows Server, inclusief SMB, NFS en FTPS, gebruiken voor lokale toegang tot uw gegevens. U kunt zoveel caches hebben als u nodig hebt over de hele wereld.   

## <a name="videos"></a>Video's
| Inleiding tot Azure File Sync | Azure Files met Sync (Ignite 2019)  |
|-|-|
| [![Screencast van de video Inleiding tot Azure File Sync - klik om af te spelen.](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast van de presentatie van Azure Files met Sync - klik om af te spelen.](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Voordelen van Azure File Sync

### <a name="cloud-tiering"></a>Cloudopslaglagen
Als opslag in cloudlagen is ingeschakeld, worden uw meest gebruikte bestanden in de cache opgeslagen op uw lokale server en worden uw minst gebruikte bestanden in een laag opgeslagen in de cloud. U kunt bepalen hoeveel lokale schijfruimte wordt gebruikt voor caching. Gelaagde bestanden kunnen snel op aanvraag worden ingeroepen, waardoor de ervaring naadloos wordt en u kunt besparen op kosten, omdat u slechts een fractie van uw gegevens on-premises hoeft op te slaan. Zie Overzicht van cloudopslaglagen voor meer informatie over [opslag in cloudlagen.](file-sync-cloud-tiering-overview.md) 

### <a name="multi-site-access-and-sync"></a>Toegang en synchronisatie voor meerdere site
Azure File Sync is ideaal voor scenario's met gedistribueerde toegang. Voor elk van uw kantoren kunt u een lokale Windows Server inrichten als onderdeel van uw Azure File Sync implementatie. Wijzigingen in een server in het ene kantoor worden automatisch gesynchroniseerd met de servers in alle andere kantoren.

### <a name="business-continuity-and-disaster-recovery"></a>Bedrijfscontinuïteit en herstel na noodgevallen
Azure File Sync wordt mogelijk gemaakt door Azure Files, dat verschillende redundantieopties biedt voor opslag met hoge beschikbaarheid. Omdat Azure robuuste kopieën van uw gegevens bevat, wordt uw lokale server een eenmalig cachingapparaat en kan het herstellen van een mislukte server worden uitgevoerd door een nieuwe server toe te voegen aan uw Azure File Sync-implementatie. In plaats van terug te zetten vanuit een lokale back-up, kunt u een andere Windows Server inrichten, de Azure File Sync-agent installeren en deze vervolgens toevoegen aan uw Azure File Sync implementatie. Azure File Sync uw bestandsnaamruimte downloadt voordat u gegevens downloadt, zodat uw server zo snel mogelijk actief en werkend kan zijn. Voor nog sneller herstel kunt u een warme stand-by-server hebben als onderdeel van uw implementatie, of u kunt Azure File Sync met Windows Clustering.

## <a name="cloud-side-backup"></a>Back-up aan de cloudzijde
Verminder uw on-premises back-upuitgaven door gecentraliseerde back-ups in de cloud te maken met behulp Azure Backup. Azure Files SMB-shares systeemeigen momentopnamemogelijkheden hebben en het proces kan worden geautomatiseerd met behulp van Azure Backup om uw back-ups te plannen en de retentie te beheren. Azure Backup kan ook worden geïntegreerd met uw on-premises servers, dus wanneer u naar de cloud herstelt, worden deze wijzigingen automatisch gedownload op uw Windows-servers.  

## <a name="next-steps"></a>Volgende stappen
* [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
* [Overzicht van cloudopslaglagen](file-sync-cloud-tiering-overview.md)
* [Azure File Sync bewaken](file-sync-monitoring.md)