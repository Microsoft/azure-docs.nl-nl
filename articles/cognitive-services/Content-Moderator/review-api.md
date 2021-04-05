---
title: Recensies, werk stromen en taken concepten-Content Moderator
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u meer informatie over de basis concepten van het hulp programma voor beoordeling. Beoordelingen, werk stromen en taken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905193"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Beoordelingen, werk stromen en taken voor content toezicht

Content Moderator combineert de door de machine ondersteunde toezicht met de mogelijkheden van Human-in-the-loop om een optimaal toezicht proces te maken voor praktijk scenario's. Dit wordt gedaan via het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com)op basis van de Cloud. In deze hand leiding vindt u informatie over de belangrijkste concepten van het hulp programma voor beoordeling: Recensies, werk stromen en taken.

## <a name="reviews"></a>Beoordelingen

Bij een beoordeling wordt inhoud geüpload naar het hulp programma beoordeling. U kunt deze weer geven door te klikken op het inhouds type onder **controleren** op het dash board. Vanuit het scherm controleren kunt u de toegepaste labels wijzigen en uw eigen aangepaste labels Toep assen. Wanneer u een beoordeling verzendt, worden de resultaten verzonden naar een opgegeven eind punt van de retour aanroep en wordt de inhoud verwijderd van de site.

> [!div class="mx-imgBorder"]
> ![De vervolg keuzelijst controle is gemarkeerd. Deze inhouds typen worden weer gegeven: afbeelding, tekst en video.](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>Beoordelingen beheren

Ga in het dash board naar **beheer**  ->  **beoordelingen beheren** om het scherm beheer weer te geven. Hier kunt u een lijst met alle beoordelingen bekijken (in behandeling en voltooid).

Met de **actie** knop met drie puntjes op elke beoordeling kunt u naar het controle scherm gaan of de geschiedenis van die beoordeling controleren.

> [!div class="mx-imgBorder"]
> ![Bekijk de website van het hulp programma op het scherm controleren](./Review-Tool-user-Guide/images/manage-reviews.png)

Gebruik de **Zoek** werkbalk om de beoordelingen te sorteren op diverse categorieën, zoals beoordelings status, tags, inhouds type, subteams, toegewezen gebruikers en gemaakt/gewijzigd op.

> [!div class="mx-imgBorder"]
> ![De werk balk Zoeken wordt weer gegeven. Er zijn verschillende keuze lijsten met invoervak voor het invoeren van zoek criteria, zoals de status van de controle en de tags.](./Review-Tool-user-Guide/images/review-search.png)

Raadpleeg de [hand leiding voor het beoordelings programma](./review-tool-user-guide/review-moderated-images.md) om aan de slag te gaan met het maken van recensies of Raadpleeg de [API-console handleiding](./try-review-api-review.md) voor informatie over hoe u dit kunt doen via een programma.

## <a name="workflows"></a>Werkstromen

Een werk stroom is een op de cloud gebaseerd gepersonaliseerd filter voor inhoud. Werk stromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie ondernemen. Met de Content Moderator-connector kunt u met een werk stroom automatisch toezicht Tags Toep assen en beoordelingen maken met verzonden inhoud.

### <a name="view-workflows"></a>Werk stromen weer geven

Als u uw bestaande werk stromen wilt weer geven, gaat u naar het [hulp programma controleren](https://contentmoderator.cognitive.microsoft.com/) en selecteert u **beheer**  >  **werk stromen**.

> [!div class="mx-imgBorder"]
> ![Standaardwerk stroom](images/default-workflow-list.png)

Werk stromen worden gedefinieerd als JSON-teken reeksen, waardoor ze via een programma toegankelijk zijn. Als u de optie **bewerken** selecteert voor uw werk stroom en vervolgens het tabblad **JSON** selecteert, ziet u een JSON-expressie zoals het volgende:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Raadpleeg de [hand leiding voor het hulp programma](./review-tool-user-guide/workflows.md) voor het maken en gebruiken van werk stromen of Raadpleeg de [API-console handleiding](./try-review-api-workflow.md) voor informatie over hoe u dit kunt doen via een programma.

## <a name="jobs"></a>Taken

Een toezicht taak fungeert als een soort wrapper voor de functionaliteit van toezicht op inhoud, werk stromen en Beoordelingen. De taak scant uw inhoud met behulp van de Content Moderator afbeeldings toezicht-API of tekst toezicht-API en controleert deze vervolgens op de aangewezen werk stroom. Op basis van de werk stroom resultaten kan de inhoud niet worden gecontroleerd in het [beoordelings programma](./review-tool-user-guide/human-in-the-loop.md). Hoewel zowel beoordelingen als werk stromen kunnen worden gemaakt en geconfigureerd met hun respectieve Api's, kunt u met de taak-API een gedetailleerd rapport verkrijgen van het hele proces (dat kan worden verzonden naar een opgegeven call back-eind punt).

Zie de [API-console handleiding](./try-review-api-job.md) om aan de slag te gaan met taken.

## <a name="next-steps"></a>Volgende stappen

* Test de [taak-API-console](try-review-api-job.md)en gebruik de rest API code voorbeelden. Als u bekend bent met Visual Studio en C#, raadpleegt u ook de [taken .net Quick](moderation-jobs-quickstart-dotnet.md)start. 
* Ga voor recensies naar aan de slag met de [API-console voor controle](try-review-api-review.md)en gebruik de rest API code voorbeelden. Zie de sectie Recensies van [.net Quick](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)start.
* Gebruik voor video revisies de [Snelstartgids voor video revisie](video-reviews-quickstart-dotnet.md)en leer hoe u [Transcripten kunt toevoegen aan de video beoordeling](video-transcript-reviews-quickstart-dotnet.md).