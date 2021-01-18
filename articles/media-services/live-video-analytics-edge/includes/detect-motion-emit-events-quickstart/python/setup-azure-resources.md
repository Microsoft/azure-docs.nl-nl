---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060615"
---
Voor deze zelfstudie hebt u de volgende Azure-resources nodig:

* IoT Hub
* Storage-account
* Azure Media Services-account
* Linux-VM in Azure, met [IoT Edge-runtime](../../../../../iot-edge/how-to-install-iot-edge.md) geïnstalleerd

Voor deze quickstart wordt u aangeraden gebruik te maken van het [installatiescript voor Live Video Analytics-resources](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) om de vereiste Azure-resources in uw Azure-abonnement te implementeren. Voer hiervoor de volgende stappen uit:

1. Open [Azure Cloud Shell](https://shell.azure.com).
1. Als u Cloud Shell voor het eerst gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslagaccount en een Microsoft Azure-bestandsshare. Selecteer **Opslag maken** om een opslagaccount te maken voor de gegevens van uw Cloud Shell-sessie. Dit opslagaccount is gescheiden van het account dat door het script wordt gemaakt voor gebruik bij uw Azure Media Services-account.
1. Selecteer in de vervolgkeuzelijst aan de linkerkant van het Cloud Shell-venster **Bash** als uw omgeving.

    ![Omgevingsselector](../../../media/quickstarts/env-selector.png)
1. Voer de volgende opdracht uit.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Als het script is voltooid, ziet u alle vereiste resources in uw abonnement.
1. Als het script is voltooid, selecteert u de accolades om de mapstructuur zichtbaar te maken. Er bevinden zich enkele bestanden in de map *~/clouddrive/lva-sample*. Van belang voor deze quickstart zijn:

     * * **~/clouddrive/lva-sample/edge-deployment/.env** _: dit bestand bevat eigenschappen die Visual Studio Code gebruikt om modules te implementeren op een edge-apparaat.
     _ * **~/clouddrive/lva-sample/appsetting.json** _ - in Visual Studio Code wordt dit bestand gebruikt om de voorbeeldcode uit te voeren.
     
    U hebt deze bestanden nodig wanneer u uw ontwikkelomgeving in Visual Studio Code instelt in de volgende sectie. U kunt deze voorlopig in een lokaal bestand kopiëren.
    
    ![App-instellingen](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Als u problemen ondervindt met Azure-resources die worden gemaakt, raadpleegt u onze _ *[probleemoplossingsgids](../../../troubleshoot-how-to.md#common-error-resolutions)* * waarmee u enkele veelvoorkomende problemen kunt oplossen.