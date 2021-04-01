---
title: Quota fouten
description: Hierin wordt beschreven hoe u resource quotum fouten oplost bij het implementeren van resources met Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 75e8abf31d035a1e3a106bc0c6561624762db5d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530417"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolve errors for resource quotas (Fouten voor resourcequota oplossen)

In dit artikel worden de quotum fouten beschreven die u kunt tegen komen bij het implementeren van resources.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Symptoom

Als u een sjabloon implementeert waarmee resources worden gemaakt die uw Azure-quota's overschrijden, wordt er een implementatie fout weer gegeven die er als volgt uitziet:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

U kunt ook het volgende zien:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Oorzaak

Quota worden toegepast per resourcegroep, abonnement, account en andere bereiken. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u een virtuele machine probeert te implementeren met meer kerngeheugens dan is toegestaan, ontvangt u een foutmelding die aangeeft dat het quotum is overschreden.
Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor de volledige quotum informatie.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="azure-cli"></a>Azure CLI

Voor Azure CLI gebruikt u de `az vm list-usage` opdracht om de quota's van virtuele machines te vinden.

```azurecli
az vm list-usage --location "South Central US"
```

Hiermee wordt het volgende geretourneerd:

```output
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Voor Power shell gebruikt u de opdracht **Get-AzVMUsage** om de quota's van de virtuele machine te vinden.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Hiermee wordt het volgende geretourneerd:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Oplossing

Als u een quota verhoging wilt aanvragen, gaat u naar de portal en doet u een ondersteunings probleem. In het ondersteunings probleem vraagt u een verhoging van uw quotum aan voor de regio waarin u de implementatie wilt uitvoeren.

> [!NOTE]
> Houd er rekening mee dat voor resource groepen het quotum geldt voor elke afzonderlijke regio en niet voor het hele abonnement. Als u 30 kernen in VS-West moet implementeren, moet u vragen om de 30 Resource Manager-kernen in VS West. Als u 30 kernen moet implementeren in een van de regio's waartoe u toegang hebt, moet u in alle regio's 30 Resource Manager-kernen vragen.
>
>

1. Selecteer **Abonnementen**.

   ![Scherm opname toont het menu van Azure Portal met geselecteerde abonnementen.](./media/error-resource-quota/subscriptions.png)

2. Selecteer het abonnement waarvoor het quotum moet worden verhoogd.

   ![Abonnement selecteren](./media/error-resource-quota/select-subscription.png)

3. **Gebruik en quota's** selecteren

   ![Gebruik en quota's selecteren](./media/error-resource-quota/select-usage-quotas.png)

4. Selecteer in de rechter bovenhoek **aanvraag verhoging**.

   ![Toename aanvragen](./media/error-resource-quota/request-increase.png)

5. Vul de formulieren in voor het type quotum dat u wilt verhogen.

   ![Formulier invullen](./media/error-resource-quota/forms.png)