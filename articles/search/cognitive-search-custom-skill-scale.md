---
title: Aangepaste vaardigheid schalen en beheren
titleSuffix: Azure Cognitive Search
description: Meer informatie over de hulpprogram ma's en technieken voor het efficiënt schalen van een aangepaste vaardigheid voor maximale door voer. Aangepaste vaardig heden roepen aangepaste AI-modellen of logica aan die u kunt toevoegen aan een AI-verrijkte index pijplijn in azure Cognitive Search.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 22e48239631850d82cbb3e3208748416087da87c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422169"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Een aangepaste vaardigheid efficiënt uitschalen

Aangepaste vaardig heden zijn web-Api's waarmee een specifieke interface wordt geïmplementeerd. Een aangepaste vaardigheid kan worden geïmplementeerd op een openbaar adresseer bare resource. De meest voorkomende implementaties voor aangepaste vaardig heden zijn:
* Azure Functions voor aangepaste logische vaardig heden
* Azure-webtoepassingen voor eenvoudige, in containers geplaatste AI-vaardig heden
* Azure Kubernetes-service voor complexere of grotere vaardig heden.

## <a name="prerequisites"></a>Vereisten

+ Bekijk de [aangepaste vaardigheids interface](cognitive-search-custom-skill-interface.md) voor een inleiding in de invoer-en uitvoer interface waarvoor een aangepaste vaardigheid moet worden geïmplementeerd.

+ Stel uw omgeving in. U kunt beginnen met [deze zelf studie end-to-end](/python/tutorial-vs-code-serverless-python-01) om een Serverloze Azure-functie in te stellen met behulp van Visual Studio code en python-extensies.

## <a name="skillset-configuration"></a>Configuratie van vaardig heden

Als u een aangepaste vaardigheid wilt configureren voor het maximaliseren van de door Voer van het indexerings proces, moet u weten wat de vaardig heden, Indexer configuraties en hoe de vaardigheid betrekking heeft op elk document. Bijvoorbeeld het aantal keren dat een kwalificatie per document wordt aangeroepen en de verwachte duur per aanroep.

### <a name="skill-settings"></a>Vaardigheids instellingen

Stel op de [aangepaste vaardigheid](cognitive-search-custom-skill-web-api.md) de volgende para meters in.

1. Stel `batchSize` de aangepaste vaardigheid in om het aantal records dat wordt verzonden naar de vaardigheid, te configureren in één aanroep van de vaardigheid.

2. Stel de `degreeOfParallelism` in om het aantal gelijktijdige aanvragen te kalibreren dat de Indexeer functie voor uw vaardig heden gaat maken.

3. Stel `timeout` in op een waarde die voldoende is voor de vaardigheid om te reageren met een geldig antwoord.

4. Stel in de `indexer` definitie in [`batchSize`](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) op het aantal documenten dat moet worden gelezen van de gegevens bron en dat deze tegelijkertijd moeten worden uitgebreid.

### <a name="considerations"></a>Overwegingen

Als u deze variabelen wilt instellen op het optimaliseren van de prestaties van de Indexeer functies, moet u bepalen of uw vaardigheid beter presteert met veel gelijktijdige kleine aanvragen of minder grote aanvragen. Hier volgen enkele vragen die u kunt overwegen:

* Wat is de kardinaliteit van de vaardigheids aanroep? Voert de kwalificatie één keer uit voor elk document, bijvoorbeeld de vaardigheid van de document classificatie, of kan de vaardigheid meerdere keren per document worden uitgevoerd, een kwalificatie van een alinea classificatie?

* Gemiddeld hoeveel documenten worden gelezen uit de gegevens bron om een kwalificatie aanvraag in te vullen op basis van de grootte van de vaardigheids batch? In het ideale geval moet dit kleiner zijn dan de Batch grootte van de Indexeer functie. Met batch grootten groter dan 1 kan uw vaardigheid records ontvangen van meerdere bron documenten. Als het aantal batches in de Indexeer functie bijvoorbeeld 5 is en het aantal vaardig heden in de batch 50 is en elk document slechts vijf records genereert, moet de Indexeer functie een aangepaste aanvraag voor de vaardigheid vullen in meerdere Indexeer functie batches.

* Het gemiddelde aantal aanvragen dat een Indexeer functie Batch kan genereren, geeft u een optimale instelling voor de mate van parallelle uitvoering. Als uw infra structuur die de vaardigheid host, dit niveau van gelijktijdigheid niet kan ondersteunen, kunt u overwegen om de mate van parallelle uitvoering te verlagen. Test uw configuratie als best practice met een paar documenten om uw keuzes te valideren op basis van de para meters.

* Testen met een kleiner voor beeld van documenten, evalueer de uitvoerings tijd van uw vaardig heden tot de totale tijd die nodig is om de subset van documenten te verwerken. Best Eden uw Indexeer functie meer tijd aan het bouwen van een batch of het wachten op een reactie van uw vaardigheid? 

* Overweeg de upstream-implicaties van parallelle uitvoering. Als de invoer naar een aangepaste vaardigheid een uitvoer van een vorige vaardigheid is, zijn alle vaardig heden in de vaardigens-schaalbaar om latentie te minimaliseren?

## <a name="error-handling-in-the-custom-skill"></a>Fout afhandeling in de aangepaste vaardigheid

Aangepaste vaardig heden moeten een geslaagde status code retour neren HTTP 200 wanneer de vaardigheid is voltooid. Als bij een of meer records in een batch fouten optreden, moet u rekening houden met het retour neren van code van meerdere status 207. De lijst met fouten of waarschuwingen voor de record moet het juiste bericht bevatten.

Items in een batch die fouten veroorzaken, resulteren in het mislukken van het bijbehorende document. Als het document moet worden voltooid, geeft u een waarschuwing.

Elke status code van meer dan 299 wordt geëvalueerd als een fout en alle verrijkingen zijn mislukt als gevolg van een mislukt document. 

### <a name="common-error-messages"></a>Veelvoorkomende foutberichten

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Stel de para meter time-out op de vaardigheid in om een langere uitvoerings duur toe te staan.

* `Could not execute skill because Web Api skill response is invalid.` Indicatie van de vaardig heden die geen bericht retour neren in de aangepaste vaardigheids antwoord indeling. Dit kan het gevolg zijn van een niet-onderschepte uitzonde ring in de vaardigheid.

* `Could not execute skill because the Web Api request failed.` Waarschijnlijk veroorzaakt door autorisatie fouten of-uitzonde ringen.

* `Could not execute skill.` Het resultaat van het vaardigheids antwoord wordt doorgaans toegewezen aan een bestaande eigenschap in de document hiërarchie.

## <a name="testing-custom-skills"></a>Aangepaste vaardig heden testen

Test eerst uw aangepaste vaardigheid met een REST API-client om te valideren:

* De vaardigheid implementeert de aangepaste vaardigheids interface voor aanvragen en antwoorden

* De vaardigheid retourneert een geldige JSON met het `application/JSON` MIME-type

* Retourneert een geldige HTTP-status code

Maak een [foutopsporingssessie](cognitive-search-debug-session.md) om uw vaardig heden toe te voegen aan de vaardig heden en zorg ervoor dat er een geldige verrijking wordt gegenereerd. Hoewel het niet mogelijk is om de prestaties van de vaardigheid af te stemmen met een foutopsporingssessie, kunt u ervoor zorgen dat de vaardigheid is geconfigureerd met geldige waarden en dat de verwachte verrijkte objecten worden geretourneerd.

## <a name="best-practices"></a>Aanbevolen procedures

* Hoewel de vaardig heden grotere nettoladingen kunnen accepteren en retour neren, kunt u het antwoord op 150 MB of minder beperken bij het retour neren van JSON.

* U kunt de Batch grootte instellen op de Indexeer functie en vaardigheid om ervoor te zorgen dat elke gegevens bron batch een volledige Payload voor uw vaardig heden genereert.

* Voor langlopende taken stelt u de time-out in op een hoge waarde om ervoor te zorgen dat de Indexeer functie geen fout bij het gelijktijdig verwerken van documenten.

* Optimaliseer de Batch grootte van de Indexeer functie, de vaardigheids Batch grootte en de vaardigheids graad van parallelle uitvoering om het laad patroon te genereren dat uw vaardig heden verwacht, minder grote aanvragen of veel kleine aanvragen.

* Bewaak aangepaste vaardig heden met gedetailleerde logboeken van fouten, omdat u scenario's kunt hebben waarin specifieke aanvragen consistent mislukken als gevolg van de gegevens variabiliteit.


## <a name="next-steps"></a>Volgende stappen
Gefeliciteerd! Uw aangepaste vaardigheid is nu aangepast om de door Voer van de Indexeer functie te maximaliseren. 

+ [Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Een aangepaste vaardigheid toevoegen aan een AI-verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
+ [Een Azure Machine Learning vaardigheid toevoegen](https://docs.microsoft.com/azure/search/cognitive-search-aml-skill)
+ [Debug-sessies gebruiken om wijzigingen te testen](https://docs.microsoft.com/azure/search/cognitive-search-debug-session)