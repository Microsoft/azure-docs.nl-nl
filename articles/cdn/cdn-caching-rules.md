---
title: Het gedrag van Azure CDN caching bepalen met cache regels | Microsoft Docs
description: U kunt de regels van de CDN-cache gebruiken om zowel globaal als met voor waarden een URL-pad en bestands extensies voor de standaard cache in te stellen of te wijzigen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: a5f4f6a6e72b57638688069111071a6e0a035c49
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018661"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Cachinggedrag in Azure CDN beheren met regels voor opslaan in cache

> [!NOTE] 
> Regels voorcaching zijn alleen beschikbaar voor de profielen **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai**. Voor **Azure CDN van Microsoft**-profielen moet u de [standaardregelengine](cdn-standard-rules-engine-reference.md) gebruiken. Voor **Azure CDN Premium van Verizon**-profielen moet u de [Verizon Premium-regelengine](./cdn-verizon-premium-rules-engine.md) gebruiken in de portal **Beheren** voor vergelijkbare functionaliteit.
 
Azure Content Delivery Network (CDN) biedt twee manieren om te bepalen hoe uw bestanden in de cache worden opgeslagen: 

- Regels voor opslaan in cache: in dit artikel wordt beschreven hoe u de regels voor het gebruik van CDN (Content Delivery Network) kunt gebruiken voor het instellen of wijzigen van het verloop van de standaard cache zowel globaal als met aangepaste voor waarden, zoals een URL-pad en bestands extensie. Azure CDN biedt twee typen regels voor opslaan in cache:

   - Globale regels voor opslaan in cache: u kunt voor elk eindpunt in uw profiel één globale regel voor opslaan in cache instellen, die geldt voor alle aanvragen op het eindpunt. De globale regel voor opslaan in cache overschrijft alle HTTP-headers met cache-instructies, indien aanwezig.

   - Aangepaste regels voor opslaan in cache: u kunt een of meer aangepaste regels voor opslaan in cache instellen voor elk eindpunt in uw profiel. Aangepaste regels voor opslaan in cache komen overeen met specifieke paden en bestandsextensies, worden in volgorde verwerkt en overschrijven de globale regel voor opslaan in cache, indien ingesteld. 

- Query reeks cache: u kunt aanpassen hoe de Azure CDN in de cache wordt behandeld voor aanvragen met query teken reeksen. Zie voor meer informatie [Control Azure CDN Caching with query strings](cdn-query-string.md). Als het bestand niet in cache kan worden opgeslagen, heeft de cache-instelling voor de query reeks geen effect, op basis van de cache regels en het standaard CDN-gedrag.

Zie [How caching Works](cdn-how-caching-works.md)(Engelstalig) voor meer informatie over het standaard gedrag van caching en het opslaan van instructie headers in de cache. 


## <a name="accessing-azure-cdn-caching-rules"></a>Azure CDN regels voor caching gebruiken

1. Open de Azure Portal, selecteer een CDN-profiel en selecteer vervolgens een eind punt.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![Pagina Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Instellingen voor cache gedrag
Voor globale en aangepaste regels voor caching kunt u de volgende instellingen voor **cache gedrag** opgeven:

- **Cache overs Laan**: de door de oorsprong vermelde cache-instructie headers worden niet in de cache geplaatst en genegeerd.

- **Negeren**: oorspronkelijke cache duur van de oorsprong negeren; gebruik in plaats daarvan de meegeleverde cache duur. Hiermee wordt de cache-Control niet overschreven: geen cache.

- **Instellen indien ontbrekend**: door de beheerder gestuurde cache-instructie headers, indien aanwezig. Gebruik anders de meegeleverde cache duur.

![Globale regels voor opslaan in cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Aangepaste regels voor opslaan in cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Verloop tijd van cache
Voor globale en aangepaste cache regels kunt u de verloop duur van de cache opgeven in dagen, uren, minuten en seconden:

- Voor de instellingen **negeren** en **instellen als** het gedrag voor het **Opslaan** van de cache ontbreekt, geldt een geldige cache duur van 0 seconden en 366 dagen. Voor een waarde van 0 seconden wordt de inhoud in de CDN opgeslagen, maar moet elke aanvraag opnieuw worden gevalideerd met de oorspronkelijke server.

- Voor de **bypass cache** -instelling wordt de cache duur automatisch ingesteld op 0 seconden en kan niet worden gewijzigd.

## <a name="custom-caching-rules-match-conditions"></a>Aangepaste regels voor caching voldoen aan de voor waarden

Voor aangepaste cache regels zijn twee match-voor waarden beschikbaar:
 
- **Pad**: deze voor waarde komt overeen met het pad van de URL, met uitzonde ring van de domein naam, en ondersteunt het Joker teken ( \* ). Bijvoorbeeld _/myfile.html_, _/mijn/map/*_ en _/My/images/*. jpg_. De maximale lengte is 260 tekens.

- **Extensie**: deze voor waarde komt overeen met de bestands extensie van het aangevraagde bestand. U kunt een lijst met door komma's gescheiden bestands extensies opgeven. Bijvoorbeeld _. jpg_, _. mp3_ of _. png_. Het maximum aantal uitbrei dingen is 50 en het maximum aantal tekens per uitbrei ding is 16. 

## <a name="global-and-custom-rule-processing-order"></a>Algemene en aangepaste verwerkings volgorde voor regels
Globale en aangepaste regels voor opslaan in cache worden in de volgende volg orde verwerkt:

- Algemene regels voor opslaan in cache hebben voor rang op de standaard instelling voor het gedrag van de CDN-cache (HTTP-cache-header-instellingen). 

- Aangepaste regels voor opslaan in cache hebben voor rang op algemene regels voor caching, waar ze van toepassing zijn. Aangepaste regels voor opslaan in cache worden van boven naar beneden verwerkt. Als een aanvraag overeenkomt met beide voor waarden, hebben regels onder aan de lijst voor rang boven de regels boven aan de lijst. Daarom moet u meer specifieke regels in de lijst plaatsen.

**Voor beeld**:
- Algemene regel voor opslaan in cache: 
   - Cache gedrag: **overschrijven**
   - Verloop tijd van cache: 1 dag

- Aangepaste regel voor opslaan in cache #1:
   - Overeenkomst voor waarde: **pad**
   - Overeenkomende waarde: _/Home/*_
   - Cache gedrag: **overschrijven**
   - Verloop tijd van cache: 2 dagen

- Aangepaste regel voor opslaan in cache #2:
   - Voor waarde match: **Extension**
   - Overeenkomende waarde: _. html_
   - Cache gedrag: **instellen als ontbreekt**
   - Verloop tijd van cache: 3 dagen

Wanneer deze regels zijn ingesteld, wordt een aanvraag voor _&lt; eind &gt; punt-hostnaam_. azureedge.net/Home/index.html activeert de regel voor aangepaste caching #2, ingesteld op: **set if Missing** en 3 dagen. Als de *index.html* `Cache-Control` -bestand of `Expires` http-headers zijn, worden deze daarom gehonoreerd. als deze headers niet zijn ingesteld, wordt het bestand drie dagen in de cache opgeslagen.

> [!NOTE] 
> Bestanden die in de cache worden opgeslagen voordat een regel wijziging de waarde voor de oorspronkelijke cache duur behouden. Als u de cache duur opnieuw wilt instellen, moet u [het bestand leegmaken](cdn-purge-endpoint.md). 
>
> Wijzigingen in de configuratie van Azure CDN kan enige tijd duren voordat het netwerk kan worden door gegeven: 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Voor **Azure CDN standaard van Verizon** -profielen wordt de doorgifte doorgaans in tien minuten voltooid.  
>

## <a name="see-also"></a>Zie ook

- [Hoe caching werkt](cdn-how-caching-works.md)
- [Zelfstudie: Azure CDN-regels voor opslaan in cache instellen](cdn-caching-rules-tutorial.md)