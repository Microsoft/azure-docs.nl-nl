---
title: Informatie over toegangsbeheer van Azure Key Vault-certificaten
description: Overzicht van toegangsbeheer van Azure Key Vault-certificaten
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92128543"
---
# <a name="certificate-access-control"></a>Toegangsbeheer voor certificaten

 Toegangsbeheer voor certificaten wordt beheerd door Key Vault en wordt gegeven door de Key Vault die deze certificaten bevat. Het beleid voor toegangsbeheer voor certificaten verschilt van het beleid voor sleutels en geheimen in dezelfde sleutelkluis. Gebruikers kunnen een of meer kluizen maken waarin zij certificaten kunnen bewaren en de segmentatie en het beheer van hun scenario naar behoren kunnen bijhouden.  

 De volgende machtigingen kunnen per principal worden gebruikt in de toegangsbeheervermelding voor geheimen van een sleutelkluis en komt nauw overeen met de bewerkingen die zijn toegestaan voor een geheimobject:  

- Machtigingen voor certificaatbeheerbewerkingen
  - **get**: De huidige certificaatversie of ongeacht welke certificaatversie ophalen
  - **list**: De huidige certificaten of versies van een certificaat weergeven  
  - **update**: Een certificaat bijwerken
  - **create**: Key Vault-certificaat maken
  - **import**: Certificaatmateriaal importeren in een Key Vault certificaat
  - **delete**: Een certificaat, het bijbehorende beleid en alle versies ervan verwijderen  
  - **recover**: Een verwijderd certificaat herstellen
  - **backup**: Een back-up maken van een certificaat in een sleutelkluis
  - **restore**: Een back-up van een certificaat herstellen in een sleutelkluis
  - **managecontacts**: Key Vault-certificaatcontactpersonen beheren  
  - **manageissuers**: Key Vault certificeringsinstanties/-verleners beheren
  - **getissuers**: De instanties/verleners van een certificaat ophalen
  - **listissuers**: De instanties/verleners van een certificaat weergeven  
  - **setissuers**: De instanties/verleners van een Key Vault-certificaat maken of bijwerken  
  - **deleteissuers**: De instanties/verleners van een Key Vault-certificaat verwijderen  
 
- Machtigingen voor bevoorrechte bewerkingen
  - **purge**: Een verwijderd certificaat opschonen (permanent verwijderen)

Raadpleeg de [Certificaatbewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault) voor meer informatie. Zie [Kluizen - toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen.

## <a name="troubleshoot"></a>Problemen oplossen
Mogelijk wordt er een foutmelding weergegeven als gevolg van een ontbrekend toegangsbeleid. Bijvoorbeeld ```Error type : Access denied or user is unauthorized to create certificate``` Om deze fout op te lossen moet u certificaten toevoegen/een machtiging maken.

## <a name="next-steps"></a>Volgende stappen

- [Informatie over Key Vault](../general/overview.md)
- [Informatie over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
