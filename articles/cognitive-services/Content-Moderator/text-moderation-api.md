---
title: Tekst toezicht-Content Moderator
titleSuffix: Azure Cognitive Services
description: Tekst toezicht gebruiken voor mogelijke ongewenste tekst, persoonlijke gegevens en aangepaste lijsten met termen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912902"
---
# <a name="learn-text-moderation-concepts"></a>Concepten van tekst toezicht leren

Gebruik de tekst toezicht modellen van Content Moderator om tekst inhoud te analyseren.

U kunt de inhoud blok keren, goed keuren of controleren op basis van uw beleid en drempel waarden (Zie [Recensies, werk stromen en taken](./review-api.md) voor meer informatie over het instellen van Human beoordelingen). Gebruik de tekst toezicht modellen voor het uitbreiden van de menselijke toezicht op omgevingen waar partners, werk nemers en consumenten tekst inhoud genereren. Het gaat hierbij om chatruimten, discussieborden, chatbots, e-commerce-catalogi en documenten.

Het antwoord van de service bevat de volgende informatie:

- Scheld woorden: op term gebaseerde overeenkomst met ingebouwde lijst met ongepaste termen in verschillende talen
- Classificatie: door de machine ondersteunde classificatie in drie categorieën
- Persoonsgegevens
- Automatisch gecorrigeerde tekst
- Oorspronkelijke tekst
- Taal

## <a name="profanity"></a>Aanstootgevend taalgebruik

Als de API in een van de [ondersteunde talen](./language-support.md)ongepaste termen detecteert, worden deze termen opgenomen in het antwoord. Het antwoord bevat ook hun locatie ( `Index` ) in de oorspronkelijke tekst. De `ListId` in de volgende voor beeld-JSON verwijst naar termen die zijn gevonden in de [lijst met aangepaste termen](try-terms-list-api.md) , indien beschikbaar.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> Voor de para meter **taal** wijst `eng` of laat u het leeg om de door de machine ondersteunde **classificatie** te zien (preview-functie). **Deze functie ondersteunt alleen Engels**.
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) **van de** ondersteunde talen die in dit artikel worden vermeld, of laat het leeg.

## <a name="classification"></a>Classificatie

De **functie voor tekst classificatie** met automatische ondersteuning van content moderator ondersteunt **alleen Engels** en helpt mogelijk ongewenste inhoud te detecteren. De gemarkeerde inhoud kan worden beoordeeld als ongepast, afhankelijk van de context. Het brengt de kans op elke categorie over en kan een menselijke beoordeling aanbevelen. De functie maakt gebruik van een getraind model om mogelijke beledigende, negatieve of discriminerende taal te identificeren. Dit omvat slang, kortere woorden, aanstootgevende woorden en opzettelijk verkeerd gespelde termen voor beoordeling. 

In de volgende extractie in de JSON-analyse wordt een voorbeeld uitvoer weer gegeven:

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>Uitleg

- `Category1` verwijst naar de mogelijke aanwezigheid van taal die in bepaalde situaties als seksueel expliciet of volwassen kan worden beschouwd.
- `Category2` verwijst naar de mogelijke aanwezigheid van een taal die in bepaalde situaties kan worden beschouwd als expliciet of verouderd.
- `Category3` verwijst naar de mogelijke aanwezigheid van taal die in bepaalde situaties als aanstootgevend kan worden beschouwd.
- `Score` ligt tussen 0 en 1. Hoe hoger de score, hoe hoger het model is om te voors pellen dat de categorie van toepassing kan zijn. Deze functie is afhankelijk van een statistisch model in plaats van hand matig gecodeerde resultaten. We raden u aan om te testen met uw eigen inhoud om te bepalen hoe elke categorie wordt uitgelijnd op uw vereisten.
- `ReviewRecommended` is waar of onwaar, afhankelijk van de drempel waarden van de interne Score. Klanten moeten beoordelen of u deze waarde moet gebruiken of besluiten over aangepaste drempel waarden op basis van hun inhouds beleid.

## <a name="personal-data"></a>Persoonsgegevens

De functie persoonlijke gegevens detecteert de mogelijke aanwezigheid van deze gegevens:

- E-mailadres
- Post adres van de Verenigde Staten
- IP-adres
- Telefoon nummer VS

In het volgende voor beeld ziet u een voor beeld van een antwoord:

```json
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
}
```

## <a name="auto-correction"></a>Automatische correctie

Stel dat de invoer tekst (de ' lzay ' en ' f0x ' opzettelijk is):

> Het qu!/St Brown f0x springt over de lzaye Dog.

Als u voor de automatische correctie vraagt, bevat het antwoord de gecorrigeerde versie van de tekst:

> De Quick Brown Fox springt over de luie hond.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Uw aangepaste lijsten met voor waarden maken en beheren

De standaard instelling is dat de algemene lijst met termen prima werkt in de meeste gevallen. u kunt het beste een scherm maken met de termen die specifiek zijn voor uw bedrijfs behoeften. U kunt bijvoorbeeld de naam van een concurrerend merk uit berichten van gebruikers filteren.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.
>

In het volgende voor beeld ziet u de overeenkomende lijst-ID:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

De Content Moderator biedt een [term List-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) met bewerkingen voor het beheren van aangepaste termen lijsten. Begin met de [API-console van de termen lijst](try-terms-list-api.md) en gebruik de rest API code voorbeelden. Bekijk ook de [term lijsten .net Quick](term-lists-quickstart-dotnet.md) start als u bekend bent met Visual Studio en C#.

## <a name="next-steps"></a>Volgende stappen

Test de Api's met de [tekst toezicht API-console](try-text-api.md). Zie ook [Recensies, werk stromen en taken](./review-api.md) voor meer informatie over het instellen van Human Beoordelingen.