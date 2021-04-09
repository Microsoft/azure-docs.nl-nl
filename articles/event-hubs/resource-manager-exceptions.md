---
title: Uitzonde ringen voor Azure Event Hubs-Resource Manager | Microsoft Docs
description: Lijst met Azure Event Hubs-uitzonde ringen die door Azure Resource Manager en voorgestelde acties worden geoppereerd.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334205"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Uitzonde ringen voor Azure Event Hubs-Resource Manager
Dit artikel bevat een lijst met uitzonde ringen die worden gegenereerd bij interactie met Azure Event Hubs met behulp van Azure Resource Manager-via-sjablonen of directe aanroepen.

> [!IMPORTANT]
> Dit document wordt regel matig bijgewerkt. Ga terug naar updates.

De volgende secties bieden verschillende uitzonde ringen/fouten die worden weer gegeven via Azure Resource Manager.

## <a name="error-code-conflict"></a>Fout code: conflict

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflict | 40300 | Het maximum aantal resources van het type EventHub is bereikt of overschreden. Werkelijk: #, Maxi maal toegestaan: # | De naam ruimte heeft het [quotum](event-hubs-quotas.md) bereikt voor het aantal Event hubs dat kan bevatten. | Verwijder ongebruikte of overbodige Event hubs uit de naam ruimte of overweeg een upgrade uit te dienen naar een [toegewezen cluster](event-hubs-dedicated-overview.md). |
| Conflict | geen | Herstel na nood gevallen (DR) configuratie kan niet worden verwijderd, omdat de replicatie wordt uitgevoerd. Failover of Verbreek de koppeling voordat u de DR-configuratie probeert te verwijderen. | [GeoDR-replicatie](event-hubs-geo-dr.md) wordt uitgevoerd, zodat de configuratie op dit moment niet kan worden verwijderd. | Als u het verwijderen van de configuratie wilt blok keren, wacht u totdat de replicatie is voltooid, het activeren van een failover of het verbreekt van de GeoDR-koppeling. |
| Conflict | geen | De naam ruimte kan niet worden bijgewerkt vanwege een conflict in de back-end. | Er wordt momenteel een andere bewerking uitgevoerd op deze naam ruimte. | Wacht tot de huidige bewerking is voltooid en probeer het vervolgens opnieuw. |

## <a name="error-code-429"></a>Fout code: 429

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | geen | Naam ruimte inrichten in overgang | Er wordt momenteel een andere bewerking uitgevoerd op deze naam ruimte. | Wacht tot de huidige bewerking is voltooid en probeer het vervolgens opnieuw. |
| 429 | geen | Herstel bewerking voor nood gevallen wordt uitgevoerd. | Er wordt momenteel een [GeoDR](event-hubs-geo-dr.md) -bewerking uitgevoerd voor deze naam ruimte of koppelen. | Wacht tot de huidige GeoDR-bewerking is voltooid en probeer het vervolgens opnieuw. |

## <a name="error-code-badrequest"></a>Fout code: onjuiste aanvraag

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount kan niet worden gewijzigd voor een Event Hub. | De Basic-of Standard-laag van Azure Event Hubs biedt geen ondersteuning voor het wijzigen van partities. | Maak een nieuwe Event Hub met het gewenste aantal partities in de basis-of standaardlaag naam ruimte. Scale-out van partitie wordt ondersteund voor [toegewezen clusters](event-hubs-dedicated-overview.md). |
| BadRequest | 40000 | De waarde ' # ' voor MessageRetentionInDays is niet geldig voor de laag Basic. de waarde mag niet langer zijn dan ' 1 ' dag (en). | Basic-laag Event Hubs naam ruimten bieden alleen ondersteuning voor het bewaren van berichten van Maxi maal 1 dag. | Als er meer dan één dag voor het bewaren van berichten gewenst is, [maakt u een standaard Event hubs naam ruimte](event-hubs-create.md). | 
| BadRequest | geen | De opgegeven naam is niet beschikbaar. | Naam ruimte namen moeten uniek zijn, en de opgegeven naam wordt al gebruikt. | Als u de eigenaar van de bestaande naam ruimte met de opgegeven naam bent, kunt u deze verwijderen, waardoor er gegevens verloren gaan. Probeer het opnieuw met dezelfde naam. Als de naam ruimte niet veilig kan worden verwijderd (of als u niet de eigenaar bent), kiest u een andere naam voor de naam ruimte. |
| BadRequest | geen | Het quotum van de naam ruimten is bereikt voor het opgegeven abonnement. | Het abonnement is bereikt met het [quotum](event-hubs-quotas.md) voor het aantal naam ruimten dat kan worden bewaard. | Overweeg niet-gebruikte naam ruimten in dit abonnement te verwijderen, een ander abonnement te maken of een upgrade naar een [toegewezen cluster](event-hubs-dedicated-overview.md)uit te brengen. |
| BadRequest | geen | Een naam ruimte die secundair is, kan niet worden bijgewerkt | De naam ruimte kan niet worden bijgewerkt, omdat het de secundaire naam ruimte is in een [GeoDR-koppeling](event-hubs-geo-dr.md). | Indien nodig moet u in plaats daarvan de wijziging in de primaire naam ruimte in deze koppeling aanbrengen. Anders verbreekt u de GeoDR-koppeling om de wijziging door te voeren. |
| BadRequest | geen | Kan niet automatisch verg Roten instellen in de basis-SKU | Automatisch verg Roten kan niet worden ingeschakeld op de Basic-laag Event Hubs naam ruimten. | Als u [automatisch verg Roten wilt inschakelen](event-hubs-auto-inflate.md) voor een naam ruimte, moet u ervoor zorgen dat het de standaard-laag is. |
| BadRequest | geen | Er is onvoldoende capaciteit om de naam ruimte te maken. Neem contact op met uw Event Hubs-beheerder. | De geselecteerde regio heeft een capaciteit en er kunnen geen naam ruimten meer worden gemaakt. | Selecteer een andere regio als huis uw naam ruimte. |
| BadRequest | geen | De bewerking kan niet worden uitgevoerd voor het entiteits type ' ConsumerGroup ', omdat de naam ruimte ' Basic ' gebruikmaakt van de laag naam ruimte.  | Event Hubs naam ruimten van de Basic-laag hebben een quotum (Event-hubs-quotas.md) van één consumer groep (de standaard instelling). Het maken van meer consumenten groepen wordt niet ondersteund. | Ga door met het gebruik van de standaard gebruikers groep ($Default) of overweeg in plaats daarvan een standaardlaag Event Hubs naam ruimte te gebruiken. | 
| BadRequest | geen | De naam van de naam ruimte bestaat niet. | De naam ruimte die is gegeven, is niet gevonden. | Controleer of de naam van de naam ruimte juist is en of deze in uw abonnement kan worden gevonden. Als dat niet het geval is, [maakt u een event hubs naam ruimte](event-hubs-create.md). | 
| BadRequest | geen | De locatie-eigenschap van de resource komt niet overeen met die van de naam ruimte. | Het maken van een Event Hub in een specifieke regio is mislukt omdat deze niet overeenkomt met de regio van de naam ruimte. | Probeer de Event Hub te maken in dezelfde regio als de naam ruimte. | 

## <a name="error-code-internal-server-error"></a>Fout code: interne server fout

| Foutcode | Fout subcode | Foutbericht | Beschrijving | Aanbeveling |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Interne server fout | geen | Interne server fout. | Er is een interne fout opgetreden voor de Event Hubs-service. | Voer de mislukte bewerking opnieuw uit. Als de bewerking blijft mislukken, neemt u contact op met de ondersteuning. |