---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763408"
---
## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services implementeren en configureren

De oplossing maakt gebruik van een Azure Media Services-account om de objectdetectievideoclips op te slaan die door het IoT Edge-gatewayapparaat worden gemaakt.

Wanneer u het Media Services-account maakt:

- U moet een accountnaam, een Azure-abonnement, een locatie, een resourcegroep en een opslagaccount opgeven. Maak een nieuw opslagaccount met behulp van de standaardinstellingen terwijl u het Media Services-account maakt.

- Kies de regio **VS - oost** als locatie.

- Maak een nieuwe resourcegroep genaamd *lva-rg* in de regio **VS - oost** voor het Media Services-account en het opslagaccount. Wanneer u klaar bent met de zelfstudies, kunt u alle resources gemakkelijk verwijderen door de resourcegroep *lva-rg* te verwijderen.

Maak het [Media Services-account in de Azure-portal](https://portal.azure.com/?r=1#create/Microsoft.MediaService).

> [!TIP]
> Deze zelfstudies maken in alle voorbeelden gebruik van de regio **VS - oost**. U kunt desgewenst uw dichtstbijzijnde regio gebruiken.

Noteer de naam van uw **Media Services**-account in het bestand *scratchpad.txt*.

Wanneer de implementatie is voltooid, opent u Cloud Shell en voert u de volgende opdracht uit om de **Resource-id** voor uw Media Services-account op te halen:

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Cloud Shell gebruiken om de resource-id op te halen":::

Noteer de **Resource-id** in het bestand *scratchpad.txt*. U gebruikt deze waarde later wanneer u de IoT Edge-module configureert.

Configureer vervolgens een Azure Active Directory-service-principal voor uw Media Services-resource. Selecteer **API-toegang** en vervolgens **Verificatie van service-principal**. Maak een nieuwe Azure Active Directory-app met dezelfde naam als uw Media Services-resource, en maak een geheim met de beschrijving *IoT Edge-toegang*.

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Azure Active Directory-app configureren voor Azure Media Services":::

Wanneer het geheim wordt gegenereerd, schuift u omlaag naar de sectie **Kopieer uw referenties om verbinding te maken met de service-principaltoepassing**. Selecteer vervolgens **JSON**. U kunt van hieruit alle referentiegegevens in één keer kopiëren. Noteer deze gegevens in het bestand *scratchpad.txt*. U gebruikt ze later wanneer u het IoT Edge-apparaat configureert.

> [!WARNING]
> Dit is uw enige kans om het geheim te bekijken en op te slaan. Als u het kwijtraakt, moet u een ander geheim genereren.

## <a name="create-the-azure-iot-central-application"></a>De Azure IoT Central-toepassing maken

In deze sectie maakt u een nieuwe Azure IoT Central-toepassing op basis van een sjabloon. U gebruikt deze toepassing in de rest van de zelfstudiereeks om een volledige oplossing te ontwikkelen.

Een nieuwe Azure IoT Central-toepassing maken:

1. Navigeer naar de website [Azure IoT Central-toepassingsbeheer](https://aka.ms/iotcentral).

1. Meld u aan met de referenties die u gebruikt om toegang te krijgen tot uw Azure-abonnement.

1. Als u wilt beginnen met het maken van een nieuwe Azure IoT Central-toepassing, selecteert u **Nieuwe toepassing** op de pagina **Ontwikkelen**.

1. Selecteer **Detailhandel**. De detailhandelspagina toont verschillende toepassingssjablonen voor detailhandels.

Een nieuwe toepassing voor videoanalyse maken:

1. Selecteer de toepassingssjabloon **Videoanalyse: object- en bewegingsdetectie**. Deze sjabloon bevat apparaatsjablonen voor de apparaten die in de zelfstudie worden gebruikt. De sjabloon bevat voorbeelden van dashboards die operators kunnen gebruiken om taken uit te voeren, zoals het bewaken en beheren van camera's.

1. Kies desgewenst een beschrijvende **Toepassingsnaam**. Deze toepassing is gebaseerd op een fictieve detailhandel genaamd Northwind Traders. In de zelfstudie wordt de **Toepassingsnaam** *Northwind Traders-videoanalyse* gebruikt.

    > [!NOTE]
    > Als u een beschrijvende **Toepassingsnaam** gebruikt, moet u nog steeds een unieke waarde voor de **Toepassings-URL** gebruiken.

1. Als u een Azure-abonnement hebt, selecteert u uw **Directory**, **Azure-abonnement** en **Verenigde Staten** als **Locatie**. Als u geen abonnement hebt, kunt u een **gratis proefversie van zeven dagen** inschakelen en de vereiste contactgegevens invullen. In deze zelfstudie worden drie apparaten gebruikt: twee camera’s en een IoT Edge-apparaat. Als u dus niet de gratis proefversie gebruikt, worden er kosten voor gebruik in rekening gebracht.

    Zie de [quickstart over het maken van een toepassing](../articles/iot-central/core/quick-deploy-iot-central.md) voor meer informatie over directory's, abonnementen en locaties.

1. Selecteer **Maken**.

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Pagina Toepassing maken van Azure IoT Central":::

### <a name="retrieve-the-configuration-data"></a>De configuratiegegevens ophalen

Later in deze zelfstudie, wanneer u de IoT Edge-gateway configureert, hebt u enkele configuratiegegevens uit de IoT Central-toepassing nodig. Het IoT Edge-apparaat heeft deze gegevens nodig om zich te registreren bij en verbinding te maken met de toepassing.

Selecteer in de sectie **Beheer** de optie **Uw toepassing** en noteer de **Toepassings-URL** en de **Toepassings-id** in het bestand *scratchpad.txt*:

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="Schermafbeelding met het venster Beheer van pagina videoanalyse met toepassings-URL en toepassings-ID gemarkeerd.":::

Selecteer **API-tokens** en genereer een nieuw token genaamd **LVAEdgeToken** voor de rol **Operator**:

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="Token genereren":::

Noteer het token in het bestand *scratchpad.txt* voor later. Nadat het dialoogvenster is gesloten, kunt u het token niet meer bekijken.

Selecteer in de sectie **Beheer** de optie **Apparaatverbinding** en selecteer vervolgens **SAS-IoT-Devices**.

Noteer de **Primaire sleutel** voor apparaten in het bestand *scratchpad.txt*. U gebruikt dit *Shared Access Signature-token voor de primaire groep* later wanneer u het IoT Edge-apparaat configureert.
