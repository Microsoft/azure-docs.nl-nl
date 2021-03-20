---
title: Facturering met Durable Functions - Azure Functions
description: Meer informatie over het interne gedrag van Durable Functions en hoe deze van invloed zijn op facturering voor Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 2ec1b080c195a47caafd0120240b5fb61ede062b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932279"
---
# <a name="durable-functions-billing"></a>Facturering met Durable Functions

[Durable Functions](durable-functions-overview.md) worden op dezelfde manier in rekening gebracht als Azure Functions. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

Wanneer u orchestrator-functies in het [Verbruiksabonnement](../consumption-plan.md) van Azure Functions uitvoert, dient u op enkele factureringseigenschappen te letten. De volgende secties beschrijven deze behaviors en hun effecten in meer detail.

## <a name="orchestrator-function-replay-billing"></a>Facturering van orchestrator-functie herhalen

[Orchestrator-functies](durable-functions-orchestrations.md) kunnen meerdere keren opnieuw worden afgespeeld gedurende de levensduur van een orchestrator. Elke herhaling wordt weergegeven door de Azure Functions-runtime als een afzonderlijke functie-aanroep. Daarom wordt in het Azure Functions-verbruiksplan elke herhaling van een Orchestrator-functie gefactureerd. Andere typen abonnementen worden niet in rekening gebracht voor het herhalen van de orchestrator-functie.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Wachten en opleveren in orchestrator-functies

Wanneer een orchestrator-functie wacht totdat een asynchrone actie is voltooid met behulp van **in afwachting van** in C# of **rendement** in JavaScript, beschouwt de runtime dat bepaalde uitvoeringen zijn voltooid. De facturering voor de orchestrator-functie stopt op dat moment. Het wordt pas hervat als de volgende orchestrator-functie opnieuw wordt afgespeeld. Er worden geen kosten in rekening gebracht voor tijd die nodig is om een orchestrator-functie te gebruiken of te leveren.

> [!NOTE]
> Functies die andere functies aanroepen, worden beschouwd als een antipatroon. Dit komt door een probleem dat bekendstaat als _dubbele facturering_. Wanneer een functie rechtstreeks een functie aanroept, worden beiden tegelijk uitgevoerd. De aangeroepen functie voert code actief uit terwijl de aanroepende functie wacht op een antwoord. In dit geval moet u betalen voor de tijd die de aanroepende functie wacht voordat de aangeroepen functie wordt uitgevoerd.
>
> Er is geen dubbele facturering in orchestrator-functies. De facturering van een orchestrator-functie wordt gestopt tijdens het wachten op het resultaat van een activiteitsfunctie of sub-orchestration.

## <a name="durable-http-polling"></a>Duurzame HTTP-polling

Met orchestrator-functies kunt u langlopende HTTP-aanroepen naar externe eindpunten maken, zoals beschreven in het artikel [HTTP-functies](durable-functions-http-features.md). Met de **CallHttpAsync**-methode in C# en de **callHttp-methode** in JavaScript wordt een HTTP-eindpunt intern gecontroleerd tijdens het navolgen van het [asynchrone 202-patroon](durable-functions-http-features.md#http-202-handling).

Er wordt momenteel geen directe facturering uitgevoerd voor interne HTTP-pollingbewerkingen. Interne polling kan er echter toe leiden dat de orchestrator-functie regelmatig opnieuw wordt afgespeeld. Er worden standaardkosten in rekening gebracht voor deze interne functieherhalingen.

## <a name="azure-storage-transactions"></a>Azure Storage-transacties

Durable Functions gebruikt Azure Storage standaard om de status permanent te houden, berichten te verwerken en partities te beheren via blob-leases. Omdat u eigenaar bent van dit opslagaccount, worden de transactiekosten in rekening gebracht op uw Azure-abonnement. Voor meer informatie over de Azure Storage-artefacten die door Durable Functions worden gebruikt, raadpleegt u het [artikel over taakhubs](durable-functions-task-hubs.md).

Verschillende factoren dragen bij aan de werkelijke Azure Storage-kosten die worden gemaakt door uw Durable Functions-app:

* Een app met één functie is gekoppeld aan één taakhub, die een set Azure Storage-resources deelt. Deze resources worden gebruikt door alle duurzame functies in een functie-app. Het werkelijke aantal functies in de functie-app heeft geen invloed op de kosten van Azure Storage-transacties.
* Elke instantie van de functie-app voert intern polls uit voor meerdere wachtrijen in het opslagaccount met behulp van een polling-algoritme met exponentiële uitstelfunctie. Een instantie van een niet-actieve app controleert de wachtrijen minder vaak dan een actieve app, wat leidt tot minder transactiekosten. Zie de sectie [wachtrij-polling in het artikel prestaties en schalen](durable-functions-perf-and-scale.md#queue-polling) voor meer informatie over polling van de wachtrij in Durable Functions.
* Bij het uitvoeren van de Azure Functions Consumption- of Premium-abonnementen, worden door de [Azure Functions Scale-controller](../event-driven-scaling.md) regelmatig polls uitgevoerd voor alle taakhub-wachtrijen op de achtergrond. Als een functie-app een lichte tot matige schaal heeft, zal slechts één enkele controller-exemplaar deze wachtrijen pollen. Als de functie-app wordt geschaald naar een groot aantal exemplaren, kunnen er meer schaalcontroller-exemplaren worden toegevoegd. Deze extra schaalcontroller-exemplaren kunnen de totale transactiekosten van de wachtrij verhogen.
* Elke functie-app-instantie is een concurrent voor een set blob-leases. Deze exemplaren zullen periodiek de Azure Blob-service aanroepen om vastgehouden leases te verlengen of om te proberen nieuwe leases te verwerven. Met het geconfigureerde aantal partities van de taakhub wordt het aantal blob-leases bepaald. Als u uitschaalt naar een groter aantal functie-app-instanties, worden de transactiekosten van Azure Storage die aan deze leasebewerkingen zijn gekoppeld, waarschijnlijk verhoogd.

In de documentatie [Azure Storage-prijzen](https://azure.microsoft.com/pricing/details/storage/) vindt u meer informatie over Azure Storage-prijzen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ Meer informatie over de prijzen van Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
