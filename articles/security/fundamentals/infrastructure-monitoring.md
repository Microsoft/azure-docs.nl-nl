---
title: Bewaking van Azure-infra structuur
description: Meer informatie over infrastructuur bewakings aspecten van het productie netwerk van Azure, zoals het scannen van beveiligings problemen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87542939"
---
# <a name="azure-infrastructure-monitoring"></a>Bewaking van Azure-infra structuur   

## <a name="configuration-and-change-management"></a>Configuratie-en wijzigings beheer
Azure controleert en werkt de configuratie-instellingen en basislijn configuraties van hardware, software en netwerk apparaten jaarlijks af. Wijzigingen worden ontwikkeld, getest en goedgekeurd vóór het invoeren van de productie omgeving vanuit een ontwikkelings-en/of test omgeving.

De basislijn configuraties die vereist zijn voor services op basis van Azure worden gecontroleerd door het Azure Security-en nalevings team en door service teams. Een beoordeling van een service team maakt deel uit van de tests die zich voordoen voordat de implementatie van de productie service wordt uitgevoerd.

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen
Met beveiligings update beheer kunt u systemen beveiligen tegen bekende beveiligings problemen. Azure gebruikt geïntegreerde implementatie systemen voor het beheren van de distributie en installatie van beveiligings updates voor micro soft-software. Azure kan ook op de resources van het micro soft Security Response Center (MSRC) tekenen. Het MSRC identificeert, bewaakt, reageert op en verhelpt beveiligings incidenten en Cloud lekken rondom de klok, elke dag van het jaar.

## <a name="vulnerability-scanning"></a>Scannen op beveiligingsproblemen
Het scannen van beveiligings problemen wordt uitgevoerd op Server besturingssystemen, data bases en netwerk apparaten. Het beveiligings probleem wordt gescand op elk kwar taal op basis van het minimum. Azure-contracten met onafhankelijke beoordelaars voor het uitvoeren van indringings tests van de Azure-grens. Er worden ook dagelijkse team oefeningen uitgevoerd en de resultaten worden gebruikt om beveiligings verbeteringen aan te brengen.

## <a name="protective-monitoring"></a>Beschermende bewaking
Azure-beveiliging heeft vereisten voor actieve bewaking gedefinieerd. Service teams configureren actieve bewakings hulpprogramma's volgens deze vereisten. Actieve bewakings hulpprogramma's omvatten micro soft Monitoring Agent (MMA) en System Center Operations Manager. Deze hulpprogram ma's worden geconfigureerd om tijd waarschuwingen te bieden aan Azure-beveiligings personeel in situaties waarvoor onmiddellijke actie is vereist.

## <a name="incident-management"></a>Incidentbeheer
Micro soft implementeert een proces voor het beheren van beveiligings incidenten om een gecoördineerde reactie op incidenten te vergemakkelijken. dit moet zich voordoen.

Als micro soft op de hoogte wordt gebracht van ongeoorloofde toegang tot klant gegevens die zijn opgeslagen op het apparaat of in zijn faciliteiten, of als het op de hoogte is van ongeoorloofde toegang tot dergelijke apparatuur of faciliteiten, wat leidt tot verlies, openbaar making of wijziging van klant gegevens, neemt micro soft de volgende acties:

- Hiermee wordt de klant van het beveiligings incident onmiddellijk op de hoogte gebracht.
- Onderzoekt het beveiligings incident en biedt klanten gedetailleerde informatie over het beveiligings incident.
- Neemt redelijke en prompt stappen voor het oplossen van de gevolgen en minimaliseert de schade die het gevolg is van het beveiligings incident.

Er is een framework voor incident beheer gemaakt waarmee rollen worden gedefinieerd en de verantwoordelijkheden worden toegewezen. Het Azure security incident management-team is verantwoordelijk voor het beheer van beveiligings incidenten, met inbegrip van escalatie en het garanderen van de betrokkenheid van gespecialiseerde teams, indien nodig. Azure Operations Manager is verantwoordelijk voor het toezicht op en de oplossing van beveiligings-en privacy-incidenten.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van micro soft voor het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Azure-netwerk architectuur](infrastructure-network.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)
