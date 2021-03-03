---
title: De toepassing Voice Assistant configureren met behulp van Azure IoT Hub
description: De toepassing Voice Assistant configureren met behulp van Azure IoT Hub
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: ec3e06b2d161785b5e6978cdf4cc6415fc0eb592
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662424"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>De toepassing Voice Assistant configureren met behulp van Azure IoT Hub

In dit artikel wordt beschreven hoe u uw Voice Assistant-toepassing configureert met behulp van IoT Hub. Voor een stapsgewijze zelf studie die u helpt bij het proces van het maken van een spraak assistent met behulp van een demo sjabloon, raadpleegt u [een no-code Voice Assistant bouwen met Azure percept Studio en Azure percept-audio](./tutorial-no-code-speech.md).

## <a name="update-your-voice-assistant-configuration"></a>De configuratie van uw Voice Assistant bijwerken

1. Open de [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) en typ **IOT hub** in de zoek balk. Klik op het pictogram om de pagina IoT Hub te openen.

1. Selecteer op de pagina IoT Hub het IoT Hub waarop het apparaat is ingericht.

1. Selecteer **IOT Edge** onder **automatische Apparaatbeheer** in het navigatie menu aan de linkerkant om alle apparaten weer te geven die zijn verbonden met uw IOT hub.

1. Selecteer het apparaat waarop uw Voice Assistant-toepassing is geïmplementeerd.

1. Klik op **set-modules**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="Bitmapafbeelding.":::

1. Controleer of de volgende vermelding aanwezig is in het gedeelte **container Registry referenties** . Voeg indien nodig referenties toe.

    |Name|Adres|Gebruikersnaam|Wachtwoord|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. Selecteer in de sectie **IOT Edge modules** de optie **azureearspeechclientmodule**.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="Bitmapafbeelding.":::

1. Klik op het tabblad **module-instellingen** . Controleer de volgende configuratie:

    |URI installatiekopie|Beleid opnieuw opstarten|Gewenste status|
    |---------|--------------|--------------|
    |azureedgedevices.azurecr.io/azureearspeechclientmodule:preload-devkit |altijd|Voer|

    Als uw instellingen niet overeenkomen, bewerkt u deze en klikt u op **bijwerken**.

1. Klik op het tabblad **omgevings variabelen** . Controleer of er geen omgevings variabelen zijn gedefinieerd.

1. Klik op het tabblad Opties voor het maken van de **container** . Controleer of uw **HostConfig** -instellingen overeenkomen met die hieronder worden weer gegeven. Als dat niet het geval is, werkt u de instellingen bij.

    ```
    {
        "HostConfig": {
            "Privileged": true,
            "Binds": [
                "/dev:/dev"
            ]
        }
    }
    ```

1. Klik op het tabblad **dubbele instellingen** voor de module. Werk de sectie **speechConfigs** als volgt bij:

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedspeechscenarios.blob.core.windows.net/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > Het tref woord dat hierboven wordt gebruikt, is een standaard beschikbaar tref woord voor openbaar gebruik. Als u uw eigen tref woord wilt gebruiken, kunt u uw eigen aangepaste sleutel woorden toevoegen door een gemaakt tabel bestand te uploaden naar Blob Storage. Blob-opslag moet worden geconfigureerd met anonieme toegang tot de container of toegang tot anonieme blobs.

## <a name="how-to-find-out-appid-key-and-region"></a>AppId, sleutel en regio ontdekken

Als u uw **appID**, **sleutel** en **regio** wilt vinden, gaat u naar [Speech Studio](https://speech.microsoft.com/):

1. Meld u aan en selecteer de juiste spraak resource.
1. Klik op de start pagina van **Speech Studio** op **aangepaste opdrachten** onder **spraak assistenten**.
1. Selecteer uw doel project.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Bitmapafbeelding.":::

1. Klik op **instellingen** in het deel venster aan de linkerkant.
1. De **appID** en de **sleutel** vindt u op het tabblad **algemene** instellingen.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Bitmapafbeelding.":::

1. Als u uw **regio** wilt vinden, opent u het tabblad **Luis resources** binnen de instellingen. De selectie van de **ontwerp bron** bevat regio-informatie.

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Bitmapafbeelding.":::

1. Nadat u uw **speechConfigs** -gegevens hebt ingevoerd, klikt u op **bijwerken**.

1. Klik op het tabblad **routes** boven aan de pagina **set-modules** . Zorg ervoor dat u over een route met de volgende waarde beschikt:

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    Voeg de route toe als deze niet bestaat.

1. Klik op **Controleren + maken**.

1. Klik op **Create**.

## <a name="next-steps"></a>Volgende stappen

Nadat u de configuratie van de Voice Assistant hebt bijgewerkt, gaat u terug naar de demo in azure percept Studio om te communiceren met de toepassing.