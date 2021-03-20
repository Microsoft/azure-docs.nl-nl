---
title: Lokale, ingebouwde RBAC-rollen van de beheerde HSM - Azure Key Vault | Microsoft Docs
description: Een overzicht van de ingebouwde rollen van de beheerde HSM die kunnen worden toegewezen aan gebruikers, service-principals, groepen en beheerde identiteiten
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a5ecd8f13a3255a565168f62f358a6a38e3cbab4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94445214"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Lokale, ingebouwde RBAC-rollen van de beheerde HSM

De lokale RBAC van de beheerde HSM bevat enkele ingebouwde rollen die u aan gebruikers, service-principals, groepen en beheerde identiteiten kunt toewijzen. Als u een principal wilt toestaan een bewerking uit te voeren, moet u deze een rol toewijzen die toestemming verleent om die bewerkingen uit te voeren. Met al deze rollen en bewerkingen kunt u alleen machtigingen beheren voor gegevensvlakbewerkingen. Als u de machtigingen voor het beheervlak voor de beheerde HSM-resource wilt beheren (bijvoorbeeld voor het maken van een nieuwe beheerde HSM of het bijwerken, verplaatsen of verwijderen van een bestaande HSM), moet u [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md) gebruiken.

## <a name="built-in-roles"></a>Ingebouwde rollen

|Naam rol|Beschrijving|Id|
|---|---|---|
|Beheerder van beheerde HSM| Verleent volledige toegang tot alle gegevensacties.|a290e904-7015-4bba-90c8-60543313cdb4|
|Crypto Officer van beheerde HSM| Verleent volledige toegang tot alle belangrijke beheer- en cryptografische bewerkingen|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Cryptogebruiker van beheerde HSM|Verleent toestemming om sleutels voor cryptografische bewerkingen te maken en gebruiken. Kan sleutels niet permanent verwijderen.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Beleidsbeheerder van beheerde HSM| Verleent toestemming om roltoewijzingen te maken en verwijderen|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Cryptoauditor van beheerde HSM|Verleent toestemming om sleutels te lezen (maar niet om te gebruiken)|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Cryptoserviceversleuteling van beheerde HSM| Verleent toestemming voor het gebruik van een sleutel voor serviceversleuteling. |33413926-3206-4cdd-b39a-83574fe37a17|
|Back-up van beheerde HSM| Verleent toestemming om back-ups te maken van enkelvoudige sleutels of de hele HSM. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Toegestane bewerkingen
> [!NOTE]  
> - Een X geeft aan dat een rol toestemming heeft de gegevensactie uit te voeren. Een lege cel geeft aan dat de rol geen toestemming heeft om die gegevensactie uit te voeren.
> - Alle namen van de gegevensactie hebben het voorvoegsel Microsoft.KeyVault/managedHsm, dat vanwege de lengte in de onderstaande tabellen is weggelaten.
> - Alle rolnaamsen hebben het voorvoegsel Managed HSM, dat vanwege de lengte in de onderstaande tabellen is weggelaten.

|Gegevensactie | Beheerder | Crypto Officer | Cryptogebruiker | Beleidsbeheerder | Cryptoserviceversleuteling | Backup | Cryptoauditor|
|---|---|---|---|---|---|---|---|
|**Beheer van beveiligingsdomein**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Sleutelbeheer**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**Belangrijke cryptografische bewerkingen**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**Rolbeheer**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**Back-up- en herstelbeheer**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Volgende stappen

- Bekijk een overzicht van [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md).
- Bekijk een zelfstudie over [Rolbeheer van beheerde HSM](role-management.md)
