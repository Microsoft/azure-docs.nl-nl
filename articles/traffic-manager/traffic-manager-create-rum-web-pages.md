---
title: Real-user-metingen met webpagina's-Azure Traffic Manager
description: In dit artikel leert u hoe u uw webpagina's kunt instellen om Real-user-metingen te verzenden naar Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580353"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Real-user-metingen naar Azure Traffic Manager verzenden met behulp van webpagina's

U kunt uw webpagina's zo configureren dat Real-user-metingen naar Traffic Manager worden verzonden door een Real-user-metingen (RUM)-sleutel te verkrijgen en de gegenereerde code op de webpagina in te sluiten.

## <a name="obtain-a-real-user-measurements-key"></a>Een Real-user-metingen sleutel verkrijgen

De metingen die u uitvoert en verzenden naar Traffic Manager vanuit uw client toepassing, worden geïdentificeerd door de service met behulp van een unieke teken reeks, de **real-User-metingen (rum)-sleutel**. U kunt een RUM-sleutel ophalen met behulp van de Azure Portal, een REST API, Power shell of Azure CLI.

Voor het verkrijgen van de sleutel RUM met behulp van Azure Portal:
1. Meld u vanuit een browser aan bij Azure Portal. Als u nog geen account hebt, kunt u zich registreren voor een gratis proefversie van één maand.

1. Zoek in de zoek balk van de portal naar de naam van het Traffic Manager profiel dat u wilt wijzigen en selecteer vervolgens het Traffic Manager profiel in de resultaten die de weer gegeven.

1. Selecteer **real-User-metingen** onder **instellingen** op de pagina Profiel Traffic Manager.

1. Selecteer **sleutel genereren** om een nieuwe rum-sleutel te maken.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="Scherm opname van het genereren van een sleutel."::: 

   **Afbeelding 1: genereren van sleutels Real-user-metingen**

1. Op de pagina worden nu de gegenereerde RUM-sleutel en een Java script-code fragment weer gegeven dat moet worden inge sloten in de HTML-pagina.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="Scherm opname van gegenereerde java script-code."::: 

    **Afbeelding 2: Real-user-metingen Key en meting java script**
 
1. Selecteer de knop **kopiëren** om de Java script-code te kopiëren. 

> [!IMPORTANT]
> Gebruik de gegenereerde java script-functie om Real-user-metingen goed te laten functioneren. Wijzigingen in dit script of de scripts die door Real-user-metingen worden gebruikt, kunnen leiden tot onvoorspelbaar gedrag.

## <a name="embed-the-code-to-an-html-web-page"></a>De code insluiten op een HTML-webpagina

Nadat u de sleutel RUM hebt verkregen, is de volgende stap het insluiten van deze gekopieerde java script naar een HTML-pagina die uw eind gebruikers bezoeken. Het bewerken van HTML kan op verschillende manieren worden uitgevoerd en het gebruik van verschillende hulpprogram ma's en werk stromen. In dit voor beeld ziet u hoe u een HTML-pagina bijwerkt om dit script toe te voegen. U kunt deze richt lijnen gebruiken om deze aan te passen aan uw HTML-bron beheer werk stroom.

1. Open de HTML-pagina in een tekst editor.

1. Plak de Java script-code die u in de laatste sectie hebt gekopieerd in het gedeelte hoofd tekst van de HTML. De gekopieerde code bevindt zich op regel 8 & 9, zie afbeelding 3.

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="Scherm opname van gegenereerde java script in de webpagina."::: 

    **Afbeelding 3: eenvoudige HTML met Inge sloten Real-user-metingen java script**

1. Sla het HTML-bestand op en host het op een webserver die is verbonden met internet.

1. De volgende keer dat deze pagina wordt weer gegeven in een webbrowser, wordt de Java script waarnaar wordt verwezen, gedownload en worden de metings-en rapportage bewerkingen uitgevoerd door het script.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [real-User-metingen](traffic-manager-rum-overview.md)
- Meer informatie [over de werking van Traffic Manager](traffic-manager-overview.md)
- Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund
- Meer informatie over het [maken van een Traffic Manager profiel](./quickstart-create-traffic-manager-profile.md)
