---
title: Azure Media Services gebeurtenissen controleren met Event Grid met behulp van CLI
description: In dit artikel wordt beschreven hoe u zich abonneert op Event Grid om Azure Media Services-gebeurtenissen te controleren met behulp van Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: d7148841083cccf4197fe353d077e5149e4afac5
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895319"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Media Services gebeurtenissen maken en bewaken met Event Grid met behulp van de Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid is een gebeurtenisservice voor de cloud. Deze service maakt gebruik van [gebeurtenis abonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. Media Services gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een Media Services gebeurtenis identificeren omdat de eigenschap Event type met ' micro soft. media. ' begint. Zie [Media Services-gebeurtenis schema's](media-services-event-schemas.md)voor meer informatie.

In dit artikel gebruikt u de Azure CLI om u te abonneren op gebeurtenissen voor uw Azure Media Services-account. Vervolgens triggert u gebeurtenissen om het resultaat weer te geven. Normaal gesproken verzendt u gebeurtenissen naar een eindpunt dat de gebeurtenisgegevens verwerkt en vervolgens in actie komt. In dit artikel verzendt u de gebeurtenissen naar een web-app waarmee de berichten worden verzameld en weer gegeven.

## <a name="prerequisites"></a>Vereisten

- Een actief Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- Installeer en gebruik de CLI lokaal. Voor dit artikel dient u gebruik te maken van Azure CLI, versie 2.0 of hoger. Voer `az --version` uit om te zien welke versie u hebt. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

    Momenteel werken niet alle [Media Services v3 CLI](/cli/azure/ams)-opdrachten in Azure Cloud Shell. U wordt aangeraden de CLI lokaal te gebruiken.

- [Een Azure Media Services-account maken](./create-account-howto.md).

    Vergeet niet de waarden die u hebt gebruikt voor de namen van de resourcegroep en het Media Services-account.

## <a name="create-a-message-endpoint"></a>Het eindpunt van een bericht maken

Voordat u zich abonneert op de gebeurtenissen voor het Media Services-account, gaan we het eind punt voor het gebeurtenis bericht maken. Het eindpunt onderneemt normaal gesproken actie op basis van de gebeurtenisgegevens. In dit artikel implementeert u een [vooraf gemaakte web-app](https://github.com/Azure-Samples/azure-event-grid-viewer) waarin de gebeurtenis berichten worden weer gegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

1. Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

Als u overschakelt naar de site Azure Event Grid viewer, ziet u dat er nog geen gebeurtenissen zijn.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Het Azure-abonnement instellen

Geef in de volgende opdracht de id van het Azure-abonnement op dat u wilt gebruiken voor het Media Services-account. U kunt een lijst met abonnementen bekijken waartoe u toegang hebt door naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) te gaan.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Abonneren op Media Services gebeurtenissen

U abonneert u op een artikel om Event Grid welke gebeurtenissen u wilt bijhouden. In het volgende voor beeld wordt een abonnement genomen op het Media Services-account dat u hebt gemaakt en wordt de URL door gegeven van de website die u hebt gemaakt als eind punt voor gebeurtenis meldingen. 

Vervang door `<event_subscription_name>` een unieke naam voor uw gebeurtenis abonnement. `<resource_group_name>`Gebruik voor en `<ams_account_name>` de waarden die u hebt gebruikt bij het maken van het Media Services-account. Geef voor de de `<endpoint_URL>` URL van uw web-app op en voeg deze toe `api/updates` aan de URL van de start pagina. Door het eind punt op te geven bij het abonneren, wordt Event Grid de route ring van gebeurtenissen naar dat eind punt verwerkt. 

1. De resource-id ophalen

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Bijvoorbeeld:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Abonneren op de gebeurtenissen

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Bijvoorbeeld:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > U kunt validatie-Handshake-waarschuwing krijgen. Een paar minuten geven en de handshake moet valideren.

Nu gaan we gebeurtenissen activeren om te zien hoe Event Grid het bericht distribueert naar uw eind punt.

## <a name="send-an-event-to-your-endpoint"></a>Een gebeurtenis verzenden naar het eindpunt

U kunt gebeurtenissen voor het Media Services-account activeren door een coderings taak uit te voeren. U kunt [deze Snelstartgids](stream-files-dotnet-quickstart.md) volgen om een bestand te coderen en te beginnen met het verzenden van gebeurtenissen. 

Bekijk opnieuw uw web-app en u zult zien dat er een validatiegebeurtenis voor een abonnement naartoe is verzonden. Via Event Grid wordt de validatiegebeurtenis verzonden zodat het eindpunt kan controleren of de gebeurtenisgegevens in aanmerking komen om ontvangen te worden. Het eind punt moet worden ingesteld `validationResponse` op `validationCode` . Zie [Event grid beveiliging en verificatie](../../event-grid/security-authentication.md)voor meer informatie. U kunt de code van de web-app bekijken om te zien hoe het abonnement wordt gevalideerd.

> [!TIP]
> Selecteer het oogpictogram om de gebeurtenisgegevens uit te breiden. Vernieuw de pagina niet als u alle gebeurtenissen wilt weer geven.

![Een abonnementgebeurtenis weergeven](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Volgende stappen

[Uploaden, coderen en streamen](stream-files-tutorial-with-api.md)