---
title: Beheerde bronnen bijwerken
description: Hierin wordt beschreven hoe u kunt werken aan resources in de beheerde resource groep voor een door Azure beheerde toepassing.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75651213"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Werken met resources in de beheerde resource groep voor door Azure beheerde toepassing

In dit artikel wordt beschreven hoe u resources bijwerkt die zijn geïmplementeerd als onderdeel van een beheerde toepassing. Als uitgever van een beheerde toepassing hebt u toegang tot de resources in de beheerde resource groep. Als u deze resources wilt bijwerken, moet u de beheerde resource groep vinden die is gekoppeld aan een beheerde toepassing en toegang krijgen tot de resource in die resource groep.

In dit artikel wordt ervan uitgegaan dat u de beheerde toepassing hebt geïmplementeerd in de [beheerde webtoepassing (IaaS) met behulp van het voorbeeld project van Azure Management Services](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) . Deze beheerde toepassing bevat een **Standard_D1_v2** virtuele machine. Als u die beheerde toepassing niet hebt geïmplementeerd, kunt u dit artikel nog steeds gebruiken om vertrouwd te raken met de stappen voor het bijwerken van een beheerde resource groep.

In de volgende afbeelding ziet u de geïmplementeerde beheerde toepassing.

![Geïmplementeerde beheerde toepassing](./media/update-managed-resources/deployed.png)

In dit artikel gebruikt u Azure CLI voor het volgende:

* De beheerde toepassing identificeren
* De beheerde resource groep identificeren
* De resource (s) van de virtuele machine in de beheerde resource groep identificeren
* Wijzig de grootte van de virtuele machine (op een kleinere grootte als deze niet wordt gebruikt of een grotere ondersteuning voor meer belasting)
* Een beleid toewijzen aan de beheerde resource groep die de toegestane locaties opgeeft

## <a name="get-managed-application-and-managed-resource-group"></a>Beheerde toepassing en beheerde resource groep ophalen

Als u de beheerde toepassingen in een resource groep wilt ophalen, gebruikt u:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Als u de ID van de beheerde resource groep wilt ophalen, gebruikt u:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>De grootte van Vm's in een beheerde resource groep wijzigen

Als u de virtuele machines in de beheerde resource groep wilt weer geven, geeft u de naam van de beheerde resource groep op.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Als u de grootte van de Vm's wilt bijwerken, gebruikt u:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Nadat de bewerking is voltooid, controleert u of de toepassing wordt uitgevoerd op Standard D2 v2.

![Beheerde toepassing met behulp van Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Beleid Toep assen op beheerde resource groep

De beheerde resource groep en de toewijzing van een beleid in dat bereik ophalen. Het beleid **e56962a6-4747-49cd-b67b-bf8b01975c4c** is een ingebouwd beleid voor het opgeven van toegestane locaties.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Als u de toegestane locaties wilt zien, gebruikt u:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

De beleids toewijzing wordt weer gegeven in de portal.

![Beleids toewijzing weer geven](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van beheerde toepassingen](overview.md) voor een inleiding tot beheerde toepassingen.
* Zie [voorbeeld projecten voor door Azure beheerde toepassingen](sample-projects.md)voor voorbeeld projecten.
