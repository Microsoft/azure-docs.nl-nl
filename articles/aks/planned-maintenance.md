---
title: Gepland onderhoud gebruiken voor uw Azure Kubernetes service-cluster (AKS) (preview)
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van gepland onderhoud in azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 8526d7c1c436074fbf6f838caf232e1abee06339
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670372"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Gepland onderhoud gebruiken om onderhouds Vensters te plannen voor uw Azure Kubernetes service (AKS)-cluster (preview)

Er wordt een regel matig onderhoud uitgevoerd op uw AKS-cluster. Dit werk kan standaard op elk gewenst moment plaatsvinden. Met gepland onderhoud kunt u wekelijkse onderhouds Vensters plannen waarmee uw besturings vlak wordt bijgewerkt, evenals uw uitvoeren-systeem op een VMSS-exemplaar en de gevolgen voor de werk belasting tot een minimum worden beperkt. Eenmaal gepland, wordt al uw onderhoud uitgevoerd tijdens het venster dat u hebt geselecteerd. U kunt een of meer wekelijkse Vensters op het cluster plannen door een dag-of tijds bereik op een specifieke dag op te geven. Onderhouds Vensters worden geconfigureerd met behulp van de Azure CLI.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Beperkingen

Bij het gebruik van gepland onderhoud gelden de volgende beperkingen:

- AKS behoudt zich het recht voor om deze vensters te verstoren voor ongeplande of heractieve onderhouds bewerkingen die urgent of kritiek zijn.
- Momenteel wordt het uitvoeren van onderhouds bewerkingen alleen beschouwd als *Best effort* en niet gegarandeerd binnen een opgegeven venster.
- Updates kunnen Maxi maal zeven dagen worden geblokkeerd.

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

U hebt ook de *AKS-preview* Azure cli-extensie versie 0.5.4 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] . Of installeer alle beschik bare updates met behulp van de opdracht [AZ extension update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Onderhoud op elke maandag op 1 toestaan: am tot 2: am

U kunt de opdracht gebruiken om een onderhouds venster toe te voegen `az aks maintenanceconfiguration add` .

> [!IMPORTANT]
> Geplande onderhouds Vensters worden opgegeven in Coordinated Universal Time (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

In de volgende voorbeeld uitvoer ziet u het onderhouds venster van 1: am tot 2: am elke maandag.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Als u het onderhoud op elk moment wilt toestaan gedurende een dag, laat u de para meter *begin-Hour* weg. Met de volgende opdracht wordt bijvoorbeeld elke maandag het onderhouds venster voor de hele dag ingesteld:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Een onderhouds configuratie met een JSON-bestand toevoegen

U kunt ook een JSON-bestand gebruiken om een onderhouds venster te maken in plaats van para meters te gebruiken. Maak een `test.json` bestand met de volgende inhoud:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

In het bovenstaande JSON-bestand wordt elke dinsdag op 1 een onderhouds venster opgegeven: am-3: am en elke woensdag om 1: am-2: am en 6: am-7: am. Er is ook een uitzonde ring van *2021-05-26T03:00:00Z* naar *2021-05-30T12:00:00Z* waarbij onderhoud niet is toegestaan, zelfs niet als het overlapt met een onderhouds venster. Met de volgende opdracht wordt de onderhouds Vensters van toegevoegd `test.json` .

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Een bestaand onderhouds venster bijwerken

Als u een bestaande onderhouds configuratie wilt bijwerken, gebruikt u de `az aks maintenanceconfiguration update` opdracht.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Alle onderhouds Vensters in een bestaand cluster weer geven

Gebruik de opdracht om alle huidige onderhouds configuratie Vensters in uw AKS-cluster weer te geven `az aks maintenanceconfiguration list` .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

In de onderstaande uitvoer ziet u dat er twee onderhouds Vensters zijn geconfigureerd voor myAKSCluster. Eén venster bevindt zich op maandag om 1: am en een ander venster op vrijdag om 4: am.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Een specifiek onderhouds configuratie venster weer geven in een AKS-cluster

Gebruik de opdracht om een specifiek onderhouds configuratie venster in uw AKS-cluster weer te geven `az aks maintenanceconfiguration show` .

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

In de volgende voorbeeld uitvoer ziet u het onderhouds venster *standaard*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Een bepaald onderhouds configuratie venster in een bestaand AKS-cluster verwijderen

Als u een bepaald onderhouds configuratie venster in uw AKS-cluster wilt verwijderen, gebruikt u de `az aks maintenanceconfiguration delete` opdracht.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Volgende stappen

- Zie [een AKS-cluster upgraden][aks-upgrade] om aan de slag te gaan met het upgraden van uw AKS-cluster


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[aks-upgrade]: upgrade-cluster.md
