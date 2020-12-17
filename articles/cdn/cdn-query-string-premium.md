---
title: Gedrag van Azure CDN caching bepalen met query reeksen-Premium-laag
description: Met Azure CDN query reeks cachet u regelt hoe bestanden in de cache worden opgeslagen wanneer een webaanvraag een query reeks bevat. In dit artikel wordt de query reeks in de cache opgeslagen in het Azure CDN Premium-product van Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018593"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Gedrag van Azure CDN caching bepalen met query reeksen-Premium-laag
> [!div class="op_single_selector"]
> * [Standaardlaag](cdn-query-string.md)
> * [Premium-laag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Overzicht
Met Azure Content Delivery Network (CDN) kunt u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een query reeks bevat. In een webaanvraag met een query reeks is de query reeks het gedeelte van de aanvraag dat wordt uitgevoerd na een vraag teken (?). Een query reeks kan een of meer sleutel-waardeparen bevatten, waarbij de veld naam en de waarde ervan gescheiden worden door een gelijkteken (=). Elk sleutel-waardepaar wordt gescheiden door een en-teken (&). Bijvoorbeeld http://www.contoso.com/content.mov?field1=value1&veld2 = waarde2. Als er meer dan één sleutel/waarde-paar in een query reeks van een aanvraag is, is de volg orde hiervan niet van belang. 

> [!IMPORTANT]
> De standaard-en Premium-CDN-producten bieden dezelfde cache functionaliteit voor de query reeks, maar de gebruikers interface wijkt af. In dit artikel wordt de interface voor **Azure CDN Premium van Verizon** beschreven. Zie [Azure CDN gedrag in cache opslaan met query reeksen-Standard-laag voor het](cdn-query-string.md)opslaan van een query reeks in de cache met Azure CDN standaard producten.
>


Er zijn drie modi voor de query reeks beschikbaar:

- **Standard-cache**: standaard modus. In deze modus geeft het CDN-knoop punt (-of-Presence) de query teken reeksen van de aanvrager door aan de oorspronkelijke server in de eerste aanvraag en slaat de Asset op in de cache. Alle volgende aanvragen voor de Asset die vanaf de POP-server worden bediend, negeren de query teken reeksen totdat het activum in de cache verloopt.

    >[!IMPORTANT] 
    > Als token autorisatie is ingeschakeld voor een pad op dit account, is de modus standaard-cache de enige modus die kan worden gebruikt. 

- **no-cache**: in deze modus worden aanvragen met query teken reeksen niet opgeslagen in de cache van het CDN-pop-knoop punt. Het POP-knoop punt haalt de Asset rechtstreeks van de oorspronkelijke server op en geeft deze door aan de aanvrager bij elke aanvraag.

- **Unique-cache**: in deze modus wordt elke aanvraag met een unieke URL, inclusief de query reeks, behandeld als een unieke Asset met een eigen cache. Bijvoorbeeld: het antwoord van de oorspronkelijke server voor een aanvraag voor bijvoorbeeld. ashx? q = test1 wordt in de cache geplaatst in het POP-knoop punt en geretourneerd voor volgende caches met dezelfde query reeks. Een aanvraag voor beeld. ashx? q = Test2 wordt in de cache opgeslagen als een afzonderlijk activum met een eigen time-to-Live-instelling.
   
    >[!IMPORTANT] 
    > Gebruik deze modus niet wanneer de query reeks para meters bevat die worden gewijzigd met elke aanvraag, zoals een sessie-ID of een gebruikers naam, omdat dit leidt tot een lage verhouding in de cache-treffer.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>De cache-instellingen voor de query reeks wijzigen voor Premium CDN-profielen
1. Open een CDN-profiel en klik vervolgens op **beheren**.
   
    ![Beheer knop voor CDN-profiel](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **http large** en Beweeg vervolgens de muis aanwijzer over het vervolg menu van de **cache-instellingen** . Klik op **query-teken reeks cache**.
   
    De cache opties voor de query reeks worden weer gegeven.
   
    ![Cache opties voor de CDN-query teken reeks](./media/cdn-query-string-premium/cdn-query-string.png)
3. Selecteer een query teken reeks modus en klik vervolgens op **bijwerken**.

> [!IMPORTANT]
> Omdat het tijd kost om de registratie door te geven via het CDN, zijn wijzigingen in de cache teken reeks instellingen mogelijk niet direct zichtbaar. Doorgifte wordt gewoonlijk in 10 minuten uitgevoerd.
 

