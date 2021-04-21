---
title: Implementeren op toegewezen host
description: Gebruik een toegewezen host om echte isolatie op hostniveau te bereiken voor uw Azure Container Instances workloads
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790829"
---
# <a name="deploy-on-dedicated-hosts"></a>Implementeren op toegewezen hosts

Dedicated is een SKU Azure Container Instances (ACI) die een geïsoleerde en toegewezen rekenomgeving biedt voor het veilig uitvoeren van containers. Als u de toegewezen SKU gebruikt, heeft elke containergroep een toegewezen fysieke server in een Azure-datacenter, waardoor volledige isolatie van workloads wordt gewaarborgd om te voldoen aan de beveiligings- en nalevingsvereisten van uw organisatie. 

De toegewezen SKU is geschikt voor containerworkloads waarvoor isolatie van workloads vanuit het perspectief van een fysieke server is vereist.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> Vanwege een aantal huidige beperkingen worden niet alle aanvragen voor het verhogen van limieten gegarandeerd goedgekeurd.

* De standaardlimiet voor elk abonnement dat de toegewezen SKU gebruikt, is 0. Als u deze SKU wilt gebruiken voor uw productiecontainerimplementaties, maakt u een Ondersteuning voor Azure [aanvraag om][azure-support] de limiet te verhogen.

## <a name="use-the-dedicated-sku"></a>De toegewezen SKU gebruiken

> [!IMPORTANT]
> Het gebruik van de toegewezen SKU is alleen beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt uitgerold. Geef deze API-versie op in uw implementatiesjabloon.
>

Vanaf API-versie 2019-12-01 is er een eigenschap onder de sectie Eigenschappen van containergroep van een implementatiesjabloon, die vereist is voor een `sku` ACI-implementatie. Op dit moment kunt u deze eigenschap gebruiken als onderdeel van een Azure Resource Manager implementatiesjabloon voor ACI. Meer informatie over het implementeren van [ACI-resources](./container-instances-multi-container-group.md)met een sjabloon in de Zelfstudie: Een groep met meerdere containers implementeren met behulp van een Resource Manager sjabloon . 

De `sku` eigenschap kan een van de volgende waarden hebben:
* `Standard` - de standaardkeuze voor ACI-implementatie, die nog steeds beveiliging op hypervisorniveau garandeert 
* `Dedicated` - wordt gebruikt voor isolatie op workloadniveau met toegewezen fysieke hosts voor de containergroep

## <a name="modify-your-json-deployment-template"></a>Uw JSON-implementatiesjabloon wijzigen

Wijzig of voeg in uw implementatiesjabloon de volgende eigenschappen toe:
* Stel `resources` onder in op `apiVersion` `2019-12-01` .
* Voeg onder de eigenschappen van de containergroep een `sku` eigenschap toe met de waarde `Dedicated` .

Hier is een voorbeeldfragment voor de sectie resources van een implementatiesjabloon voor een containergroep die gebruikmaakt van de toegewezen SKU:

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

Hieronder volgt een volledige sjabloon voor het implementeren van een voorbeeldcontainergroep met één container-exemplaar:

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

## <a name="deploy-your-container-group"></a>Uw containergroep implementeren

Als u het implementatiesjabloonbestand op uw bureaublad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell map door het bestand naar het bestand te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Een geslaagde implementatie vindt plaats op een toegewezen host.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
