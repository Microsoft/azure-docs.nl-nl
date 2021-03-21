---
title: 'Zelfstudie: een app schalen die wordt uitgevoerd in Azure Service Fabric Mesh'
description: In deze zelfstudie leert u hoe u de services kunt schalen in een toepassing die wordt uitgevoerd in Service Fabric Mesh.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625380"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Zelfstudie: Een toepassing schalen die wordt uitgevoerd in Service Fabric Mesh

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Deze zelfstudie is deel twee van een serie. Leer hier hoe u handmatig het aantal service-instanties schaalt van een toepassing die [eerder is geïmplementeerd naar Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). Wanneer u klaar bent, hebt u een front-end service met drie instanties en een gegevensservice met twee instanties.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Het gewenste aantal instanties van de service configureren
> * Een upgrade uitvoeren

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een toepassing in Service Fabric Mesh implementeren met behulp van een sjabloon](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Een toepassing schalen die wordt uitgevoerd in Service Fabric Mesh
> * [Een toepassing upgraden die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Een app verwijderen](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u nog geen abonnement op Azure hebt, kunt u een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* [Installeer de Azure CLI en Azure Service Fabric Mesh CLI lokaal](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Services handmatig in- en uitschalen

Een van de belangrijkste voordelen van het implementeren van toepassingen naar Service Fabric Mesh is de mogelijkheid voor u om services eenvoudig in of uit te schalen. Dit is handig voor het afhandelen van wisselende belastingen van uw services of het verbeteren van de beschikbaarheid.

In deze zelfstudie wordt het voorbeeld To Do List gebruikt als voorbeeld. Dit voorbeeld is [eerder geïmplementeerd](service-fabric-mesh-tutorial-template-deploy-app.md) en moet nu worden uitgevoerd. De toepassing heeft twee services: WebFrontEnd en ToDoService. Elke service is in eerste instantie geïmplementeerd met de waarde 1 voor het aantal replica's.  Als u het aantal actieve replica's voor de service WebFrontEnd wilt zien, voert u deze opdracht uit:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Als u het aantal actieve replica's voor de service ToDoService wilt zien, voert u deze opdracht uit:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

In de implementatiesjabloon voor de toepassingsresource heeft elke service een eigenschap *replicaCount* die kan worden gebruikt om in te stellen hoe vaak die service wordt geïmplementeerd. Een toepassing kan bestaan uit meerdere services, waarbij elke service een unieke waarde voor *replicaCount* heeft, die samen worden geïmplementeerd en beheerd. Om het aantal service-replica's te schalen, wijzigt u de waarde *replicaCount* voor elke service die u wilt schalen in de implementatiesjabloon of het parametersbestand.  Vervolgens voert u een upgrade van de toepassing uit.

### <a name="modify-the-deployment-template-parameters"></a>Parameters van implementatiesjabloon wijzigen

Wanneer de sjabloon waarden bevat die naar verwachting zullen wijzigen nadat de toepassing is geïmplementeerd, of als u de mogelijkheid wilt hebben om de waarden per implementatie aan te passen (als u van plan bent om deze sjabloon te hergebruiken voor andere implementaties), is de aanbevolen procedure om de waarden door te geven via parameters.

Eerder werd de toepassing geïmplementeerd via de bestanden [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) en [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Open het bestand [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) lokaal en stel *frontEndReplicaCount* in op 3 en *serviceReplicaCount* op 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Sla de wijzigingen in het parameterbestand op.  De parameters *frontEndReplicaCount* en *serviceReplicaCount* worden gedeclareerd in de sectie *parameters* van het bestand [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

De eigenschap *replicaCount* van de WebFrontEnd-service verwijst naar de parameter *frontEndReplicaCount* en de eigenschap *replicaCount* van de ToDoService-service naar de parameter *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Als de sjabloon is gewijzigd, moet u de toepassing upgraden.

### <a name="upgrade-your-application"></a>Uw toepassing upgraden

Terwijl de toepassing wordt uitgevoerd, kunt u deze bijwerken door de sjabloon en het bijgewerkte parameterbestand opnieuw te implementeren:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Hiermee start u een rolling upgrade van uw toepassing en als het goed is, ziet u binnen enkele minuten een toename van het aantal service-instanties.  Als u het aantal actieve replica's voor de service WebFrontEnd wilt zien, voert u deze opdracht uit:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Als u het aantal actieve replica's voor de service ToDoService wilt zien, voert u deze opdracht uit:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Het gewenste aantal instanties van de service configureren
> * Een upgrade uitvoeren

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Een toepassing upgraden die wordt uitgevoerd in Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
