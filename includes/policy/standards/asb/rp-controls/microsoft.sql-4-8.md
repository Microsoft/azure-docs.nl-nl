---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/24/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0eb83417350d5dca85802269b698aee30a310f6f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105048"
---
|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Voor SQL Managed Instance moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |TDE (Transparent Data Encryption) implementeren met uw eigen sleutel biedt u verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM, en bevordering van scheiding van taken. Deze aanbeveling is van toepassing op organisaties met een gerelateerde nalevingsvereiste. |AuditIfNotExists, uitgeschakeld |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Voor SQL-servers moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |TDE (Transparent Data Encryption) implementeren met uw eigen sleutel biedt verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM, en bevordering van scheiding van taken. Deze aanbeveling is van toepassing op organisaties met een gerelateerde nalevingsvereiste. |AuditIfNotExists, uitgeschakeld |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Transparent Data Encryption in SQL-databases moet zijn ingeschakeld](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Transparante gegevensversleuteling moet zijn ingeschakeld om data-at-rest te beveiligen en te voldoen aan de nalevingsvereisten |AuditIfNotExists, uitgeschakeld |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
