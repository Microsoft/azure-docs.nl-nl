---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fb5f6dbb410a15bf88fccc1ed3f9c3ca0f830c70
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "96296081"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Synapse-werk ruimten moeten door de klant beheerde sleutels gebruiken om gegevens in rust te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |Gebruik door de klant beheerde sleutels om de versleuteling te beheren bij de rest van de gegevens die zijn opgeslagen in azure Synapse-werk ruimten. Door de klant beheerde sleutels bieden dubbele code ring door een tweede laag versleuteling toe te voegen boven op de standaard versleuteling met door service beheerde sleutels. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[IP-firewall regels in azure Synapse-werk ruimten moeten worden verwijderd](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56fd377d-098c-4f02-8406-81eb055902b8) |Als u alle IP-firewall regels verwijdert, wordt de beveiliging verbeterd door ervoor te zorgen dat uw Azure Synapse-werk ruimte alleen toegankelijk is vanuit een persoonlijk eind punt. Met deze configuratie controles kunt u firewall regels maken die open bare netwerk toegang tot de werk ruimte toestaan. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceFirewallRules_Audit.json) |
|[Het virtuele netwerk van de beheerde werk ruimte in azure Synapse-werk ruimten moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d9dbfa3-927b-4cf0-9d0f-08747f971650) |Als u een virtueel netwerk voor een beheerde werk ruimte inschakelt, zorgt u ervoor dat uw werk ruimte is geïsoleerd van andere werk ruimten. Gegevens integratie en Spark-resources die zijn geïmplementeerd in dit virtuele netwerk, bieden ook isolatie op gebruikers niveau voor Spark-activiteiten. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceManagedVnet_Audit.json) |
|[Privé-eindpunt verbindingen in azure Synapse-werk ruimten moeten zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Privé-eind punten kunnen worden geconfigureerd om privé verbinding te maken met een Azure Synapse-werk ruimte. Dit wordt gebruikt om een beveiligd communicatie kanaal af te dwingen voor de Azure Synapse-werk ruimte. |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Synapse beheerde privé-eind punten moeten alleen verbinding maken met resources in goedgekeurde Azure Active Directory tenants](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a003702-13d2-4679-941b-937e58c443f0) |Beveilig uw Synapse-werk ruimte door alleen verbindingen toe te staan met resources in goedgekeurde Azure Active Directory-tenants (Azure AD). De goedgekeurde Azure AD-tenants kunnen worden gedefinieerd tijdens de beleids toewijzing. |Controleren, uitgeschakeld, weigeren |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/Workspace_DataExfiltrationPrevention_Deny.json) |
