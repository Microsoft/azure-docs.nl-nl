---
title: Een Azure CDN-eind punt verwijderen | Microsoft Docs
description: Meer informatie over het verwijderen van alle inhoud in de cache van een Azure Content Delivery Network-eind punt. De elementen van de Edge-knoop punten worden in de cache opgeslagen totdat de time to Live verloopt.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: e90086be18e344e3061b9e683780f4427b3c15a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018627"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Een Azure CDN-eind punt verwijderen
## <a name="overview"></a>Overzicht
Azure CDN rand knooppunten worden assets in de cache opgeslagen totdat de TTL (time-to-Live) van het activum verloopt.  Wanneer de TTL van het activum verloopt, wanneer een client de Asset aanvraagt vanuit het Edge-knoop punt, haalt het Edge-knoop punt een nieuwe bijgewerkte kopie van de Asset op om de client aanvraag te leveren en de cache op te slaan.

De best practice om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen, is om uw assets voor elke update te maken en ze als nieuwe Url's te publiceren.  CDN haalt onmiddellijk de nieuwe assets op voor de volgende client aanvragen.  Soms wilt u in de cache opgeslagen inhoud uit alle Edge-knoop punten verwijderen en alle nieuwe bijgewerkte assets laten afdwingen.  Dit kan worden veroorzaakt door updates van uw webtoepassing, of om snel assets bij te werken die onjuiste informatie bevatten.

> [!TIP]
> Houd er rekening mee dat bij het leegmaken alleen de inhoud in de cache op de CDN edge-servers wordt gewist.  Downstream-caches, zoals proxy servers en lokale browser caches, kunnen nog steeds een kopie van het bestand in de cache opslaan.  Het is belang rijk om dit te onthouden wanneer u de time-to-Live van een bestand instelt.  U kunt afdwingen dat een stroomafwaartse client de meest recente versie van het bestand aanvraagt door elke keer dat u het bijwerkt een unieke naam te geven of door gebruik te maken van de cache van de [query reeks](cdn-query-string.md).  
> 
> 

In deze zelf studie wordt u begeleid bij het opschonen van assets van alle Edge-knoop punten van een eind punt.

## <a name="walkthrough"></a>Walkthrough
1. Blader in [Azure Portal](https://portal.azure.com)naar het CDN-profiel met het eind punt dat u wilt leegmaken.
2. Klik op de Blade CDN-profiel op de knop opschonen.
   
    ![Blade CDN-profiel](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    De Blade leegmaken wordt geopend.
   
    ![Blade voor het opschonen van CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Selecteer op de Blade leegmaken het service adres dat u wilt verwijderen uit de vervolg keuzelijst URL.
   
    ![Formulier leegmaken](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > U kunt ook de Blade leegmaken openen door te klikken op de knop **opschonen** op de Blade van het CDN-eind punt.  In dat geval wordt het **URL** -veld vooraf ingevuld met het service adres van dat specifieke eind punt.
   > 
   > 
4. Selecteer welke assets u wilt verwijderen uit de Edge-knoop punten.  Als u alle assets wilt wissen, klikt u op het selectie vakje **Alles opschonen** .  Als dat niet het geval is, typt u het pad van elk activum dat u wilt verwijderen in het tekstvak **pad** . De onderstaande indelingen worden ondersteund in het pad.
    1. **Enkelvoudige URL opschonen**: afzonderlijke activa opschonen door de volledige URL op te geven, met of zonder de bestands extensie, bijvoorbeeld, `/pictures/strasbourg.png` ; `/pictures/strasbourg`
    2. **Joker tekens opschonen**: sterretje ( \* ) kan worden gebruikt als Joker teken. Verwijder alle mappen, submappen en bestanden onder een eind punt met `/*` in het pad of verwijder alle submappen en bestanden onder een specifieke map door de map op te geven gevolgd door `/*` , bijvoorbeeld `/pictures/*` .  Houd er rekening mee dat het opschonen van joker tekens momenteel niet wordt ondersteund door Azure CDN vanuit Akamai. 
    3. **Basis domein opschonen**: de hoofdmap van het eind punt met '/' in het pad opschonen.
   
   > [!TIP]
   > Paden moeten worden opgegeven voor opschoning en moeten een relatieve URL zijn die overeenkomt met de volgende [reguliere expressie](/dotnet/standard/base-types/regular-expression-language-quick-reference). Het leegmaken van **alle** en het weghalen van **joker tekens** wordt momenteel niet ondersteund door de **Azure CDN van de Akamai** .
   > > Eén URL leegmaken `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Query reeks `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Joker teken opschonen `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Als u tekst hebt ingevoerd, worden **er meer** tekstvaks weer gegeven, zodat u een lijst met meerdere assets kunt bouwen.  U kunt assets verwijderen uit de lijst door te klikken op de knop met het weglatings teken (...).
   > 
5. Klik op de knop **opschonen** .
   
    ![Knop leegmaken](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Opschoon aanvragen nemen ongeveer 10 minuten in beslag bij **Azure CDN van micro soft**, ongeveer 2 minuten met **Azure CDN van Verizon** (Standard en Premium) en ongeveer 10 seconden met **Azure CDN van Akamai**.  Azure CDN heeft op een bepaald moment een limiet van 100 gelijktijdige opschoon aanvragen op profiel niveau. 
> 
> 

## <a name="see-also"></a>Zie ook
* [Vooraf assets op een Azure CDN-eindpunt laden](cdn-preload-endpoint.md)
* [Azure CDN REST API referentie-een eind punt verwijderen of vooraf laden](/rest/api/cdn/endpoints)

