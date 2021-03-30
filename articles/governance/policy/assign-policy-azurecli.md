---
title: 'Quickstart: Nieuwe beleidstoewijzing met Azure CLI'
description: In deze quickstart gebruikt u Azure CLI om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9955f911f9a92d7b353a8f3d022af7884b5a6aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090135"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Quickstart: Een beleidstoewijzing maken om niet-conforme resources met Azure CLI te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken.

Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze gids wordt Azure CLI gebruikt om een beleidstoewijzing te maken om niet-compatibele resources te identificeren in uw Azure-omgeving.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Voor deze quickstart moet u versie 2.0.76 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

- Registreer de resourceprovider Azure Policy Insights met behulp van Azure CLI. Als u de resourceprovider registreert, controleer dan of uw abonnement ermee werkt. Als u een resourceprovider wilt registreren, moet u toestemming hebben om de bewerking van de resourceprovider te registeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Zie [Resourceproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over het registreren en weergeven van resourceproviders.

- Installeer de [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-API’s.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze snelstart maakt u een beleidstoewijzing en wijst u de definitie **Controleer virtuele machines die niet gebruikmaken van beheerde schijven** toe. Deze beleidsdefinitie identificeert resources die niet voldoen aan de voorwaarden die zijn vastgelegd in de beleidsdefinitie.

Voer de volgende opdracht uit om een beleidstoewijzing te maken:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

De voorgaande opdracht maakt gebruik van de volgende informatie:

- **Naam**: de werkelijke naam van de toewijzing. Voor dit voorbeeld is _audit-vm-manageddisks_ gebruikt.
- **Weergavenaam**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing _Virtuele machines zonder beheerde schijven controleren_.
- **Beleid**: de id van de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_. Voer de volgende opdracht uit om de id van de beleidstoewijzing te verkrijgen: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Bereik**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Dit kan variëren van een abonnement tot resourcegroepen. Vergeet niet om &lt;scope&gt; te vervangen door de naam van uw resourcegroep.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Als u de resources wilt zien die niet compatibel zijn onder deze nieuwe toewijzing, voert u de volgende opdrachten uit om de id van de beleidstoewijzing te verkrijgen:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Zie [az policy assignment](/cli/azure/policy/assignment) voor meer informatie over beleidstoewijzings-id's.

Voer daarna de volgende opdracht uit om de resource-id's te verkrijgen van de niet-compatibele resources. Deze worden uitgevoerd naar een JSON-bestand:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

De resultaten zien er ongeveer als volgt uit:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

De resultaten zijn vergelijkbaar met wat in de weergave van de Azure-portal meestal wordt vermeld onder **Niet-compatibele resources**.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de beleidstoewijzing te verwijderen:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)
