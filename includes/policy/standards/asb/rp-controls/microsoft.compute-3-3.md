---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1b7e8fab6aafb599ed49939333ac998564cfa27f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511768"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows-machines controleren waarop opgegeven leden van de groep Beheerders ontbreken](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Hiertoe moeten vereiste onderdelen worden geïmplementeerd in het bereik van de beleidstoewijzing. Zie [https://aka.ms/gcpol](https://aka.ms/gcpol) voor meer informatie. Machines voldoen niet als de lokale groep Beheerders niet één of meer leden bevat die worden vermeld in de beleidsparameter. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[Windows-machines controleren met extra accounts in de groep Administrators](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Hiertoe moeten vereiste onderdelen worden geïmplementeerd in het bereik van de beleidstoewijzing. Zie [https://aka.ms/gcpol](https://aka.ms/gcpol) voor meer informatie. Machines voldoen niet als de lokale groep Beheerders leden bevat die niet worden vermeld in de beleidsparameter. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |
|[Windows-machines controleren die opgegeven leden van de groep Beheerders bevatten](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Hiertoe moeten vereiste onderdelen worden geïmplementeerd in het bereik van de beleidstoewijzing. Zie [https://aka.ms/gcpol](https://aka.ms/gcpol) voor meer informatie. Machines voldoen niet als de lokale groep Beheerders één of meer leden bevat die worden vermeld in de beleidsparameter. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
