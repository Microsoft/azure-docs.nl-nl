---
title: Gepland onderhoud gebruiken voor uw AKS Azure Kubernetes Service cluster (preview)
titleSuffix: Azure Kubernetes Service
description: Informatie over het gebruik van gepland onderhoud in Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: f1e0822e77d8466b1b9796041fbdba53c3f9c91f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782905"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Gepland onderhoud gebruiken om onderhoudsvensters te plannen voor Azure Kubernetes Service AKS-cluster (preview)

Op uw AKS-cluster wordt regelmatig onderhoud automatisch uitgevoerd. Dit werk kan standaard op elk moment plaatsvinden. Met gepland onderhoud kunt u wekelijkse onderhoudsvensters plannen waarmee uw besturingsvlak en kube-system Pods op een VMSS-exemplaar worden bijgewerkt en de impact op de werkbelasting wordt geminimaliseerd. Zodra dit is gepland, wordt al uw onderhoud uitgevoerd tijdens het venster dat u hebt geselecteerd. U kunt een of meer wekelijkse vensters op uw cluster plannen door een dag of tijdsbereik op een specifieke dag op te geven. Onderhoudsvensters worden geconfigureerd met behulp van de Azure CLI.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Beperkingen

Wanneer u gepland onderhoud gebruikt, gelden de volgende beperkingen:

- AKS behoudt zich het recht voor om deze vensters te breken voor niet-geplande/reactieve onderhoudsbewerkingen die urgent of kritiek zijn.
- Op dit moment wordt het uitvoeren van onderhoudsbewerkingen alleen beschouwd als *'best-effort'* en worden ze niet gegarandeerd binnen een opgegeven periode.
- Updates kunnen niet langer dan zeven dagen worden geblokkeerd.

### <a name="install-aks-preview-cli-extension"></a>De CLI-extensie aks-preview installeren

U hebt ook de Azure *CLI-extensie aks-preview* versie 0.5.4 of hoger nodig. Installeer de *Azure CLI-extensie aks-preview* met behulp van de [opdracht az extension add.][az-extension-add] U kunt ook beschikbare updates installeren met behulp van [de opdracht az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Sta onderhoud toe op elke maandag van 01:00 tot 02:00 uur

Als u een onderhoudsvenster wilt toevoegen, kunt u de opdracht `az aks maintenanceconfiguration add` gebruiken.

> [!IMPORTANT]
> Geplande onderhoudsvensters worden opgegeven in Coordinated Universal Time (UTC).

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

De volgende voorbeelduitvoer toont het onderhoudsvenster van 1:00 uur tot 2:00 uur elke maandag.

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

Als u onderhoud wilt toestaan op elk moment van de dag, laat u de parameter *start-uur* weg. Met de volgende opdracht stelt u bijvoorbeeld elke maandag het onderhoudsvenster voor de volledige dag in:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Een onderhoudsconfiguratie toevoegen met een JSON-bestand

U kunt ook een JSON-bestand gebruiken om een onderhoudsvenster te maken in plaats van parameters. Maak een `test.json` bestand met de volgende inhoud:

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

Het bovenstaande JSON-bestand geeft elke dinsdag om 13:00 - 3:00 uur en elke woensdag om 13:00 - 2:00 uur en om 6:00 - 7:00 uur aan. Er is ook een uitzondering van *2021-05-26T03:00:00Z* tot *2021-05-30T12:00:00Z,* waarbij onderhoud niet is toegestaan, zelfs niet als het overlapt met een onderhoudsvenster. Met de volgende opdracht worden de onderhoudsvensters van `test.json` toegevoegd.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Een bestaand onderhoudsvenster bijwerken

Gebruik de opdracht om een bestaande onderhoudsconfiguratie bij te `az aks maintenanceconfiguration update` werken.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Alle onderhoudsvensters in een bestaand cluster opsommen

Als u alle huidige onderhoudsconfiguratievensters in uw AKS-cluster wilt zien, gebruikt u de `az aks maintenanceconfiguration list` opdracht .

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

In de onderstaande uitvoer ziet u dat er twee onderhoudsvensters zijn geconfigureerd voor myAKSCluster. Eén venster is op maandag om 13:00 uur en een ander venster is op vrijdag om 4:00 uur.

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

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Een specifiek onderhoudsconfiguratievenster in een AKS-cluster tonen

Als u een specifiek onderhoudsconfiguratievenster in uw AKS-cluster wilt zien, gebruikt u de `az aks maintenanceconfiguration show` opdracht .

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

In de volgende voorbeelduitvoer ziet u het onderhoudsvenster voor *standaard*:

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

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Een bepaald onderhoudsconfiguratievenster in een bestaand AKS-cluster verwijderen

Gebruik de opdracht om een bepaald onderhoudsconfiguratievenster in uw AKS-cluster te `az aks maintenanceconfiguration delete` verwijderen.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="next-steps"></a>Volgende stappen

- Zie Een AKS-cluster upgraden om aan de slag te gaan met het upgraden [van uw AKS-cluster][aks-upgrade]


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
