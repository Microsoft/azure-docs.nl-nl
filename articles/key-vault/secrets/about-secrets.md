---
title: Over Azure Key Vault-geheimen - Azure Key Vault
description: Overzicht van Azure Key Vault-geheimen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 4ded48fe8f04d2cdba40650974fd5002d659e381
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705265"
---
# <a name="about-azure-key-vault-secrets"></a>Over Azure Key Vault-geheimen

[Key Vault](../general/overview.md) biedt beveiligde opslag van algemene geheimen, zoals wachtwoorden en databaseverbindingsreeksen.

Vanuit het oogpunt van een ontwikkelaar worden geheime waarden door Key Vault-API's als tekenreeksen geaccepteerd en geretourneerd. Intern worden geheimen in Key Vault opgeslagen en beheerd als reeksen van octetten (8-bits bytes), met een maximale grootte van 25.000 bytes elk. De Key Vault-service biedt geen semantiek voor geheimen. Alleen de gegevens worden geaccepteerd, versleuteld, opgeslagen en er wordt een geheime id ('id') geretourneerd. De id kan worden gebruikt om het geheim op een later tijdstip op te halen.  

In het geval van zeer gevoelige gegevens is het raadzaam dat klanten extra beveiligingslagen overwegen. Dit kan bijvoorbeeld door het versleutelen van gegevens met behulp van een afzonderlijke beveiligingssleutel vóór de opslag in Key Vault.  

Key Vault biedt ook ondersteuning voor een contentType-veld voor geheimen. Clients kunnen het inhoudstype van een geheim opgeven om te helpen bij het interpreteren van de geheime gegevens wanneer deze worden opgehaald. Dit veld mag niet langer zijn dan 255 tekens. Er zijn geen vooraf gedefinieerde waarden. Het voorgestelde gebruik is als een soort hint voor het interpreteren van de geheime gegevens. Als bij een implementatie bijvoorbeeld beide wachtwoorden en certificaten als geheimen worden opgeslagen kan dit veld worden gebruikt om onderscheid te maken. Er zijn geen vooraf gedefinieerde waarden.  

## <a name="encryption"></a>Versleuteling

Alle geheimen in uw sleutelkluis worden versleuteld opgeslagen. Deze versleuteling is transparant en vereist geen actie van de gebruiker. De Azure Key Vault-service versleutelt uw geheimen wanneer u ze toevoegt en ontsleutelt ze automatisch wanneer u ze leest. De versleutelingssleutel is uniek voor elke sleutelkluis.

## <a name="secret-attributes"></a>Geheime kenmerken

Naast de geheime gegevens kunnen de volgende kenmerken worden opgegeven:  

- *exp*: IntDate, optioneel; standaardwaarde is **forever**. Met het kenmerk *exp* (verlooptijd) wordt de verlooptijd aangegeven waarop of waarna de geheime gegevens NIET MOETEN worden opgehaald, behalve in [bepaalde situaties](#date-time-controlled-operations). Dit veld is alleen bedoeld als **informatiebron**, omdat gebruikers van de sleutelkluisservice de informatie krijgen dat een bepaald geheim niet mag worden gebruikt. De waarde MOET een getal zijn dat een IntDate-waarde bevat.   
- *nbf*: IntDate, optioneel; standaardwaarde is **now**. Het kenmerk *nbf* (not before; niet voor) geeft het tijdstip aan waarvóór de geheime gegevens NIET MOGEN worden opgehaald, behalve in [bepaalde situaties](#date-time-controlled-operations). Dit veld is alleen bedoeld als **informatiebron**. De waarde MOET een getal zijn dat een IntDate-waarde bevat. 
- *enabled*: boolean, optioneel; standaardwaarde is **true**. Dit kenmerk geeft aan of de geheime gegevens kunnen worden opgehaald. Het kenmerk enabled wordt gebruikt in combinatie met *nbf* en *exp* wanneer er een bewerking plaatsvindt tussen *nbf* en *exp*. Het wordt alleen toegestaan als enabled is ingesteld op **true**. Bewerkingen buiten het tijdvenster van *nbf* en *exp* worden automatisch geweigerd, behalve in [bepaalde situaties](#date-time-controlled-operations).  

Er zijn aanvullende alleen-lezen kenmerken die in elk antwoord zijn opgenomen dat geheime kenmerken bevat:  

- *created*: IntDate, optioneel. Het kenmerk created geeft aan wanneer deze versie van het geheim is gemaakt. Deze waarde is null voor geheimen die vóór het toevoegen van dit kenmerk zijn gemaakt. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *updated*: IntDate, optioneel. Het kenmerk updated geeft aan wanneer deze versie van het geheim is bijgewerkt. Deze waarde is null voor geheimen die voor het laatst zijn bijgewerkt vóór het toevoegen van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.

Raadpleeg [Overzicht van sleutels, geheimen en certificaten in Azure Key Vault](../general/about-keys-secrets-certificates.md) voor informatie over de algemene kenmerken voor elk objecttype voor een sleutelkluis

### <a name="date-time-controlled-operations"></a>Bewerkingen met datum-/tijdcontrole

De bewerking **get** van een geheim werkt voor nog-niet-geldige of verlopen geheimen, buiten het tijdvenster *nbf* / *exp*. Het aanroepen van de bewerking **get** van een geheim voor een nog-niet-geldig geheim, kan voor testdoeleinden worden gebruikt. Het ophalen (**get** ting) van een verlopen geheim kan voor herstelbewerkingen worden gebruikt.

## <a name="secret-access-control"></a>Toegangsbeheer voor geheimen

Toegangsbeheer voor geheimen die in Key Vault worden beheerd, wordt geboden op het niveau van de sleutelkluis die die geheimen bevat. Het beleid voor toegangsbeheer voor geheimen verschilt van het beleid voor toegangsbeheer voor sleutels in dezelfde sleutelkluis. Gebruikers kunnen een of meer kluizen maken voor het bewaren van geheimen en moeten geheimen segmenteren en beheren overeenkomstig dat scenario.   

De volgende machtigingen kunnen per principal worden gebruikt in de toegangsbeheervermelding voor geheimen in een kluis en komen nauw overeen met de bewerkingen die voor een geheim-object zijn toegestaan:  

- Machtigingen voor beheerbewerkingen van geheimen
  - *get*: een geheim lezen  
  - *list*: vermelding van de geheimen of de versies van een geheim die in een sleutelkluis zijn opgeslagen  
  - *set*: een geheim maken  
  - *delete*: een geheim verwijderen  
  - *recover*: een verwijderd geheim herstellen
  - *backup*: een back-up van een geheim in een sleutelkluis maken
  - *restore*: een back-up van een geheim terugzetten naar een sleutelkluis

- Machtigingen voor bevoorrechte bewerkingen
  - *purge*: een verwijderd geheim opschonen (definitief verwijderen)

Zie [Geheimbewerkingen in de REST API-naslag voor Key Vault](/rest/api/keyvault) voor meer informatie over het werken met geheimen. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen. 

Instructiegidsen voor het beheren van de toegang in Key Vault:
- [Key Vault-toegangsbeleid toewijzen met behulp van CLI](../general/assign-access-policy-cli.md)
- [Key Vault-toegangsbeleid toewijzen met behulp van PowerShell](../general/assign-access-policy-powershell.md)
- [Key Vault-toegangsbeleid toewijzen met behulp van Azure Portal](../general/assign-access-policy-portal.md)
- [Toegang bieden tot sleutels, certificaten en geheimen in Key Vault met op rollen gebaseerd toegangsbeheer in Azure (preview)](../general/rbac-guide.md)

## <a name="secret-tags"></a>Geheimtags  
U kunt aanvullende toepassingsspecifieke metagegevens opgeven in de vorm van tags. Key Vault ondersteunt maximaal 15 tags, die elk een naam van 256 tekens en een waarde van 256 tekens kunnen bevatten.  

>[!Note]
>Tags kunnen worden gelezen door een oproepende functie als ze de machtiging *list* (weergeven) of *get* (ophalen) hebben.

## <a name="azure-storage-account-key-management"></a>Azure Storage-accountsleutelbeheer

In Key Vault kunnen [Azure Storage-accountsleutels](https://docs.microsoft.com/azure/storage/common/storage-account-overview) worden beheerd:

- Intern kan Key Vault sleutels synchroniseren met een Azure Storage-account. 
- Key Vault genereert de sleutels regelmatig opnieuw (roteren van sleutels).
- Sleutelwaarden worden nooit geretourneerd als antwoord op een oproepende functie.
- Key Vault beheert sleutels voor opslagaccounts en klassieke opslagaccounts.

Zie voor meer informatie:
- [Toegangssleutels voor opslagaccount](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)
- [Beheer van opslagaccountsleutels in Azure Key Vault ](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Toegangsbeheer voor opslagaccount

De volgende machtigingen kunnen worden gebruikt voor het autoriseren van een gebruiker of toepassingsprincipal om bewerkingen onder een beheerd opslagaccount uit te voeren:  

- Machtigingen voor beheerde opslagaccounts en SAS-definitiebewerkingen
  - *get*: hiermee wordt informatie opgehaald over een opslagaccount 
  - *list*: hiermee worden opslagaccounts weergeven die door een Key Vault worden beheerd
  - *update*: hiermee wordt een opslagaccount bijgewerkt
  - *delete*: Een opslagaccount verwijderen  
  - *recover*: Een verwijderd opslagaccount herstellen
  - *backup*: hiermee wordt een back-up van een opslagaccount gemaakt
  - *restore*: hiermee wordt een back-up van een opslagaccount teruggezet naar een sleutelkluis
  - *set*: hiermee wordt een opslagaccount gemaakt of bijgewerkt
  - *regeneratekey*: hiermee wordt een opgegeven sleutelwaarde voor een opslagaccount opnieuw gegenereerd
  - *getsas*: hiermee wordt informatie opgehaald over een SAS-definitie voor een opslagaccount
  - *listsas*: hiermee worden opslag-SAS-definities voor een opslagaccount weergegeven
  - *deletesas*: hiermee worden SAS-definities uit een opslagaccount verwijderd
  - *setsas*: hiermee worden nieuwe SAS-definities/-kenmerken voor een opslagaccount gemaakt of bijgewerkt

- Machtigingen voor bevoorrechte bewerkingen
  - *purge*: hiermee wordt een beheerd opslagaccount opgeschoond (definitief verwijderd)

Zie [Opslagaccountbewerkingen in de REST API-naslag voor Key Vault](/rest/api/keyvault) voor meer informatie. Raadpleeg [Kluizen: maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [Kluizen: toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy) voor meer informatie over het instellen van machtigingen.

Instructiegidsen voor het beheren van de toegang in Key Vault:
- [Key Vault-toegangsbeleid toewijzen met behulp van CLI](../general/assign-access-policy-cli.md)
- [Key Vault-toegangsbeleid toewijzen met behulp van PowerShell](../general/assign-access-policy-powershell.md)
- [Key Vault-toegangsbeleid toewijzen met behulp van Azure Portal](../general/assign-access-policy-portal.md)
- [Toegang bieden tot sleutels, certificaten en geheimen in Key Vault met op rollen gebaseerd toegangsbeheer in Azure (preview)](../general/rbac-guide.md)


## <a name="next-steps"></a>Volgende stappen

- [Informatie over Key Vault](../general/overview.md)
- [Informatie over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Over sleutels](../keys/about-keys.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
