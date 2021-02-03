---
title: Live video Analytics implementeren op een IoT Edge apparaat-Azure
description: In dit artikel worden de stappen beschreven die u helpen bij het implementeren van live video Analytics op uw IoT Edge-apparaat. U kunt dit bijvoorbeeld doen als u toegang hebt tot een lokale Linux-machine en/of eerder een Azure Media Services-account hebt gemaakt.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: ff5dbc8e643137008aa7819b455adcf97c05bfc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491787"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Live video Analytics implementeren op een IoT Edge apparaat

In dit artikel worden de stappen beschreven die u helpen bij het implementeren van live video Analytics op uw IoT Edge-apparaat. U kunt dit bijvoorbeeld doen als u toegang hebt tot een lokale Linux-machine en/of eerder een Azure Media Services-account hebt gemaakt.

> [!NOTE]
> Ondersteuning voor ARM64-apparaten is beschikbaar in live video Analytics op IoT Edge builds `1.0.4` en nieuwer.
> Ondersteuning voor de uitvoering van Azure IoT Edge-runtime op ARM64-apparaten vindt u in [openbare preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

* Een x86-64-of een ARM64-apparaat met een van de [ondersteunde Linux-besturings systemen](../../iot-edge/support.md#operating-systems)
* Azure-abonnement waarvoor u [eigenaars bevoegdheden](../../role-based-access-control/built-in-roles.md#owner) hebt
* [IoT Hub maken en instellen](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge-apparaat registreren](../../iot-edge/how-to-manual-provision-symmetric-key.md)
* [Installeer de Azure IoT Edge runtime op op Debian gebaseerde Linux-systemen](../../iot-edge/how-to-install-iot-edge.md)
* [Een Azure Media Services-account maken](../latest/create-account-howto.md)

    * Gebruik een van deze regio's: VS-Oost 2, VS-West, VS-midden, VS-West, Japan-Oost, VS-West, VS-West 2, westelijk-Centraal VS, Canada-oost, UK-zuid, Frankrijk-centraal, Frankrijk-zuid, Zwitserland-noord, Zwitserland-west en Japan-West.
    * U wordt aangeraden v2-opslag accounts voor algemeen gebruik (GPv2) te gebruiken

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Azure-resources configureren voor het gebruik van live video Analytics

### <a name="create-custom-azure-resource-manager-role"></a>Aangepaste Azure Resource Manager-rol maken

Zie [aangepaste Azure Resource Manager rol maken](create-custom-azure-resource-manager-role-how-to.md) en deze toewijzen aan een service-principal voor gebruik in live video-analyses.

### <a name="set-up-a-premium-streaming-endpoint"></a>Een Premium-streaming-eind punt instellen

Als u live video Analytics wilt gebruiken om video continu in de Cloud op te nemen en vervolgens [query-api's](playback-recordings-how-to.md#query-api) te gebruiken voordat u deze weer afspeelt, wordt u aangeraden uw media service bij te werken voor het gebruik van een Premium- [streaming-eind punt](../latest/streaming-endpoint-concept.md#types).  

Dit is een optionele taak. U kunt hiervoor de volgende Azure CLI-opdracht gebruiken:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

U kunt deze opdracht gebruiken om het streaming-eind punt te starten 

> [!IMPORTANT]
> Uw abonnement wordt op dit moment gefactureerd.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

Volg de stappen in dit artikel om referenties op te halen voor toegang tot de media service-Api's: [Open de media service-api's](../latest/access-api-howto.md?tabs=portal) en selecteer het tabblad Portal.

## <a name="create-and-use-local-user-account-for-deployment"></a>Een lokaal gebruikers account maken en gebruiken voor implementatie
Als u de live video Analytics op IoT Edge module wilt uitvoeren, maakt u een lokale gebruikers account met zo min mogelijk bevoegdheden. Voer bijvoorbeeld de volgende opdrachten uit op uw Linux-computer:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Machtigingen verlenen voor opslag van apparaten

Nu u een lokaal gebruikers account hebt gemaakt, 

* U hebt een lokale map nodig om de configuratie gegevens van de toepassing op te slaan. Maak een map en verleen machtigingen voor het lokalegebruiker-account naar die map met behulp van de volgende opdrachten:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* U hebt ook een map nodig om [Video's op te nemen in een lokaal bestand](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Gebruik de volgende opdrachten om een lokale map voor hetzelfde te maken:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Live video Analytics Edge-module implementeren

Met de live video Analytics op IoT Edge worden de eigenschappen van de module dubbel weer gegeven die zijn gedocumenteerd in het [Configuratie schema](module-twin-configuration-schema.md)van de module. 

### <a name="deploy-using-the-azure-portal"></a>Implementeren met behulp van de Azure-portal

De Azure Portal begeleidt u bij het maken van een implementatie manifest en het pushen van de implementatie naar een IoT Edge-apparaat.  
#### <a name="select-your-device-and-set-modules"></a>Uw apparaat selecteren en modules instellen

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/) en ga naar uw IoT Hub.
1. Selecteer **IOT Edge** in het menu.
1. Klik op de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer **modules instellen**.

#### <a name="configure-a-deployment-manifest"></a>Een implementatie manifest configureren

Een implementatie manifest is een JSON-document waarin wordt beschreven welke modules moeten worden geïmplementeerd, hoe gegevens stromen tussen de modules en gewenste eigenschappen van de module apparaatdubbels. De Azure Portal heeft een wizard die u helpt bij het maken van een implementatie manifest. Het bevat drie stappen die zijn georganiseerd in tabbladen: **modules**, **routes** en **revisie + maken**.

#### <a name="add-modules"></a>Modules toevoegen

1. Klik in de sectie **IOT Edge modules** van de pagina op de vervolg keuzelijst **toevoegen** en selecteer **IoT Edge module** om de pagina **IOT Edge module toevoegen** weer te geven.
1. Geef op het tabblad **module-instellingen** een naam op voor de module en geef vervolgens de URI van de container installatie kopie op:   
    Voorbeelden:
    
    * **Module naam IOT Edge**: lvaEdge
    * **Afbeeldings-URI**: mcr.Microsoft.com/Media/Live-video-Analytics:2.0    
    
    ![Scherm afbeelding toont het tabblad module-instellingen.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Selecteer **toevoegen** totdat u waarden hebt opgegeven op de tabbladen **module-instellingen**, **container maken** en **module dubbele instellingen** , zoals beschreven in deze procedure.
    
    > [!WARNING]
    > Azure IoT Edge is hoofdletter gevoelig wanneer u aanroepen naar modules uitvoert. Noteer de exacte teken reeks die u gebruikt als de module naam.

1. Open het tabblad **omgevings variabelen** .
   
   Voeg de volgende waarden toe aan de invoer vakken die u ![ omgevings variabelen ziet](./media/deploy-iot-edge-device/environment-variables.png) 

1. Open het tabblad **Opties voor container maken** .

    ![Opties voor het maken van containers](./media/deploy-iot-edge-device/container-create-options.png)
 
    Kopieer en plak de volgende JSON in het vak om de grootte van de logboek bestanden te beperken die door de module worden geproduceerd.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   De sectie ' bindingen ' in de JSON heeft 2 vermeldingen:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": dit wordt gebruikt om de permanente toepassings configuratie gegevens te binden uit de container en op te slaan op het apparaat van de rand.
   1. "/var/media:/var/media": Hiermee worden de media mappen tussen het edge-apparaat en de container gebonden. Dit wordt gebruikt voor het opslaan van de video-opnames wanneer u een media grafiek topologie uitvoert die ondersteuning biedt voor het opslaan van video clips op het edge-apparaat.
   
1. Kopieer de volgende JSON op het tabblad **module dubbele instellingen** en plak deze in het vak.
 
    ![Dubbele instellingen](./media/deploy-iot-edge-device/twin-settings.png)

    Voor het uitvoeren van live video Analytics op IoT Edge is een set verplichte twee eigenschappen vereist om te kunnen worden uitgevoerd, zoals vermeld in het [Configuratie schema](module-twin-configuration-schema.md)van de module.  

    De JSON die u moet invoeren in het invoervak voor de module dubbele instellingen ziet er als volgt uit:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Dit zijn de **vereiste** eigenschappen en voor de bovenstaande JSON,  
    * {subscriptionID}: dit is de ID van uw Azure-abonnement
    * {resourceGroupName}: dit is de resource groep waartoe uw media service-account behoort
    * {AMS-account-name}-dit is de naam van uw Media Services-account
    
    Als u de andere waarden wilt ophalen, raadpleegt u [Access Azure Media Services API](../latest/access-api-howto.md?tabs=portal) en selecteert u het tabblad Portal.  
    * aadTenantId: dit is de ID van uw Tenant en is hetzelfde als de AadTenantId van de bovenstaande koppeling.
    * aadServicePrincipalAppId: dit is de App-ID van de service-principal voor uw media service-account en is hetzelfde als de AadClientId van de bovenstaande koppeling.
    * aadServicePrincipalSecret: dit is het wacht woord van de Service-Principal en is hetzelfde als de AadSecret van de bovenstaande koppeling.

    Hieronder vindt u enkele aanvullende **Aanbevolen** eigenschappen die kunnen worden toegevoegd aan de JSON en helpt bij het bewaken van de module. Zie [controle en logboek registratie](monitoring-logging.md)voor meer informatie:
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    Hier volgen enkele **optionele** eigenschappen die u in de JSON kunt toevoegen:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > De dubbele eigenschap **allowUnsecuredEndpoints** is ingesteld op True voor het doel van de zelf studies en de Quick starts.   
   U moet deze eigenschap instellen op **Onwaar** als deze wordt uitgevoerd in de productie omgeving. Dit zorgt ervoor dat alle onbeveiligde eind punten worden geblokkeerd door de toepassing en dat de grafiek topologieën moeten worden uitgevoerd als er geldige verbindings referenties nodig zijn.  
   
    Selecteer toevoegen om de module dubbele eigenschappen toe te voegen.
1. Selecteer **volgende: routes** om door te gaan naar de sectie routes.
    Routes opgeven.

Behoud de standaard routes en selecteer **volgende: controleren + maken** om door te gaan naar de sectie beoordeling.

#### <a name="review-deployment"></a>Implementatie controleren

In het gedeelte beoordeling ziet u het JSON-implementatie manifest dat is gemaakt op basis van uw selecties in de vorige twee secties. Er zijn ook twee modules gedeclareerd die u niet hebt toegevoegd: $edgeAgent en $edgeHub. Deze twee modules vormen de IoT Edge runtime en zijn de standaard instellingen voor elke implementatie.

Controleer uw implementatie gegevens en selecteer vervolgens maken.

### <a name="verify-your-deployment"></a>Uw implementatie verifiëren

Nadat u de implementatie hebt gemaakt, keert u terug naar de pagina IoT Edge van uw IoT-hub.

1. Selecteer het IoT Edge apparaat waarop de implementatie is gericht om de details ervan te openen.
2. Controleer in de details van het apparaat of de module Blob Storage wordt vermeld als **opgegeven in de implementatie en wordt gerapporteerd door het apparaat**.

Het kan even duren voordat de module op het apparaat is gestart en vervolgens weer aan IoT Hub is gemeld. Vernieuw de pagina om de bijgewerkte status weer te geven.
Status code: 200 – OK betekent dat [de runtime van IOT Edge](../../iot-edge/iot-edge-runtime.md) in orde is en goed werkt.

![Scherm afbeelding toont een status waarde voor een IoT Edge runtime.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Een directe methode aanroepen

Vervolgens kunt u het voor beeld testen door een directe methode aan te roepen. Lees [directe methoden voor live video Analytics op IOT Edge](direct-methods.md) om inzicht te krijgen in alle directe methoden van onze lvaEdge-module.

1. Als u op de door u gemaakte Edge-module klikt, gaat u naar de pagina met de configuratie.  

    ![Scherm afbeelding toont de configuratie pagina van een Edge-module.](./media/deploy-iot-edge-device/modules.png)
1. Klik op de menu optie directe methode.

    > [!NOTE] 
    > U moet een waarde toevoegen in de secties van de verbindings reeks, zoals u kunt zien op de huidige pagina. U hoeft niets te verbergen of te wijzigen in de sectie **instellings naam** . U kunt dit openbaar houden.

    ![Directe methode](./media/deploy-iot-edge-device/module-details.png)
1. Voer vervolgens ' GraphTopologyList ' in het vak methode naam in.
1. Kopieer en plak de volgende JSON-nettolading in het vak payload.
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. Klik boven aan de pagina op de optie methode aanroepen

    ![Directe methoden](./media/deploy-iot-edge-device/direct-method.png)
1. U ziet een status bericht van 200 in het vak resultaat

    ![De status 200 bericht](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Volgende stappen

Probeer [Quick Start: aan de slag-live video Analytics op IOT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Als u doorgaat met de bovenstaande Snelstartgids, gebruikt u het apparaat dat is toegevoegd aan de IoT Hub via dit artikel, in plaats van de standaard instelling, wanneer u de directe methoden aanroept met behulp van Visual Studio code `lva-sample-device` .
