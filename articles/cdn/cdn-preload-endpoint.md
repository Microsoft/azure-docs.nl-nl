---
title: Vooraf geladen assets op een Azure CDN eind punt | Microsoft Docs
description: Meer informatie over het vooraf laden van inhoud in cache op een Azure Content Delivery Network-eind punt. Deze functie is beschikbaar in bepaalde versies van het product.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385044"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Vooraf assets op een Azure CDN-eindpunt laden
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Standaard worden assets alleen in de cache opgeslagen wanneer ze zijn aangevraagd. Omdat de rand servers de inhoud nog niet hebben opgeslagen in de cache en de aanvraag moeten door sturen naar de oorspronkelijke server, kan de eerste aanvraag van elke regio langer duren dan volgende aanvragen. Als u deze latentie voor eerste treffers wilt vermijden, laadt u uw assets vooraf. Naast het bieden van een betere klant ervaring, kan het netwerk verkeer op de oorspronkelijke server worden gereduceerd.

> [!NOTE]
> Het vooraf laden van assets is handig voor grote gebeurtenissen of inhoud die gelijktijdig beschikbaar wordt voor veel gebruikers, zoals een nieuwe film versie of een software-update.
> 
> 

In deze zelf studie wordt u begeleid bij het vooraf laden van inhoud in de cache op alle Azure CDN Edge-knoop punten.

## <a name="to-pre-load-assets"></a>Activa vooraf laden
1. Blader in het [Azure Portal](https://portal.azure.com)naar het CDN-profiel met het eind punt dat u vooraf wilt laden. Het deel venster profiel wordt geopend.
    
2. Klik op het eind punt in de lijst. Het deel venster eind punt wordt geopend.
3. Selecteer **laden** in het deel venster CDN-eind punt.
   
    ![Deel venster CDN-eind punt](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Het deel venster **belasting** wordt geopend.
   
    ![Deel venster CDN laden](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Voor het **pad naar inhoud** voert u het volledige pad in van elk activum dat u wilt laden (bijvoorbeeld `/pictures/kitten.png` ).
   
   > [!TIP]
   > Wanneer u begint met het invoeren van tekst, worden er meer tekst vakken voor **het inhoudsdistributiepad weer** gegeven, zodat u een lijst met meerdere assets kunt bouwen. Als u assets uit de lijst wilt verwijderen, selecteert u de knop met het weglatings teken (...) en selecteert u vervolgens **verwijderen**.
   > 
   > Elk inhoudsdistributiepad moet een relatieve URL zijn die voldoet aan de volgende [reguliere expressies](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Laad één bestandspad: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Laad één bestand met een query reeks: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Omdat elk activum een eigen pad moet hebben, is er geen joker teken functionaliteit voor het vooraf laden van assets.
   > 
   > 
   
    ![Knop laden](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Wanneer u klaar bent met het invoeren van inhouds paden, selecteert u **laden**.
   

> [!NOTE]
> Er geldt een limiet van 10 laad aanvragen per minuut per CDN-profiel en er kunnen Maxi maal 50 gelijktijdige paden tegelijk worden verwerkt. Elk pad heeft een maximale padlengte van 1024 tekens.
> 
> 

## <a name="see-also"></a>Zie ook
* [Een Azure CDN-eind punt verwijderen](cdn-purge-endpoint.md)
* [Azure CDN REST API verwijzing: inhoud op een eind punt vooraf laden](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN REST API verwijzing: inhoud uit een eind punt verwijderen](/rest/api/cdn/cdn/endpoints/purgecontent)