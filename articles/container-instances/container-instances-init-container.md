---
title: Init-containers uitvoeren
description: Voer init-containers uit in Azure Container Instances om installatietaken uit te voeren in een containergroep voordat de toepassingscontainers worden uitgevoerd.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 9ccaf1a67d6ca3bcff422acb591b528cc72a9608
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763933"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Een init-container uitvoeren voor installatietaken in een containergroep

Azure Container Instances ondersteunt *init-containers* in een containergroep. Init-containers worden uitgevoerd tot voltooiing voordat de toepassingscontainer of -containers worden starten. Net als [bij Kubernetes init-containers,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)gebruikt u een of meer init-containers om initialisatielogica uit te voeren voor uw app-containers, zoals het instellen van accounts, het uitvoeren van installatiescripts of het configureren van databases.

In dit artikel wordt beschreven hoe u een Azure Resource Manager gebruikt om een containergroep te configureren met een init-container.

## <a name="things-to-know"></a>Dingen die u moet weten

* **API-versie:** u hebt ten minste Azure Container Instances API-versie 2019-12-01 nodig om init-containers te implementeren. Implementeer met behulp `initContainers` van een eigenschap in een [YAML-bestand](container-instances-multi-container-yaml.md) of een [Resource Manager sjabloon](container-instances-multi-container-group.md).
* **Volgorde van uitvoering:** Init-containers worden uitgevoerd in de volgorde die is opgegeven in de sjabloon en vóór andere containers. Standaard kunt u maximaal 59 init-containers per containergroep opgeven. Er moet ten minste één niet-init-container in de groep zijn.
* **Hostomgeving:** Init-containers worden uitgevoerd op dezelfde hardware als de rest van de containers in de groep.
* **Resources:** u geeft geen resources op voor init-containers. Aan hen worden de totale resources toegekend, zoals CPU's en geheugen die beschikbaar zijn voor de containergroep. Terwijl een init-container wordt uitgevoerd, worden er geen andere containers uitgevoerd in de groep.
* **Ondersteunde eigenschappen:** Init-containers kunnen groepseigenschappen zoals volumes, geheimen en beheerde identiteiten gebruiken. Ze kunnen echter geen poorten of een IP-adres gebruiken als deze zijn geconfigureerd voor de containergroep. 
* **Beleid voor opnieuw** opstarten: elke init-container moet worden afgesloten voordat de volgende container in de groep wordt gestart. Als een init-container niet wordt afgesloten, is de herstartactie afhankelijk van het beleid voor opnieuw opstarten dat [is](container-instances-restart-policy.md) geconfigureerd voor de groep:

    |Beleid in groep  |Policy in init  |
    |---------|---------|
    |Altijd     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Nooit     |Nooit         |
* **Kosten:** voor de containergroep worden kosten in rekening gebracht vanaf de eerste implementatie van een init-container.

## <a name="resource-manager-template-example"></a>Resource Manager sjabloonvoorbeeld

Begin met het kopiëren van de volgende JSON naar een nieuw bestand met de naam `azuredeploy.json` . Met de sjabloon stelt u een containergroep in met één init-container en twee toepassingscontainers:

* Met *de container init1* wordt de [afbeelding busybox](https://hub.docker.com/_/busybox) uitgevoerd vanuit Docker Hub. Deze blijft 60 seconden in de slaapstand en schrijft vervolgens een opdrachtregelreeks naar een bestand in een [leegDir-volume](container-instances-volume-emptydir.md).
* Beide toepassingscontainers voeren de `aci-wordcount` Microsoft-containerafbeelding uit:
    * De *container wordcount* voert de wordcount-app uit in de standaardconfiguratie, waarmee woordfrequenties worden geteld in het spel Van Shakespeare van *Shakespeare.*
    * In *de container van* de app wordcount wordt de opdrachtregelreeks uit het emptDir-volume gelezen om in plaats daarvan de wordcount-app uit te voeren op Deen van Shakespeare en *Moetent*.

Zie Omgevingsvariabelen instellen `aci-wordcount` [in container-exemplaren](container-instances-environment-variables.md)voor meer informatie en voorbeelden van het gebruik van de afbeelding.

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
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "busybox",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create]

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

In een groep met een init-container wordt de implementatietijd verhoogd vanwege de tijd die het kost om de init-container of containers te voltooien.


## <a name="view-container-logs"></a>Containerlogboeken ophalen

Als u wilt controleren of de init-container is uitgevoerd, bekijkt u de logboekuitvoer van de app-containers met behulp van [de opdracht az container logs.][az-container-logs] Met `--container-name` het argument geeft u de container op waaruit logboeken moeten worden op halen. In dit voorbeeld haalt u de logboeken op voor *de containers voor het* maken van een container voor het maken van een *container, die* verschillende opdrachtuitvoer tonen:

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

Uitvoer:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

Uitvoer:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Volgende stappen

Init-containers helpen u bij het uitvoeren van installatie- en initialisatietaken voor uw toepassingscontainers. Zie Taken in containers uitvoeren met beleid voor opnieuw opstarten voor meer informatie over het uitvoeren van op taken gebaseerde [containers.](container-instances-restart-policy.md)

Azure Container Instances biedt andere opties om het gedrag van toepassingscontainers te wijzigen. Enkele voorbeelden:

* [Omgevingsvariabelen instellen in container-exemplaren](container-instances-environment-variables.md)
* [Stel de opdrachtregel in een containerin instance in om de standaardopdrachtregelbewerking te overschrijven](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs
