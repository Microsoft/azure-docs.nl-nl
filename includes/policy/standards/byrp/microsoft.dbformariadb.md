---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/17/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b2f286d3229b572ea06ef607c8d8ee8189a7f9da
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700258"
---
## <a name="azure-security-benchmark"></a>Azure Security-benchmark

De [Azure Security-benchmark](../../../../articles/security/benchmarks/overview.md) biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. Bekijk de [toewijzingsbestanden van de Azure Security-benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) om te zien hoe deze service volledig is toegewezen aan de Azure Security-benchmark.

Raadpleeg [Naleving van Azure Policy-regelgeving - Azure Security-benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Het privé-eindpunt moet worden ingeschakeld voor MariaDB-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|Gegevensherstel |9.1 |Regelmatige geautomatiseerde back-ups verzekeren |[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Gegevensherstel |9.2 |Complete back-ups van het systeem uitvoeren en back-ups maken van door klant beheerde sleutels |[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Raadpleeg [Naleving van Azure Policy-regelgeving - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.
Zie [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) voor meer informatie over deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Back-up |1619.09l1Organizational.7 - 09.l |Er worden inventarisrecords voor de back-upkopieën, inclusief inhoud en huidige locatie, onderhouden. |[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Back-up |1624.09l3Organizational.12 - 09.l |De organisatie voert dagelijks incrementele of differentiële back-ups uit en ze voert wekelijks volledige back-ups uit om media te scheiden. |[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|Back-up |1627.09l3Organizational.6 - 09.l |De organisatie test de back-upgegevens na elke back-up om de betrouwbaarheid en gegevensintegriteit van de media te controleren. Daarna wordt ten minste jaarlijks een dergelijke test uitgevoerd. |[Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

