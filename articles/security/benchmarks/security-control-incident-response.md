---
title: Azure-beveiligings beheer-reactie op incidenten
description: Reactie van incidenten van Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb4c4c5a0cf6610af17aabc562c42d2e0eb4e6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409090"
---
# <a name="security-control-incident-response"></a>Beveiligings beheer: respons op incidenten

Beveilig de gegevens van de organisatie en de reputatie ervan door een infra structuur voor incidenten te ontwikkelen en te implementeren (zoals plannen, gedefinieerde rollen, training, communicatie, beheer toezicht) om snel een aanval te detecteren en vervolgens effectief de schade te veroorzaken, de aanwezigheid van de aanvaller te uitroeien en de integriteit van het netwerk en de systemen te herstellen.

## <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Klant |

Maak een antwoord gids voor incidenten voor uw organisatie. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.  

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10,2 | 19.8 | Klant |

Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. 

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het oplossen van waarschuwingen op basis van de ernst van de Azure-resources en -omgeving waarin het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](../../azure-resource-manager/management/tag-resources.md)

## <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10.3 | 19 | Klant |

Voer oefeningen uit om de incident respons mogelijkheden van uw systemen te testen op een regel matige uitgebracht om uw Azure-resources te beschermen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10.4 | 19,5 | Klant |

Contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10.5 | 19,6 | Klant |

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 10,6 | 19 | Klant |

Gebruik de functie werk stroom automatisering in Azure Security Center om automatisch reacties te activeren via ' Logic Apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](../../security-center/workflow-automation.md)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [Indringings tests en rode team oefeningen](security-control-penetration-tests-red-team-exercises.md)