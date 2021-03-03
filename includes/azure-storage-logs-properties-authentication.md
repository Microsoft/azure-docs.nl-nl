---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 61576de4a57d55ea9d1ea209c52df556f0069617
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751113"
---
| Eigenschap | Beschrijving |
|:--- |:---|
|**identiteit/type** | Het type verificatie dat is gebruikt om de aanvraag uit te voeren. Bijvoorbeeld: `OAuth` ,, `Kerberos` `SAS Key` , `Account Key` of `Anonymous` |
|**identiteits-tokenHash**|Dit veld is alleen gereserveerd voor intern gebruik. |
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
