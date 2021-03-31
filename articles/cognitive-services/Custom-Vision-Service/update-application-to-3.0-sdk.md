---
title: Uw project bijwerken naar de 3,0-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het bijwerken van Custom Vision projecten van de vorige versie van de API naar de 3,0-API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647503"
---
# <a name="update-to-the-30-api"></a>Bijwerken naar de 3,0-API

Custom Vision heeft nu de algemene Beschik baarheid bereikt en heeft een API-update ondergaan.
Deze update bevat enkele nieuwe functies en belang rijke wijzigingen:

* De Voorspellings-API is nu onderverdeeld in twee op basis van het project type.
* Voor de export optie VAIDK (Vision AI Developer Kit) moet een project op een specifieke manier worden gemaakt.
* Er zijn standaard herhalingen verwijderd in het voor deel van het publiceren/ongedaan maken van de publicatie van een benoemde iteratie.

In deze hand leiding wordt uitgelegd hoe u uw projecten bijwerkt om te werken met de nieuwe API-versie. Zie de [release opmerkingen](release-notes.md) voor een volledige lijst met de wijzigingen.

## <a name="use-the-updated-prediction-api"></a>De bijgewerkte Voorspellings-API gebruiken

De 2. x-Api's hebben dezelfde Voorspellings aanroep gebruikt voor zowel afbeeldings classificaties als object detector projecten. Beide project typen zijn acceptabel voor de aanroepen van **PredictImage** en **PredictImageUrl** . Vanaf 3,0 hebben we deze API gesplitst zodat u het project type moet overeenkomen met de aanroep:

* Gebruik **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** en **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** om voor spellingen te ontvangen voor afbeeldings classificatie projecten.
* Gebruik **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** en **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** om voor spellingen te ontvangen voor object detectie projecten.

## <a name="use-the-new-iteration-publishing-workflow"></a>De nieuwe werk stroom voor het publiceren van iteraties gebruiken

De 2. x-Api's hebben de standaard herhaling of een opgegeven iteratie-ID gebruikt om te kiezen welke herhaling moet worden gebruikt voor de voor spelling. Vanaf 3,0 hebben we een publicatie stroom aangenomen waarbij u eerst een iteratie onder een opgegeven naam publiceert vanuit de trainings-API. Vervolgens geeft u de naam door aan de Voorspellings methoden om op te geven welke iteratie moet worden gebruikt.

> [!IMPORTANT]
> De 3,0-Api's maken geen gebruik van de standaard herhalings functie. Totdat we de oudere Api's hebben overgezet, kunt u de 2. x-Api's blijven gebruiken om een herhaling als de standaard waarde te scha kelen. Deze Api's worden gedurende een bepaalde tijd onderhouden en u kunt de methode **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** aanroepen om een herhaling als standaard te markeren.

### <a name="publish-an-iteration"></a>Een herhaling publiceren

Zodra een iteratie is getraind, kunt u deze beschikbaar stellen voor voor spellingen met behulp van de methode **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** . Als u een iteratie wilt publiceren, hebt u de resource-ID voor de voor spelling nodig, die beschikbaar is op de instellingen pagina van de CustomVision-website.

![De pagina met de Custom Vision website-instellingen met de Voorspellings Resource-ID wordt beschreven.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> U kunt deze informatie ook ophalen via de [Azure-Portal](https://portal.azure.com) door naar de Custom Vision Voorspellings bron te gaan en **Eigenschappen** te selecteren.

Zodra de herhaling is gepubliceerd, kunnen apps deze gebruiken voor voor spelling door de naam op te geven in de API-aanroep voor de voor spelling. Als u een iteratie niet beschikbaar wilt maken voor Voorspellings aanroepen, gebruikt u de **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** -API.

## <a name="next-steps"></a>Volgende stappen

* [Naslag documentatie voor trainings-API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Naslag documentatie voor de voor Spellings-API (REST)](https://go.microsoft.com/fwlink/?linkid=865445)