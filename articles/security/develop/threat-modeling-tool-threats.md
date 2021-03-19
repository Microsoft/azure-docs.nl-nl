---
title: Bedreigingen-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: De pagina bedreigings categorie voor de Microsoft Threat Modeling Tool, die categorieën bevat voor alle blootgestelde gegenereerde bedreigingen.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727835"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool bedreigingen

De Threat Modeling Tool is een kern element van micro soft Security Development Lifecycle (SDL). Hiermee kunnen software architecten potentiële beveiligings problemen vroegtijdig identificeren en verhelpen, wanneer ze betrekkelijk eenvoudig en rendabel zijn om te kunnen oplossen. Als gevolg hiervan worden de totale ontwikkelings kosten aanzienlijk verminderd. Daarnaast hebben we het hulp programma ontworpen met niet-beveiligings experts in het licht, waardoor het maken van bedreigings modellen eenvoudiger is voor alle ontwikkel aars door duidelijke richt lijnen te bieden voor het creëren en analyseren van bedreigings models.

> Ga naar de **[Threat Modeling tool](threat-modeling-tool.md)** om vandaag nog aan de slag te gaan.

De Threat Modeling Tool helpt u bij het beantwoorden van bepaalde vragen, zoals hieronder:

* Hoe kan een aanvaller de verificatie gegevens wijzigen?
* Wat is de impact als een aanvaller de gebruikers profiel gegevens kan lezen?
* Wat gebeurt er als de toegang tot de data base van het gebruikers profiel wordt geweigerd?

## <a name="stride-model"></a>STRIDE-model

Micro soft maakt gebruik van het STRIDE-model, dat verschillende soorten bedreigingen categoriseert en de algehele beveiligings gesprekken vereenvoudigt, om u te helpen bij het formuleren van deze typen vragen.

| Categorie | Beschrijving |
| -------- | ----------- |
| **Adresvervalsing (spoofing)** | Is illegaal toegankelijk en gebruikt vervolgens de verificatie gegevens van een andere gebruiker, zoals gebruikers naam en wacht woord |
| **Onrechtmatige wijziging** | Omvat het kwaad aardige wijziging van gegevens. Voor beelden zijn niet-geautoriseerde wijzigingen die zijn aangebracht aan permanente gegevens, zoals die in een Data Base worden bewaard, en de wijziging van gegevens omdat deze tussen twee computers via een open netwerk, zoals Internet, loopt. |
| **Verwerping** | Gekoppeld aan gebruikers die geen actie hebben ondernomen zonder andere partijen om anderszins te bewijzen, bijvoorbeeld wanneer een gebruiker een ongeldige bewerking uitvoert in een systeem dat geen mogelijkheid heeft om de verboden bewerkingen te traceren. Niet-afwijzing verwijst naar de mogelijkheid van een systeem tot het aantal afwijzings bedreigingen. Een gebruiker die een item koopt, kan bijvoorbeeld op het moment dat er een bevestiging moet worden ondertekend voor het item. De leverancier kan vervolgens de ondertekende ontvangst gebruiken als bewijs dat de gebruiker het pakket heeft ontvangen |
| **Openbaarmaking van informatie** | Omvat de bloot stelling van informatie aan personen die geen toegang zouden moeten hebben, bijvoorbeeld de mogelijkheid van gebruikers om een bestand te lezen waaraan geen toegang is verleend, of de mogelijkheid van indringers om gegevens te lezen die onderweg zijn tussen twee computers |
| **Denial of Service** | Denial of service-aanvallen (DoS) weigeren service voor geldige gebruikers, bijvoorbeeld door een webserver tijdelijk niet beschikbaar of onbruikbaar te maken. U moet zich beschermen tegen bepaalde soorten DoS-bedreigingen om de beschik baarheid en betrouw baarheid van het systeem te verbeteren |
| **Onrechtmatige uitbreiding van toegangsrechten** | Een onbevoegde gebruiker krijgt bevoorrechte toegang, waardoor de toegang tot het hele systeem kan worden aangetast of verwijderd. Uitbrei ding van bevoegdheids dreigingen zijn onder andere de omstandigheden waarin een aanvaller alle systeem beveiligingen effectief heeft gepromoveerd en deel uitmaakt van het vertrouwde systeem zelf, een gevaarlijke situatie inderdaad |

## <a name="next-steps"></a>Volgende stappen

Ga door naar **[Threat Modeling tool oplossingen](threat-modeling-tool-mitigations.md)** voor meer informatie over de verschillende manieren waarop u deze bedreigingen kunt oplossen met Azure.