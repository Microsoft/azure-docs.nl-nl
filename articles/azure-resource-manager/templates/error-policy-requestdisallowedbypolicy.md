---
title: RequestDisallowedByPolicy-fout
description: Beschrijft de oorzaak van de RequestDisallowedByPolicy-fout bij het implementeren van resources met Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75477666"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>RequestDisallowedByPolicy-fout met Azure-resource beleid

In dit artikel wordt de oorzaak van de RequestDisallowedByPolicy-fout beschreven. deze fout kan ook worden opgelost.

## <a name="symptom"></a>Symptoom

Tijdens de implementatie ontvangt u mogelijk een **RequestDisallowedByPolicy** -fout die voor komt dat u de resources maakt. In het volgende voor beeld ziet u de fout:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik een van de volgende methoden om details op te halen over het beleid dat uw implementatie heeft geblokkeerd:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Geef in Power shell de beleids-id als de `Id` para meter op om details op te halen over het beleid dat uw implementatie heeft geblokkeerd.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Geef in azure CLI de naam op van de beleids definitie:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Oplossing

Voor beveiliging of naleving kunnen uw abonnements beheerders beleids regels toewijzen die beperken hoe bronnen worden geïmplementeerd. Uw abonnement kan bijvoorbeeld een beleid hebben dat voor komt dat open bare IP-adressen, netwerk beveiligings groepen, User-Defined routes of route tabellen worden gemaakt. In het fout bericht in de sectie **symptomen** wordt de naam van het beleid weer gegeven.
U kunt dit probleem oplossen door de bron beleidsregels te controleren en te bepalen hoe u resources implementeert die voldoen aan het beleid.

Raadpleeg voor meer informatie de volgende artikelen:

- [Wat is Azure Policy?](../../governance/policy/overview.md)
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)
