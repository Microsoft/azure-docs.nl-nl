---
title: Problemen oplossen tijdens de installatie van Azure percept DK
description: Tips voor het oplossen van problemen met enkele veelvoorkomende problemen die tijdens de installatie-ervaring worden gevonden
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608490"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Gids voor het oplossen van problemen met Azure percept DK Setup

Raadpleeg de onderstaande tabel voor tijdelijke oplossingen voor veelvoorkomende problemen die tijdens de [installatie van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)zijn gevonden. Als uw probleem blijft bestaan, neemt u contact op met de klanten ondersteuning van Azure.

|Probleem|Reden|Tijdelijke oplossing|
|:-----|:------|:----------|
|Wanneer u verbinding maakt met de aanmeldings pagina's van het Azure-account of de Azure Portal, kunt u zich automatisch aanmelden met een account in de cache. Als dit niet het account is dat u wilt gebruiken, kan dit leiden tot een ervaring die inconsistent is met de documentatie.|Dit wordt meestal veroorzaakt door een instelling in de browser om een account te onthouden dat u eerder hebt gebruikt.|Klik op de pagina van Azure op uw account naam in de rechter bovenhoek en selecteer **Afmelden**. U kunt zich nu aanmelden met het juiste account.|
|Het toegangs punt voor Azure percept DK Wi-Fi (SCZ-XXXX of apd-xxxx) wordt niet weer gegeven in de lijst met beschik bare Wi-Fi netwerken.|Dit is doorgaans een tijdelijk probleem dat binnen vijf tien minuten wordt opgelost.|Wacht totdat het netwerk wordt weer gegeven. Als deze niet meer dan 15 minuten wordt weer gegeven, moet u het apparaat opnieuw opstarten.|
|De verbinding met het Azure percept DK Wi-Fi Access Point wordt regel matig verbroken.|Dit kan worden veroorzaakt door een slechte verbinding tussen het apparaat en de hostcomputer. Dit kan ook worden veroorzaakt door interferentie van andere Wi-Fi verbindingen op de hostcomputer.|Zorg ervoor dat de antennes goed zijn gekoppeld aan de dev kit. Als de dev kit ver weg is van de hostcomputer, kunt u deze dichter verplaatsen. Schakel andere Internet verbindingen, zoals LTE/5G, uit als deze op de hostcomputer worden uitgevoerd.|
|Op de hostcomputer wordt een beveiligings waarschuwing weer gegeven over de verbinding met het Azure percept DK-toegangs punt.|Dit is een bekend probleem dat in een latere update wordt opgelost.|Het is veilig om door te gaan met de installatie-ervaring.|
|Het toegangs punt van Azure percept DK Wi-Fi (SCZ-XXXX of apd-xxxx) wordt weer gegeven in de lijst netwerk, maar er kan geen verbinding worden gemaakt.|Dit kan worden veroorzaakt door een tijdelijke beschadiging van het Wi-Fi toegangs punt van de dev kit.|Start de dev kit opnieuw op en probeer het opnieuw.|
|Er kan geen verbinding worden gemaakt met een Wi-Fi netwerk tijdens de installatie.|Het Wi-Fi netwerk moet momenteel verbinding hebben met internet om te kunnen communiceren met Azure. EAP [PEAP/MSCHAP], organisatie portals en Enter prise EAP-TLS-connectiviteit worden momenteel niet ondersteund.|Zorg ervoor dat uw Wi-Fi netwerk type wordt ondersteund en een Internet verbinding heeft.|