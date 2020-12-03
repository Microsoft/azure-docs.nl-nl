---
title: 'Zelfstudie: Een app implementeren in Azure Service Fabric Mesh'
description: In deze zelfstudie leert u hoe u een toepassing kunt implementeren in Service Fabric Mesh met behulp van een sjabloon.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 54ac7b27ada62a969dd40428fd9a753bb5a99530
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499829"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Zelfstudie: Een toepassing in Service Fabric Mesh implementeren met behulp van een sjabloon

Deze zelfstudie is deel één van een serie. U leert hou een Azure Service Fabric Mesh-toepassing kunt implementeren met behulp van een sjabloon.  De toepassing bestaat uit een ASP.NET-web-front-endservice en een ASP.NET Core Web API-back-endservice. Deze zijn te vinden in Docker Hub.  U haalt de twee containerinstallatiekopieën uit Docker Hub en pusht ze vervolgens naar uw eigen persoonlijke register. Vervolgens maakt u een Azure RM-sjabloon voor de toepassing en implementeert u de toepassing vanuit uw containerregister naar Service Fabric Mesh. Als u klaar bent, hebt u een eenvoudige takenlijsttoepassing die in Service Fabric Mesh wordt uitgevoerd.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een persoonlijke Azure Container Registry-instantie maken
> * Een containerinstallatiekopie naar het register pushen
> * RM-sjabloon en parameterbestanden maken
> * Een toepassing implementeren in Service Fabric Mesh

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * Een toepassing in Service Fabric Mesh implementeren met behulp van een sjabloon
> * [Services schalen in een toepassing die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Een toepassing upgraden die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Een app verwijderen](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* [Docker installeren](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Installeer de Azure CLI en Azure Service Fabric Mesh CLI lokaal](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Een containerregister maken

De containerinstallatiekopieën die aan de services in uw Service Fabric Mesh-toepassing zijn gekoppeld, moeten in een containerregister worden opgeslagen.  In deze zelfstudie wordt een persoonlijke Azure Container Registry-instantie (ACR) gebruikt. 

Gebruik de volgende stappen om een ACR-instantie te maken.  U kunt deze stap overslaan als u al een ACR-instantie hebt ingesteld.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure en stel het actieve abonnement in.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik de volgende opdracht om een resourcegroep met de naam *myResourceGroup* in de locatie *EastUS* te maken.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Het containerregister maken

Maak een ACR-exemplaar met behulp van de opdracht `az acr create`. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. In het volgende voorbeeld wordt de naam *myContainerRegistry* gebruikt. Kies een andere naam als u een fout ziet dat de registernaam wordt gebruikt.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Als het register is gemaakt, ziet u uitvoer die vergelijkbaar is met het volgende:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>De installatiekopieën naar Azure Container Registry pushen

In deze zelfstudie wordt de takenlijsttoepassing als voorbeeld gebruikt.  De containerinstallatiekopieën voor de [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/)- en [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/)-services zijn te vinden in Docker Hub. Raadpleeg [Build a Service Fabric Mesh web app (Een Service Fabric Mesh-webtoepassing bouwen)](service-fabric-mesh-tutorial-create-dotnetcore.md) voor informatie over het bouwen van de toepassing in Visual Studio. Service Fabric Mesh kan Windows- of Linux-containers in Docker uitvoeren.  Selecteer **Overschakelen naar Linux-containers** in Docker als u met Linux-containers werkt.  Selecteer **Overschakelen naar Windows-containers** in Docker als u met Windows-containers werkt.

U moet eerst een containerinstallatiekopie hebben om een installatiekopie naar een ACR-instantie te pushen. Gebruik de [docker pull](https://docs.docker.com/engine/reference/commandline/pull/)-opdracht om de [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/)- en [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/)-installatiekopieën uit Docker Hub op te halen als u nog geen lokale containerinstallatiekopieën.

>[!NOTE]
> Met ingang van 2 november 2020 zijn [snelheidslimieten voor downloaden van toepassing](https://docs.docker.com/docker-hub/download-rate-limit/) op anonieme en geverifieerde aanvragen bij Docker Hub, vanuit accounts van gratis Docker-abonnementen. Deze aanvragen worden afgedwongen op IP-adres. 
> 
> Deze opdrachten maken gebruik van openbare installatiekopieën van Docker Hub. Houd er rekening mee dat er limieten kunnen gelden. Zie [Verifiëren met Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub) voor meer informatie.

De Windows-installatiekopieën ophalen:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Voordat u een installatiekopie naar het register kunt pushen, moet u deze taggen met de volledige gekwalificeerde naam van de ACR-aanmeldingsserver.

Voer de volgende opdracht uit om de volledig naam van de aanmeldingsserver van uw ACR-instantie te verkrijgen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Label nu uw Docker-installatiekopie met de opdracht [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). In het volgende voorbeeld worden de installatiekopieën seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 en seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 gelabeld. Vervang de namen van de installatiekopieën in de volgende opdracht als u andere installatiekopieën gebruikt.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Meld u aan bij de Azure Container Registry.

```azurecli
az acr login -n myContainerRegistry
```

Push de installatiekopie naar de ACR-instantie met de opdracht [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Containerinstallatiekopieën opvragen

Voer de volgende opdracht uit om de opslagplaatsen in uw ACR-instantie te zien:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

In het volgende voorbeeld worden de tags in de **azure-mesh-todo-service**-opslagplaats vermeld.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

De voorgaande uitvoer bevestigt de aanwezigheid van `azure-mesh-todo-service:1.0-nanoserver-1709` in het persoonlijke containerregister.  Verifieer ook of `azure-mesh-todo-webfrontend:1.0-nanoserver-1709` aanwezig is.

## <a name="retrieve-credentials-for-the-registry"></a>Referenties voor het register ophalen

> [!IMPORTANT]
> Het is niet aan te bevelen om de gebruiker met beheerdersrechten in te schakelen voor een ACR-instantie voor productiescenario’s. Dit gebeurt hier gemakshalve. Gebruik voor productiescenario’s een [service-principal](../container-registry/container-registry-auth-service-principal.md) voor zowel gebruikers- als systeemverificatie.

Als u een containerinstantie wilt implementeren vanuit het register dat met behulp van een sjabloon werd gemaakt, moet u de referenties voor het register opgeven tijdens de implementatie. Schakel eerst de gebruiker met beheerdersrechten voor uw register in met behulp van de volgende opdracht:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Haal vervolgens de servernaam, gebruikersnaam en het wachtwoord voor aanmelden bij het register op met behulp van de volgende opdrachten:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Gebruik de geretourneerde naam-, gebruikersnaam- en wachtwoordwaarden voor de ACR-aanmeldingsserver als u de RM-sjabloon en parameterbestanden in het volgende gedeelte maakt.

## <a name="download-and-explore-the-template-and-parameters-files"></a>De sjabloon- en parameterbestanden downloaden en verkennen

Een Service Fabric Mesh-toepassing is een Azure-resource die u met behulp van Azure Resource Manager-sjablonen (RM) kunt implementeren en beheren. Raadpleeg [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) en [De structuur en syntaxis van RM-sjablonen begrijpen](../azure-resource-manager/templates/template-syntax.md) als u de concepten van implementatie en beheer van uw Azure-oplossingen niet kent.

In deze zelfstudie wordt de takenlijst als voorbeeld gebruikt.  Download de bestanden [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) en [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) zodat u geen nieuwe sjabloon- en parameterbestanden hoeft te bouwen.

### <a name="parameters"></a>Parameters
Wanneer de sjabloon waarden bevat die naar verwachting zullen wijzigen nadat de toepassing is geïmplementeerd, of als u de mogelijkheid wilt hebben om de waarden per implementatie aan te passen (als u van plan bent om deze sjabloon te hergebruiken voor andere implementaties), is de aanbevolen procedure om de waarden door te geven via parameters. De juiste manier om dit te doen, is door een gedeelte ‘parameters’ aan de bovenkant van uw implementatiesjabloon te maken, waarin u parameternamen en -eigenschappen opgeeft waarnaar later in de implementatiesjabloon wordt verwezen. Elke parameterdefinitie bevat *type*, *standaardwaarde* en een optioneel gedeelte *metagegevens* met een *beschrijving*.

Het parametergedeelte is aan de bovenkant van uw implementatiesjabloon gedefinieerd, direct voor het gedeelte *resources*:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

In de [mesh_rp.windows.json-implementatiesjabloon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) zijn de volgende parameters gedeclareerd: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Beschrijvingen voor elke parameter zijn te vinden in het implementatiesjabloonbestand.

Deze parameters worden gebruikt in het bestand [mesh_rp.windows.parameter.json-parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  Als u een afzonderlijk parameterbestand gebruikt, biedt dat de mogelijkheid om de parameterwaarden per implementatie te wijzigen zonder de implementatiesjabloon zelf bij te werken.

### <a name="overview-of-the-application-and-services"></a>Overzicht van de toepassing en services

Services worden in de sjabloon gespecificeerd als eigenschappen van de toepassingsresource.  Toepassing worden geïmplementeerd in een privénetwerk, die als een resource in de sjabloon is gedeclareerd.  Services kunnen volumes gebruiken om gegevens vast te leggen, die als resources in de sjabloon zijn gedeclareerd.  Voor elke service zijn het type besturingssysteem, codepakketten, aantal replica’s en het netwerk als eigenschappen van de service gespecificeerd.  Geef voor elk codepakket het containerinstallatiekopie, eindpunten, geheugen en CPU-resources en referenties voor de installatiekopie-opslagplaats op. De sjabloon voor een Service Fabric Mesh-toepassing met meerdere services ziet er op hoofdlijn als volgt uit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Raadpleeg het [mesh_rp.windows.json-implementatiesjabloonbestand](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) voor specifieke kenmerken van de takenlijsttoepassing.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>De toepassing implementeren in Service Fabric Mesh
Maak de toepassing en gerelateerde resources met behulp van de volgende opdracht en geef de referenties uit de vorige stap [Referenties voor het register ophalen](#retrieve-credentials-for-the-registry) op.

Werk in het parameterbestand de volgende parameterwaarden bij:

|Parameter|Waarde|
|---|---|
|location|De regio waarin de toepassing moet worden geïmplementeerd.  Bijvoorbeeld: ‘eastus’.|
|registryPassword|Het wachtwoord dat u eerder hebt opgehaald in [Referenties voor het register ophalen](#retrieve-credentials-for-the-registry). Deze parameter in de sjabloon is een beveiligde tekenreeks en zal niet in de implementatiestatus of `az mesh service show`-opdrachten worden weergegeven.|
|registryUserName|De gebruikersnaam die u hebt opgehaald in [Referenties voor het register ophalen](#retrieve-credentials-for-the-registry).|
|registryServer|De naam van de registerserver die u hebt opgehaald in [Referenties voor het register ophalen](#retrieve-credentials-for-the-registry).|
|frontEndImage|Het containerinstallatiekopie voor de front-endservice.  Bijvoorbeeld `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|Het containerinstallatiekopie voor de back-endservice.  Bijvoorbeeld `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Voer de volgende opdracht uit om de toepassing te implementeren:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Deze opdracht genereert een JSON-fragment dat hieronder wordt weergegeven. Kopieer de ```publicIPAddress```-eigenschap in de sectie ```outputs``` van de JSON-uitvoer.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Deze informatie wordt opgehaald uit de sectie ```outputs``` in de ARM-sjabloon. Zoals hieronder wordt weergegeven, verwijst deze sectie naar de Gateway-resource om het openbare IP-adres op te halen. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>De toepassing openen

Haal het openbare IP-adres voor het service-eindpunt op zodra de toepassing is geïmplementeerd. De implementatieopdracht retourneert het openbare IP-adres van het service-eindpunt. Eventueel kunt u ook de netwerkbron opvragen om het openbare IP-adres van het service-eindpunt te vinden. De netwerkbronnaam voor deze toepassing is `todolistappNetwork`. Haal informatie hierover op met behulp van de volgende opdracht. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Navigeer naar het IP-adres in een webbrowser.

## <a name="check-application-status"></a>De toepassingsstatus controleren

U kunt de status van de toepassing controleren met de opdracht voor het weergeven van de app. De toepassingsnaam voor de geïmplementeerde toepassing is ‘todolistapp’; haal daarom de gegevens op.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Bekijk de logboeken voor de geïmplementeerde toepassing via de opdracht `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een privécontainerregister maken
> * Een containerinstallatiekopie naar het register pushen
> * Een RM-sjabloon en parameterbestand maken
> * Een toepassing implementeren in Service Fabric Mesh

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een toepassing schalen die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)