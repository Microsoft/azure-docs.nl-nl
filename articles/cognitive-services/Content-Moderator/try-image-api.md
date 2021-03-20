---
title: Gematige installatie kopieën met de API-console-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de afbeeldings toezicht-API in azure Content Moderator voor het initiëren van scan-en revisie werk stromen voor afbeeldings inhoud.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 542fadd5e5ab91be7b7113064bf8c998dae08d12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912936"
---
# <a name="moderate-images-from-the-api-console"></a>Gematige installatie kopieën van de API-console

Gebruik de [afbeeldings toezicht-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) in azure content moderator voor het initiëren van scan-en revisie werk stromen voor afbeeldings inhoud. De taak toezicht scant uw inhoud voor scheld woorden en vergelijkt deze met aangepaste en gedeelde ip's.

## <a name="use-the-api-console"></a>De API-console gebruiken
Voordat u de API in de online console kunt testen, moet u uw abonnements sleutel hebben. Dit bevindt zich op het tabblad **instellingen** in het vak **OCP-APIM-Subscription-Key** . Zie [Overzicht](overview.md) voor meer informatie.

1. Ga naar de [afbeeldings toezicht-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   De pagina **installatie kopie-** toezicht controle op afbeelding wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Afbeelding proberen: selectie van het pagina-gebied evalueren](images/test-drive-region.png)
  
   De **afbeelding-evalueren** van de API-console wordt geopend.

3. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

   ![Afbeelding uitproberen-sleutel van console abonnement evalueren](images/try-image-api-1.PNG)

4. Gebruik de standaard voorbeeld afbeelding in het vak **hoofd tekst van aanvraag** of geef een afbeelding op die u wilt scannen. U kunt de installatie kopie zelf als binaire-bits gegevens verzenden of een openbaar toegankelijke URL voor een installatie kopie opgeven. 

   Voor dit voor beeld gebruikt u het pad in het vak **hoofd tekst van aanvraag** en selecteert u **verzenden**. 

   ![Afbeelding proberen: de hoofd tekst van de console evalueren](images/try-image-api-2.PNG)

   Dit is de installatie kopie op die URL:

   ![Afbeelding proberen-voorbeeld installatie kopie van console evalueren](images/sample-image.jpg) 

5. Selecteer **Verzenden**.

6. De API retourneert een waarschijnlijkheids score voor elke classificatie. Er wordt ook een bepalen of de installatie kopie voldoet aan de voor waarden (**True** of **False**). 

   ![Afbeelding uitproberen-test-en voorwaarde bepaling van de console evalueren](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Gezichtsdetectie

U kunt de afbeeldings toezicht-API gebruiken om gezichten te vinden in een afbeelding. Deze optie kan nuttig zijn wanneer u privacy-problemen hebt en u wilt voor komen dat een specifiek gezicht wordt gepost op uw platform. 

1. Selecteer in de [afbeeldings toezicht-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)in het linkermenu onder **afbeelding** **zoeken naar gezichten**. 

   De pagina **gezichten zoeken** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Afbeelding proberen: gezichten zoeken pagina regio selecteren](images/test-drive-region.png)

   De **afbeelding-gezichten vindt** u de API-console wordt geopend.

3. Geef een afbeelding op die moet worden gescand. U kunt de installatie kopie zelf als binaire-bits gegevens verzenden of een openbaar toegankelijke URL naar een afbeelding opgeven. Dit voor beeld is een koppeling naar een afbeelding die wordt gebruikt in een CNN-artikel.

   ![Afbeelding van voor beeld van gezichten zoeken](images/try-image-api-face-image.jpg)

   ![Afbeelding van voor beeld van gezichten zoeken](images/try-image-api-face-request.png)

4. Selecteer **Verzenden**. In dit voor beeld vindt de API twee gezichten en worden de coördinaten ervan in de afbeelding geretourneerd.

   ![Afbeelding proberen: voor beeld van gezichten zoeken](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Tekst detectie via de OCR-mogelijkheid

U kunt de Content Moderator OCR-mogelijkheid gebruiken om tekst in afbeeldingen te detecteren.

1. Selecteer in de [afbeeldings toezicht-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)in het linkermenu, onder **afbeelding**, **OCR**. 

   De pagina **afbeelding-OCR** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Afbeelding: selectie OCR-pagina regio](images/test-drive-region.png)

   De **afbeelding: OCR API-** console wordt geopend.

3. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

4. Gebruik de standaard voorbeeld afbeelding in het vak **hoofd tekst van aanvraag** . Dit is de afbeelding die wordt gebruikt in de vorige sectie.

5. Selecteer **Verzenden**. De geëxtraheerde tekst wordt weer gegeven in JSON:

   ![Afbeelding: vak voor beeld van OCR-voorbeeld reactie](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of volg de [.NET SDK Quick](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) start om afbeeldings toezicht toe te voegen aan uw toepassing.