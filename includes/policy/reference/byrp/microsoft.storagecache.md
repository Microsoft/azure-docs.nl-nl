---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f14136e77bb0018ea5e15f6e07fa7a633addf49f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876002"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HPC Cache accounts moeten door de klant beheerde sleutel gebruiken voor versleuteling](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |Versleuteling in de rest van Azure HPC Cache met door de klant beheerde sleutels. Standaard worden klantgegevens versleuteld met door de service beheerde sleutels, maar door de klant beheerde sleutels zijn doorgaans vereist om te voldoen aan wettelijke nalevingsstandaarden. Met door de klant beheerde sleutels kunnen de gegevens worden versleuteld met een Azure Key Vault sleutel die door u is gemaakt en eigendom is. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. |Controleren, uitgeschakeld, weigeren |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
