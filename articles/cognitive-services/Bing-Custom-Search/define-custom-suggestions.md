---
title: Aangepaste suggesties voor automatische suggesties definiëren-Bing Aangepaste zoekopdrachten
titleSuffix: Azure Cognitive Services
description: Aangepaste automatische suggestie retourneert een lijst met voorgestelde zoek opdracht reeksen die relevant zijn voor uw zoek ervaring.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 986a7c8f1282b76c2ae94eebcc2c31dc3b3e7cad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352011"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Uw aangepaste automatische suggesties configureren

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Aangepaste automatische suggestie retourneert een lijst met voorgestelde zoek opdracht reeksen die relevant zijn voor uw zoek ervaring. De voorgestelde query teken reeksen zijn gebaseerd op een gedeeltelijke query teken reeks die de gebruiker in het zoekvak heeft. De lijst bevat een maximum van 10 suggesties. 

U geeft aan of u alleen aangepaste suggesties wilt retour neren of ook Bing suggesties wilt toevoegen. Als u Bing Suggestions opneemt, worden er aangepaste suggesties voor de Bing-suggesties weer gegeven. Als u voldoende relevante suggesties levert, is het mogelijk dat de geretourneerde lijst met suggesties geen Bing-suggesties bevat. Bing-suggesties zijn altijd in de context van uw aangepaste zoek exemplaar. 

Klik op het tabblad **Automatische suggestie** om suggesties voor zoek query's voor uw exemplaar te configureren.  

> [!NOTE]
> Als u deze functie wilt gebruiken, moet u zich abonneren op een aangepaste zoek opdracht op het juiste niveau (Zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Het kan tot 24 uur duren voordat suggesties worden weer gegeven in het verzorgings eindpunt (API of gehoste gebruikers interface).

## <a name="enable-bing-suggestions"></a>Bing-suggesties inschakelen

Als u Bing-suggesties wilt inschakelen, schakelt u de schuif regelaar voor **automatische Bing-suggesties** in op de positie aan. De schuif regelaar wordt blauw.

## <a name="add-your-own-suggestions"></a>Uw eigen suggesties toevoegen

Als u uw eigen query teken reeks suggesties wilt toevoegen, voegt u deze toe aan de lijst onder door de **gebruiker gedefinieerde suggesties**. Nadat u een suggestie in de lijst hebt toegevoegd, drukt u op ENTER of klikt u op het **+** pictogram. U kunt de suggestie in elke taal opgeven. U kunt Maxi maal 5.000 query teken reeks suggesties toevoegen.

## <a name="upload-suggestions"></a>Suggesties voor uploaden

Als optie kunt u een lijst met suggesties uit een bestand uploaden. Het bestand moet één Zoek query teken reeks per regel bevatten. Als u het bestand wilt uploaden, klikt u op het pictogram uploaden en selecteert u het bestand dat u wilt uploaden. De service extraheert de suggesties uit het bestand en voegt deze toe aan de lijst.

## <a name="remove-suggestions"></a>Suggesties verwijderen

Als u een suggestie voor een query reeks wilt verwijderen, klikt u op het pictogram verwijderen naast het voor Stel dat u wilt verwijderen.

## <a name="block-suggestions"></a>Suggesties blok keren

Als u Bing Suggestions opneemt, kunt u een lijst met zoek opdracht reeksen toevoegen die niet door Bing moeten worden geretourneerd. Klik op **geblokkeerde suggesties weer geven** om geblokkeerde query reeksen toe te voegen. Voeg de query reeks toe aan de lijst en druk op ENTER of klik op het **+** pictogram. U kunt Maxi maal 50 geblokkeerde query reeksen toevoegen.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Het kan tot 24 uur duren voordat aangepaste wijzigingen in de configuratie van de automatische suggestie van kracht worden.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Automatische suggesties inschakelen in de gehoste UI

Klik op **gehoste gebruikers interface** om suggesties voor query reeksen voor uw gehoste gebruikers interface in te scha kelen. Schuif omlaag naar de sectie **aanvullende configuratie** . Selecteer onder **zoeken** **op** Internet voor **Automatische suggestie inschakelen**. Als u automatische suggesties wilt inschakelen, moet u een indeling selecteren die een zoekvak bevat.


## <a name="calling-the-autosuggest-api"></a>De automatische suggestie-API aanroepen

Als u voorgestelde query teken reeksen wilt ophalen met behulp van de Bing Custom Search-API, stuurt `GET` u een aanvraag naar het volgende eind punt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Het antwoord bevat een lijst met `SearchAction` objecten die de voorgestelde query teken reeksen bevatten.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Elke suggestie bevat een `displayText` en- `query` veld. Het `displayText` veld bevat de voorgestelde query teken reeks die u gebruikt om de vervolg keuzelijst van uw zoekvak in te vullen.

Als de gebruiker een voorgestelde query teken reeks in de vervolg keuzelijst selecteert, gebruikt u de query reeks in het `query` veld bij het aanroepen van de [Bing Custom Search-API](overview.md).


## <a name="next-steps"></a>Volgende stappen

- [Aangepaste suggesties ophalen]()
- [Zoek uw aangepaste exemplaar](./search-your-custom-view.md)
- [Aangepaste gehoste gebruikers interface configureren en gebruiken](./hosted-ui.md)