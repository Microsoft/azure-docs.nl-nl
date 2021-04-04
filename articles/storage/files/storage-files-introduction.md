---
title: Inleiding tot Azure Files | Microsoft Docs
description: Een overzicht van Azure Files, een service waarmee u netwerkbestandsshares kunt maken en gebruiken in de cloud volgens het industriestandaard SMB-protocol.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070130"
---
# <a name="what-is-azure-files"></a>Wat is Azure Files?
Met Azure Files beschikt u over volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het toonaangevende protocol [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) of [Network File System (NFS)](https://en.wikipedia.org/wiki/Network_File_System). Azure-bestandsshares kunnen gelijktijdig worden gekoppeld door on-premises of cloudimplementaties. SMB-bestandsshares van Azure Files zijn toegankelijk via Windows-, Linux- en macOS-clients. NFS-bestandsshares van Azure Files zijn toegankelijk via Linux- of macOS-clients. Bovendien kunnen Azure-bestandsshares in de cache worden opgeslagen op Windows-servers met Azure File Sync voor snelle toegang tot locaties waar de gegevens worden gebruikt.

## <a name="videos"></a>Video's
| Inleiding tot Azure File Sync | Azure Files met Sync (Ignite 2019)  |
|-|-|
| [![Screencast van de video Inleiding tot Azure File Sync - klik om af te spelen.](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Screencast van de presentatie van Azure Files met Sync - klik om af te spelen.](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

De volgende video's behandelen enkele algemene gebruiksscenario's van Azure Files:
* [Uw bestandsserver vervangen door een serverloze Azure-bestandsshare](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [Aan de slag met FSLogix-profielcontainers op Azure Files in Windows Virtual Desktop met gebruikmaking van AD-verificatie](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Waarom Azure Files handig is
Azure-bestandsshares kunnen worden gebruikt voor het volgende:

* **Ter vervanging van of ter aanvulling op on-premises bestandsservers**:  
    Azure Files kan worden gebruikt om bestandsshares op traditionele on-premises bestandsservers of NAS-apparaten volledig te vervangen of aan te vullen. Populaire besturingssystemen, zoals Windows, macOS en Linux kunnen een Azure-bestandsshare rechtstreeks koppelen, waar ter wereld deze zich ook bevinden. SMB-bestandsshares in Azure Files kunnen ook met Azure File Sync worden gerepliceerd naar Windows-Servers, on-premises of in de cloud, voor een krachtige en gedistribueerde opslag in de cache van gegevens waar deze worden gebruikt. Met de recente versie van [AD-verificatie voor Azure Files](storage-files-active-directory-overview.md), kunnen SMB-bestandsshares in Azure Files blijven werken met on-premises AD-hosting voor toegangsbeheer. 

* **'Lift- en shift'-toepassingen**:  
    Met Azure Files kunnen toepassingen die een bestandsshare voor het opslaan van gegevens uit een bestandstoepassing verwachten eenvoudig worden 'gelift en geshift' naar de cloud. Azure Files biedt zowel het 'klassieke' lift- en shift-scenario, waarbij zowel de toepassing als de gegevens worden verplaatst naar Azure, als het 'hybride' lift- en shift-scenario, waarin de toepassingsgegevens worden verplaatst naar Azure Files en de toepassing nog steeds on-premises wordt uitgevoerd. 

* **Cloudontwikkeling vereenvoudigen**:  
    Azure Files kan ook op verschillende manieren worden gebruikt voor het vereenvoudigen van nieuwe cloudontwikkelingsprojecten. Bijvoorbeeld:
    * **Gedeelde toepassingsinstellingen**:  
        Een algemeen patroon voor gedistribueerde toepassingen is de aanwezigheid van configuratiebestanden op een centrale locatie waar ze toegankelijk zijn vanuit veel andere toepassingsexemplaren. Toepassingsexemplaren kunnen hun configuratie laden via de File REST-API, en mensen kunnen hier indien nodig toegang toe krijgen door de SMB-share lokaal te koppelen.

    * **Diagnostische gegevens delen**:  
        Een Azure-bestandsshare is een handige locatie voor cloudtoepassingen voor het wegschrijven van hun logboeken, metrische gegevens en crashdumps. Logboeken kunnen door de toepassingsexemplaren worden geschreven via de File REST-API en ontwikkelaars kunnen er toegang toe krijgen door de bestandsshare te koppelen met hun lokale computer. Dit biedt veel flexibiliteit, aangezien ontwikkelaars kunnen ontwikkelen in de cloud zonder hun bestaande tooling waarmee ze bekend zijn en graag werken te hoeven opgeven.

    * **Ontwikkelen/testen/fouten opsporen**:  
        Wanneer ontwikkelaars of beheerders op virtuele machines in de cloud werken, hebben ze vaak een set hulpprogramma's nodig. Het kopiëren van deze hulpprogramma's naar elke VM kan een tijdrovend proces zijn. Door een Azure-bestandsshare lokaal te koppelen aan de virtuele machines, kunnen de ontwikkelaar en beheerder indien nodig snel toegang krijgen tot hun hulpprogramma's.
* **Containerisatie**:  
    Azure-bestandsshares kunnen worden gebruikt als permanente volumes voor stateful-containers. Containers bieden een 'build once, run anywhere'-functionaliteit, waarmee ontwikkelaars sneller kunnen innoveren. Voor de containers die bij elke start toegang krijgen tot onbewerkte gegevens, is een gedeeld bestandssysteem vereist om deze containers toegang te geven tot het bestandssysteem, ongeacht het exemplaar waarop deze worden uitgevoerd.

## <a name="key-benefits"></a>Belangrijkste voordelen
* **Gedeelde toegang**. Azure-bestandsshares bieden ondersteuning voor de toonaangevende SMB- en NFS-protocollen, wat betekent dat u uw lokale bestandsshares naadloos kunt vervangen door de Azure-bestandsshares zonder dat u zich zorgen hoeft te maken over de compatibiliteit van toepassingen. Het delen van een bestandssysteem op meerdere machines, toepassingen/exemplaren is een belangrijk voordeel van Azure Files voor toepassingen die gegevens moeten kunnen delen. 
* **Volledig beheerd**. Azure-bestandsshares kunnen worden gemaakt zonder de noodzaak om hardware of een besturingssysteem te beheren. Dit betekent dat u geen essentiële beveiligingsupgrades hoeft te installeren op het serverbesturingssysteem of defecte harde schijven moet vervangen.
* **Scripts en hulpprogramma’s**. PowerShell-cmdlets en de Azure-CLI zijn beschikbaar voor het maken, koppelen en beheren van Azure-bestandsshares als onderdeel van het beheer van Azure-toepassingen. U kunt Azure-bestandsshares maken en beheren met behulp van Azure Portal en Azure Storage Explorer. 
* **Flexibiliteit**. Azure Files is vanaf de grond opgebouwd om altijd beschikbaar te zijn. Het vervangen van on-premises bestandsshares door Azure Files betekent dat u niet langer problemen ondervindt door lokale stroomstoringen of netwerkstoringen. 
* **Vertrouwde programmeerbaarheid**. Toepassingen die worden uitgevoerd in Azure hebben toegang tot de gegevens in de share via [System I/O-API's](/dotnet/api/system.io.file). Dat betekent dat ontwikkelaars bestaande code en vaardigheden kunnen inzetten voor het migreren van bestaande toepassingen. Naast System IO-API's kunt u [Azure Storage-clientbibliotheken](/previous-versions/azure/dn261237(v=azure.100)) of de [REST-API van Azure Storage](/rest/api/storageservices/file-service-rest-api) gebruiken.

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over de beschikbare protocollen voor bestandsshares](storage-files-compare-protocols.md)
* [Azure-bestandsshare maken](storage-how-to-create-file-share.md)
* [Een SMB-share verbinden en koppelen in Windows](storage-how-to-use-files-windows.md)
* [Een SMB-share verbinden en koppelen in Linux](storage-how-to-use-files-linux.md)
* [Een SMB-share verbinden en koppelen in macOS](storage-how-to-use-files-mac.md)
* [Een NFS-share maken](storage-files-how-to-create-nfs-shares.md)
