---
title: Apparaatupdate voor azure-besturingssysteem in realtime | Microsoft Docs
description: Aan de slag met Apparaatupdate voor Azure Real-Time-besturingssysteem
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 40ff9431288e7afa39ea1b83706170e94866db11
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107887357"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Zelfstudie apparaatupdate voor Azure IoT Hub met behulp van Het realtime-besturingssysteem van Azure (RTOS)

In deze zelfstudie wordt bestudie hoe u de apparaatupdate voor IoT Hub-agent maakt in Azure RTOS NetXMbo. Het biedt ook eenvoudige API's voor ontwikkelaars om de apparaatupdatefunctie in hun toepassing te integreren. Bekijk [voorbeelden van](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) belangrijke evaluatieborden met de aan de slag-handleidingen voor het configureren, bouwen en implementeren van de OTA-updates (over-the-air) op de apparaten.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Aan de slag
> * Uw apparaat taggen
> * Een apparaatgroep maken
> * Een update van een afbeelding implementeren
> * De update-implementatie bewaken

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
* Toegang tot een IoT Hub. U wordt aangeraden een S1(Standard)-laag of hoger te gebruiken.
* Een apparaatupdate-exemplaar en een account die zijn gekoppeld aan uw IoT Hub. Volg de handleiding voor het [maken en koppelen van een](create-device-update-account.md) apparaatupdateaccount als u dit nog niet eerder hebt gedaan.

## <a name="get-started"></a>Aan de slag

Elk board-specifiek voorbeeldproject Azure RTOS bevat code en documentatie over het gebruik van Apparaatupdate voor IoT Hub op het project. 
1. Download de board-specifieke voorbeeldbestanden van Azure RTOS [voorbeelden van apparaatupdates.](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)
2. Zoek de map docs in het gedownloade voorbeeld.
3. Volg in de documenten de stappen voor het voorbereiden van Azure-resources en het account en het registreren van IoT-apparaten.
5. Volg vervolgens de documenten om een nieuwe firmware-afbeelding te maken en een manifest voor uw bord te importeren.
6. Publiceer vervolgens de firmware-afbeelding en het manifest naar Apparaatupdate voor IoT Hub.
7. Download ten slotte het project en voer het uit op uw apparaat.

Meer informatie over [Azure RTOS](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Uw apparaat taggen

1. Zorg ervoor dat de apparaattoepassing wordt uitgevoerd vanuit de vorige stap.
2. Meld u [aan Azure Portal](https://portal.azure.com) en navigeer naar de IoT Hub.
3. Zoek in IoT-apparaten in het navigatiedeelvenster aan de linkerkant uw IoT-apparaat en navigeer naar de apparaat dubbel.
4. Verwijder in de apparaat dubbel een bestaande waarde voor de tag Apparaatupdate door deze in te stellen op null.
5. Voeg een nieuwe waarde voor de tag Apparaatupdate toe, zoals hieronder wordt weergegeven.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Updategroep maken

1. Ga naar de IoT Hub u eerder verbinding hebt met uw apparaatupdate-exemplaar.
2. Selecteer de optie Apparaatupdates onder Automatisch apparaatbeheer in de navigatiebalk aan de linkerkant.
3. Selecteer het tabblad Groepen bovenaan de pagina. 
4. Selecteer de knop Toevoegen om een nieuwe groep te maken.
5. Selecteer de IoT Hub tag die u in de vorige stap hebt gemaakt in de lijst. Selecteer Updategroep maken.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Schermopname van tagselectie." lightbox="media/create-update-group/select-tag.PNG":::

[Meer informatie over](create-update-group.md) het toevoegen van tags en het maken van updategroepen

## <a name="deploy-new-firmware"></a>Nieuwe firmware implementeren

1. Zodra de groep is gemaakt, ziet u een nieuwe update die beschikbaar is voor uw apparaatgroep, met een koppeling naar de update onder Updates in behandeling. Mogelijk moet u eenmaal vernieuwen. 
2. Klik op de beschikbare update.
3. Controleer of de juiste groep is geselecteerd als de doelgroep. Plan uw implementatie en selecteer vervolgens Update implementeren.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Update selecteren" lightbox="media/deploy-update/select-update.png":::

4. Bekijk de compatibiliteitsgrafiek. U ziet nu dat de update wordt uitgevoerd. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Update wordt uitgevoerd" lightbox="media/deploy-update/update-in-progress.png":::

5. Nadat uw apparaat is bijgewerkt, ziet u de compatibiliteitsgrafiek en de update van de implementatiedetails. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update is geslaagd" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Een update-implementatie bewaken

1. Selecteer het tabblad Implementaties bovenaan de pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Tabblad Implementaties" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecteer de implementatie die u hebt gemaakt om de implementatiedetails weer te geven.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Implementatiedetails" lightbox="media/deploy-update/deployment-details.png":::

3. Selecteer Vernieuwen om de meest recente statusdetails weer te geven. Ga door met dit proces totdat de status verandert in Geslaagd.

U hebt nu een end-to-end-update van de afbeelding voltooid met Apparaatupdate voor IoT Hub op een Raspberry Pi 3 B+-apparaat. 

## <a name="cleanup-resources"></a>Resources opruimen

Wanneer u uw apparaatupdateaccount, exemplaar, IoT Hub en IoT-apparaat niet meer hoeft op te schonen. 

## <a name="next-steps"></a>Volgende stappen

Bekijk voor meer Azure RTOS en hoe het werkt met Azure https://azure.com/rtos IoT.
