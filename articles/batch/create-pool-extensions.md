---
title: Extensies gebruiken met batch-Pools
description: Uitbrei dingen zijn kleine toepassingen die de configuratie van het achteraf inrichten en instellen voor batch Compute-knoop punten vergemakkelijken.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417168"
---
# <a name="use-extensions-with-batch-pools"></a>Extensies gebruiken met batch-Pools

Uitbrei dingen zijn kleine toepassingen die de configuratie van het achteraf inrichten en instellen voor batch Compute-knoop punten vergemakkelijken. U kunt een van de uitbrei dingen selecteren die zijn toegestaan door Azure Batch en ze hebben geïnstalleerd op de reken knooppunten wanneer ze zijn ingericht. Daarna kan de uitbrei ding de beoogde bewerking uitvoeren.

U kunt de live status controleren van de uitbrei dingen die u gebruikt en de informatie ophalen die ze retour neren om de mogelijkheden van detectie, correctie of diagnose te kunnen volgen.

## <a name="prerequisites"></a>Vereisten

- Pools met uitbrei dingen moeten de [configuratie van virtuele machines](nodes-and-pools.md#virtual-machine-configuration)gebruiken.
- Het extensie type CustomScript is gereserveerd voor de Azure Batch-service en kan niet worden overschreven.

### <a name="supported-extensions"></a>Ondersteunde extensies

De volgende uitbrei dingen kunnen momenteel worden geïnstalleerd bij het maken van een batch-pool. 

- Azure Key Vault-extensie voor zowel [Linux](../virtual-machines/extensions/key-vault-linux.md) als [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Log Analytics en bewakings extensie voor [Linux](../virtual-machines/extensions/oms-linux.md) en [Windows](../virtual-machines/extensions/oms-windows.md)
- Azure-beveiligings pakket

U kunt ondersteuning aanvragen voor aanvullende uitgevers en/of extensie typen door een ondersteunings aanvraag te openen.

## <a name="create-a-pool-with-extensions"></a>Een groep maken met extensies

In het onderstaande voor beeld maakt u een batch-pool met Linux-knoop punten die gebruikmaakt van de Azure Key Vault-extensie.

REST API-URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Aanvraagtekst

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Extensie gegevens ophalen uit een groep

In het volgende voor beeld worden gegevens opgehaald uit de Azure Key Vault-extensie.

REST API-URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Antwoord tekst

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over verschillende manieren om [toepassingen en gegevens naar pool knooppunten te kopiëren](batch-applications-to-pool-nodes.md).
- Meer informatie over het werken met [knoop punten en groepen](nodes-and-pools.md).
