---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478457"
---
## <a name="azure-security-benchmark"></a>Azure Security-benchmark

De [Azure Security-benchmark](../../../../articles/security/benchmarks/overview.md) biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. Bekijk de [toewijzingsbestanden van de Azure Security-benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) om te zien hoe deze service volledig is toegewezen aan de Azure Security-benchmark.

Raadpleeg [Naleving van Azure Policy-regelgeving - Azure Security-benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gegevensbeveiliging |4.8 |Gevoelige gegevens 'at rest' versleutelen |[Automation-accountvariabelen moeten worden versleuteld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Wanneer u een Automation-accountvariabele maakt, kunt u de versleuteling en opslag door Azure Automation als een beveiligde asset opgeven. Nadat u de variabele hebt gemaakt, kunt u de versleutelingsstatus ervan niet meer wijzigen zonder de variabele opnieuw te maken. Als u met Automation-accountvariabelen gevoelige gegevens opslaat die nog niet zijn versleuteld, moet u deze verwijderen en opnieuw maken als versleutelde variabelen. Azure Security Center raadt aan om alle Azure Automation-variabelen te versleutelen, zoals beschreven in [Automation-accountvariabelen moeten worden versleuteld](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Als u niet-versleutelde variabelen hebt die u wilt uitsluiten van deze beveiligingsaanbeveling, raadpleegt u [Een resource uitsluiten van aanbevelingen en de beveiligingsscore](../../../../articles/security-center/exempt-resource.md) om een uitzonderingsregel te maken.