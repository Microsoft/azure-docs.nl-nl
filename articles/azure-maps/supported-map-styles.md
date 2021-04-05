---
title: Ondersteunde ingebouwde Azure Maps kaart stijlen
description: Meer informatie over de ingebouwde kaart stijlen die Azure Maps ondersteunt, zoals Road, blank_accessible, Satellite, satellite_road_labels, road_shaded_relief en avond.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896936"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps ondersteunde ingebouwde kaart stijlen

Azure Maps biedt ondersteuning voor verschillende ingebouwde kaart stijlen, zoals hieronder wordt beschreven.

## <a name="road"></a>Tour

Een **weg** kaart is een standaard kaart met wegen. Het bevat ook natuurlijke en kunst matige functies en de labels voor deze functies.

![kaart stijl weg](./media/supported-map-styles/road.png)

**Toepasselijke Api's:**

* [Kaart afbeelding](/rest/api/maps/render/getmapimage)
* [Kaart tegel](/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="blank-and-blank_accessible"></a>leeg en blank_accessible

De **lege** en **blank_accessible** kaart stijlen bieden een leeg canvas voor het visualiseren van gegevens. De **blank_accessible** stijl blijft scherm lezers-updates bieden met de locatie Details van de kaart, ook al wordt de basis toewijzing niet weer gegeven.

> [!Note]
> In de Web-SDK kunt u de achtergrond kleur van de kaart wijzigen door de CSS- `background-color` stijl van het element map div in te stellen.

**Toepasselijke Api's:**

* Web SDK-kaart besturings element

## <a name="satellite"></a>satellite

De **satelliet** stijl is een combi natie van satelliet-en lucht foto beelden.

![stijl van kaart voor satelliet tegel](./media/supported-map-styles/satellite.png)

**Toepasselijke Api's:**

* [Satelliet tegel](/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="satellite_road_labels"></a>satellite_road_labels

Deze kaart stijl bestaat uit een hybride van de weg en labels op satelliet-en lucht foto-afbeelding.

![stijl van satellite_road_labelse kaart](./media/supported-map-styles/satellite-road-labels.png)

**Toepasselijke Api's:**

* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="grayscale_dark"></a>grayscale_dark

het **donkere grijs tinten** is een donkere versie van het kaart type weg.

![stijl van gray_scalee kaart](./media/supported-map-styles/grayscale-dark.png)

**Toepasselijke Api's:**

* [Kaart afbeelding](/rest/api/maps/render/getmapimage)
* [Kaart tegel](/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="grayscale_light"></a>grayscale_light

**grijs waarden licht** is een lichte versie van het kaart type weg.

![stijl van licht grijs waarden diagram](./media/supported-map-styles/grayscale-light.png)

**Toepasselijke Api's:**
* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="night"></a>nacht

**'s avonds** is een donkere versie van de kaart stijl met gekleurde wegen en symbolen.

![stijl van nacht kaart](./media/supported-map-styles/night.png)

**Toepasselijke Api's:**

* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="road_shaded_relief"></a>road_shaded_relief

**Uitlichting** van de weg is een Azure Maps hoofd stijl voltooid met conto uren van de aarde.

![stijl van de kaart met schaduw verlichting](./media/supported-map-styles/shaded-relief.png)

**Toepasselijke Api's:**

* [Kaart tegel](/rest/api/maps/render/getmaptile)
* Web SDK-kaart besturings element
* Besturings element Android-kaart
* Power BI-visual

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** is een donkere kaart stijl met een hoger contrast dan de andere stijlen.

![donkere kaart stijl met hoog contrast](./media/supported-map-styles/high-contrast-dark.png)

**Toepasselijke Api's:**

* Web SDK-kaart besturings element
* Power BI-visual

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het instellen van een kaart stijl in Azure Maps:

[Een kaartstijl kiezen](./choose-map-style.md)