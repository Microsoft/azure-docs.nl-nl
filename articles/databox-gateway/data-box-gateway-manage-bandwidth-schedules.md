---
title: Bandbreedte planningen beheren op Azure Data Box Gateway | Microsoft Docs
description: Beschrijft hoe u de Azure Portal gebruikt om bandbreedteschema's te beheren in uw Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 55676128f482c8fb4dbdeab0523e4556f3957af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582026"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>De Azure Portal gebruiken om bandbreedteschema's te beheren in uw Azure Data Box Gateway  

Dit artikel beschrijft hoe u gebruikers beheert in uw Azure Data Box Gateway. Met bandbreedteschema's kunt u het gebruik van netwerkbandbreedte beheren over schema's voor meerdere tijdstippen. Deze schema's kunnen worden toegepast op upload- en downloadbewerkingen van uw apparaat naar de cloud.

U kunt de bandbreedteschema's voor uw Data Box Gateway toevoegen, wijzigen of verwijderen via de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een schema toevoegen
> * Een schema wijzigen
> * Een schema verwijderen

## <a name="add-a-schedule"></a>Een schema toevoegen

Voer in Azure Portal de volgende stappen uit om een gebruiker toe te voegen.

1. Ga in de Azure Portal voor uw Data Box Gateway naar **Bandbreedte**.
2. Klik in het rechterdeelvenster op **+ Schema toevoegen**.

    ![Klikken op Gebruiker toevoegen](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Doe het volgende in **Schema toevoegen**: 

   1. Geef de **begin dag**, de **eind datum**, de **begin tijd** en de **eind tijd** van de planning op. 
   2. Als dit schema de hele dag wordt uitgevoerd, kunt u de optie **Hele dag** inschakelen. 
   3. **Bandbreedte frequentie** is de band breedte in megabits per seconde (Mbps) die wordt gebruikt door uw apparaat in bewerkingen met betrekking tot de Cloud (zowel uploads als down Loads). Geef voor dit veld een waarde op tussen 1 en 1000. 
   4. Schakel **Onbeperkte** bandbreedte in als u de datumupload en -download niet wilt regelen. 
   5. Klik op **Add**.

      ![Klik op gebruiker toevoegen 2](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Er wordt een schema gemaakt met de opgegeven parameters. Dit schema wordt vervolgens weergegeven in de lijst van bandbreedteschema's in de portal.


## <a name="edit-schedule"></a>Schema bewerken

Voer de volgende stappen uit als u een bandbreedteschema wilt bewerken. 

1. Ga in de Azure Portal naar uw Data Box Gateway-resource en vervolgens naar Bandbreedte. 
2. Klik in de lijst met bandbreedteschema's op een schema dat u wilt wijzigen.

    ![Gebruiker wijzigen](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Breng de gewenste wijzigingen aan en sla de wijzigingen op.

    ![Gebruiker wijzigen 2](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Wanneer het schema is gewijzigd, wordt de lijst met schema's bijgewerkt met het gewijzigde schema.

    ![Gebruiker 3 wijzigen](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Een schema verwijderen

Voer de volgende stappen uit als u een bandbreedteschema wilt verwijderen dat is gekoppeld aan uw Data Box Gateway-apparaat.

1. Ga in de Azure Portal naar uw Data Box Gateway-resource en vervolgens naar **Bandbreedte**.  

2. Selecteer in de lijst met bandbreedteschema's een schema dat u wilt verwijderen. Klik met de rechtermuisknop om het contextmenu weer te geven en klik op **Verwijderen**. 

   ![Een gebruiker verwijderen](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Wanneer de planning is verwijderd, wordt de lijst met schema's bijgewerkt.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Bandbreedte beheren](data-box-gateway-manage-bandwidth-schedules.md). 
