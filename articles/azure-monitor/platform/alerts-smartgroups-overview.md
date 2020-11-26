---
title: Slimme groepen
description: Slimme groepen zijn aggregaties van waarschuwingen waarmee u waarschuwings ruis kunt verminderen
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/15/2018
ms.openlocfilehash: 743bd1a674c034cd6a0350f959289ac3ecb568de
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186606"
---
# <a name="smart-groups"></a>Slimme groepen

Een gemeen schappelijke uitdaging bij het omgaan met waarschuwingen is de ruis te verhelpen om erachter te komen wat de voor delen zijn: slimme groepen zijn bedoeld als oplossing voor dat probleem.  

Slimme groepen worden automatisch gemaakt met behulp van machine learning algoritmen voor het combi neren van gerelateerde waarschuwingen die een enkel probleem vertegenwoordigen.  Wanneer een waarschuwing wordt gemaakt, wordt deze toegevoegd aan een nieuwe slimme groep of een bestaande slimme groep op basis van informatie zoals historische patronen, vergelijk bare eigenschappen en vergelijk bare structuur. Als% CPU op meerdere virtuele machines in een abonnement bijvoorbeeld tegelijk leidt tot veel afzonderlijke waarschuwingen, en als dergelijke waarschuwingen op elk moment in het verleden zijn opgetreden, worden deze waarschuwingen waarschijnlijk gegroepeerd in één enkele slimme groep, waardoor een mogelijke algemene hoofd oorzaak wordt voorgesteld. Dit betekent dat slimme groepen niet alleen kunnen worden gebruikt om de ruis te reduceren door gerelateerde waarschuwingen te beheren als één geaggregeerde eenheid, maar deze hand leidingen begeleiden de mogelijke algemene hoofd oorzaken voor hun waarschuwingen.

Op dit moment beschouwt het algoritme alleen waarschuwingen van dezelfde monitor service binnen een abonnement. Slimme groepen kunnen Maxi maal 99% van waarschuwings ruis verminderen via deze consolidatie. U kunt de reden bekijken dat waarschuwingen zijn opgenomen in een groep op de pagina Details van de Smart groep.

U kunt de details van Smart groepen bekijken en de status instellen op dezelfde manier als met waarschuwingen. Elke waarschuwing is lid van slechts één slimme groep. 

## <a name="smart-group-state"></a>Status van slimme groep

De status van een slimme groep is een vergelijkbaar concept met de waarschuwings status, waarmee u het oplossings proces op het niveau van een slimme groep kunt beheren. Net als bij de status van de waarschuwing, heeft het een **nieuwe** status, die kan worden gewijzigd in **bevestigd** of **gesloten**, wanneer een slimme groep wordt gemaakt.

De volgende statussen van de Smart Group worden ondersteund.

| Staat | Beschrijving |
|:---|:---|
| Nieuw | Het probleem is zojuist gedetecteerd en nog niet gecontroleerd. |
| Bevestigd | Een beheerder heeft de slimme groep gecontroleerd en ermee begonnen. |
| Gesloten | Het probleem is opgelost. Nadat een Smart-groep is gesloten, kunt u deze opnieuw openen door deze te wijzigen in een andere status. |

[Meer informatie over het wijzigen van de status van uw slimme groep.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

> [!NOTE]
>  Als u de status van een Smart groep wijzigt, wordt de status van de waarschuwingen voor afzonderlijke leden niet gewijzigd.

## <a name="smart-group-details-page"></a>Pagina Details van Smart Group

De detail pagina met een slimme groep wordt weer gegeven wanneer u een slimme groep selecteert. Het bevat details over de slimme groep, met inbegrip van de reden voor het maken van de groep, en stelt u in staat om de status ervan te wijzigen.
 
![Details van slimme groep](media/alerts-smartgroups-overview/smart-group-detail.png)


De detail pagina van de Smart groep bevat de volgende secties.

| Sectie | Beschrijving |
|:---|:---|
| Waarschuwingen | Hier worden de afzonderlijke waarschuwingen weer gegeven die zijn opgenomen in de slimme groep. Selecteer een waarschuwing om de pagina met de details van de waarschuwing te openen. |
| Geschiedenis | Een lijst met alle acties die worden uitgevoerd door de slimme groep en eventuele wijzigingen. Dit is momenteel beperkt tot status wijzigingen en wijzigingen in het waarschuwings lidmaatschap. |

## <a name="smart-group-taxonomy"></a>Slimme groeps taxonomie

De naam van een Smart Group is de naam van de eerste waarschuwing. U kunt een slimme groep niet maken of een andere naam geven.

## <a name="next-steps"></a>Volgende stappen

- [Slimme groepen beheren](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [De status van de waarschuwing en de slimme groep wijzigen](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)