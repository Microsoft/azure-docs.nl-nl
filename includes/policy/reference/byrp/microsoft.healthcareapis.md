---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1e8175239c1f4100cb3484f067e1aa73d2547f70
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612032"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR moet een door de klant beheerde sleutel gebruiken voor het versleutelen van gegevens in rust](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |Gebruik een door de klant beheerde sleutel om de versleuteling van data-at-rest te beheren van de gegevens die in Azure API for FHIR zijn opgeslagen als dit een vereiste is vanwege regelgeving of naleving. Door de klant beheerde sleutels bieden ook dubbele versleuteling door een tweede laag versleuteling toe te voegen boven op de standaard die wordt uitgevoerd met door service beheerde sleutels. |controle, uitgeschakeld |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[De Azure-API voor FHIR moet een privé-koppeling gebruiken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |De Azure-API voor FHIR moet over ten minste een goedgekeurde privé-eindpuntverbinding beschikken. Clients in een virtueel netwerk hebben beveiligde toegang tot resources met privé-eindpuntverbindingen door middel van privékoppelingen. Voor meer informatie gaat u naar: [https://aka.ms/fhir-privatelink](https://aka.ms/fhir-privatelink). |Controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[CORS mag er niet toe leiden dat elk domein toegang tot uw API voor FHIR heeft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |Gebruik van Cross-Origin Resource Sharing (CORS) mag er niet toe leiden dat alle domeinen toegang hebben tot uw API voor FHIR. Als u uw API voor FHIR wilt beveiligen, verwijdert u de toegang voor alle domeinen en definieert u de domeinen die zijn toegestaan om verbinding te maken. |controle, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |
