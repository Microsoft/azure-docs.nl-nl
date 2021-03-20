---
title: Een web-app voor ruimtelijke analyse implementeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van ruimtelijke analyse in een webtoepassing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: de011fb0f827ea90efe33e237bbf1c5100dc76a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98183469"
---
# <a name="how-to-deploy-a-people-counting-web-application"></a>Procedure: een web-app voor het tellen van een persoon implementeren

In dit artikel vindt u informatie over het integreren van ruimtelijke analyse in een web-app die inzicht heeft in de beweging van personen, en wordt het aantal mensen bewaakt dat een fysieke ruimte beslaat. 

In deze zelfstudie leert u het volgende:

* De container voor ruimtelijke analyse implementeren
* De bewerking en camera configureren
* De IoT Hub-verbinding configureren in de webtoepassing
* De webtoepassing implementeren en testen

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/cognitive-services/)
* Basis informatie over Azure IoT Edge implementatie configuraties en een [Azure IOT hub](../../iot-hub/index.yml)
* Een geconfigureerde [hostcomputer](spatial-analysis-container.md).

## <a name="deploy-the-spatial-analysis-container"></a>De container voor ruimtelijke analyse implementeren

Vul de [aanvraag toepassing](https://aka.ms/csgate) in om toegang te krijgen om de container uit te voeren. 

Volg [de installatie van de hostcomputer](./spatial-analysis-container.md) om de hostcomputer te configureren en een IOT edge apparaat te verbinden met Azure IOT hub. 

### <a name="deploy-an-azure-iot-hub-service-in-your-subscription"></a>Een Azure IoT Hub-service implementeren in uw abonnement

Maak eerst een exemplaar van een Azure IoT Hub-service met de Standard-prijs categorie (S1) of de gratis laag (S0). Volg deze instructies om dit exemplaar te maken met behulp van de Azure CLI.

Vul de vereiste para meters in:
* Abonnement: de naam of ID van uw Azure-abonnement
* Resource groep: Maak een naam voor de resource groep
* IOT-hub-naam: Maak een naam voor uw IoT Hub
* IoTHub: de naam van de IoT Hub die u hebt gemaakt 
* Rand apparaatnaam: een naam maken voor uw edge-apparaat

```azurecli
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "<Resource Group Name>" --location "WestUS"

az iot hub create --name "<IoT Hub Name>" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "<IoT Hub Name>" --device-id "<Edge Device Name>" --edge-enabled
```

### <a name="deploy-the-container-on-azure-iot-edge-on-the-host-computer"></a>De container implementeren op Azure IoT Edge op de hostcomputer

Implementeer de container voor ruimtelijke analyse als een IoT-module op de hostcomputer met behulp van de Azure CLI. Voor het implementatie proces is een manifest bestand voor implementatie vereist waarin de vereiste containers, variabelen en configuraties voor uw implementatie worden beschreven. U vindt een voor beeld [Azure stack Edge-specifiek](https://go.microsoft.com/fwlink/?linkid=2142179)implementatie manifest [, niet-Azure stack Edge-specifiek implementatie](https://go.microsoft.com/fwlink/?linkid=2152189)manifest en [Azure VM met GPU-specifiek implementatie manifest](https://go.microsoft.com/fwlink/?linkid=2152189) op github, die een eenvoudige implementatie configuratie voor de container *met ruimtelijke analyse* bevatten. 

U kunt ook de Azure IoT-uitbrei dingen voor Visual Studio code gebruiken om bewerkingen uit te voeren met uw IoT-hub. Ga naar [Deploy Azure IOT Edge-modules van Visual Studio code](../../iot-edge/how-to-deploy-modules-vscode.md) voor meer informatie.

> [!NOTE] 
> De containers *ruimtelijke analyse-telegrafie* en *ruimtelijke analyse* zijn optioneel. U kunt ervoor kiezen om deze te verwijderen uit de *DeploymentManifest.jsin* het bestand. Raadpleeg het artikel over [telemetrie en probleem oplossing](./spatial-analysis-logging.md) voor meer informatie. U kunt drie voorbeeld *DeploymentManifest.jsvinden op* bestanden op github, voor [Azure stack edge-apparaten](https://go.microsoft.com/fwlink/?linkid=2142179), een [desktop machine](https://go.microsoft.com/fwlink/?linkid=2152189)of een [Azure-VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189)

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

De meeste **omgevings variabelen** voor de IOT Edge module zijn al ingesteld in de voorbeeld *DeploymentManifest.jsop* bestanden die hierboven zijn gekoppeld. Zoek in het bestand naar de `BILLING_ENDPOINT` `API_KEY` omgevings variabelen en, zoals hieronder wordt weer gegeven. Vervang de waarden door de eind punt-URI en de API-sleutel die u eerder hebt gemaakt. Zorg ervoor dat de waarde van de gebruiksrecht overeenkomst is ingesteld op ' accepteren '. 

```json
"EULA": { 
    "value": "accept"
},

"BILLING_ENDPOINT":{ 
    "value": "<Use a key from your Computer Vision resource>"
},
"API_KEY":{
    "value": "<Use the endpoint from your Computer Vision resource>"
}
```

### <a name="configure-the-operation-parameters"></a>De bewerkings parameters configureren

Nu de eerste configuratie van de container met *ruimtelijke analyse* is voltooid, is de volgende stap het configureren van de para meters voor bewerkingen en het toevoegen ervan aan de implementatie. 

De eerste stap is het bijwerken van het voor beeld-implementatie manifest dat hierboven is gekoppeld en configureer de operationId voor `cognitiveservices.vision.spatialanalysis-personcount` , zoals hieronder wordt weer gegeven:


```json
"personcount": {
    "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "<Replace RTSP URL here>",
        "VIDEO_SOURCE_ID": "<Replace with friendly name>",
        "VIDEO_IS_LIVE":true,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[<Replace with your values>], \"events\": [{\"type\":\"count\"}], \"threshold\":<use 0 for no threshold.}]}"
    }
},
```

Nadat het implementatie manifest is bijgewerkt, volgt u de instructies van de camera fabrikant voor het installeren van de camera, configureert u de camera-URL en configureert u de gebruikers naam en het wacht woord. 

Stel vervolgens `VIDEO_URL` in op de RTSP-URL van de camera en de referenties voor het maken van een verbinding met de camera.

Als het edge-apparaat meer dan één GPU heeft, selecteert u de GPU waarop deze bewerking moet worden uitgevoerd. Zorg ervoor dat u taken verdeelt van de bewerkingen waarbij er niet meer dan acht bewerkingen tegelijk op één GPU worden uitgevoerd.  

Configureer vervolgens de zone waarin u personen wilt tellen. Als u de zone veelhoek wilt configureren, volgt u eerst de instructies van de fabrikant voor het ophalen van een frame van de camera. Als u elk hoek punt van de veelhoek wilt bepalen, selecteert u een punt op het frame, neemt u de x-, y-pixel coördinaten van het punt op ten opzichte van de linkerkant, de bovenste hoek van het kader en deelt u deze door de corresponderende kader afmetingen. Stel de resultaten in als x-en y-coördinaten van het hoek punt. U kunt de configuratie van de zone veelhoek instellen in het `SPACEANALYTICS_CONFIG` veld.

Dit is een voor beeld van een video frame dat laat zien hoe de hoekpunt coördinaten worden berekend voor een frame met een grootte van 1920/1080.
![Voorbeeld frame van video](./media/spatial-analysis/sample-video-frame.jpg)

U kunt ook een betrouwbaarheids drempel selecteren voor wanneer gedetecteerde personen worden geteld en gebeurtenissen worden gegenereerd. Stel de drempel waarde in op 0 als u wilt dat alle gebeurtenissen worden uitgevoerd.

### <a name="execute-the-deployment"></a>De implementatie uitvoeren

Nu het implementatie manifest is voltooid, gebruikt u deze opdracht in de Azure CLI om de container op de hostcomputer te implementeren als een IoT Edge module.

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json -–subscription "<subscriptionId>"
```

Vul de vereiste para meters in:

* IoT Hub naam: de naam van uw Azure-IoT Hub
* DeploymentManifest.jsop: de naam van het implementatie bestand
* IoT Edge apparaatnaam: de naam van het IoT Edge apparaat van de hostcomputer
* Abonnement: uw abonnements-ID of-naam

Met deze opdracht wordt de implementatie gestart en kunt u de implementatie status weer geven in uw Azure IoT Hub-exemplaar in de Azure Portal. De status kan worden weer gegeven als *417: de implementatie configuratie van het apparaat is niet ingesteld* totdat het apparaat klaar is met het downloaden van de container installatie kopieën en wordt gestart.

### <a name="validate-that-the-deployment-was-successful"></a>Controleren of de implementatie is geslaagd

Zoek de *runtime status* in de instellingen van de IOT Edge-module voor de module ruimtelijke analyse in uw IOT hub-exemplaar op de Azure Portal. De **gewenste waarde** en **gerapporteerde waarde** voor de *runtime status* moeten zeggen `Running` . Hieronder ziet u hoe dit eruitziet op het Azure Portal.

![Voor beeld van implementatie verificatie](./media/spatial-analysis/deployment-verification.png)

Op dit moment wordt de bewerking uitgevoerd door de container met ruimtelijke analyse. Het verzendt AI Insights voor de `cognitiveservices.vision.spatialanalysis-personcount` bewerking en stuurt deze inzichten als telemetrie naar uw Azure IOT hub-exemplaar. Als u aanvullende camera's wilt configureren, kunt u het manifest bestand van de implementatie bijwerken en de implementatie opnieuw uitvoeren.

## <a name="person-counting-web-application"></a>De persoon die de webtoepassing telt

Deze persoon die de webtoepassing telt, stelt u in staat om snel een voor beeld-web-app te configureren en deze in uw Azure-omgeving te hosten.

### <a name="get-the-person-counting-app-container"></a>De app-container voor het tellen van de persoon ophalen

Een container formulier van deze app is beschikbaar op de Azure Container Registry. Gebruik de volgende opdracht docker pull om deze te downloaden. Neem contact op met micro soft projectarchon@microsoft.com voor het toegangs token.

```bash
docker login rtvsofficial.azurecr.io -u <token name> -p <password>
docker pull rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0
```

Push de container naar uw Azure Container Registry (ACR).

```bash
az acr login --name <your ACR name>

docker tag rtvsofficial.azurecr.io/acceleratorapp.personcount:1.0 [desired local image name]

docker push [desired local image name]
```

Als u de container wilt installeren, maakt u een nieuwe Azure Web App for Containers en vult u de vereiste para meters in. Ga vervolgens naar het tabblad **docker** en selecteer **Eén Container** en vervolgens **Azure container Registry**. Gebruik uw exemplaar van Azure Container Registry waar u de bovenstaande afbeelding hebt gepusht.

![Details van de installatie kopie opgeven](./media/spatial-analysis/solution-app-create-screen.png)

Nadat u de bovenstaande para meters hebt ingevoerd, klikt u op **controleren + maken** en maakt u de app.

### <a name="configure-the-app"></a>De app configureren 

Wacht totdat de installatie is voltooid en navigeer naar uw resource in de Azure Portal. Ga naar de **configuratie** sectie en voeg de volgende twee **Toepassings instellingen** toe.

* `EventHubConsumerGroup` : De teken reeks naam van de Consumer groep van uw Azure IoT Hub, u kunt een nieuwe consumenten groep maken in uw IoT Hub of de standaard groep gebruiken. 
* `IotHubConnectionString` : De connection string naar uw Azure IoT Hub, kan dit worden opgehaald uit de sectie sleutels van de Azure IoT Hub resource ![ para meters configureren](./media/spatial-analysis/solution-app-config-page.png)

Zodra deze twee instellingen zijn toegevoegd, klikt u op **Opslaan**. Klik vervolgens op **verificatie/autorisatie** in het navigatie menu links en werk het bij met het gewenste verificatie niveau. U kunt het beste Azure Active Directory (Azure AD) Express. 

### <a name="test-the-app"></a>De app testen

Ga naar de Azure-web-app en controleer of de implementatie is gelukt en of de web-app wordt uitgevoerd. Ga naar de geconfigureerde URL: `<yourapp>.azurewebsites.net` om de actieve app weer te geven.

![De implementatie testen](./media/spatial-analysis/solution-app-output.png)

## <a name="get-the-personcount-source-code"></a>De PersonCount-bron code ophalen
Als u de bron code voor deze toepassing wilt bekijken of wijzigen, kunt u deze vinden [op github](https://github.com/Azure-Samples/cognitive-services-spatial-analysis).

## <a name="next-steps"></a>Volgende stappen

* [Ruimtelijke-analyse bewerkingen configureren](./spatial-analysis-operations.md)
* [Logboekregistratie en problemen oplossen](spatial-analysis-logging.md)
* [Gids voor camera plaatsing](spatial-analysis-camera-placement.md)
* [Hand leiding voor zone-en lijn plaatsing](spatial-analysis-zone-line-placement.md)
