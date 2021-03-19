---
title: HTTP-gedrag negeren met Azure CDN-Verizon Premium-regel engine
description: Met de regel engine kunt u aanpassen hoe HTTP-aanvragen worden verwerkt door Azure CDN van Verizon Premium, zoals het blok keren van de levering van bepaalde soorten inhoud, het opgeven van een cache beleid en het aanpassen van HTTP-headers.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: a49912bc2275e478d657f06587c4ddc830210d3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040204"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>HTTP-gedrag negeren met behulp van de Azure CDN van de Verizon Premium-regel engine

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht

Met de engine voor Azure CDN regels kunt u aanpassen hoe HTTP-aanvragen worden verwerkt. Bijvoorbeeld: het blok keren van de levering van bepaalde inhouds typen, het definiëren van een cache beleid of het wijzigen van een HTTP-header. In deze zelf studie wordt gedemonstreerd hoe u een regel maakt waarmee het cache gedrag van CDN-assets wordt gewijzigd. Zie voor meer informatie over de syntaxis van de regel engine de [referentie voor Azure CDN regels-engine](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Access

Voor toegang tot de regel engine moet u eerst **beheren** selecteren vanaf de bovenkant van de pagina **CDN-profiel** om toegang te krijgen tot de Azure CDN-beheer pagina. Afhankelijk van of uw eind punt is geoptimaliseerd voor dynamische site versnelling (DSA), opent u de regel engine met de regels die geschikt zijn voor uw type eind punt:

- Eind punten die zijn geoptimaliseerd voor algemene Internet levering of andere non-DSA-optimalisatie:
    
    Selecteer het tabblad **http groot** en selecteer vervolgens **regel engine**.

    ![Regel engine voor HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Eind punten die zijn geoptimaliseerd voor DSA:
    
    Selecteer het tabblad **ADN** en selecteer vervolgens **regel engine**.
    
    ADN is een term die door Verizon wordt gebruikt om DSA-inhoud op te geven. Alle regels die u hier maakt, worden genegeerd door eind punten in uw profiel die niet zijn geoptimaliseerd voor DSA.

    ![Regel engine voor DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Zelfstudie

1. Selecteer op de pagina **CDN-profiel** de optie **beheren**.
   
    ![Beheer knop voor CDN-profiel](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    De CDN-beheer portal wordt geopend.

2. Selecteer het tabblad **http groot** en selecteer vervolgens **regel engine**.
   
    De opties voor een nieuwe regel worden weer gegeven.
   
    ![Opties voor nieuwe CDN-regel](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > De volg orde waarin meerdere regels worden weer gegeven, is van invloed op de manier waarop ze worden verwerkt. Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven. Als u bijvoorbeeld een regel hebt waarmee u toegang krijgt tot een resource op basis van een aanvraag eigenschap en een regel die de toegang tot alle aanvragen weigert, overschrijft de tweede regel de eerste. Met regels worden eerdere regels alleen overschreven als ze met dezelfde eigenschappen communiceren.
   >

3. Voer een naam in het tekstvak **naam/beschrijving** in.

4. Identificeer het type aanvragen waarop de regel van toepassing is. Gebruik de standaard voorwaarde matching **altijd**.
   
   ![Voor waarde voor overeenkomst met CDN-regel](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Er zijn meerdere match voorwaarden beschikbaar in de vervolg keuzelijst. Voor informatie over de momenteel geselecteerde match-voor waarde selecteert u het blauwe pictogram voor informatie aan de linkerkant.
   >
   >  Zie [voorwaardelijke expressies voor regel engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)voor een gedetailleerde lijst met voorwaardelijke expressies.
   >  
   > Voor een gedetailleerde lijst met match-voor waarden raadpleegt u de [voor waarden van regels engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Als u een nieuwe functie wilt toevoegen, selecteert u de **+** knop naast **functies**.  Selecteer in de vervolg keuzelijst aan de linkerkant de optie **interne Max. leeftijd forceren**.  Typ **300** in het tekstvak dat wordt weer gegeven. Wijzig de resterende standaard waarden niet.
   
   ![CDN-regel functie](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Er zijn meerdere functies beschikbaar in de vervolg keuzelijst. Voor informatie over de momenteel geselecteerde functie selecteert u het blauwe informatie pictogram aan de linkerkant.
   >
   > Voor een **afdwinging van de interne maximale leeftijd** worden de activa `Cache-Control` en `Expires` kopteksten overschreven om te bepalen wanneer de Asset door het CDN Edge-knoop punt van de oorsprong wordt vernieuwd. In dit voor beeld slaat het element van de CDN-rand het activum gedurende 300 seconden of 5 minuten op in de cache voordat het het activum van de oorsprong vernieuwt.
   >
   > Zie [regels engine-functies](cdn-verizon-premium-rules-engine-reference-features.md)voor een gedetailleerde lijst met functies.
   >
   >

6. Selecteer **toevoegen** om de nieuwe regel op te slaan.  De nieuwe regel is nu in afwachting van goed keuring. Nadat deze is goedgekeurd, wordt de status gewijzigd van **XML in behandeling** naar **actief XML**.
   
   > [!IMPORTANT]
   > Het kan Maxi maal tien minuten duren voordat de wijzigingen zijn doorgevoerd in Azure CDN.
   >
   >

## <a name="see-also"></a>Zie ook

- [Overzicht van Azure CDN](cdn-overview.md)
- [Referentie voor regels-engine](cdn-verizon-premium-rules-engine-reference.md)
- [Criteria voor overeenkomst in de regelengine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Voorwaardelijke expressies in de regelengine](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Functies van de regelengine](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure vrijdag: de krachtige nieuwe Premium-functies van Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)