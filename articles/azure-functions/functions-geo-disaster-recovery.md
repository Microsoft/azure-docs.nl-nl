---
title: Azure Functions geo-nood herstel en hoge Beschik baarheid
description: Hoe u geografische regio's gebruikt voor redundantie en failover in Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506278"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geo-nood herstel

Wanneer de hele Azure-regio's of-Data Centers downtime hebben, is het van cruciaal belang om de verwerking in een andere regio voort te zetten.  In dit artikel worden enkele strategieën uitgelegd die u kunt gebruiken om functies te implementeren om herstel na nood gevallen mogelijk te maken.

## <a name="basic-concepts"></a>Basisbegrippen

Azure Functions uitgevoerd in een specifieke regio.  Voor een hogere Beschik baarheid kunt u dezelfde functies implementeren in meerdere regio's.  In meerdere regio's kunt u uw functies uitvoeren in het patroon *actief/actief* of actief */passief* .  

* Actief/actief. Beide regio's zijn actief en ontvangen gebeurtenissen (gedupliceerd of geroteerd). Actief/actief wordt aanbevolen voor HTTPS-functies in combi natie met Azure front deur.
* Actief/passief. Een regio is actief en ontvangt gebeurtenissen, terwijl een secundaire activiteit niet actief is.  Als failover is vereist, wordt de secundaire regio geactiveerd en wordt de verwerking verouderd.  Dit wordt aanbevolen voor niet-HTTP-functies, zoals Service Bus en Event Hubs.

Lees hoe u [apps in meerdere regio's kunt uitvoeren](/azure/architecture/reference-architectures/app-service-web-app/multi-region) voor meer informatie over implementaties met meerdere regio's.

## <a name="activeactive-for-https-functions"></a>Actief/actief voor HTTPS-functies

Voor het bezorgen van actieve/actieve implementaties van functies, is een onderdeel vereist dat de gebeurtenissen tussen beide regio's kan coördineren.  Voor HTTPS-functies wordt deze coördinatie uitgevoerd met behulp van [Azure front-deur](../frontdoor/front-door-overview.md).  Azure front deur kan HTTPS-aanvragen en Round Robins tussen meerdere regionale functies routeren en afronden.  Ook wordt de status van elk eind punt periodiek gecontroleerd.  Als een regionale functie niet meer reageert op status controles, zal de Azure front-deur het niet draaien en alleen verkeer door sturen naar goede functies.  

![Architectuur voor de voor deur en functie van Azure](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Actief/actief voor niet-HTTPS-functies

U kunt nog steeds actieve/actieve implementaties voor niet-HTTPS-functies.  U moet echter nadenken over hoe de twee regio's met elkaar zullen communiceren of onderling worden gecoördineerd.  Als u dezelfde functie-app in twee regio's hebt geïmplementeerd, worden deze allemaal geactiveerd op dezelfde Service Bus wachtrij. ze fungeren als concurrerende consumenten in de wachtrij van die wachtrij.  Hoewel dit betekent dat elk bericht alleen wordt verwerkt door een van de instanties, betekent dit ook dat er nog steeds een Single Point of Failure is op het Service Bus.  Als u twee Service Bus-wacht rijen implementeert (één in een primaire regio, een in een secundaire regio) en de twee functie-apps naar hun regio wachtrij verwijzen, wordt de uitdaging nu in de wachtrij geplaatst tussen de twee regio's.  Dit betekent vaak dat elke uitgever een bericht probeert te publiceren naar *beide* regio's en dat elk bericht wordt verwerkt door zowel actieve functie-apps.  Hoewel hiermee een actief/actief patroon wordt gemaakt, worden er ook andere uitdagingen gegenereerd voor het dupliceren van Compute en wanneer of hoe gegevens worden geconsolideerd.  Daarom wordt het aanbevolen om niet-HTTPS-triggers het actieve/passieve patroon te gebruiken.

## <a name="activepassive-for-non-https-functions"></a>Actief/passief voor niet-HTTPS-functies

Active/Passive biedt slechts een manier voor het verwerken van elk bericht, maar biedt een mechanisme voor het uitvoeren van een failover naar een secundaire regio in het geval van een ramp.  Azure Functions werkt naast [Azure service bus geo-Recovery](../service-bus-messaging/service-bus-geo-dr.md) en [Azure Event hubs geo-herstel](../event-hubs/event-hubs-geo-dr.md).

Het gebruik van Azure Event Hubs triggers als voor beeld: het actieve/passieve patroon houdt het volgende in.

* Azure Event hub is geïmplementeerd op zowel de primaire als de secundaire regio.
* Geo-ramp is ingeschakeld om de primaire en secundaire Event hub te koppelen.  Hiermee wordt ook een alias gemaakt die u kunt gebruiken om verbinding te maken met Event hubs en te scha kelen van primair naar secundair zonder de verbindings gegevens te wijzigen.
* Functie-apps die zijn geïmplementeerd in een primaire en secundaire regio.
* De functie-apps worden geactiveerd op de *direct* (niet-alias) Connection String voor de desbetreffende Event hub. 
* Uitgevers van de Event Hub moeten publiceren naar het alias connection string. 

![Architectuur van actief/passief voor beeld](media/functions-geo-dr/active-passive.png)

Voordat een failover wordt verzonden naar de gedeelde alias, wordt deze gerouteerd naar de primaire Event Hub.  De primaire functie-app luistert alleen naar de primaire Event Hub.  De secundaire functie-app wordt passief en niet-actief.  Zodra een failover wordt gestart, wordt de uitgevers die naar de gedeelde alias verzenden, nu doorgestuurd naar de secundaire Event Hub.  De secundaire functie-app wordt nu actief en start de activering automatisch.  Een efficiënte failover naar een secundaire regio kan volledig worden aangedreven vanuit de Event Hub, waarbij de functies alleen actief worden wanneer de respectieve Event Hub actief is.

Lees meer over informatie en overwegingen voor failover met [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) en [Event hubs](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Volgende stappen

* [Azure front deur maken](../frontdoor/quickstart-create-front-door.md)
* [Overwegingen voor Event Hubs failover](../event-hubs/event-hubs-geo-dr.md#considerations)
