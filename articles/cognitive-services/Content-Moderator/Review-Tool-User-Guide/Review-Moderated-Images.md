---
title: Gebruik inhouds beoordelingen via het hulp programma voor beoordeling-Content Moderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u met het hulp programma controleren afbeeldingen in een webportal kunt controleren.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 77d7b8a0bf4d7fe9a94a61ea3f2f4279246ffb69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903884"
---
# <a name="create-human-reviews"></a>Mensen beoordelingen maken

In deze hand leiding leert u hoe u [beoordelingen](../review-api.md#reviews) kunt instellen op de website van het controle programma. Beoordelingen van de winkel en de weer gave van inhoud voor menselijke moderators om te beoordelen. Moderators kunnen de toegepaste labels wijzigen en hun eigen aangepaste labels Toep assen. Wanneer een gebruiker een beoordeling voltooit, worden de resultaten verzonden naar een opgegeven eind punt van de retour aanroep en wordt de inhoud verwijderd van de site.

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="image-reviews"></a>Beoordelingen van afbeeldingen

1. Ga naar het [hulp programma voor controle](https://contentmoderator.cognitive.microsoft.com/), selecteer het tabblad **try** en upload enkele afbeeldingen om te controleren.
1. Zodra de verwerking van de geüploade installatie kopieën is voltooid, gaat u naar het tabblad **controleren** en selecteert u **afbeelding**.

    ![Chrome-browser met het beoordelings programma met de optie voor het controleren van de afbeelding gemarkeerd](images/review-images-1.png)

    De afbeeldingen worden weer gegeven met labels die zijn toegewezen door het automatische toezicht proces. De installatie kopieën die u via het hulp programma voor beoordeling hebt verzonden, zijn niet zichtbaar voor andere revisoren.

1. U kunt de beoordelingen eventueel verplaatsen **naar** de schuif regelaar (1) om het aantal installatie kopieën dat op het scherm wordt weer gegeven, aan te passen. Klik op de knoppen met een **Label** of een **Label** (2) om de afbeeldingen dienovereenkomstig te sorteren. Klik op het deel venster tag (3) om het in of uit te scha kelen.

    ![Chrome-browser met het beoordelings programma met gelabelde afbeeldingen voor beoordeling](images/review-images-2.png)

1. Als u meer informatie wilt weer geven over een afbeelding, klikt u op het beletsel teken in de miniatuur en selecteert u **Details weer geven**. U kunt een installatie kopie toewijzen aan een subteam met de optie **verplaatsen naar** (Zie de sectie [teams](./configure.md#manage-team-and-subteams) voor meer informatie over subteams).

    ![Een afbeelding met de optie Details weer geven gemarkeerd](images/review-images-3.png)

1. Blader op de pagina Details van de afbeeldings controle-informatie.

    ![Een afbeelding met toezicht details die worden weer gegeven in een afzonderlijk deel venster](images/review-images-4.png)

1. Zodra u de label toewijzingen hebt gecontroleerd en zo nodig hebt bijgewerkt, klikt u op **volgende** om uw beoordelingen in te dienen. Nadat u hebt ingediend, hebt u ongeveer vijf seconden op de knop **vorige** om terug te gaan naar het vorige scherm en de afbeeldingen opnieuw te bekijken. Daarna worden de afbeeldingen niet meer in de verzend wachtrij geplaatst en is de knop **vorige** niet meer beschikbaar.

## <a name="text-reviews"></a>Beoordelingen van teksten

Tekst beoordelingen worden op dezelfde manier gebruikt als voor beeld Beoordelingen. In plaats van inhoud te uploaden, kunt u tekst schrijven of plakken (Maxi maal 1.024 tekens). Content Moderator analyseert vervolgens de tekst en past labels toe (naast andere informatie over de controle, zoals Gods taal en persoonlijke gegevens). In tekst beoordelingen kunt u de toegepaste Tags en/of aangepaste labels Toep assen voordat u de beoordeling verzendt.

![Scherm afbeelding van het hulp programma beoordeling met gemarkeerde tekst in een Chrome-browser venster](../images/reviewresults_text.png)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u beoordelingen kunt instellen en gebruiken via het [hulp programma content moderator beoordeling](https://contentmoderator.cognitive.microsoft.com). Raadpleeg vervolgens de [API-console handleiding](../try-review-api-review.md) of de [.NET SDK Quick](../client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) start voor meer informatie over het programmatisch maken van Beoordelingen.