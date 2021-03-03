---
title: Schaling configureren voor Azure Cloud Services (uitgebreide ondersteuning)
description: Opties voor schalen inschakelen voor Azure Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cfa5be01a0d36764086c6c9adf97e6cb166d2bb6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728158"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Schaal opties configureren met Azure Cloud Services (uitgebreide ondersteuning) 

Voor waarden kunnen worden geconfigureerd om de implementaties van Cloud Services (uitgebreide ondersteuning) in en uit te scha kelen. Deze voor waarden kunnen worden gebaseerd op CPU-gebruik, schijf belasting en netwerk belasting. 

Houd rekening met de volgende informatie bij het configureren van schalen van uw Cloud service-implementaties:
- Het schalen van het basis gebruik. Grotere rolinstanties verbruiken meer kernen en u kunt alleen binnen de kern limiet van uw abonnement schalen. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md) voor meer informatie.
- Schalen op basis van de wachtrij bericht drempel wordt ondersteund. Zie [Aan de slag met Azure Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie.
- Om te zorgen voor een hoge Beschik baarheid van uw Cloud service (uitgebreide ondersteunings toepassingen), moet u ervoor zorgen dat deze wordt geïmplementeerd met twee of meer rolinstanties.
- Aangepaste automatisch schalen kan alleen plaatsvinden als alle rollen de status **gereed** hebben.

## <a name="configure-and-manage-scaling"></a>Schalen configureren en beheren

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Selecteer de implementatie van de Cloud service (uitgebreide ondersteuning) waarvoor u het schalen wilt configureren. 
3. Selecteer de Blade **schaal** . 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Afbeelding toont het selecteren van de optie Extern bureaublad in de Azure Portal":::

4. Op een pagina wordt een lijst weer gegeven met alle rollen waarin schalen kan worden geconfigureerd. Selecteer de rol die u wilt configureren. 
5. Selecteer het type schaal dat u wilt configureren
    - Met **hand matig schalen** wordt het aantal absolute exemplaren ingesteld.
        1. Selecteer **hand matig schalen**.
        2. Voer het aantal exemplaren in dat u omhoog of omlaag wilt schalen.
        3. Selecteer **Opslaan**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Afbeelding toont het instellen van hand matig schalen in de Azure Portal":::

        4. De schaal bewerking wordt onmiddellijk gestart. 
        
    - Met **aangepaste automatisch schalen** kunt u regels instellen die bepalen hoeveel of hoe weinig er moet worden geschaald. 
        1. **Aangepaste automatisch schalen** selecteren
        2. Kies om te schalen op basis van een metrische waarde of een aantal instanties.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Afbeelding toont het instellen van aangepaste automatisch schalen in de Azure Portal":::

        3. Als u wilt schalen op basis van een metriek, voegt u regels toe die nodig zijn om de gewenste schaal resultaten te krijgen.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Afbeelding toont het instellen van aangepaste regels voor automatisch schalen in de Azure Portal":::

        4. Selecteer **Opslaan**.
        5. De schaal bewerkingen worden gestart zodra een regel wordt geactiveerd.
        
6. U kunt bestaande schaal regels weer geven of aanpassen die op uw implementaties worden toegepast door het tabblad **schaal** te selecteren.

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Afbeelding toont het aanpassen van een bestaande schaal regel in de Azure Portal":::

## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).