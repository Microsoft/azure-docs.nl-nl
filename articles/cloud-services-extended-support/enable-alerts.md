---
title: Bewaking in Cloud Services inschakelen (uitgebreide ondersteuning) met behulp van de Azure Portal
description: Bewaking voor Cloud Services-exemplaren (uitgebreide ondersteunings instanties) inschakelen met behulp van de Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744599"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Schakel bewaking voor Cloud Services (uitgebreide ondersteuning) in met behulp van de Azure Portal

In dit artikel wordt uitgelegd hoe u waarschuwingen kunt inschakelen voor bestaande implementaties van Cloud service (uitgebreide ondersteuning). 

## <a name="add-monitoring-rules"></a>Bewakings regels toevoegen
1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer de implementatie van de Cloud service (uitgebreide ondersteuning) waarvoor u waarschuwingen wilt inschakelen. 
3. Selecteer de Blade **waarschuwingen** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Afbeelding toont het selecteren van het tabblad waarschuwingen in de Azure Portal.":::

4. Selecteer het pictogram **nieuwe waarschuwing** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Afbeelding toont de selectie van de optie nieuwe waarschuwing toevoegen.":::

5. Voer de gewenste voor waarden en vereiste acties in op basis van de metrische gegevens die u wilt volgen. U kunt de regels definiëren op basis van afzonderlijke metrische gegevens of het activiteiten logboek. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Afbeelding laat zien waar voor waarden aan waarschuwingen worden toegevoegd.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Afbeelding toont de configuratie van de signaal logica.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Afbeelding toont het configureren van de logica van de actie groep.":::

6. Wanneer u klaar bent met het instellen van waarschuwingen, slaat u de wijzigingen op en op basis van de ingestelde metrische gegevens ziet u dat de Blade **waarschuwingen** in de loop van de tijd worden ingevuld.

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).