---
title: Een update implementeren met Device update voor Azure IoT Hub | Microsoft Docs
description: Implementeer een update met Device update voor Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679236"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Een update implementeren met update voor het apparaat voor IoT Hub

Meer informatie over het implementeren van een update voor een IoT-apparaat met apparaat bijwerken voor IoT Hub.

## <a name="prerequisites"></a>Vereisten

* [Toegang tot een IOT hub met het bijwerken van het apparaat voor IOT hub ingeschakeld](create-device-update-account.md). U kunt het beste een S1 (standaard)-laag of hoger gebruiken voor uw IoT Hub. 
* [Ten minste één update is voor het ingerichte apparaat geïmporteerd.](import-update.md) 
* Een IoT-apparaat (of simulator) dat is ingericht voor het bijwerken van het apparaat binnen IoT Hub.
* [Er is een tag toegewezen aan het IoT-apparaat dat u wilt bijwerken. Het apparaat maakt deel uit van ten minste één update groep.](create-update-group.md)
* Ondersteunde browsers:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Een update implementeren

1. Ga naar [Azure Portal](https://portal.azure.com)

2. Navigeer naar de Blade update van het apparaat van uw IoT Hub.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Selecteer het tabblad groepen boven aan de pagina. [Meer informatie](device-update-groups.md) over apparaatgroepen. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Tabblad groepen" lightbox="media/deploy-update/updated-view.png":::

4. Bekijk de lijst met update nalevings diagrammen en groepen. Er wordt een nieuwe update beschikbaar voor uw apparaatgroep, met een koppeling naar de update onder in behandeling zijnde updates (mogelijk moet u één keer vernieuwen). [Meer informatie over update naleving.](device-update-compliance.md) 

5. Selecteer de beschik bare update.

6. Bevestig dat de juiste groep is geselecteerd als de doel groep. Plan uw implementatie en selecteer vervolgens update implementeren.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Update selecteren" lightbox="media/deploy-update/select-update.png":::

7. De compliance-grafiek weer geven. U ziet dat de update nu wordt uitgevoerd. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="De update wordt uitgevoerd" lightbox="media/deploy-update/update-in-progress.png":::

8. Nadat het apparaat is bijgewerkt, ziet u dat uw nalevings diagram en de update van de implementatie gegevens overeenkomen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update geslaagd" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Een update-implementatie bewaken

1. Selecteer het tabblad implementaties boven aan de pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Tabblad implementaties" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecteer de implementatie die u hebt gemaakt om de details van de implementatie weer te geven.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

3. Selecteer vernieuwen om de meest recente status gegevens weer te geven. Herhaal dit proces totdat de status is gewijzigd in geslaagd.


## <a name="retry-an-update-deployment"></a>Een update-implementatie opnieuw proberen

Als uw implementatie om een of andere reden mislukt, kunt u de implementatie opnieuw proberen voor apparaten die niet kunnen worden geïmplementeerd. 

1. Ga naar het tabblad implementaties en selecteer de implementatie die is mislukt. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

2. Klik op de apparaatstatus ' mislukt ' in het deel venster gedetailleerde implementatie-informatie.

3. Klik op niet-werkende apparaten opnieuw proberen en bevestig het bevestigings bericht. 

## <a name="next-steps"></a>Volgende stappen

[Algemene problemen](troubleshoot-device-update.md)
