---
title: Azure Stack Edge Pro GPU bandbreedte schema's beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt om bandbreedte planningen te beheren op uw Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 7b091ff1ec825ac2292345183eb77bc37f02f6e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637970"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro-gpu"></a>Gebruik de Azure Portal om bandbreedte planningen te beheren op uw Azure Stack Edge Pro GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u bandbreedte schema's beheert op uw Azure Stack Edge Pro. Met bandbreedteschema's kunt u het gebruik van netwerkbandbreedte beheren over schema's voor meerdere tijdstippen. Deze schema's kunnen worden toegepast op upload- en downloadbewerkingen van uw apparaat naar de cloud.

U kunt de bandbreedte schema's voor uw Azure Stack Edge Pro toevoegen, wijzigen of verwijderen via de Azure Portal.

In dit artikel leert u het volgende: 

> [!div class="checklist"]
> * Een schema toevoegen
> * Een schema wijzigen
> * Een schema verwijderen


## <a name="add-a-schedule"></a>Een schema toevoegen

Voer de volgende stappen uit in de Azure Portal om een schema toe te voegen.

1. Ga in de Azure Portal voor uw Azure Stack Edge-resource naar **band breedte**.
2. Selecteer **+ schema toevoegen** in het rechterdeel venster.

    ![Band breedte selecteren](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-1.png)

3. Doe het volgende in **Schema toevoegen**:

   1. Geef de **begin dag**, de **eind datum**, de **begin tijd** en de **eind tijd** van de planning op.
   2. Controleer de optie **alle dagen** als dit schema de hele dag moet worden uitgevoerd.
   3. **Bandbreedte frequentie** is de band breedte in megabits per seconde (Mbps) die wordt gebruikt door uw apparaat in bewerkingen met betrekking tot de Cloud (zowel uploads als down Loads). Geef een getal tussen 64 en 2.147.483.647 op voor dit veld.
   4. Selecteer **onbeperkte band breedte** als u de upload van de datum niet wilt beperken en downloaden.
   5. Selecteer **Toevoegen**.

      ![Schema toevoegen](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-2.png)

3. Er wordt een schema gemaakt met de opgegeven parameters. Dit schema wordt vervolgens weergegeven in de lijst van bandbreedteschema's in de portal.

    ![Lijst met bandbreedte schema's bijgewerkt](media/azure-stack-edge-gpu-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Schema bewerken

Voer de volgende stappen uit als u een bandbreedteschema wilt bewerken.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **band breedte**.
2. Selecteer in de lijst met bandbreedte schema's een schema dat u wilt wijzigen.

   ![Bandbreedte planning selecteren](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-1.png)

3. Breng de gewenste wijzigingen aan en sla de wijzigingen op.

    ![Gebruiker wijzigen](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-2.png)

4. Wanneer het schema is gewijzigd, wordt de lijst met schema's bijgewerkt met het gewijzigde schema.

    ![Gebruiker wijzigen 2](media/azure-stack-edge-gpu-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Een schema verwijderen

Voer de volgende stappen uit om een bandbreedte schema te verwijderen dat is gekoppeld aan uw Azure Stack Edge Pro-apparaat.

1. Ga in het Azure Portal naar de resource Azure Stack Edge en ga vervolgens naar **band breedte**.  

2. Selecteer in de lijst met bandbreedteschema's een schema dat u wilt verwijderen. Selecteer **verwijderen** in het **schema bewerken**. Selecteer **Ja** als u om bevestiging wordt gevraagd.

   ![Een gebruiker verwijderen](media/azure-stack-edge-gpu-manage-bandwidth-schedules/delete-schedule-2.png)

3. Wanneer de planning is verwijderd, wordt de lijst met schema's bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van shares](azure-stack-edge-gpu-manage-shares.md).
