---
title: Definieer en gebruik inhouds werk stromen via het hulp programma voor beoordeling-Content Moderator
titleSuffix: Azure Cognitive Services
description: U kunt de Azure Content Moderator Workflow Designer gebruiken om aangepaste werk stromen en drempel waarden te definiëren op basis van uw inhouds beleid.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 6eb2a2d2762b60a12bb9a24b92e2edae4b846cd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96904139"
---
# <a name="define-and-use-moderation-workflows"></a>Toezicht werk stromen definiëren en gebruiken

In deze hand leiding leert u hoe u [werk stromen](../review-api.md#workflows) kunt instellen en gebruiken op de website van het [controle programma](https://contentmoderator.cognitive.microsoft.com) . Werk stromen zijn op de cloud gebaseerde aangepaste filters die u kunt gebruiken om inhoud efficiënter af te handelen. Werk stromen kunnen verbinding maken met verschillende services om inhoud op verschillende manieren te filteren en vervolgens de juiste actie ondernemen. In deze hand leiding wordt beschreven hoe u de Content Moderator connector (die standaard is opgenomen) kunt gebruiken om inhoud te filteren en mensen beoordelingen in te stellen in een typisch toezicht scenario.

## <a name="create-a-new-workflow"></a>Een nieuwe werk stroom maken

Ga naar het [hulp programma content moderator controle](https://contentmoderator.cognitive.microsoft.com/) en meld u aan. Op het tabblad **instellingen** selecteert u **werk stromen**.

![Werk stroom instelling](images/2-workflows-0.png)

Selecteer op het volgende scherm **werk stroom toevoegen**.

![Een werk stroom toevoegen](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>Een naam en beschrijving toewijzen

Geef een naam op voor uw werk stroom, voer een beschrijving in en kies of de werk stroom afbeeldingen of tekst zal verwerken.

![Naam en beschrijving van werk stroom](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>Evaluatie criteria definiëren

In het volgende scherm gaat u naar de sectie **als** . Kies **voor waarde** in het bovenste vervolg keuzemenu. Zo kunt u de voor waarde configureren waaronder de werk stroom actie moet ondernemen. Als u meerdere voor waarden wilt gebruiken, kiest u in plaats daarvan **combi natie** . 

Selecteer vervolgens een connector. In dit voor beeld wordt **Content moderator** gebruikt. Afhankelijk van de connector die u kiest, krijgt u verschillende opties voor gegevens uitvoer. Zie de sectie [connectors](./configure.md#connectors) in de instellingen handleiding voor het hulp programma voor meer informatie over het instellen van andere connectors.

![Werk stroom connector selecteren](images/image-workflow-connect-to.PNG)

Kies de gewenste uitvoer om te gebruiken en stel de voor waarden in om deze te controleren.

![Werk stroom voorwaarde definiëren](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>De actie definiëren

Ga naar de sectie **vervolgens** , waar u een actie selecteert. In het volgende voor beeld wordt een afbeeldings revisie gemaakt en wordt een tag toegewezen. U kunt eventueel ook een alternatief (else) pad toevoegen en hiervoor een actie instellen.

![Werk stroom actie definiëren](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>De werk stroom opslaan

Noteer de naam van de werk stroom. u hebt de naam nodig om een toezicht taak te starten met de werk stroom-API (zie hieronder). Sla tot slot de werk stroom op met behulp van de knop **Opslaan** boven aan de pagina.

## <a name="test-the-workflow"></a>De werkstroom testen

Nu u een aangepaste werk stroom hebt gedefinieerd, test u deze met voorbeeld inhoud. Ga naar **werk stromen** en selecteer de bijbehorende knop voor het uitvoeren van de **werk stroom** .

![Werk stroom testen](images/image-workflow-execute.PNG)

Sla deze [voorbeeld afbeelding](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg) op uw lokale station op. Selecteer vervolgens **bestand (en) kiezen** en upload de installatie kopie naar de werk stroom.

![Een loper met een citaat dat is opgelegd op de afbeelding](images/sample-text.jpg)

### <a name="track-progress"></a>Voortgang bijhouden

U kunt de voortgang van de werk stroom weer geven in het volgende pop-upvenster.

![Werk stroom uitvoering bijhouden](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>Werk stroom actie verifiëren

Ga naar het tabblad **afbeelding** onder **controleren** en controleer of er een nieuwe afbeeldings controle is gemaakt.

![Afbeeldingen beoordelen](images/image-workflow-review.PNG)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u toezicht werk stromen kunt instellen en gebruiken vanuit het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com). Bekijk vervolgens de [API-console handleiding](../try-review-api-workflow.md) voor informatie over het programmatisch maken van werk stromen.
