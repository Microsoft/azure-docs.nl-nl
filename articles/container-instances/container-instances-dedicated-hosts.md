---
title: Implementeren op specifieke host
description: Gebruik een specifieke host om te profiteren van de echte isolatie op hostniveau voor uw Azure Container Instances-workloads
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 68b9b31cdfb55e8150b05e3efd35389320905cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034268"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementeren op toegewezen hosts

' Dedicated ' is een Azure Container Instances-SKU (ACI) die een geïsoleerde en toegewezen reken omgeving biedt voor het veilig uitvoeren van containers. Het gebruik van de toegewezen SKU resulteert in elke container groep die een speciale fysieke server heeft in een Azure-Data Center, zodat de isolatie van de volledige werk belasting wordt gewaarborgd om te voldoen aan de beveiligings-en nalevings vereisten van uw organisatie. 

De toegewezen SKU is geschikt voor werkbelastingen van containers waarvoor isolatie van de werk belasting vereist is vanuit een oogpunt van een fysieke server.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> Vanwege enkele actuele beperkingen worden niet alle aanvragen voor het beperken van limieten gegarandeerd goedgekeurd.

* De standaard limiet voor elk abonnement voor het gebruik van de toegewezen SKU is 0. Als u deze SKU wilt gebruiken voor de implementaties van productie containers, maakt u een [Azure-ondersteuningsaanvraag][azure-support] om de limiet te verhogen.

## <a name="use-the-dedicated-sku"></a>De toegewezen SKU gebruiken

> [!IMPORTANT]
> Het gebruik van de toegewezen SKU is alleen beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt geïmplementeerd. Geef deze API-versie op in uw implementatie sjabloon.
>

Vanaf API-versie 2019-12-01 is er een `sku` eigenschap in de sectie eigenschappen van container groep van een implementatie sjabloon, die vereist is voor een ACI-implementatie. Op dit moment kunt u deze eigenschap gebruiken als onderdeel van een Azure Resource Manager implementatie sjabloon voor ACI. Meer informatie over het implementeren van ACI-resources met een sjabloon in de [zelf studie: een groep met meerdere containers implementeren met behulp van een resource manager-sjabloon](./container-instances-multi-container-group.md). 

De `sku` eigenschap kan een van de volgende waarden hebben:
* `Standard` -de standaard implementatie keuze van ACI, die nog steeds beveiliging op Hyper Visor niveau garandeert 
* `Dedicated` -wordt gebruikt voor isolatie van het werkbelasting niveau met toegewezen fysieke hosts voor de container groep

## <a name="modify-your-json-deployment-template"></a>De JSON-implementatie sjabloon wijzigen

Wijzig of Voeg in uw implementatie sjabloon de volgende eigenschappen toe:
* `resources`Stel onder in `apiVersion` op `2019-12-01` .
* Voeg onder de eigenschappen van de container groep een `sku` eigenschap met waarde toe `Dedicated` .

Hier volgt een voorbeeld fragment voor het gedeelte resources van een sjabloon voor container groep implementatie die gebruikmaakt van de toegewezen SKU:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Hieronder volgt een volledige sjabloon die een voor beeld-container groep implementeert waarop één container exemplaar wordt uitgevoerd:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Uw container groep implementeren

Als u het implementatie sjabloon bestand op uw bureau blad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell Directory door het bestand naar de map te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Een geslaagde implementatie vindt plaats op een specifieke host.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
