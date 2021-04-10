---
title: OKD implementeren in azure
description: Implementeer OKD in Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: dc14b10081cf175581d29524dcea60c52763b03c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667207"
---
# <a name="deploy-okd-in-azure"></a>OKD implementeren in azure

U kunt een van de twee manieren gebruiken om OKD (voorheen openstaande oorsprong) in azure te implementeren:

- U kunt alle benodigde onderdelen van Azure Infrastructure hand matig implementeren en vervolgens de [OKD-documentatie](https://docs.okd.io)volgen.
- U kunt ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-origin) gebruiken die de implementatie van het OKD-cluster vereenvoudigt.

## <a name="deploy-using-the-okd-template"></a>Implementeren met behulp van de OKD-sjabloon

Als u wilt implementeren met behulp van de Resource Manager-sjabloon, gebruikt u een parameter bestand voor het opgeven van de invoer parameters. Als u de implementatie verder wilt aanpassen, splitst u de GitHub-opslag plaats en wijzigt u de juiste items.

Enkele algemene aanpassings opties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.jsop)
- Naam conventies (variabelen in azuredeploy.jsop)
- Details van open Shift-cluster, gewijzigd via een hosts-bestand (deployOpenShift.sh)

Voor de [OKD-sjabloon](https://github.com/Microsoft/openshift-origin) zijn meerdere vertakkingen beschikbaar voor verschillende versies van OKD.  Op basis van uw behoeften kunt u rechtstreeks vanuit de opslag plaats implementeren of u kunt het opslag plaats opsplitsen en aangepaste wijzigingen aanbrengen voordat u implementeert.

Gebruik de `appId` waarde van de service-principal die u eerder hebt gemaakt voor de `aadClientId` para meter.

Hier volgt een voor beeld van een parameter bestand met de naam azuredeploy.parameters.jsop met alle vereiste invoer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Vervang de para meters door uw specifieke informatie.

Verschillende releases hebben mogelijk verschillende para meters. Controleer de benodigde para meters voor de vertakking die u gebruikt.

### <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI


> [!NOTE] 
> Voor de volgende opdracht is Azure CLI 2.0.8 of hoger vereist. U kunt de CLI-versie controleren met de `az --version` opdracht. Zie [Azure cli installeren](/cli/azure/install-azure-cli)voor informatie over het bijwerken van de CLI-versie.

In het volgende voor beeld worden het OKD-cluster en alle gerelateerde resources geïmplementeerd in een resource groep met de naam openshiftrg, met een implementatie naam van myOpenShiftCluster. Er wordt rechtstreeks vanuit de GitHub-opslag plaats naar de sjabloon verwezen tijdens het gebruik van een lokaal parameter bestand met de naam azuredeploy.parameters.jsop.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Het volt ooien van de implementatie duurt ten minste 30 minuten, op basis van het totale aantal geïmplementeerde knoop punten. Wanneer de implementatie is voltooid, wordt de URL van de open Shift-console en de DNS-naam van de open Shift-Master afgedrukt op de Terminal. U kunt ook de sectie outputs van de implementatie bekijken vanuit het Azure Portal.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

Als u niet wilt dat de opdracht regel wordt ingewisseld om de implementatie te volt ooien, voegt u `--no-wait` als een van de opties voor de groeps implementatie toe. De uitvoer van de implementatie kan worden opgehaald uit de Azure Portal in de implementatie sectie voor de resource groep.

## <a name="connect-to-the-okd-cluster"></a>Verbinding maken met het OKD-cluster

Wanneer de implementatie is voltooid, maakt u verbinding met de open Shift-console met uw browser met behulp van de `OpenShift Console Url` . U kunt ook SSHen naar de OKD-Master. Hieronder volgt een voor beeld waarin de uitvoer van de implementatie wordt gebruikt:

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [AZ Group delete](/cli/azure/group) om de resource groep, open Shift-cluster en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met de openshift-implementatie oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met OKD](https://docs.okd.io)
