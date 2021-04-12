---
title: Gegevensbeveiliging in Azure Security Center | Microsoft Docs
description: In dit document wordt uitgelegd hoe gegevens worden beheerd en beveiligd in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596276"
---
# <a name="azure-security-center-data-security"></a>Gegevensbeveiliging in Azure Security Center

Om klanten te helpen bedreigingen te voorkomen, te detecteren en erop te reageren, verzamelt en verwerkt Azure Security Center gegevens over beveiliging, zoals configuratie-informatie, metagegevens, gebeurtenislogboeken en nog veel meer. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service.

In dit artikel wordt uitgelegd hoe gegevens worden beheerd en beveiligd in Security Center.

## <a name="data-sources"></a>Gegevensbronnen
Security Center analyseert gegevens uit de volgende bronnen om inzicht in uw beveiligingsstatus te geven, beveiligingsproblemen te identificeren, oplossingen aan te raden en actieve bedreigingen te detecteren:

- **Azure-services**: gebruikt informatie over de configuratie van de Azure-services die u hebt geïmplementeerd door te communiceren met de resourceprovider van die service.
- **Netwerkverkeer**: gebruikt steekproefgewijs netwerkverkeermetagegevens uit de infrastructuur van Microsoft, zoals bron-/doel-IP/poort, pakketgrootte en netwerkprotocol.
- **Partneroplossingen**: gebruikt beveiligingswaarschuwingen van geïntegreerde partneroplossingen, zoals firewalls en antimalwareoplossingen.
- **Uw computers**: gebruiken configuratie-informatie en informatie over beveiligingsgebeurtenissen, zoals Windows-gebeurtenis- en auditlogboeken en syslog-berichten van uw computers.


## <a name="data-protection"></a>Gegevensbeveiliging

### <a name="data-segregation"></a>Gegevensscheiding
Gegevens worden op een logische manier apart van elkaar gehouden, in elk onderdeel van de service. Alle gegevens worden gemarkeerd per organisatie. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service.

### <a name="data-access"></a>Toegang tot gegevens
Om beveiligingsaanbevelingen te doen en mogelijke beveiligingsrisico's te onderzoeken, hebben medewerkers van Microsoft toegang tot gegevens die zijn verzameld of geanalyseerd door Azure-services, waaronder procesgebeurtenissen en andere artefacten, die onbedoeld klantgegevens of persoonlijke gegevens van uw computers bevatten. 

We voldoen aan de [bijlage voor gegevensbeveiliging voor Microsoft Online Services](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), die stellen dat Microsoft niet de klantgegevens gebruikt of gegevens ervan afleidt voor reclame- of vergelijkbare commerciële doeleinden. We gebruiken klantgegevens alleen indien nodig om u Azure-services te bieden, met inbegrip van doeleinden die compatibel zijn met het leveren van die services. U behoudt alle rechten op de klantgegevens.

### <a name="data-use"></a>Gegevensgebruik
Microsoft gebruikt informatie over patronen en bedreigingen die worden gezien tussen meerdere tenants voor het verbeteren van onze mogelijkheden voor voorkoming en detectie; wij doen dit in overeenstemming met de privacyverplichtingen beschreven in onze [Privacyverklaring](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Gegevensverzameling van computers beheren
Wanneer u Security Center inschakelt in Azure, wordt gegevensverzameling ingeschakeld voor elk van uw Azure-abonnementen. U kunt ook gegevensverzameling inschakelen voor uw abonnementen in Security Center. Wanneer gegevensverzameling is ingeschakeld, levert Security Center de Log Analytics-agent op alle bestaande en ondersteunde virtuele machines in Azure en op nieuwe virtuele machines die worden gemaakt.

De Log Analytics-agent scant op verschillende aan beveiliging gerelateerde configuraties en legt gebeurtenissen vast in [Event Tracing for Windows](/windows/win32/etw/event-tracing-portal)-traceringen (ETW). Bovendien worden door het besturingssysteem gebeurtenislogboekgebeurtenissen gegenereerd tijdens het uitvoeren van de machine. Voorbeelden van dergelijke gegevens zijn: besturingssysteemtype en -versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen, aangemelde gebruiker en tenant-ID. De Log Analytics-agent leest vermeldingen in gebeurtenislogboeken en ETW-traceringen en kopieert deze voor analyse naar uw werkruimte(n). De Log Analytics-agent schakelt ook procesgebeurtenissen en controle van de opdrachtregel in.

Als u geen gebruikmaakt van Azure Defender, kunt u het verzamelen van gegevens van virtuele machines ook uitschakelen in het beveiligingsbeleid. Het verzamelen van gegevens is vereist voor abonnementen die door Azure Defender worden beveiligd. De verzameling van momentopnamen en artefacten voor de VM-schijf is nog steeds ingeschakeld, zelfs als het verzamelen van gegevens is uitgeschakeld.

U kunt de werkruimte en de regio opgeven waar de gegevens die van uw computers worden verzameld, worden opgeslagen. De standaardinstelling is om gegevens op te slaan die zijn verzameld van uw computers in de dichtstbijzijnde werkruimte, zoals wordt weergegeven in de volgende tabel:

| Geografisch gebied van virtuele machine                                      | Geografisch gebied van werkruimte  |
|---------------------------------------------|----------------|
| Verenigde Staten, Brazilië, Zuid-Afrika         | Verenigde Staten  |
| Canada                                      | Canada         |
| Europa (met uitzondering van het Verenigd Koninkrijk)           | Europa         |
| Verenigd Koninkrijk                              | Verenigd Koninkrijk |
| Azië (met uitzondering van India, Japan, Korea, China) | Azië en Stille Oceaan   |
| Korea                                       | Azië en Stille Oceaan   |
| India                                       | India          |
| Japan                                       | Japan          |
| China                                       | China          |
| Australië                                   | Australië      |
|                                             |                |

> [!NOTE]
> Met **Azure Defender voor Storage** worden artefacten regionaal opgeslagen op basis van de locatie van de gerelateerde Azure-resource. Meer informatie vindt u in [Inleiding tot Azure Defender voor Storage](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Gegevensverbruik

Klanten hebben toegang tot aan Security Center gerelateerde gegevens uit de volgende gegevensstromen:


| Streamen                                                                                | Gegevenstypen                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure-activiteitenlogboek](../azure-monitor/essentials/activity-log.md)                       | Alle beveiligingswaarschuwingen, goedgekeurde [Just-In-Time](security-center-just-in-time.md)-toegangsaanvragen van Security Center en alle waarschuwingen die zijn gegenereerd door [besturingselementen voor adaptieve toepassingen](security-center-adaptive-application.md).|
| [Azure Monitor-logboeken](../azure-monitor/data-platform.md)                      | Alle beveiligingswaarschuwingen.                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Beveiligingswaarschuwingen en -aanbevelingen, resultaten van evaluatie van beveiligingsproblemen, informatie over de beveiligingsscores, status van nalevingscontroles, en meer.                                                                       |
| [REST-API voor Azure Security Center](/rest/api/securitycenter/) | Beveiligingswaarschuwingen en -aanbevelingen, en meer.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe gegevens worden beheerd en beveiligd in Azure Security Center. 

Zie [Wat is Azure Security Center?](security-center-introduction.md) voor meer informatie over Azure Security Center.