---
title: Update van het apparaat voor Azure Real-Time-Operating System | Microsoft Docs
description: Aan de slag met het apparaat bijwerken voor Azure realtime-besturings systeem
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629050"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Zelf studie over het bijwerken van apparaten voor Azure IoT Hub met behulp van Azure real-time besturings systeem (RTO'S)

In deze zelf studie leert u hoe u de update van het apparaat voor IoT Hub agent maakt in azure RTO'S NetX Duo. Het biedt ook eenvoudige Api's voor ontwikkel aars om de update mogelijkheid van het apparaat te integreren in hun toepassing. Bekijk de voor [beelden](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) van de evaluatie boards voor belangrijkste halfgeleiders die de aan de slag-hand leidingen bevatten voor meer informatie over het configureren, bouwen en implementeren van de over-the-Air (OTA)-updates op de apparaten.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Aan de slag
> * Uw apparaat labelen
> * Een apparaatgroep maken
> * Een update voor een installatie kopie implementeren
> * De update-implementatie controleren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* Toegang tot een IoT Hub. U wordt aangeraden een S1-laag (Standard) of hoger te gebruiken.
* Een update-exemplaar van het apparaat en het account dat is gekoppeld aan uw IoT Hub. Volg de hand leiding voor het [maken en koppelen](http://create-device-update-account.md/) van een update account voor een apparaat als u dit nog niet eerder hebt gedaan.

## <a name="get-started"></a>Aan de slag

Elk bord-specifiek voor beeld van een Azure RTO'S-project bevat code en documentatie over het gebruik van update voor het bijwerken van het apparaat voor IoT Hub. 
1. Down load de bord-specifieke voorbeeld bestanden van [Azure rto's en update](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)-voor beelden van apparaten.
2. Zoek naar de map docs in het gedownloade voor beeld.
3. Volg de stappen in de documenten voor het voorbereiden van Azure-resources, het account en het registreren van IoT-apparaten.
5. Volg de documenten volgende om een nieuwe firmware-installatie kopie te maken en een manifest voor uw bord te importeren.
6. Vervolgens publiceert u de installatie kopie van de firmware en het manifest naar de update voor het apparaat voor IoT Hub.
7. Down load het project tot slot en voer het op uw apparaat uit.

Meer informatie over [Azure rto's](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Uw apparaat labelen

1. Zorg ervoor dat de apparaat-app uit de vorige stap wordt uitgevoerd.
2. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar de IOT hub.
3. Zoek in het navigatie deel venster van IoT-apparaten naar uw IoT-apparaat en navigeer naar het dubbele apparaat.
4. Verwijder in het dubbele apparaat alle bestaande waarde voor het bijwerken van het apparaat door ze in te stellen op null.
5. Voeg een nieuwe waarde voor het update label van het apparaat toe, zoals hieronder wordt weer gegeven.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Update groep maken

1. Ga naar de IoT Hub die u eerder hebt verbonden met het update-exemplaar van uw apparaat.
2. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.
3. Selecteer het tabblad groepen boven aan de pagina. 
4. Selecteer de knop toevoegen om een nieuwe groep te maken.
5. Selecteer in de lijst het IoT Hub label dat u in de vorige stap hebt gemaakt. Selecteer update groep maken.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Scherm opname van label selectie." lightbox="media/create-update-group/select-tag.PNG":::

[Meer informatie](create-update-group.md) over het toevoegen van tags en het maken van update groepen

## <a name="deploy-new-firmware"></a>Nieuwe firmware implementeren

1. Zodra de groep is gemaakt, ziet u een nieuwe update die beschikbaar is voor uw apparaatgroep, met een koppeling naar de update onder in behandeling zijnde updates. Mogelijk moet u één keer vernieuwen. 
2. Klik op de beschik bare update.
3. Bevestig dat de juiste groep is geselecteerd als de doel groep. Plan uw implementatie en selecteer vervolgens update implementeren.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Update selecteren" lightbox="media/deploy-update/select-update.png":::

4. De compliance-grafiek weer geven. U ziet dat de update nu wordt uitgevoerd. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="De update wordt uitgevoerd" lightbox="media/deploy-update/update-in-progress.png":::

5. Nadat het apparaat is bijgewerkt, ziet u dat uw nalevings diagram en de update van de implementatie gegevens overeenkomen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update geslaagd" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Een update-implementatie bewaken

1. Selecteer het tabblad implementaties boven aan de pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Tabblad implementaties" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecteer de implementatie die u hebt gemaakt om de details van de implementatie weer te geven.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

3. Selecteer vernieuwen om de meest recente status gegevens weer te geven. Herhaal dit proces totdat de status is gewijzigd in geslaagd.

U hebt nu een voltooide end-to-end update van de installatie kopie voltooid met update voor het bijwerken van het apparaat voor IoT Hub op een Raspberry Pi 3 B +-apparaat. 

## <a name="cleanup-resources"></a>Resources opruimen

Wanneer u uw apparaat-update account, instance, IoT Hub en IoT-apparaat niet meer nodig hebt opschonen. 

## <a name="next-steps"></a>Volgende stappen

Bekijk voor meer informatie over Azure RTO'S en hoe het werkt met Azure IoT https://azure.com/rtos .
