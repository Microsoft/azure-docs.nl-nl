---
title: Latentie in Blob-opslag - Azure Storage
description: Begrijp en meet latentie voor Blob-opslagbewerkingen en leer hoe u uw Blob-opslagtoepassingen kunt ontwerpen met een lage latentie.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75749725"
---
# <a name="latency-in-blob-storage"></a>Latentie in Blob-opslag

Latentie, ook wel reactietijd genoemd, is de tijd die een toepassing moet wachten voordat een aanvraag is voltooid. Latentie kan de prestaties van een toepassing direct beïnvloeden. Lage latentie is vaak belangrijk voor scenario's met mensen in de lus, zoals het uitvoeren van creditcardtransacties of het laden van webpagina's. Voor systemen die op hoge snelheid binnenkomende gebeurtenissen moeten verwerken, zoals de registratie van telemetriegegevens of IoT-gebeurtenissen, is ook een lage latentie vereist. In dit artikel wordt beschreven wat latentie voor bewerkingen op blok-blobs inhoudt en hoe u dit meet. Ook wordt beschreven hoe u uw toepassingen kunt ontwerpen voor een lage latentie.

Azure Storage biedt twee verschillende prestatieopties voor blok-blobs: premium en standaard. Premium-blok-blobs bieden aanzienlijk lagere en meer consistente latentie dan standaard-blok-blobs via hoogwaardige SSD-schijven. Zie **Blok-blob-opslag met premium-prestaties** in [Azure Blob-opslag: dynamische, statische en archieftoegangslagen](storage-blob-storage-tiers.md) voor meer informatie.

## <a name="about-azure-storage-latency"></a>Info over Azure Storage-latentie

Azure Storage-latentie is gerelateerd aan aanvraagsnelheden voor Azure Storage bewerkingen. Aanvraagsnelheden worden ook wel invoer-/uitvoerbewerkingen per seconde (IOPS) genoemd.

Als u de aanvraagsnelheid wilt berekenen, bepaalt u eerst hoe lang het voltooien van elke aanvraag duurt en vervolgens berekent u hoeveel aanvragen per seconde kunnen worden verwerkt. Stel bijvoorbeeld dat een aanvraag in 50 milliseconden (ms) wordt voltooid. Een toepassing waarvoor één thread met één openstaande lees- of schrijfbewerking wordt gebruikt, moet 20 IOPS (1 seconde of 1000 ms/50 ms per aanvraag) kunnen behalen. Als het aantal threads wordt verdubbeld naar twee, zou de toepassing in theorie 40 IOPS moeten kunnen behalen. Als de openstaande asynchrone lees- of schrijfbewerkingen voor elke thread worden verdubbeld naar twee, moet de toepassing 80 IOPS kunnen behalen.

In de praktijk gaan de aanvraagsnelheden niet altijd zo lineair omhoog vanwege overhead in de client door taakplanning, het wisselen van context enzovoort. Aan de kant van de service kan de latentie variëren als gevolg van de druk op het Azure Storage-systeem, verschillen in de gebruikte opslagmedia, interferentie van andere workloads, onderhoudstaken en andere factoren. Ten slotte kan de netwerkverbinding tussen de client en de server van invloed zijn op de Azure Storage-latentie door congestie, omleidingen of andere onderbrekingen.

De Azure Storage-bandbreedte, ook wel doorvoer genoemd, is gerelateerd aan de aanvraagsnelheid en kan worden berekend door de aanvraagsnelheid (IOPS) te vermenigvuldigen met de aanvraaggrootte. Stel bijvoorbeeld dat er 160 aanvragen per seconde zijn, waarbij elke 256 KiB gegevens resulteert in een doorvoer van 40.960 KiB per seconde of 40 MiB per seconde.

## <a name="latency-metrics-for-block-blobs"></a>Metrische latentiegegevens voor blok-blobs

Azure Storage voorziet in twee soorten metrische latentiegegevens voor blok-blobs. Deze gegevens kunnen worden bekeken in Azure Portal:

- Met **E2E-latentie (end-to-end)** wordt het interval gemeten vanaf het moment waarop Azure Storage het eerste pakket van de aanvraag ontvangt tot het moment waarop Azure Storage een bevestiging van de client ontvangt voor het laatste pakket van de reactie.

- Met **de latentie van de server** wordt het interval gemeten vanaf het moment waarop Azure Storage het laatste pakket van de aanvraag ontvangt tot het moment waarop het eerste pakket van de reactie wordt geretourneerd vanuit Azure Storage.

In de volgende afbeelding ziet u het **gemiddelde succes voor E2E-latentie** en het **gemiddelde succes voor de serverlatentie** voor een voorbeeldworkload waarmee de `Get Blob`-bewerking wordt aangeroepen:

![Schermopname met metrische latentiegegevens voor de bewerking Blob ophalen](media/storage-blobs-latency/latency-metrics-get-blob.png)

Onder normale omstandigheden liggen de end-to-end latentie en serverlatentie dicht bij elkaar. U ziet dit ook bij de voorbeeldworkload in de afbeelding.

Als u de metrische gegevens van uw end-to-end en serverlatentie bekijkt en ziet dat de end-to-end latentie aanzienlijk hoger is dan de serverlatentie, gaat u na wat de bron van de extra latentie kan zien en onderneemt u actie.

Als uw end-to-end en serverlatentie ongeveer hetzelfde zijn, maar u een lagere latentie wilt, kunt u overwegen om te migreren naar premium-blok-blob-opslag.

## <a name="factors-influencing-latency"></a>Factoren die van invloed zijn op de latentie

Latentie wordt het meest beïnvloed door de grootte van de bewerking. Het duurt langer om grotere bewerkingen te voltooien door de hoeveelheid gegevens die via het netwerk moet worden overgedragen en door Azure Storage moet worden verwerkt.

In het volgende diagram ziet u de totale tijd voor bewerkingen van verschillende grootten. Voor kleine hoeveelheden gegevens wordt het latentie-interval hoofdzakelijk veroorzaakt door het verwerken van de aanvraag in plaats van de gegevensoverdracht. Het latentie-interval neemt slechts een klein beetje toe als de grootte van de bewerking toeneemt (gemarkeerd met 1 in het onderstaande diagram). Naarmate de grootte van de bewerking verder toeneemt, wordt er meer tijd besteed aan het overdragen van gegevens, zodat het totale latentie-interval wordt gesplitst tussen de verwerking van de aanvraag en de gegevensoverdracht (gemarkeerd met 2 in het onderstaande diagram). Bij grote bewerkingen wordt het latentie-interval hoofdzakelijk veroorzaakt door de gegevensoverdracht en is het verwerken van de aanvraag niet zo belangrijk (gemarkeerd met 3 in het onderstaande diagram).

![Schermopname met de totale bewerkingstijd per bewerkingsgrootte](media/storage-blobs-latency/operation-time-size-chart.png)

Clientconfiguratiefactoren zoals gelijktijdigheid en threading beïnvloeden de latentie ook. De totale doorvoer is afhankelijk van het aantal opslagaanvragen dat op een bepaald moment wordt uitgevoerd en van hoe uw toepassing threading verwerkt. Clientresources, zoals CPU, geheugen, lokale opslag en netwerkinterfaces, beïnvloeden de latentie ook.

Voor de verwerking van Azure Storage-aanvragen zijn vereist CPU- en geheugenresources van de client vereist. Als de client onder druk staat vanwege een virtuele machine die niet krachtig genoeg is, of een runaway-proces in het systeem, zijn er minder resources beschikbaar voor het verwerken van Azure Storage-aanvragen. Conflicten of onvoldoende clientresources leiden tot een toename van de end-to-end latentie zonder dat de serverlatentie toeneemt, waardoor de metrische gegevens van beide steeds verder uit elkaar komen te liggen.

Net zo belangrijk is de netwerkinterface en netwerk-pipe tussen de client en Azure Storage. Alleen al de fysieke afstand kan een aanzienlijke factor zijn, bijvoorbeeld als een client-VM zich in een andere Azure-regio of on-premises bevindt. Andere factoren, zoals netwerk-hops, ISP-routering en internetstatus, kunnen van invloed zijn op de algehele opslaglatentie.

Als u de latentie wilt beoordelen, bepaalt u eerst de metrische basislijngegevens voor uw scenario. Met metrische basislijngegevens krijgt u de verwachte end-to-end en serverlatentie in de context van uw toepassingsomgeving, afhankelijk van uw workloadprofiel, toepassingsconfiguratie-instellingen, clientresources, netwerk-pipe en andere factoren. Wanneer u metrische basislijngegevens hebt, kunt u op eenvoudige wijze abnormale en normale omstandigheden identificeren. U kunt met de metrische basislijngegevens ook bekijken wat de effecten zijn als parameters worden gewijzigd (bijvoorbeeld de toepassingsconfiguratie of VM-grootten).

## <a name="next-steps"></a>Volgende stappen

- [Schaalbaarheids- en prestatiedoelen voor Blob-opslag](scalability-targets.md)
- [Controlelijst voor prestaties en schaalbaarheid voor Blob-opslag](storage-performance-checklist.md)
