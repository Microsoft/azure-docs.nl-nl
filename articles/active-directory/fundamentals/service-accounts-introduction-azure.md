---
title: Inleiding tot het beveiligen van Azure Active Directory-service accounts
description: Uitleg van de typen service accounts die beschikbaar zijn in Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d657f1df14b083631227cb7c19f64b65be8801d0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010462"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Inleiding tot het beveiligen van Azure-service accounts

Er zijn drie soorten service accounts voor Azure Active Directory: beheerde identiteiten, service-principals en op gebruikers gebaseerde service accounts. Service accounts zijn een speciaal type account dat is bedoeld voor een niet-menselijke entiteit, zoals een toepassing, API of andere service. Deze entiteiten worden uitgevoerd in de beveiligings context van het service account. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Typen Azure Active Directory-service accounts

Voor services die worden gehost in azure, kunt u het beste een beheerde identiteit gebruiken, indien mogelijk, en een Service-Principal als dat niet het geval is. Beheerde identiteiten kunnen niet worden gebruikt voor services die buiten Azure worden gehost. In dat geval wordt een Service-Principal aangeraden. Als u een beheerde identiteit of een Service-Principal kunt gebruiken. U wordt aangeraden een Azure Active Directory gebruikers account niet als service account te gebruiken. Raadpleeg de volgende tabel voor een samen vatting.
 

| Service-hosting| Beheerde identiteit| Service-principal| Azure-gebruikers account |
| - | - | - | - |
|De service wordt gehost in Azure.| Ja. <br>Aanbevolen als de service <br>ondersteunt een beheerde identiteit.| Ja.| Niet aanbevolen. |
| De service wordt niet gehost in Azure.| Nee| Ja. Aanbevelingen.| Niet aanbevolen. |
| Service is multi tenant| Nee| Ja. Aanbevelingen.| Nee. |


## <a name="managed-identities"></a>Beheerde identiteiten

Beheerde identiteiten zijn beveiligde Azure Active Directory-identiteiten (Azure AD) die zijn gemaakt om identiteiten voor Azure-resources te bieden. Er zijn [twee soorten beheerde identiteiten](../managed-identities-azure-resources/overview.md#managed-identity-types): 
 
* Door het systeem toegewezen beheerde identiteiten kunnen rechtstreeks worden toegewezen aan een exemplaar van een service. 

* Door de gebruiker toegewezen beheerde identiteiten kunnen worden gemaakt als een zelfstandige resource. 

Zie [beheerde identiteiten beveiligen](service-accounts-managed-identities.md)voor meer informatie. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../managed-identities-azure-resources/overview.md) voor algemene informatie over beheerde identiteiten?

## <a name="service-principals"></a>Service-principals

Als u een beheerde identiteit niet kunt gebruiken om uw toepassing aan te duiden, gebruikt u een service-principal. Service-principals kunnen worden gebruikt met zowel één Tenant als multi tenant-toepassingen. 

Een Service-Principal is de lokale weer gave van een toepassings object in één Azure AD-Tenant. De functie fungeert als de identiteit van het toepassings exemplaar, definieert wie toegang heeft tot de toepassing en welke resources de toepassing kan openen. Een service-principal wordt gemaakt in (lokaal naar) elke Tenant waar de toepassing wordt gebruikt en verwijst naar het wereld wijde unieke toepassings object. De Tenant beveiligt de aanmelding van de Service-Principal en de toegang tot de resources.

Er zijn twee mechanismen voor verificatie met Service-principals: client certificaten en client geheimen. Certificaten zijn veiliger: gebruik client certificaten, indien mogelijk. In tegens telling tot client geheimen kunnen client certificaten niet per ongeluk in code worden inge sloten.

Zie [service-principals beveiligen](service-accounts-principal.md)voor meer informatie over het beveiligen van service-principals.

 
## <a name="next-steps"></a>Volgende stappen


Zie voor meer informatie over het beveiligen van Azure-service accounts:

[Beheerde identiteiten beveiligen](service-accounts-managed-identities.md)

[Service-principals beveiligen](service-accounts-principal.md)

[Azure-service accounts beheren](service-accounts-governing-azure.md)
