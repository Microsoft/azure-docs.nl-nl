---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ca8963ed8928745a6d5918c86021199432339c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611942"
---
| Eigenschap | Beschrijving |
|:--- |:---|
|**identiteit/type** | Het type verificatie dat is gebruikt om de aanvraag uit te voeren. Bijvoorbeeld: `OAuth` ,, `Kerberos` `SAS Key` , `Account Key` of `Anonymous` |
|**identiteits-tokenHash**|De SHA-256-hash van het verificatie token dat voor de aanvraag wordt gebruikt. <br>Wanneer het verificatie type is `Account Key` , is de notatie "Key1 \| KEY2 (sha256-hash van de sleutel)". Bijvoorbeeld: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>Als verificatie type is `SAS Key` , is de notatie "Key1 \| KEY2 (SHA 256 hash van de sleutel), SASSIGNATURE (SHA 256-hash van de SAS-token)". Bijvoorbeeld: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. Wanneer het verificatie type is `OAuth` , is de indeling SHA 256 hash van het OAuth-token. Bijvoorbeeld: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DW25C`<br> Voor andere verificatie typen is er geen tokenHash-veld. |
|**autorisatie/actie** | De actie die is toegewezen aan de aanvraag. |
|**autorisatie-roleAssignmentId** | De roltoewijzings-ID. Bijvoorbeeld: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorisatie-Roledefinitionid hebben** | De roldefinitie-ID. Bijvoorbeeld: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals/id** | De ID van de beveiligingsprincipal. Bijvoorbeeld: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals/type** | Het type van de beveiligingsprincipal. Bijvoorbeeld: `ServicePrincipal`. |
|**aanvrager/appID** | De Open Authorization (OAuth)-toepassings-ID die wordt gebruikt als aanvrager. <br> Bijvoorbeeld: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**aanvrager/doel groep** | De OAuth-doel groep van de aanvraag. Bijvoorbeeld: `https://storage.azure.com`. |
|**aanvrager/objectId** | De OAuth-object-ID van de aanvrager. In het geval van Kerberos-verificatie vertegenwoordigt de object-id van door Kerberos geverifieerde gebruiker. Bijvoorbeeld: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**aanvrager/tenantId** | De OAuth-Tenant-ID van de identiteit. Bijvoorbeeld: `72f988bf-86f1-41af-91ab-222222222222`.|
|**aanvrager/tokenIssuer** | De certificaat verlener van het OAuth-token. Bijvoorbeeld: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**aanvrager/UPN** | De UPN (User Principal Name) van de aanvrager. Bijvoorbeeld: `someone@contoso.com`. |
|**aanvrager/gebruikers naam** | Dit veld is alleen gereserveerd voor intern gebruik.|
