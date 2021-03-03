---
title: Problemen oplossen tijdens de on-boarding-ervaring van Azure percept DK
description: Tips voor het oplossen van problemen met enkele van de meest voorkomende problemen die tijdens de on-boarding-ervaring zijn gevonden
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662319"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Gids voor probleem oplossing voor Azure percept DK-onboarding

Hier volgen enkele problemen die kunnen optreden tijdens de onboarding-ervaring van Azure percept DK. Als u de stappen in deze hand leiding hebt uitgevoerd, blijft het probleem zich nog steeds voordoen. Neem contact op met de klanten ondersteuning van Azure.

|Probleem|Reden|Tijdelijke oplossing|
|:-----|:------|:----------|
|Wanneer u verbinding maakt met de aanmeldings pagina's van het Azure-account of de Azure Portal, kunt u zich automatisch aanmelden met een account in de cache. Als dit niet het account is dat u wilt gebruiken, kan dit leiden tot een ervaring die inconsistent is met de documentatie.|Dit wordt meestal veroorzaakt door een instelling in de browser om een account te onthouden dat u eerder hebt gebruikt.|Klik op de pagina van Azure op de rechter bovenhoek van de pagina waar de account naam wordt weer gegeven en klik vervolgens op afmelden. U kunt zich nu aanmelden met het juiste account.|
|Het Azure percept DK Access Point-netwerk (SCZ-xxxx) wordt niet weer gegeven in de lijst met beschik bare Wi-Fi netwerken.|Dit is doorgaans een tijdelijk probleem dat zichzelf met een beetje tijd kan oplossen.|Wacht totdat het netwerk wordt weer gegeven. Als deze niet meer dan 15 minuten na, start u het apparaat opnieuw op.|
|De verbinding met het Azure percept DK-toegangs punt verbreekt regel matig.|Dit kan worden veroorzaakt door een slechte verbinding tussen het apparaat en de hostcomputer. Dit kan ook worden veroorzaakt door interferentie van andere Wi-Fi verbindingen op de hostcomputer.|Zorg ervoor dat de antennes goed zijn gekoppeld aan de dev kit. Als de dev kit ver weg is van de hostcomputer, kunt u deze dichter verplaatsen. Schakel andere Internet verbindingen, zoals LTE/5G, uit als deze op de hostcomputer worden uitgevoerd.|
|Op de hostcomputer wordt een beveiligings waarschuwing weer gegeven over de verbinding met het Azure percept DK-toegangs punt.|Dit is een bekend probleem dat in een latere update wordt opgelost.|Het is veilig om door te gaan met de voorbereidings ervaring via het Devkit Wi-Fi Access Point.|
|Het netwerk van het Azure percept DK-toegangs punt (SCZ-xxxx) wordt weer gegeven in de lijst netwerk, maar er kan geen verbinding worden gemaakt.|Dit kan worden veroorzaakt door een tijdelijke beschadiging van het Devkit Wi-Fi toegangs punt.|Start de Devkit opnieuw op en probeer het opnieuw.|
|Er kan geen verbinding worden gemaakt met een Wi-Fi netwerk tijdens de installatie.|Het Wi-Fi netwerk moet momenteel verbinding hebben met Internet zodat we kunnen communiceren met Azure. EAP [PEAP/MSCHAP], organisatie portals en Enter prise EAP-TLS-connectiviteit worden momenteel niet ondersteund.|Zorg ervoor dat het type Wi-Fi netwerk waarmee u verbinding maakt, wordt ondersteund en een Internet verbinding heeft.|