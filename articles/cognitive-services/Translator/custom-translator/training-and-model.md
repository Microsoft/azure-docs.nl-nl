---
title: Wat is training en model? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: Een model is het systeem, dat vertalingen biedt voor een specifiek taal paar. Het resultaat van een geslaagde training is een model. Bij het trainen van een model zijn drie wederzijds exclusieve gegevens sets vereist voor de trainings gegevensset, het afstemmen van de gegevensset en het testen van de gegevensset.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897931"
---
# <a name="what-are-trainings-and-models"></a>Wat zijn trainingen en modellen?

Een model is het systeem, dat vertalingen biedt voor een specifiek taal paar.
Het resultaat van een geslaagde training is een model. Bij het trainen van een model zijn drie wederzijds exclusieve document typen vereist: training, afstemming en testen. U kunt ook een woordenlijst document type opgeven. Raadpleeg de uitlijning van een [zin](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Als er alleen trainings gegevens worden weer gegeven bij het in de wachtrij plaatsen van een training, zullen aangepaste vertalers automatisch afstemmings-en test gegevens verzamelen. Er wordt een wille keurige subset van zinnen gebruikt uit uw trainings documenten en deze zinnen worden uitgesloten van de trainings gegevens zelf.

## <a name="training-document-type-for-custom-translator"></a>Type trainings document voor aangepaste vertaler

Documenten die zijn opgenomen in de Trainingsset, worden door de aangepaste vertaler gebruikt als basis voor het bouwen van uw model. Tijdens het uitvoeren van de training worden zinnen die aanwezig zijn in deze documenten uitgelijnd (of gekoppeld). U kunt Liberties nemen bij het opstellen van uw set trainings documenten. U kunt documenten toevoegen waarvan u denkt dat ze in één model relevant zijn voor de relevantie. Sluit ze opnieuw uit om de impact van de [Bleu (tweetalige evaluatie-onderstudie)](what-is-bleu-score.md)te bekijken. Als u de afstemmings set en de test constante constant houdt, kunt u experimenteren met de samen stelling van de Trainingsset. Deze aanpak is een efficiënte manier om de kwaliteit van uw Vertaal systeem te wijzigen.

U kunt meerdere trainingen binnen een project uitvoeren en de Bleu- [scores](what-is-bleu-score.md) voor alle trainings runs vergelijken. Wanneer u meerdere trainingen uitvoert om te vergelijken, moet u ervoor zorgen dat dezelfde afstemmings-en test gegevens elke keer worden opgegeven. Zorg er ook voor dat u de resultaten hand matig controleert op het tabblad [testen](how-to-view-system-test-results.md) .

## <a name="tuning-document-type-for-custom-translator"></a>Type afstemmings document voor aangepaste vertaler

Parallelle documenten die zijn opgenomen in deze set, worden door de aangepaste vertaler gebruikt om het Vertaal systeem te optimaliseren voor optimale resultaten.

De afstemmings gegevens worden gebruikt tijdens de training om alle para meters en wegingen van het Vertaal systeem aan de optimale waarden aan te passen. Kies uw afstemmings gegevens zorgvuldig: de afstemmings gegevens moeten representatief zijn voor de inhoud van de documenten die u in de toekomst wilt vertalen. De afstemmings gegevens hebben een grote invloed op de kwaliteit van de geproduceerde vertalingen. Met afstemming kan het Vertaal systeem vertalingen bieden die het dichtst bij de voor beelden vallen die u bij de afstemmings gegevens levert. U hebt niet meer dan 2500 zinnen nodig in uw afstemmings gegevens. Voor een optimale Vertaal kwaliteit kunt u het beste de afstemmings hand matig selecteren door de meest representatieve selectie van zinnen te kiezen.

Wanneer u uw afstemmings maakt, kiest u zinnen die een zinvolle en representatieve lengte vormen van de toekomstige zinnen die u verwacht te vertalen. U moet ook zinnen met woorden en zinsdelen kiezen die u wilt vertalen in de geschatte distributie die u verwacht in uw toekomstige vertalingen. In de praktijk levert een lengte van 7 tot 10 woorden de beste resultaten op, omdat deze zinnen voldoende context bevatten voor het weer geven van de verbuiging en een woord lengte hebben die significant is, zonder dat dit complex is.

Een goede beschrijving van het type zinnen dat in de afstemmings moet worden gebruikt, is Prose: de werkelijke Fluent-zinnen. Geen tabel cellen, geen Poems, geen lijsten met dingen, niet alleen lees tekens of getallen in een normale taal van de zin.

Als u uw afstemmings gegevens hand matig selecteert, mag deze niet dezelfde zinnen bevatten als uw trainings-en test gegevens. De afstemmings gegevens hebben een grote invloed op de kwaliteit van de vertalingen. Kies de zinnen zorgvuldig.

Als u niet zeker weet wat u moet kiezen voor uw afstemmings gegevens, selecteert u alleen de trainings gegevens en laat u aangepaste Translator de afstemmings gegevens selecteren. Wanneer u de aangepaste vertaler de afstemmings gegevens automatisch wilt laten kiezen, gebruikt deze een wille keurige subset van zinnen uit uw documenten voor tweetalige trainingen en worden deze zinnen niet in het trainings materiaal zelf opgenomen.

## <a name="testing-dataset-for-custom-translator"></a>Gegevensset voor aangepaste Translator testen

Parallelle documenten die zijn opgenomen in de testset, worden gebruikt voor het berekenen van de Score van de BLEU (tweetalige evaluatie). Deze score geeft de kwaliteit van het Vertaal systeem aan. Deze score vertelt u eigenlijk hoe nauw keurige vertalingen van het Vertaal systeem die het resultaat zijn van deze training overeenkomen met de referentie zinnen in de test gegevensset.

De BLEU-Score is een meting van de Delta tussen de automatische vertaling en de referentie-omzetting. De waarde ligt tussen 0 en 100. Een Score van 0 geeft aan dat er geen enkel woord van de verwijzing wordt weer gegeven in de vertaling. Een Score van 100 geeft aan dat de automatische vertaling precies overeenkomt met de verwijzing: hetzelfde woord bevindt zich op exact dezelfde positie. De score die u ontvangt, is het gemiddelde van de BLEUe score voor alle zinnen van de test gegevens.

De test gegevens moeten parallelle documenten bevatten waarbij de doel taal zinnen de meest wenselijkste vertalingen zijn van de bijbehorende bron zinnen in het bron doel paar. U kunt ook de criteria gebruiken die u hebt gebruikt om de afstemmings gegevens samen te stellen. De test gegevens hebben echter geen invloed op de kwaliteit van het Vertaal systeem. Het wordt uitsluitend gebruikt voor het genereren van de BLEU-score voor u.

U hebt niet meer dan 2.500 zinnen nodig als test gegevens. Wanneer u ervoor kiest om het systeem automatisch te testen, wordt een wille keurige subset van zinnen uit uw tweetalige trainings documenten gebruikt en worden deze zinnen niet in het trainings materiaal zelf opgenomen.

U kunt de aangepaste vertalingen van de testset bekijken en deze vergelijken met de vertalingen die zijn opgegeven in de testset door te navigeren naar het tabblad test in een model.