---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c8943d7b02a6d6d6d8c5957166a4c12302ae69bc
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090806"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps Integratieserviceomgeving moet worden versleuteld met door de klant beheerde sleutels](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |Implementeer in Integratieserviceomgeving om versleuteling op rest van Logic Apps gegevens te beheren met door de klant beheerde sleutels. Standaard worden klant gegevens versleuteld met door service beheerde sleutels, maar door de klant beheerde sleutels zijn doorgaans vereist om te voldoen aan de normen voor naleving van regelgeving. Door de klant beheerde sleutels zorgen ervoor dat de gegevens kunnen worden versleuteld met een Azure Key Vault sleutel die u hebt gemaakt en waarvan u eigenaar bent. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[Logic Apps moet worden geïmplementeerd in Integratieserviceomgeving](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |Wanneer u Logic Apps in Integratieserviceomgeving in een virtueel netwerk implementeert, worden geavanceerde Logic Apps netwerk-en beveiligings functies ontgrendeld en hebt u meer controle over uw netwerk configuratie. Zie voor meer informatie: [https://aka.ms/integration-service-environment](https://aka.ms/integration-service-environment). Als u in Integratieserviceomgeving implementeert, is versleuteling ook toegestaan met door de klant beheerde sleutels waarmee u de versleutelings sleutels kunt beheren. Dit is vaak om te voldoen aan nalevings vereisten. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Bron Logboeken in Logic Apps moeten worden ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Het inschakelen van bron logboeken controleren. Hiermee kunt u een activiteitenspoor opnieuw maken om te gebruiken voor onderzoeksdoeleinden wanneer een beveiligingsincident optreedt of wanneer uw netwerk is aangetast |AuditIfNotExists, uitgeschakeld |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
