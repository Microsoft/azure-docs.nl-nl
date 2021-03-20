---
title: Index toewijzing van Azure Kinect Body
description: Meer informatie over het uitvoeren van query's op een body-index toewijzing in azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, poort, hoofd tekst, volgen, index, segmentatie, kaart
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277340"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Index toewijzing van Azure Kinect Body bijhouden

De index toewijzing van de hoofd tekst bevat de exemplaar segmentatie toewijzing voor elke hoofd tekst in de diepte camera-opname. Elke pixel wordt toegewezen aan de corresponderende pixel in de diepte-of IR-afbeelding. De waarde voor elke pixel vertegenwoordigt de hoofd tekst waarvan de pixel deel uitmaakt. Dit kan een achtergrond (waarde `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) of de index van een gedetecteerde zijn `k4abt_body_t` .

![Voor beeld van Body-index toewijzing](./media/concepts/body-index-map.png)

>[!NOTE]
> De hoofd tekst van de index wijkt af van de id van de hoofd tekst. U kunt een query uitvoeren op de hoofd code-id van een bepaalde hoofdtekst index door API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92)aan te roepen.


## <a name="using-body-index-map"></a>Body-index toewijzing gebruiken

De Body-index toewijzing is opgeslagen als a `k4a_image_t` en heeft dezelfde resolutie als de diepte-of IR-afbeelding. Elke pixel is een 8-bits waarde. Er kan een query worden uitgevoerd vanuit een `k4abt_frame_t` door te roepen `k4abt_frame_get_body_index_map` . De ontwikkelaar is verantwoordelijk voor het vrijgeven van het geheugen voor de Body-index toewijzing door aan te roepen `k4a_image_release()` .

## <a name="next-steps"></a>Volgende stappen

[Uw eerste app om een lichaam te volgen](build-first-body-app.md)
