---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78694874819f8a90f0ab318fb96cda6b8816a689
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880891"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Beveiliging tegen leegmaken moet zijn ingeschakeld voor sleutelkluizen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |Kwaadwillende verwijdering van een sleutelkluis kan leiden tot permanent gegevensverlies. Een kwaadwillende insider in uw organisatie kan sleutelkluizen verwijderen en leegmaken. Beveiliging tegen leegmaken beschermt u tegen aanvallen van insiders door een verplichte bewaarperiode tijdens voorlopige verwijdering af te dwingen voor sleutelkluizen. Gedurende de periode van voorlopige verwijdering kan niemand binnen uw organisatie of Microsoft uw sleutelkluizen leegmaken. |Controleren, Weigeren, Uitgeschakeld |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
