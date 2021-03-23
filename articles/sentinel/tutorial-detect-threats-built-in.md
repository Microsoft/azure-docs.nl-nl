---
title: Bedreigingen detecteren met ingebouwde analyse regels in azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van out-of-Box-Threat-detectie regels, gebaseerd op ingebouwde sjablonen, waarmee u wordt gewaarschuwd wanneer er iets verdacht is.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773552"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Zelfstudie: Bedreigingen standaard detecteren

Zodra u [uw gegevens bronnen](quickstart-onboard.md) aan Azure Sentinel hebt gekoppeld, moet u een melding ontvangen wanneer er iets verdacht is. Daarom biedt Azure Sentinel out-of-the-box-ingebouwde sjablonen die u helpen bij het maken van regels voor het detecteren van bedreigingen. Deze sjablonen zijn ontworpen door het team van beveiligings experts en analisten van micro soft op basis van bekende bedreigingen, veelvoorkomende aanvals vectoren en escalatie ketens van verdachte activiteiten. Regels die zijn gemaakt op basis van deze sjablonen, doorzoeken automatisch in uw omgeving op alle activiteiten die er verdacht uitzien. Veel sjablonen kunnen worden aangepast om te zoeken naar activiteiten of ze te filteren op basis van uw behoeften. De waarschuwingen die door deze regels worden gegenereerd, maken incidenten die u kunt toewijzen en onderzoeken in uw omgeving.

Deze zelf studie helpt u bij het detecteren van bedreigingen met Azure Sentinel:

> [!div class="checklist"]
> * Out-of-the-box-bedreigings detecties gebruiken
> * Bedreigingsreacties automatiseren

## <a name="about-out-of-the-box-detections"></a>Over standaarddetectie

Als u alle vooraf gedefinieerde detecties wilt weergeven, gaat u naar **Analyse** en vervolgens naar **Regelsjablonen**. Dit tabblad bevat alle ingebouwde regels van Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Ingebouwde detecties gebruiken om bedreigingen te vinden met Azure Sentinel":::

De volgende sjabloon typen zijn beschikbaar:

- **Micro soft-beveiliging**
   
   Micro soft-beveiligings sjablonen maken automatisch Azure Sentinel-incidenten op basis van de waarschuwingen die zijn gegenereerd in andere micro soft-beveiligings oplossingen, in realtime. U kunt micro soft-beveiligings regels als sjabloon gebruiken om nieuwe regels met soort gelijke logica te maken. Zie voor meer informatie over beveiligings regels [automatisch incidenten maken op basis van beveiligings waarschuwingen van micro soft](create-incidents-from-alerts.md).

- **Fusion** 

    Op basis van de Fusion Technology maakt geavanceerde aanvals detectie in azure Sentinel gebruik van schaal bare machine learning algoritmen die een groot aantal waarschuwingen en gebeurtenissen met een lage kwaliteit kunnen verlichten over meerdere producten tot hoge betrouw baarheid en bruikbare incidenten. Fusion is standaard ingeschakeld. Omdat de logica verborgen is en daarom niet kan worden aangepast, kunt u slechts één regel met deze sjabloon maken.

    > [!IMPORTANT]
    > Enkele van de detecties in de Fusion Rule-sjabloon zijn momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.
    >
    > Als u wilt zien welke detecties in Preview zijn, raadpleegt u [Geavanceerde detectie van aanvals mogelijkheden in azure](fusion.md).

- **Analyse van machine learning-gedrag**

    Deze sjablonen zijn gebaseerd op eigen algoritmen van micro soft machine learning, zodat u niet de interne logica kunt zien van hoe ze werken en wanneer ze worden uitgevoerd. Omdat de logica verborgen is en daarom niet kan worden aangepast, kunt u slechts één regel maken met elke sjabloon van dit type.

    > [!IMPORTANT]
    > - De machine learning gedrags analyse regel sjablonen zijn momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.
    >
    > - Door regels te maken en in te scha kelen op basis van de ML gedrags Analytics-sjablonen, **geeft u micro soft toestemming om ingedeelde gegevens buiten de Geografie van uw Azure-Sentinel-werk ruimte te kopiëren** als dat nodig is voor verwerking door de machine learning motoren en modellen.

- **Gepland**

    Geplande analyse regels zijn gebaseerd op ingebouwde query's geschreven door micro soft-beveiligings experts. U kunt de query logica bekijken en er wijzigingen in aanbrengen. U kunt de sjabloon geplande regel gebruiken en de query logica en plannings instellingen aanpassen om nieuwe regels te maken.

## <a name="use-out-of-the-box-detections"></a>Out-of-the-box-detecties gebruiken

1. Als u een ingebouwde sjabloon wilt gebruiken, klikt u op de naam van de sjabloon en klikt u vervolgens op de knop **regel maken** in het detail venster om een nieuwe actieve regel te maken op basis van deze sjabloon. Elke sjabloon bevat een lijst met vereiste gegevens bronnen. Wanneer u de sjabloon opent, worden de gegevens bronnen automatisch gecontroleerd op Beschik baarheid. Als er een beschikbaarheids probleem is, kan de knop **regel maken** worden uitgeschakeld of wordt er een waarschuwing weer gegeven.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Paneel voor beeld van detectie regel":::
 
1. Als u op de knop **regel maken** klikt, wordt de wizard regel maken geopend op basis van de geselecteerde sjabloon. Alle details worden aangevuld en met de **geplande** of **micro soft-beveiligings** sjablonen kunt u de logica en andere regel instellingen aanpassen aan uw specifieke behoeften. U kunt dit proces herhalen om extra regels te maken op basis van de ingebouwde sjabloon. Nadat u de stappen in de wizard voor het maken van de regel hebt uitgevoerd, hebt u een regel gemaakt op basis van de sjabloon. De nieuwe regels worden weer gegeven op het tabblad **actieve regels** .

    Zie [zelf studie: aangepaste analyse regels maken voor het detecteren van bedreigingen](tutorial-detect-threats-custom.md)voor meer informatie over het aanpassen van uw regels in de wizard voor het maken van een regel.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u aan de slag gaat met het detecteren van bedreigingen met behulp van Azure Sentinel. 

Als u wilt weten hoe u uw reacties op bedreigingen kunt automatiseren, [stelt u automatische reacties op dreigingen in azure Sentinel in](tutorial-respond-threats-playbook.md).
