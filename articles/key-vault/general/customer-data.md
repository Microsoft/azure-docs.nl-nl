---
title: Azure Key Vault functies voor klantgegevens - Azure Key Vault | Microsoft Docs
description: Meer informatie over klantgegevens die Azure Key Vault ontvangen tijdens het maken of bijwerken van kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4d45c019a6ba073d7553c09784736959faf89d27
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749776"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault functies voor klantgegevens

Azure Key Vault ontvangt klantgegevens tijdens het maken of bijwerken van kluizen, beheerde HSM-pools, sleutels, geheimen, certificaten en beheerde opslagaccounts. Deze klantgegevens zijn rechtstreeks zichtbaar in de Azure Portal en via de REST API. Klantgegevens kunnen worden bewerkt of verwijderd door het object dat de gegevens bevat bij te werken of te verwijderen.

Logboeken voor systeemtoegang worden gegenereerd wanneer een gebruiker of toepassing toegang heeft tot Key Vault. Gedetailleerde toegangslogboeken zijn beschikbaar voor klanten die Azure Insights gebruiken.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Klantgegevens identificeren

De volgende informatie identificeert klantgegevens binnen Azure Key Vault:

- Toegangsbeleid voor Azure Key Vault bevatten object-ID's die gebruikers, groepen of toepassingen vertegenwoordigen
- Certificaatonderwerpen kunnen e-mailadressen of andere gebruikers- of organisatie-id's bevatten
- Certificaatcontacten kunnen e-mailadressen, namen of telefoonnummers van gebruikers bevatten
- Certificaatverleners kunnen e-mailadressen, namen, telefoonnummers, accountreferenties en organisatiegegevens bevatten
- Willekeurige tags kunnen worden toegepast op objecten in Azure Key Vault. Deze objecten omvatten kluizen, sleutels, geheimen, certificaten en opslagaccounts. De gebruikte tags kunnen persoonlijke gegevens bevatten
- Azure Key Vault-toegangslogboeken bevatten object-ID's, [UPN's](../../active-directory/hybrid/plan-connect-userprincipalname.md)en IP-adressen voor elke REST API-aanroep
- Azure Key Vault diagnostische logboeken kunnen object-ID's en IP-adressen bevatten voor REST API-aanroepen

## <a name="deleting-customer-data"></a>Klantgegevens verwijderen

Dezelfde REST API's, Portal-ervaring en SDK's die worden gebruikt voor het maken van kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts, kunnen deze objecten ook bijwerken en verwijderen.

Met soft-delete kunt u verwijderde gegevens 90 dagen na verwijdering herstellen. Wanneer u een tijdelijke verwijderbewerking gebruikt, kunnen de gegevens permanent worden verwijderd voordat de bewaarperiode van 90 dagen is verstreken door een opstingsbewerking uit te voeren. Als de kluis of het abonnement is geconfigureerd om opstingsbewerkingen te blokkeren, is het niet mogelijk om gegevens permanent te verwijderen totdat de geplande bewaarperiode is verstreken.

## <a name="exporting-customer-data"></a>Klantgegevens exporteren

Met dezelfde REST API's, portalervaring en SDK's die worden gebruikt voor het maken van kluizen, sleutels, geheimen, certificaten en beheerde opslagaccounts kunt u deze objecten ook weergeven en exporteren.

Azure Key Vault toegangslogboeken is een optionele functie die kan worden ingeschakeld voor het genereren van logboeken voor elke REST API-aanroep. Deze logboeken worden overgedragen naar een opslagaccount in uw abonnement, waar u het retentiebeleid kunt toepassen dat voldoet aan de vereisten van uw organisatie.

Azure Key Vault diagnostische logboeken die persoonsgegevens bevatten, kunnen worden opgehaald door een exportaanvraag in te voeren in de portal voor gebruikers privacy. Deze aanvraag moet worden gedaan door de tenantbeheerder.

## <a name="next-steps"></a>Volgende stappen

- [Logboekregistratie van Azure Key Vault](logging.md)

- [Azure Key Vault: overzicht van voorlopig verwijderen](./key-vault-recovery.md)

- [Azure Key Vault sleutelbewerkingen](/rest/api/keyvault/key-operations)

- [Azure Key Vault geheime bewerkingen](/rest/api/keyvault/secret-operations)

- [Azure Key Vault certificaten en beleidsregels](/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault opslagaccountbewerkingen](/rest/api/keyvault/storage-account-key-operations)