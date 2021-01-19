---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c8be26441afa3b13dc74a2f0e915bdd25cac633e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047301"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor Azure Data Box-taken moet dubbele versleuteling zijn ingeschakeld voor data-at-rest op het apparaat](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Schakel een tweede laag van op software gebaseerde versleuteling in voor data-at-rest op het apparaat. Het apparaat is al beveiligd via Advanced Encryption Standard 256-bits-versleuteling voor data-at-rest. Met deze optie wordt een tweede laag van gegevensversleuteling toegevoegd. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box-taken moeten een door de klant beheerde sleutel gebruiken om het wachtwoord voor ontgrendelen van het apparaat te versleutelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Gebruik een door de klant beheerde sleutel om de versleuteling van het wachtwoord voor ontgrendeling van het apparaat voor Azure Data Box te beheren. Door de klant beheerde sleutels helpen u bij het beheren van de toegang tot het wachtwoord voor ontgrendeling van het apparaat door de Data Box-service om het apparaat voor te bereiden en gegevens geautomatiseerd te kopiëren. De gegevens op het apparaat zelf zijn al versleuteld met Advanced Encryption Standard 256-bits-versleuteling en het wachtwoord voor ontgrendeling van het apparaat wordt standaard versleuteld met een door Microsoft beheerde sleutel. |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
