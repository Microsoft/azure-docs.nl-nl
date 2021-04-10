---
title: Apparaatgroep maken in apparaat bijwerken voor Azure IoT Hub | Microsoft Docs
description: Een apparaatgroep maken in apparaat bijwerken voor Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679241"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Apparaatgroepen maken in update voor het apparaat voor IoT Hub
Met Device update voor IoT Hub kunt u een update implementeren op een groep IoT-apparaten.

## <a name="prerequisites"></a>Vereisten

* [Toegang tot een IOT hub met het bijwerken van het apparaat voor IOT hub ingeschakeld](create-device-update-account.md). U kunt het beste een S1 (standaard)-laag of hoger gebruiken voor uw IoT Hub. 
* Een IoT-apparaat (of simulator) dat is ingericht voor het bijwerken van het apparaat binnen IoT Hub.
* [Ten minste één update is voor het ingerichte apparaat geïmporteerd.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Een tag toevoegen aan uw apparaten  

Met Device update voor IoT Hub kunt u een update implementeren op een groep IoT-apparaten. Als u een groep wilt maken, gaat u als eerste stap een tag toevoegen aan de doelset apparaten in IoT Hub. Labels kunnen alleen worden toegevoegd aan het apparaat nadat het is verbonden met het apparaat bijwerken.

In de onderstaande documentatie wordt beschreven hoe u een tag toevoegt en bijwerkt.

### <a name="programmatically-update-device-twin"></a>Het apparaat wordt programmatisch bijgewerkt

U kunt het apparaat met behulp van RegistryManager bijwerken na het registreren van het apparaat bij het bijwerken van het apparaat. 
[Meer informatie over het toevoegen van tags met behulp van een .NET-voor beeld-app.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Meer informatie over eigenschappen van labels](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Label indeling apparaat bijwerken

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Taken gebruiken

Het is mogelijk om een taak op meerdere apparaten te plannen om een update van het apparaat toe te voegen of bij te werken volgens [deze](../iot-hub/iot-hub-devguide-jobs.md) voor beelden. [Meer informatie](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Deze actie gaat verder met het quotum van uw huidige IOT hub-berichten en het wordt aangeraden om Maxi maal 50.000 apparaatspecifieke Tags per keer te wijzigen. Als u uw dagelijkse IoT Hub bericht quotum overschrijdt, moet u mogelijk meer IoT Hub-eenheden aanschaffen. Meer informatie vindt u op [quota's en beperking](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Direct dubbele updates

Labels kunnen ook rechtstreeks worden toegevoegd of bijgewerkt in het apparaat.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar uw IOT hub.

2. Zoek in het navigatie deel venster van IoT-apparaten of IoT Edge in het linkernavigatievenster uw IoT-apparaat op en navigeer naar het dubbele apparaat.

3. Verwijder in het dubbele apparaat alle bestaande waarde voor het bijwerken van het apparaat door ze in te stellen op null.

4. Voeg een nieuwe waarde voor het update label van het apparaat toe, zoals hieronder wordt weer gegeven. [Voor beeld van een dubbele JSON-document van een apparaat met tags.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Beperkingen

* U kunt elke wille keurige waarde toevoegen aan uw tag, behalve ' oncategoriseer ', wat een gereserveerde waarde is.
* De label waarde mag niet langer zijn dan 255 tekens.
* De label waarde mag alfanumerieke tekens en de volgende speciale tekens bevatten: '. ', '-', ' _ ', ' ~ '.
* Tags en groeps namen zijn hoofdletter gevoelig.
* Een apparaat kan slechts één tag met de naam ADUGroup hebben. bij latere toevoegingen van een tag met die naam wordt de bestaande waarde voor label naam ADUGroup vervangen.
* Eén apparaat kan slechts tot één groep behoren.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Een apparaatgroep maken door een bestaande IoT Hub-tag te selecteren

1. Ga naar de [Azure Portal](https://portal.azure.com).

2. Selecteer de IoT Hub u eerder verbinding hebt gemaakt met het update-exemplaar van uw apparaat.

3. Selecteer de optie apparaat bijwerken onder Automatische Apparaatbeheer in de navigatie balk aan de linkerkant.

4. Selecteer het tabblad groepen boven aan de pagina. U kunt het aantal apparaten dat is verbonden met de update van het apparaat weer geven dat nog niet is gegroepeerd.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Scherm opname van niet-gegroepeerde apparaten." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Selecteer de knop toevoegen om een nieuwe groep te maken.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Scherm opname van de apparaatgroep toevoegen." lightbox="media/create-update-group/add-group.png":::

6. Selecteer een IoT Hub-tag in de lijst en selecteer vervolgens Update groep maken.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Scherm opname van label selectie." lightbox="media/create-update-group/select-tag.png":::

7. Zodra de groep is gemaakt, ziet u dat de lijst update nalevings diagram en groepen wordt bijgewerkt.  Diagram naleving bijwerken toont het aantal apparaten in verschillende statussen van naleving: bij de nieuwste update, nieuwe updates die beschikbaar zijn, worden updates uitgevoerd en de apparaten die nog niet zijn gegroepeerd. [Meer informatie over update naleving.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Scherm opname van de weer gave naleving van updates." lightbox="media/create-update-group/updated-view.png":::

8. U ziet nu de zojuist gemaakte groep en eventuele beschik bare updates voor de apparaten in de nieuwe groep. U kunt de update implementeren in de nieuwe groep vanuit deze weer gave door te klikken op de naam van de update. Zie volgende stap: update implementeren voor meer informatie.

## <a name="view-device-details-for-the-group-you-created"></a>Details van het apparaat weer geven voor de groep die u hebt gemaakt

1. Ga naar de zojuist gemaakte groep en klik op de naam van de groep.

2. Een lijst met apparaten die deel uitmaken van de groep wordt samen met de eigenschappen van de update van het apparaat weer gegeven. In deze weer gave ziet u ook de update nalevings informatie voor alle apparaten die lid zijn van de groep. Diagram naleving bijwerken toont het aantal apparaten in verschillende statussen van naleving: bij de nieuwste update worden nieuwe updates beschikbaar en updates uitgevoerd.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Scherm afbeelding van de detail weergave van de apparaatgroep." lightbox="media/create-update-group/group-details.png":::

3. U kunt ook op elk afzonderlijk apparaat in een groep klikken om te worden omgeleid naar de detail pagina van het apparaat in IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Scherm opname van de weer gave van de apparaatgegevens." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Volgende stappen 

[Update implementeren](deploy-update.md)

[Meer informatie over apparaatgroepen](device-update-groups.md)

[Meer informatie over update naleving.](device-update-compliance.md)
