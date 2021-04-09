---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896105"
---
## <a name="creating-and-running-a-scan"></a>Een scan maken en uitvoeren

> [!Note] 
> De onderstaande stappen en schermopnames illustreren het algemene proces om scans te beheren in verschillende typen gegevensbronnen. Uw opties variëren mogelijk, afhankelijk van de typen gegevensbronnen waarmee u werkt.

Doe het volgende om een nieuwe scan te maken en uit te voeren:

1. Navigeer naar de **Bronnen**

1. Selecteer de gegevensbron die u hebt geregistreerd.

1. Selecteer **+ Nieuwe scan**

1. Selecteer de referentie om verbinding te maken met uw gegevensbron. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Scan instellen":::

1. U kunt uw scan uitbreiden naar specifieke del van de gegevensbron, bijvoorbeeld mappen, verzamelingen of schema's, door de juiste items in de lijst aan te vinken.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Het bereik van uw scan opgeven":::

1. Selecteer een scanregelset voor uw scan. U kunt kiezen tussen de systeemstandaard, de bestaande aangepaste regelset, of een nieuwe maken.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Scanregelset":::

1. Kies de scantrigger. U kunt een schema instellen of de scan eenmalig uitvoeren.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="activeren":::

1. Controleer uw scan en selecteer **Opslaan en uitvoeren**.

## <a name="viewing-your-scans-and-scan-runs"></a>Uw scans en scanuitvoeringen bekijken

Ga als volgt te werk om bestaande scans te bekijken:

1. Ga naar het beheercentrum. Selecteer **Gegevensbronnen** onder het gedeelte **Bronnen en scannen**. 

2. Selecteer de gewenste gegevensbron. U ziet een lijst bestaande scans van die gegevensbron.

3. Selecteer de scan waarvan u de resultaten wilt bekijken.

4. Op deze pagina ziet u alle vorige scanuitvoeringen, samen met metrische gegevens en statussen voor elke scanuitvoering. Ook wordt weergegeven of uw scan gepland of handmatig is uitgevoerd, op hoeveel assets classificaties waren toegepast, hoeveel assets zijn ontdekt, de begin- en eindtijd van de scan en de totale duur van de scan.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Uw scans beheren - bewerken, verwijderen of annuleren

Doe het volgende om een scan te beheren of te verwijderen:

1. Ga naar het beheercentrum. Selecteer **Gegevensbronnen** in het gedeelte **Bronnen en scannen** en selecteer vervolgens de gewenste gegevensbron.

2. Selecteer de share die u wilt beheren. U kunt de scan bewerken door **Bewerken** te selecteren.

3. U kunt uw scan verwijderen door **Verwijderen** te selecteren. 
