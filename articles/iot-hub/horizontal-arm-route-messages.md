---
title: ARM-sjabloon gebruiken voor het publiceren van Azure IoT Hub, opslagaccount, routeringsberichten
description: ARM-sjabloon gebruiken voor het publiceren van Azure IoT Hub, opslagaccount, routeringsberichten
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981286"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Quickstart: Een Azure IoT Hub en een opslagaccount implementeren met een ARM-sjabloon

In deze quickstart gebruikt u een Azure Resource Manager-sjabloon (ARM-sjabloon) om een IoT Hub te maken voor het doorsturen van berichten naar Azure Storage, en een opslagaccount voor het opslaan van berichten. Nadat u handmatig een virtueel IoT-apparaat aan de hub hebt toegevoegd om de berichten te verzenden, configureert u de verbindingsgegevens in een toepassing met de naam  *arm-read-write*, om berichten van het apparaat naar de hub te verzenden. De hub is zo geconfigureerd dat de berichten die naar de hub worden verzonden, automatisch worden doorgestuurd naar het opslagaccount. Aan het einde van deze quickstart kunt u het opslagaccount openen en de verzonden berichten bekijken.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, wordt `101-iothub-auto-route-messages` uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages) genoemd.

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Er worden twee Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>De sjabloon implementeren en de voorbeeld-app uitvoeren

In deze sectie vindt u de stappen om de sjabloon te implementeren, een virtueel apparaat te maken en de toepassing arm-read-write uit te voeren om de berichten te verzenden.

1. Maak de resources door de ARM-sjabloon te implementeren.

    > [!TIP]
    > Selecteer onderstaande knop om de implementatie van de sjabloon te starten. Terwijl deze wordt uitgevoerd, stelt u de toepassing arm-read-write in om te worden uitgevoerd.

    [![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Download de [IoT C#-voorbeelden](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/) en pak ze uit.

1. Open een opdrachtvenster en ga naar de map waarin u de IoT C#-voorbeelden hebt uitgepakt. Zoek de map met het bestand arm-read-write.csproj. In dit opdrachtvenster maakt u de omgevingsvariabelen. Meld u aan bij [Azure Portal](https://portal.azure.com) om de sleutels op te halen. Selecteer **Resourcegroepen** en selecteer vervolgens de resourcegroep die u voor deze quickstart hebt gemaakt.

   ![De resourcegroep selecteren](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. U ziet de IoT Hub en het opslagaccount die zijn gemaakt tijdens de implementatie van de ARM-sjabloon. Wacht tot de sjabloon volledig is geïmplementeerd voordat u verder gaat. Selecteer vervolgens de resourcegroep om uw resources weer te geven.

   ![Resources in de resourcegroep weergeven](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. U hebt de **naam van de hub** nodig. Selecteer de hub in de lijst met resources. Kopieer de naam van de hub bovenin de IoT Hub-sectie naar het klembord van Windows.

   ![De hubnaam kopiëren](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Vervang hiermee de naam van de hub in deze opdracht waar deze voorkomt en voer deze opdracht uit in het opdrachtvenster:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   dit ziet er als volgt uit:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. De volgende omgevingsvariabele is de IoT-apparaatcode. Voeg een nieuw apparaat aan de hub toe door **IOT-apparaten** te selecteren in het menu IoT Hub voor de hub.

   ![IoT-apparaten selecteren](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Selecteer aan de rechterkant van het scherm **+ NIEUW** om een nieuw apparaat toe te voegen.

   Vul de naam van het nieuwe apparaat in. In deze quickstart wordt een naam gebruikt die begint met **Contoso-Test-Device**. Sla het apparaat op en open dat scherm opnieuw om de apparaatsleutel op te halen. (De sleutel wordt voor u gegenereerd wanneer u het deelvenster sluit.) Selecteer de primaire of secundaire sleutel en kopieer deze naar het Windows-klembord. Stel in het opdrachtvenster de opdracht in die u wilt uitvoeren en druk op **Enter**. De opdracht moet er als volgt uitzien, maar met de sleutel van het apparaat erin geplakt:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. De laatste omgevingsvariabele is de **Apparaat-id**. Stel in het opdrachtvenster de opdracht in en voer deze uit.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   Deze ziet er als volgt uit:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Als u de omgevingsvariabelen wilt zien die u hebt gedefinieerd, typt u SET op de opdrachtregel en drukt u op **Enter**. Zoek vervolgens naar variabelen die beginnen met **IoT**.

   ![Omgevingsvariabelen weergeven](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Nu de omgevingsvariabelen zijn ingesteld, voert u de toepassing uit vanuit hetzelfde opdrachtvenster. Omdat u hetzelfde venster gebruikt, zijn de variabelen toegankelijk in het geheugen wanneer u de toepassing uitvoert.

1. Als u de toepassing wilt uitvoeren, typt u de volgende opdracht in het opdrachtvenster en drukt u op **Enter**.

    `dotnet run arm-read-write`

   De toepassing genereert en toont berichten op de console wanneer elk bericht wordt verzonden naar de IoT-hub. De hub is in de ARM-sjabloon geconfigureerd voor automatische routering. Berichten die de tekst `level = storage` bevatten, worden automatisch doorgestuurd naar het opslagaccount. Laat de app 10 tot 15 minuten werken en druk dan één of twee keer op **Enter** totdat deze stopt.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer de resourcegroep. Selecteer vervolgens het opslagaccount.

1. Zoom in op het opslagaccount totdat u bestanden vindt.

   ![De opslagaccountbestanden bekijken](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Selecteer een van de bestanden en selecteer **Downloaden**. Download het bestand naar een locatie die u later kunt vinden. Het bestand krijgt een numerieke naam, bijvoorbeeld 47. Voeg _.txt_ toe aan het einde en dubbelklik op het bestand om het te openen.

1. Wanneer u het bestand opent, heeft elke rij betrekking op een ander bericht. De hoofdtekst van elk bericht is ook versleuteld. Dit is nodig om query's uit te voeren op basis van de hoofdtekst van het bericht.

   ![De verzonden berichten weergeven](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Deze berichten zijn gecodeerd in UTF-32 en base64. Als u het bericht terugleest, moet u het decoderen van base64 en UTF-32 om het te kunnen lezen als ASCII. Als u geïnteresseerd bent, kunt u de methode ReadOneRowFromFile in de zelfstudie voor routering gebruiken om een van deze berichtbestanden te lezen en te decoderen naar ASCII. ReadOneRowFromFile bevindt zich in de opslagplaats voor IoT C#-voorbeelden die u voor deze quickstart hebt uitgepakt. Dit is het pad naar het begin van de map: *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs.* Stel de booleaanse waarde `readTheFile` in op True en codeer het pad naar het bestand op schijf. Het wordt dan geopend en de eerste rij in het bestand wordt vertaald.

U hebt een ARM-sjabloon geïmplementeerd om een IoT Hub en een opslagaccount te maken. En u hebt een programma uitgevoerd om berichten naar de hub te verzenden. De berichten worden vervolgens automatisch opgeslagen in het opslagaccount, waar ze kunnen worden weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt verwijderen die tijdens deze quickstart zijn toegevoegd, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). Selecteer **Resourcegroepen** en zoek de resourcegroep die u voor deze quickstart hebt gebruikt. Selecteer de resourcegroep en selecteer vervolgens *Verwijderen*. Hiermee worden alle resources in de groep verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
