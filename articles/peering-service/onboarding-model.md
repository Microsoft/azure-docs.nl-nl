---
title: Model voor onboarding van Azure peering-service
description: Meer informatie over het onboarden van de service Azure peering
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872076"
---
# <a name="onboarding-peering-service-model"></a>Onboarding van peering-service model

Het onboarding-proces van de peering-service bestaat uit twee modellen, zoals hieronder wordt weer gegeven:

 - Onboarding van peering-service verbinding

 - Telemetrie voor de onboarding van peering-service verbindingen

Actie plannen voor de hierboven genoemde modellen worden hieronder beschreven:

| **Stap** | **Actie**| **Wat u krijgt**|
|-----------|---------|---------|---------|
|1|Klant voor het inrichten van de connectiviteit van een connectiviteits partner (geen interactie met micro soft). |Een Internet provider die goed verbonden is met micro soft en voldoet aan de technische vereisten voor het uitvoeren van een goede en betrouw bare verbinding met micro soft.  |
|2 (optioneel)|Klant registreert locaties in de Azure Portal. Een locatie wordt gedefinieerd door: ISP/IXP naam, fysieke locatie van de site van de klant (status niveau), het IP-voor voegsel dat door de service provider of de onderneming aan de locatie wordt verstrekt.  |Telemetrie: Internet route bewaking, prioriteit van verkeer van micro soft naar de dichtstbijzijnde POP-locatie van de gebruiker. |



## <a name="onboarding-peering-service-connection"></a>Onboarding van peering-service verbinding

Ga als volgt te werk om een onboarding van peering service-verbinding uit te voeren:

Neem contact op met de Internet provider (ISP) of de Internet Exchange (IX)-partner voor het verkrijgen van peering-service om uw netwerk te verbinden met het micro soft-netwerk.

Zorg ervoor dat de [connectiviteits providers](location-partners.md) zijn gekoppeld aan micro soft voor de peering-service. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Telemetrie voor de onboarding van peering-service verbindingen

Klanten kunnen ervoor kiezen om hun telemetrie, zoals BGP route Analytics, te bewaken over de latentie en prestaties van netwerken bij het openen van het micro soft-netwerk. Dit kan worden bereikt door de verbinding te registreren in de Azure Portal.

Voor een onboarding van de telemetrie van peering-service verbindingen moet de klant de verbinding van de peering service registreren in de Azure Portal. Raadpleeg de [REGI stering service-Azure Portal](azure-portal.md) voor meer informatie over de procedure.

Hierna kunt u telemetrie meten door [hier](measure-connection-telemetry.md)te verwijzen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het registreren van peering [-service-Azure Portal de inschrijving van peering-service](azure-portal.md).

Zie [verbindings-telemetrie meten](measure-connection-telemetry.md)voor meer informatie over het meten van verbindings-telemetrie.
