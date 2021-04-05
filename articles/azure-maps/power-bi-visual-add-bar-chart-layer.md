---
title: Een staaf grafiek laag toevoegen aan de Azure Maps Power BI Visual | Microsoft Azure kaarten
description: In dit artikel leert u hoe u de laag van een staaf diagram kunt gebruiken in de Visual Microsoft Azure Maps voor Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896273"
---
# <a name="add-a-bar-chart-layer"></a>Een laag met een staafdiagram toevoegen

De **laag staaf diagram** is handig voor het nemen van gegevens aan de volgende dimensie door visualisatie van locatie gegevens als 3D-balken of cilinders op de kaart toe te staan. Net als bij de tekenlaag kan het staaf diagram later eenvoudig twee metrieken visualiseren met behulp van kleur en relatieve hoogte. Een meting moet worden toegevoegd aan de Bucket **grootte** van het deel venster **velden** , zodat de balken een hoogte hebben. Als er geen meting wordt gegeven, worden balken zonder hoogte weer gegeven als platte vier kanten of cirkels, afhankelijk van de optie voor de **balk vorm** .

> [!div class="mx-imgBorder"]
> ![Een kaart waarin punt gegevens worden weer gegeven met de laag staaf diagram](media/power-bi-visual/bar-chart-layer-styled.png)

Gebruikers kunnen de kaart kantelen en draaien om uw gegevens uit verschillende perspectieven te bekijken. De kaart kan worden gekanteld of vertoond met een van de volgende methoden.

-   Schakel de optie **Navigatie besturings elementen** in de **kaart instellingen** van het deel venster **indeling** in. Hiermee wordt een knop voor het kantelen van de kaart toegevoegd.
-   Druk op de rechter muisknop omlaag en sleep de muis omhoog of omlaag.
-   Met een aanraak scherm raakt u de kaart met twee vingers en sleept u deze naar boven of beneden.
-   Terwijl de kaart is gericht, houdt u de **SHIFT** -toets ingedrukt en drukt u op de pijl **omhoog** of **omlaag** .

De kaart kan worden gedraaid met een van de volgende methoden.

-   Schakel de optie **Navigatie besturings elementen** in de **kaart instellingen** van het deel venster **indeling** in. Hiermee wordt een knop voor het draaien van de kaart toegevoegd.
-   Druk op de rechter muisknop omlaag en sleep de muis naar links of rechts.
-   Gebruik een touchscreen om de kaart met twee vingers te raken en te draaien.
-   Terwijl de kaart is gericht, houdt u de **SHIFT** -toets ingedrukt en drukt u op de toets met de **linker** -of **rechter pijl** .

Hieronder ziet u alle instellingen in het deel venster **indeling** die beschikbaar zijn in de sectie **staaf diagram laag** .

| Instelling              | Beschrijving      |
|----------------------|------------------|
| Staaf vorm            | De vorm van de 3D-balk.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Vak: balken worden weer gegeven als rechthoekige vakken.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cilinder – staven worden weer gegeven als cilinders. |
| Hoogte               | De hoogte van elke balk. Als een veld wordt door gegeven aan de Bucket **grootte** van het deel venster **velden** , worden de balken geschaald ten opzichte van deze hoogte waarde. |
| Schaal hoogte bij zoomen | Hiermee wordt aangegeven of de hoogte van de balken ten opzichte van het zoom niveau moet worden geschaald. |
| Breedte                | De breedte van elke balk.  |
| Schaal breedte bij zoomen  | Geeft aan of de breedte van de balken ten opzichte van het zoom niveau moeten worden geschaald.  |
| Opvulkleur           | Kleur van elke balk. Deze optie is verborgen wanneer een veld wordt door gegeven aan de Bucket **legenda** van het deel venster **velden** en een afzonderlijke **gegevens kleur** sectie wordt weer gegeven in het deel venster **opmaak** . |
| Transparantie         | Transparantie van elke balk. |
| Min. zoom niveau             | De tegels met mini maal zoom niveau zijn beschikbaar. |
| Maxi maal zoom niveau             | Er zijn maximale zoom niveau tegels beschikbaar. |
| Laag positie       | Geeft de positie van de laag aan ten opzichte van andere kaart lagen. |

> [!NOTE]
> Als de balken een kleine waarde voor breedte hebben en de optie **schaal breedte bij inzoomen** is uitgeschakeld, kunnen ze verdwijnen wanneer de weer gegeven breedte minder is dan een pixel groot. Wanneer de optie **schaal breedte bij zoomen** is ingeschakeld, worden er echter aanvullende berekeningen uitgevoerd wanneer het zoom niveau verandert, wat de prestaties van grote gegevens sets kan beïnvloeden.

## <a name="next-steps"></a>Volgende stappen

Meer context toevoegen aan de kaart:

> [!div class="nextstepaction"]
> [Een referentielaag toevoegen](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Een titellaag toevoegen](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Realtime verkeer weergeven](power-bi-visual-show-real-time-traffic.md)

De Visual aanpassen:

> [!div class="nextstepaction"]
> [Tips en trucs voor het gebruik van opmaak in Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Titels, legenda's en achtergronden van visualisaties aanpassen](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)