---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1a70bbf434084faca5c8648f8901ec77675ca5fe
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220308"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure-gegevens fabrieken moeten worden versleuteld met een door de klant beheerde sleutel (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |Gebruik door de klant beheerde sleutels (CMKs) voor het beheren van de versleuteling op rest van uw Azure Data Factory. Standaard worden de klantgegevens versleuteld met door service beheerde sleutels, maar CMK‘s zijn doorgaans vereist om te voldoen aan de normen voor naleving van regelgeving. CMK‘s zorgen ervoor dat de gegevens worden versleuteld met een Azure Key Vault-sleutel die u hebt gemaakt en waarvan u eigenaar bent. U hebt de volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, met inbegrip van rotatie en beheer. Zie voor meer informatie over CMK-versleuteling: [https://aka.ms/adf-cmk](https://aka.ms/adf-cmk). |Controleren, Weigeren, Uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
