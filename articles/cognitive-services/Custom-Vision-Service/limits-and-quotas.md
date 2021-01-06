---
title: Limieten en quota-Custom Vision Service
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de verschillende typen licentie sleutels en de limieten en quota voor de Custom Vision Service uitgelegd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: 8a8ea8d5f13f72b0da1e11a27b69da2570eda543
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913478"
---
# <a name="limits-and-quotas"></a>Limieten en quota

Er zijn twee lagen met sleutels voor de Custom Vision-service. U kunt zich aanmelden voor een F0 (gratis) of een S0 (standaard)-abonnement via de Azure Portal. Bekijk de pagina met overeenkomende [Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor meer informatie over prijzen en trans acties.

Het aantal trainings afbeeldingen per project en Tags per project wordt naar verwachting in de loop van de tijd voor S0-projecten verhoogd.

|Factor|**F0**|**S0**|
|-----|-----|-----|
|Projecten|2|100|
|Trainings afbeeldingen per project |5\.000|100.000|
|Voor spellingen/maand|10.000 |Onbeperkt|
|Tags/project|50|500|
|Iteraties |10|10|
|Min gelabelde afbeeldingen per tag, classificatie (50 + aanbevolen) |5|5|
|Min gelabelde afbeeldingen per tag, object detectie (50 + aanbevolen)|15|15|
|Hoe lang de Voorspellings afbeeldingen zijn opgeslagen|30 dagen|30 dagen|
|[Voorspellings](https://go.microsoft.com/fwlink/?linkid=865445) bewerkingen met opslag (trans acties per seconde)|2|10|
|[Voorspellings](https://go.microsoft.com/fwlink/?linkid=865445) bewerkingen zonder opslag (trans acties per seconde)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (API-aanroepen per seconde)|2|10|
|[Andere API-aanroepen](https://go.microsoft.com/fwlink/?linkid=865446) (trans acties per seconde)|10|10|
|Geaccepteerde afbeeldings typen|jpg, PNG, BMP, GIF|jpg, PNG, BMP, GIF|
|Minimale afbeeldings hoogte/-breedte in pixels|256 (zie opmerking)|256 (zie opmerking)|
|Maximale hoogte of breedte van afbeelding in pixels|10.240|10.240|
|Maximale afbeeldings grootte (uploaden van trainings afbeelding) |6 MB|6 MB|
|Maximale afbeeldings grootte (voor spelling)|4 MB|4 MB|
|Afbeelding van het maximum aantal regio's per object detectie|300|300|
|Afbeelding van maximum aantal labels per classificatie|100|100|

