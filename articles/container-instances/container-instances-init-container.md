---
title: Init-containers uitvoeren
description: Voer init-containers uit in Azure Container Instances om installatie taken uit te voeren in een container groep voordat de toepassings containers worden uitgevoerd.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 5a729263ee632eb9227694ec8684eb6889c6324b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85954278"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>Een init-container uitvoeren voor installatie taken in een container groep

Azure Container Instances ondersteunt *init-containers* in een container groep. Init-containers worden uitgevoerd tot voltooiing voordat de container of containers van de toepassing worden gestart. Net als bij [Kubernetes init-containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)gebruikt u een of meer init-containers om initialisatie logica voor uw app-containers uit te voeren, zoals het instellen van accounts, het uitvoeren van installatie scripts of het configureren van data bases.

In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon gebruikt om een container groep te configureren met een init-container.

## <a name="things-to-know"></a>Dingen die u moet weten

* **API-versie** -u hebt ten minste Azure container instances API-versie 2019-12-01 nodig voor het implementeren van init-containers. Implementeren met behulp `initContainers` van een eigenschap in een [yaml-bestand](container-instances-multi-container-yaml.md) of een [Resource Manager-sjabloon](container-instances-multi-container-group.md).
* **Volg orde van uitvoering** -init-containers worden uitgevoerd in de volg orde die is opgegeven in de sjabloon en vóór andere containers. Standaard kunt u een maximum van 59 init-containers per container groep opgeven. Ten minste één niet-init-container moet zich in de groep bevinden.
* **Host Environment** -init-containers worden uitgevoerd op dezelfde hardware als de rest van de containers in de groep.
* **Resources** : u geeft geen resources voor init-containers op. Ze krijgen het totale aantal resources, zoals Cpu's en geheugen, dat beschikbaar is voor de container groep. Terwijl een init-container wordt uitgevoerd, worden er geen andere containers uitgevoerd in de groep.
* **Ondersteunde eigenschappen** : init-containers kunnen groeps eigenschappen, zoals volumes, geheimen en beheerde identiteiten, gebruiken. Ze kunnen echter geen poorten of IP-adressen gebruiken als deze zijn geconfigureerd voor de container groep. 
* **Beleid voor opnieuw opstarten** : elke init-container moet zijn afgesloten voordat de volgende container in de groep wordt gestart. Als een init-container niet kan worden afgesloten, is de actie voor opnieuw opstarten afhankelijk van het [beleid voor opnieuw opstarten](container-instances-restart-policy.md) dat is geconfigureerd voor de groep:

    |Beleid in groep  |Beleid in init  |
    |---------|---------|
    |Altijd     |OnFailure         |
    |OnFailure     |OnFailure         |
    |Nooit     |Nooit         |
* **Kosten** : de container groep keert kosten in rekening van de eerste implementatie van een init-container.

## <a name="resource-manager-template-example"></a>Voor beeld van Resource Manager-sjabloon

Begin door de volgende JSON te kopiëren naar een nieuw bestand met de naam `azuredeploy.json` . Met de sjabloon wordt een container groep met één init-container en twee toepassings containers ingesteld:

* De *init1* -container voert de [busybox](https://hub.docker.com/_/busybox) -installatie kopie uit vanuit docker hub. De slaap stand duurt 60 seconden en schrijft vervolgens een opdracht regel teken reeks naar een bestand in een [emptyDir-volume](container-instances-volume-emptydir.md).
* In beide toepassings containers wordt de micro soft `aci-wordcount` container-installatie kopie uitgevoerd:
    * De *Hamlet* -container voert de WordCount-app uit in de standaard configuratie, waarbij het tellen van de woord frequenties in de Play- *Hamlet* van Shakespeare.
    * De *Juliet* -app-container leest de opdracht regel teken reeks van het emptDir-volume om de WordCount-app uit te voeren in plaats van de *Romeo en Juliet* van Shakespeare.

`aci-wordcount`Zie [omgevings variabelen instellen in container instanties](container-instances-environment-variables.md)voor meer informatie en voor beelden met behulp van de installatie kopie.

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

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

In een groep met een init-container wordt de implementatie tijd verhoogd vanwege de tijd die nodig is om de init-container of containers te volt ooien.


## <a name="view-container-logs"></a>Containerlogboeken ophalen

Als u wilt controleren of de init-container met succes is uitgevoerd, bekijkt u de logboek uitvoer van de app-containers met behulp van de opdracht [AZ container logs][az-container-logs] . Het `--container-name` argument geeft de container aan waaruit logboeken moeten worden opgehaald. In dit voor beeld haalt u de logboeken op voor de *Hamlet* -en *Juliet* -containers, waarin verschillende opdracht uitvoer wordt weer gegeven:

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

Init-containers helpen u bij het uitvoeren van installatie-en initialisatie taken voor uw toepassings containers. Zie voor meer informatie over het uitvoeren van taak containers [container taken uitvoeren met beleids regels voor opnieuw opstarten](container-instances-restart-policy.md).

Azure Container Instances biedt andere opties voor het wijzigen van het gedrag van toepassings containers. Enkele voorbeelden:

* [Omgevings variabelen instellen in container instanties](container-instances-environment-variables.md)
* [Stel de opdracht regel in een container exemplaar in om de standaard opdracht regel bewerking te overschrijven](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-container-logs]: /cli/azure/container#az-container-logs
