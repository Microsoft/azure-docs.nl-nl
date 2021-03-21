---
title: Gemiddelde tekst met behulp van de API voor tekst toezicht-Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Test: tekst toezicht testen met behulp van de tekst toezicht-API in de online-console.'
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001997"
---
# <a name="moderate-text-from-the-api-console"></a>Tekst met gemiddelde van de API-console

Gebruik de [tekst toezicht-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in azure content moderator om de tekst inhoud te scannen voor scheld woorden en deze te vergelijken met aangepaste en gedeelde lijsten.

## <a name="get-your-api-key"></a>Uw API-sleutel ophalen

Voordat u de API in de online console kunt testen, moet u uw abonnements sleutel hebben. Dit bevindt zich op het tabblad **instellingen** in het vak **OCP-APIM-Subscription-Key** . Zie [Overzicht](overview.md) voor meer informatie.

## <a name="navigate-to-the-api-reference"></a>Ga naar de API-verwijzing

Ga naar de [tekst toezicht API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  De pagina **tekst scherm** wordt geopend.

## <a name="open-the-api-console"></a>Open de API-console

Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

  ![Pagina regio tekst scherm selecteren](images/test-drive-region.png)

  De API-console voor **tekst schermen** wordt geopend.

## <a name="select-the-inputs"></a>De invoer selecteren

### <a name="parameters"></a>Parameters

Selecteer de query parameters die u wilt gebruiken in het tekst scherm. Voor dit voor beeld gebruikt u de standaard waarde voor **taal**. U kunt deze waarde ook leeg laten, omdat de waarschijnlijke taal door de bewerking automatisch wordt gedetecteerd als onderdeel van de uitvoering.

> [!NOTE]
> Voor de para meter **taal** wijst `eng` of laat u het leeg om de door de machine ondersteunde **classificatie** te zien (preview-functie). **Deze functie ondersteunt alleen Engels**.
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) **van de** ondersteunde talen die in dit artikel worden vermeld, of laat het leeg.

Selecteer **waar** voor **auto correctie**, **PII** en **classificeren (preview)**. Laat het veld **ListID** leeg.

  ![Query parameters voor de console tekst scherm](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Inhoudstype

Selecteer bij **inhouds type** het type inhoud dat u wilt scherm. Voor dit voor beeld gebruikt u het standaard type **tekst/onbewerkte** inhoud. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

### <a name="sample-text-to-scan"></a>Te scannen voorbeeld tekst

Voer in het vak hoofd tekst van de aanvraag een gedeelte van de **inhoud** in. In het volgende voor beeld ziet u een opzettelijke intypen in de tekst.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Het antwoord analyseren

Het volgende antwoord toont de verschillende inzichten van de API. Het bevat mogelijk ongepaste, persoonlijke gegevens, classificatie (preview) en de automatisch gecorrigeerde versie.

> [!NOTE]
> De functie ' classificatie ' die door de machine wordt ondersteund, is beschikbaar in de preview-versie en ondersteunt alleen Engels.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Raadpleeg de conceptuele hand leiding voor [tekst toezicht](text-moderation-api.md) voor een gedetailleerde uitleg van alle secties in het JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of volg de [.NET SDK Quick](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) start om te integreren met uw toepassing.