---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dee9765198bb0fbece75834253a4dd85a56dd488
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096428"
---
## <a name="azure-security-benchmark"></a>Azure Security-benchmark

De [Azure Security-benchmark](../../../../articles/security/benchmarks/overview.md) biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. Bekijk de [toewijzingsbestanden van de Azure Security-benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) om te zien hoe deze service volledig is toegewezen aan de Azure Security-benchmark.

Raadpleeg [Naleving van Azure Policy-regelgeving - Azure Security-benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Netwerkbeveiliging |NS-1 |Beveiliging voor intern verkeer implementeren |[Azure Cosmos DB-accounts moeten firewallregels bevatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Netwerkbeveiliging |NS-4 |Toepassingen en Services beveiligen tegen aanvallen van externe netwerken |[Azure Cosmos DB-accounts moeten firewallregels bevatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|Gegevensbeveiliging |DP-5 |Gevoelige gegevens in rust versleutelen |[Voor Azure Cosmos DB-accounts moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

De [Azure Security-benchmark](../../../../articles/security/benchmarks/overview.md) biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. Bekijk de [toewijzingsbestanden van de Azure Security-benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) om te zien hoe deze service volledig is toegewezen aan de Azure Security-benchmark.

Raadpleeg [Naleving van Azure Policy-regelgeving - Azure Security-benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Netwerkbeveiliging |1.1 |Resources beschermen met behulp van netwerkbeveiligingsgroepen of Azure Firewall op uw virtueel netwerk |[Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC niveau 3

Zie [Azure Policy regelgevings naleving-CMMC niveau 3](../../../../articles/governance/policy/samples/cmmc-l3.md)als u wilt weten hoe de beschik bare Azure Policy ingebouwde invoeg toepassingen voor alle Azure-Services zijn gekoppeld aan deze nalevings standaard.
Zie [Cyber beveiliging rijpheid-model certificering (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)voor meer informatie over deze nalevings standaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Reageren op incidenten |IR. 2.093 |Gebeurtenissen detecteren en rapporteren. |[Advanced Threat Protection implementeren voor Cosmos DB-accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Raadpleeg [Naleving van Azure Policy-regelgeving - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md) om te zien hoe de beschikbare ingebouwde modules voor Azure Policy voor alle Azure-services zijn toegewezen aan deze nalevingsstandaard.
Zie [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) voor meer informatie over deze nalevingsstandaard.

|Domain |Id van besturingselement |Titel van besturingselement |Beleid<br /><sub>(Azure-portal)</sub> |Beleidsversie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Scheiding in netwerken |0805.01m1Organizational.12 - 01.m |De beveiligingsgateways van de organisatie (zoals firewalls) dwingen beveiligingsbeleid af en zijn geconfigureerd voor het filteren van verkeer tussen domeinen, het blokkeren van onbevoegde toegang en worden gebruikt voor het onderhouden van de scheiding tussen interne bekabelde, interne draadloze en externe netwerksegmenten (bijvoorbeeld het internet), waaronder DMZ's. Bovendien wordt beleid voor toegangsbeheer afgedwongen voor elk domein. |[Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Scheiding in netwerken |0806.01m2Organizational.12356 - 01.m |Het netwerk van de organisatie is logisch en fysiek gesegmenteerd met een gedefinieerde beveiligingsperimeter en een gestaffelde set besturingselementen, met inbegrip van subnetwerken voor openbaar toegankelijke systeemonderdelen die logisch zijn gescheiden van het interne netwerk, op basis van de vereisten van de organisatie. Verkeer wordt beheerd op basis van de vereiste functionaliteit en classificatie van de gegevens/systemen op basis van een risicoanalyse en hun respectieve beveiligingsvereisten. |[Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Scheiding in netwerken |0894.01m2Organizational.7 - 01.m |Netwerken worden gescheiden van netwerken op productieniveau bij het migreren van fysieke servers, toepassingen of gegevens naar gevirtualiseerde servers. |[Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|Netwerkbesturingselementen |0864.09m2Organizational.12 - 09.m |Beperkingen ten aanzien van het gebruik en richtlijnen voor de implementatie zijn formeel gedefinieerd voor VoIP, met inbegrip van de autorisatie en bewaking van de service. |[Cosmos DB moet gebruikmaken van een service-eindpunt voor een virtueel netwerk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

