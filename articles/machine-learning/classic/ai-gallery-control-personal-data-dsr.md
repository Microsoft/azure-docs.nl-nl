---
title: 'ML Studio (klassiek): Azure AI Gallery gegevens beheren-Azure'
description: U kunt de gebruikers gegevens van uw product exporteren en verwijderen uit Azure AI Gallery met behulp van de interface of de AI Gallery Catalog-API. In dit artikel wordt beschreven hoe u dit kunt doen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519232"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Gebruikers gegevens in een product weer geven en verwijderen uit Azure AI Gallery



U kunt de gebruikers gegevens van uw product weer geven en verwijderen uit Azure AI Gallery met behulp van de interface of de AI Gallery Catalog-API. In dit artikel leest u hoe.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Uw gegevens in de AI-galerie weer geven met de gebruikers interface

U kunt items weer geven die u hebt gepubliceerd via de gebruikers interface van de Azure AI Gallery-website. Gebruikers kunnen open bare en niet-vermelde oplossingen, projecten, experimenten en andere gepubliceerde items bekijken:

1.    Meld u aan bij de [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profiel afbeelding in de rechter bovenhoek en vervolgens op de naam van het account om uw profiel pagina te laden.
3.    Op de profiel pagina worden alle items weer gegeven die zijn gepubliceerd in de galerie, met inbegrip van niet-gevermeldde vermeldingen.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>De AI Gallery Catalog-API gebruiken om uw gegevens weer te geven

U kunt de gegevens die zijn verzameld via de AI Gallery Catalog-API weer geven, die toegankelijk zijn op https://catalog.cortanaanalytics.com/entities . Als u gegevens wilt weer geven, hebt u de auteur-ID nodig. Als u niet-vermelde entiteiten wilt weer geven via de catalogus-API, hebt u een toegangs token nodig.

Catalogus reacties worden geretourneerd in JSON-indeling.

### <a name="get-an-author-id"></a>Een auteur-ID ophalen
De auteur-ID is gebaseerd op het e-mail adres dat wordt gebruikt bij het publiceren naar de Azure AI Gallery. Dit verandert niet:

1. Meld u aan bij [Azure AI Gallery](https://gallery.azure.ai/).
2. Klik op de profiel afbeelding in de rechter bovenhoek en vervolgens op de naam van het account om uw profiel pagina te laden.
3. De URL in de adres balk bevat de alfanumerieke ID die volgt `authorId=` . Bijvoorbeeld voor de URL: \
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    Auteur-ID: \
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Uw toegangs Token ophalen

U hebt een toegangs token nodig om niet-vermelde entiteiten te bekijken via de catalogus-API. Zonder een toegangs token kunnen gebruikers nog steeds open bare entiteiten en andere gebruikers gegevens weer geven.

Als u een toegangs token wilt ophalen, moet u de `DataLabAccessToken` header van een HTTP-aanvraag inspecteren die de browser maakt naar de catalogus-API terwijl u bent aangemeld:

1.    Meld u aan bij de [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klik op de profiel afbeelding in de rechter bovenhoek en vervolgens op de naam van het account om uw profiel pagina te laden.
3.    Open het deel venster browser Ontwikkelhulpprogramma's door op F12 te drukken, selecteer het tabblad netwerk en vernieuw de pagina. 
4. Filter aanvragen op de teken reeks *catalogus* door te typen in het tekstvak filter.
5.    Zoek in aanvragen naar de URL `https://catalog.cortanaanalytics.com/entities` een GET-aanvraag en selecteer het tabblad *headers* . Schuif omlaag naar de sectie *aanvraag headers* .
6.    Onder de header `DataLabAccessToken` bevindt zich het alfanumerieke token. Om uw gegevens veilig te houden, deelt u dit token niet.

### <a name="view-user-information"></a>Gebruikers gegevens weer geven
Bekijk in de volgende URL de informatie in het profiel van een gebruiker met behulp van de auteur-ID die u in de vorige stappen hebt gekregen `[AuthorId]` :

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

Dit URL-verzoek is bijvoorbeeld:

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

Retourneert een antwoord zoals:

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>Open bare entiteiten weer geven

De catalogus-API slaat informatie over gepubliceerde entiteiten op in de Azure AI Gallery die u ook rechtstreeks kunt bekijken op de website van de [AI-galerie](https://gallery.azure.ai/). 

Als u gepubliceerde entiteiten wilt weer geven, gaat u naar de volgende URL en vervangt u deze door `[AuthorId]` de auteur-id die is verkregen in [een Auteur-id ophalen](#get-an-author-id) hierboven.

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

Bijvoorbeeld:

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>Onvermelde en open bare entiteiten weer geven

Met deze query worden alleen open bare entiteiten weer gegeven. Als u al uw entiteiten, inclusief ongelijsteerde, wilt weer geven, geeft u het toegangs token op dat u hebt verkregen uit de vorige sectie.

1.    Maak met een hulp programma zoals [postman](https://www.getpostman.com)een HTTP GET-aanvraag naar de catalogus-URL, zoals wordt beschreven in [uw toegangs Token ophalen](#get-your-access-token).
2.    Maak een HTTP-aanvraag header `DataLabAccessToken` met de naam, waarbij de waarde is ingesteld op het toegangs token.
3.    Verzend de HTTP-aanvraag.

> [!TIP]
> Als niet-vermelde entiteiten niet worden weer gegeven in reacties van de catalogus-API, heeft de gebruiker mogelijk een ongeldig of verlopen toegangs token. Meld u af bij de Azure AI Gallery en herhaal de stappen in [uw toegangs Token ophalen](#get-your-access-token) om het token te vernieuwen. 
