---
title: NFS Blob Storage gebruiken met de Azure HPC-cache
description: Beschrijft procedures en beperkingen bij het gebruik van ADLS-NFS Blob Storage met Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259962"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>Met NFS gekoppelde Blob-opslag (PREVIEW) met Azure HPC cache gebruiken

Vanaf maart 2021 kunt u met NFS gekoppelde BLOB-containers met Azure HPC-cache gebruiken. Meer informatie over de [ondersteuning voor NFS 3,0-protocollen vindt u in Azure Blob-opslag](../storage/blobs/network-file-system-protocol-support.md) op de documentatie site van de Blob-opslag.

> [!NOTE]
> Ondersteuning voor NFS 3,0-protocol in Azure Blob-opslag is in Preview en mag niet worden gebruikt in productie omgevingen. Controleer op updates en meer informatie in de [ondersteunings documentatie](../storage/blobs/network-file-system-protocol-support.md)van het NFS-protocol.

Azure HPC cache maakt gebruik van met NFS ingeschakelde Blob-opslag in het ADLS-NFS-opslag doel type. Deze opslag doelen zijn vergelijkbaar met normale NFS-opslag doelen, maar hebben ook een overlap ping met gewone Azure Blob-doelen.

In dit artikel worden strategieën en beperkingen uitgelegd die u moet begrijpen wanneer u ADLS-NFS-opslag doelen gebruikt.

Lees ook de documentatie over NFS-blobs, met name deze secties die compatibele en niet-compatibele scenario's beschrijven:

* [Overzicht van de functies](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [Functies die nog niet worden ondersteund](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [Prestatieoverwegingen](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>Consistentie vereisten begrijpen

HPC cache vereist sterke consistentie voor ADLS-NFS-opslag doelen. Standaard worden door NFS ingeschakelde Blob-opslag geen meta gegevens van het bestand bijgewerkt, waardoor HPC-cache geen nauw keurige bestands versies kan worden vergeleken.

Om dit verschil te omzeilen, wordt NFS-kenmerk caching automatisch door Azure HPC-cache uitgeschakeld voor een BLOB-container die is ingeschakeld voor NFS als opslag doel.

Deze instelling blijft behouden voor de levens duur van de container, zelfs als u deze verwijdert uit de cache.

## <a name="preload-data-with-nfs-protocol"></a>Gegevens vooraf laden met NFS-protocol

In een BLOB-container met NFS *kan een bestand alleen worden bewerkt door het protocol dat wordt gebruikt toen het werd gemaakt*. Dat wil zeggen, als u de Azure REST API gebruikt om een container te vullen, kunt u NFS niet gebruiken om deze bestanden bij te werken. Omdat Azure HPC cache alleen gebruikmaakt van NFS, kunnen er geen bestanden worden bewerkt die zijn gemaakt met de Azure-REST API.

Het is geen probleem voor de cache als uw container leeg is of als de bestanden zijn gemaakt met behulp van NFS.

Als de bestanden in uw container zijn gemaakt met de REST API van Azure Blob in plaats van NFS, wordt de Azure HPC-cache beperkt tot deze acties voor de oorspronkelijke bestanden:

* Het bestand in een map weer geven.
* Lees het bestand (en bewaar het in het cache geheugen voor volgende Lees bewerkingen).
* Verwijder het bestand.
* Het bestand leegmaken (afgekapt tot 0).
* Sla een kopie van het bestand op. De kopie is gemarkeerd als een door NFS gemaakt bestand en kan worden bewerkt met behulp van NFS.

De inhoud van een bestand dat is gemaakt met REST **kan niet worden** bewerkt met de Azure HPC-cache. Dit betekent dat een gewijzigd bestand niet kan worden opgeslagen van een client naar het doel van de opslag.

Het is belang rijk om deze beperking te begrijpen, omdat dit kan leiden tot problemen met de integriteit van gegevens als u gebruiks modellen voor lezen/schrijven in de cache gebruikt voor bestanden die niet met NFS zijn gemaakt.

> [!TIP]
> Meer informatie over lees-en schrijf cache in inzicht in de [gebruiks modellen](cache-usage-models.md)van de cache.

### <a name="write-caching-scenarios"></a>Cache scenario's schrijven

Deze cache gebruiks modellen omvatten schrijf cache:

* **Meer dan 15% schrijf bewerkingen**
* **Meer dan 15% schrijf bewerkingen, waarbij de back-upserver elke 30 seconden wordt gecontroleerd op wijzigingen**
* **Meer dan 15% schrijf bewerkingen, waarbij de back-upserver elke 60 seconden wordt gecontroleerd op wijzigingen.**
* **Meer dan 15% schrijf bewerkingen, schrijf elke 30 seconden een back-up naar de server**

Gebruik deze gebruiks modellen alleen voor het bewerken van bestanden die zijn gemaakt met NFS.

Als u schrijf cache gebruikt voor bestanden die door REST zijn gemaakt, kunnen de wijzigingen in het bestand verloren gaan. Dit komt doordat de cache geen bestands bewerkingen meer opslaat in de opslag container.

Hier vindt u informatie over het opslaan van de schrijf bewerkingen naar bestanden met een andere risico:

1. De cache accepteert bewerkingen van clients en retourneert een geslaagd bericht bij elke wijziging.
1. De cache houdt het gewijzigde bestand in de opslag ruimte en wacht op extra wijzigingen.
1. Nadat een tijd is verstreken, probeert de cache het gewijzigde bestand op te slaan in de back-end-container. Op dit moment wordt er een fout bericht weer gegeven omdat het probeert te schrijven naar een bestand dat is gemaakt met NFS.

   Het is te laat om aan te geven dat de client computer niet is geaccepteerd en dat de cache geen manier heeft om het oorspronkelijke bestand bij te werken. De wijzigingen van clients gaan dus verloren.

### <a name="read-caching-scenarios"></a>Cache scenario's lezen

Lees cache scenario's zijn geschikt voor bestanden die zijn gemaakt met NFS of Azure Blob REST API.

Deze gebruiks modellen gebruiken alleen lees cache:

* **Zware, incidentele schrijf bewerkingen lezen**
* **Clients schrijven naar het NFS-doel, waarbij de cache wordt omzeild**
* **Lees zo lang de back-upserver om de 3 uur wordt gecontroleerd**

U kunt deze gebruiks modellen gebruiken met bestanden die zijn gemaakt door REST API of NFS. NFS-schrijf bewerkingen die vanuit een client naar de back-end-container worden verzonden, zullen nog steeds mislukken, maar worden onmiddellijk uitgevoerd en er wordt een fout bericht naar de client geretourneerd.

Een werk stroom voor het lezen van een cache kan nog steeds bestands wijzigingen omvatten, zolang deze niet in de cache zijn opgeslagen. Clients kunnen bijvoorbeeld bestanden uit de container openen, maar hun wijzigingen terugschrijven als een nieuw bestand, of ze kunnen gewijzigde bestanden opslaan op een andere locatie.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Beperkingen van Network Lock Manager (NLM) herkennen

BLOB-containers die zijn ingeschakeld voor NFS bieden geen ondersteuning voor Network Lock Manager (NLM). Dit is een algemeen gebruikt NFS-protocol om bestanden te beveiligen tegen conflicten.

Als uw NFS-werk stroom oorspronkelijk is geschreven voor hardwarematige opslag systemen, bevatten uw client toepassingen mogelijk NLM-aanvragen. Als u deze beperking wilt omzeilen tijdens het verplaatsen van uw proces naar Blob-opslag met NFS, moet u ervoor zorgen dat uw clients NLM uitschakelen wanneer ze de cache koppelen.

Als u NLM wilt uitschakelen, gebruikt u de optie ``-o nolock`` in de opdracht van de client ``mount`` . Met deze optie voor komt u dat de clients NLM-vergren delingen aanvragen en fouten in antwoord ontvangen.

In enkele gevallen erkent Azure HPC cache zelf NLM-aanvragen van de client. Het cache gebruiks model met de naam **Read zware, incidentele schrijf bewerkingen** erkent NLM-aanvragen namens de back-end-opslag. Dit systeem voor komt fouten op de client, maar er wordt niet daad werkelijk een vergren deling gemaakt op de ADLS-NFS-container.

Als u het gebruiks model van een ADLS-NFS-opslag doel overschakelt van of naar een **zware Lees bewerking**, moet u alle clients opnieuw koppelen met behulp van de ``nolock`` optie.

Meer informatie over NLM, HPC cache en gebruiks modellen is opgenomen in inzicht in de [gebruiks modellen](cache-usage-models.md#know-when-to-remount-clients-for-nlm)van de cache.

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Schrijf bewerkingen naar met NFS ingeschakelde containers stroom lijnen met HPC-cache

Met behulp van de Azure HPC-cache kunt u de prestaties verbeteren in een werk belasting waarbij schrijf wijzigingen worden opgenomen in een ADLS-NFS-opslag doel.

> [!NOTE]
> U moet NFS gebruiken om uw ADLS-NFS-opslag container te vullen als u de bestanden wilt wijzigen via de Azure HPC-cache.

Een van de beperkingen die worden beschreven in het artikel over de [prestatie overwegingen](../storage/blobs/network-file-system-protocol-support-performance.md) voor NFS-prestaties, is dat ADLS-NFS-opslag niet erg efficiënt is bij het overschrijven van bestaande bestanden. Als u gebruikmaakt van een Azure HPC-cache met met NFS gekoppelde Blob-opslag, wordt door de cache terugkerende herschrijf bewerkingen afhandeld wanneer clients een actief bestand wijzigen. De latentie van het schrijven van een bestand naar de back-end-container is verborgen voor de-clients.

Houd bij het [vooraf laden van gegevens met NFS-protocol](#preload-data-with-nfs-protocol)de hierboven beschreven beperkingen in acht.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [ADLS-opslag doel vereisten voor NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)
* Een [Blob Storage-account](../storage/blobs/network-file-system-protocol-support-how-to.md) maken dat is ingeschakeld voor NFS
