---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a8a9edb92ac01d49c521e770adde8f7bb04d8872
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105031644"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC-cache accounts moeten door de klant beheerde sleutel gebruiken voor versleuteling](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Versleuteling van de rest van een Azure HPC-cache met door de klant beheerde sleutels beheren. Standaard worden klant gegevens versleuteld met door service beheerde sleutels, maar door de klant beheerde sleutels zijn doorgaans vereist om te voldoen aan de normen voor naleving van regelgeving. Door de klant beheerde sleutels zorgen ervoor dat de gegevens kunnen worden versleuteld met een Azure Key Vault sleutel die u hebt gemaakt en waarvan u eigenaar bent. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. |Controleren, uitgeschakeld, weigeren |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
