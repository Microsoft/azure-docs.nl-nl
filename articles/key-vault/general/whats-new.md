---
title: Nieuwe functies voor Azure Key Vault
description: Recente updates voor Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 01/12/2020
ms.author: mbaldwin
ms.openlocfilehash: ef2c42ae76dc5e1511494635904228a8b574d483
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132185"
---
# <a name="whats-new-for-azure-key-vault"></a>Nieuwe functies voor Azure Key Vault

Dit zijn de nieuwe functies in Azure Key Vault. Nieuwe functies en verbeteringen worden ook aangekondigd in het [Key Vault-kanaal voor Azure-updates](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="october-2020"></a>Oktober 2020

> [!WARNING]
> Deze updates kunnen mogelijk Azure Key Vault-implementaties beïnvloeden.

Voor de ondersteuning van [voorlopig verwijderen nu standaard ingeschakeld](#soft-delete-on-by-default) zijn er twee wijzigingen aangebracht in de PowerShell-cmdlets van Azure Key Vault:

- De parameters DisableSoftDelete en EnableSoftDelete van [Update-AzKeyVault](/powershell/module/az.keyvault/update-azkeyvault) zijn afgeschaft.
- De uitvoer van de [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)-cmdlet heeft geen `SecretValueText`-kenmerk meer.

## <a name="july-2020"></a>Juli 2020

> [!WARNING]
> Deze twee updates kunnen mogelijk Azure Key Vault-implementaties beïnvloeden.

### <a name="soft-delete-on-by-default"></a>Voorlopig verwijderen standaard ingeschakeld

**Voor alle sleutelkluizen moet Voorlopig verwijderen worden ingeschakeld**, zowel voor nieuwe als bestaande. De optie om geen gebruik te maken van Voorlopig verwijderen, wordt de komende maanden afgeschaft. Raadpleeg het artikel [Voorlopig verwijderen wordt ingeschakeld in alle sleutelkluizen](soft-delete-change.md) voor de volledige details over deze wijziging die fouten kan veroorzaken, en voor de stappen voor het vinden en vooraf bijwerken van betrokken sleutelkluizen.

### <a name="azure-tls-certificate-changes"></a>Wijzigingen in Azure TLS-certificaat

Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze wijziging wordt doorgevoerd omdat de huidige CA-certificaten niet voldoen aan een van de forumbasislijnvereisten voor CA's/browsers.  Zie [wijzigingen in het Azure TLS-certificaat](../../security/fundamentals/tls-certificate-changes.md) voor volledige informatie.

## <a name="june-2020"></a>Juni 2020

Azure Monitor voor Key Vault is nu beschikbaar als preview-versie.  Azure Monitor biedt uitgebreide bewaking van uw sleutelkluizen door een uniforme weergave te bieden van uw aanvragen, prestaties, fouten en latentie in Key Vault. Zie [Azure Monitor voor Azure Key Vault (preview)](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="may-2020"></a>Mei 2020

Key Vault BYOK (Bring Your Own Key) is nu algemeen beschikbaar. Raadpleeg de [Azure Key Vault BYOK-specificatie](../keys/byok-specification.md) en leer hoe u [Met HSM beveiligde sleutels importeert in Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Maart 2020

Privé-eind punten zijn nu beschikbaar als preview-versie. Met Azure Private Link hebt u via een privé-eindpunt in uw virtuele netwerk toegang tot Azure Key Vault en in Azure gehoste services van klanten of partners.  Meer informatie over [Sleutelkluis integreren met Azure Private Link](private-link-service.md)

## <a name="2019"></a>2019

- Release van de Azure Key Vault-SDK's van de volgende generatie. Raadpleeg de quickstarts over Azure Key Vault-geheimen voor [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) en [Node.js](../secrets/quick-create-node.md) voor gebruiksvoorbeelden
- Nieuwe Azure-beleidsregels voor het beheren van sleutelkluiscertificaten. Zie de [Ingebouwde Azure Policy-definities voor Key Vault](../policy-reference.md).
- Azure Key Vault-extensie van virtuele machine is nu algemeen beschikbaar.  Zie [Key Vault-extensie van virtuele machine voor Linux](../../virtual-machines/extensions/key-vault-linux.md) en [Key Vault-extensie van virtuele machine voor Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Gebeurtenisgestuurd beheer van geheimen voor Azure Key Vault is nu beschikbaar in Azure Event Grid. Bekijk [het Event Grid-schema voor gebeurtenissen in Azure Key Vault](../../event-grid/event-schema-key-vault.md] en [Key Vault-meldingen ontvangen en beantwoorden met Azure Event Grid](event-grid-tutorial.md) voor meer informatie.

## <a name="2018"></a>2018

Nieuwe functies en integraties die dit jaar zijn uitgebracht:

- Integratie met Azure Functions. Zie [De rotatie van een geheim automatiseren](../secrets/tutorial-rotation.md) voor een voorbeeldscenario waarin [Azure Functions](../../azure-functions/index.yml) wordt gebruikt voor sleutelkluisbewerkingen.
- [Integratie met Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Hiermee biedt Azure Databricks nu ondersteuning voor twee geheimbereiktypen: Ondersteund in Azure Key Vault en Databricks. Raadpleeg [Een geheimbereik met ondersteuning in Azure Key Vault maken](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) voor meer informatie
- [Service-eindpunten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nieuwe functies die dit jaar zijn uitgebracht:

- Beheerde opslagaccountsleutels. Met de functie Opslagaccountsleutels is eenvoudigere integratie met Azure Storage toegevoegd. Bekijk het overzichtsonderwerp [Overzicht van Beheerde opslagaccountsleutels](../secrets/overview-storage-keys.md) voor meer informatie.
- Voorlopig verwijderen. Met de functie Voorlopig verwijderen is gegevensbescherming van uw sleutelkluizen en sleutelkluisobjecten verbeterd. Bekijk het overzichtsonderwerp [Overzicht van Voorlopig verwijderen](./soft-delete-overview.md) voor meer informatie.

## <a name="2015"></a>2015

Nieuwe functies die dit jaar zijn uitgebracht:
- Certificaatbeheer. Als functie toegevoegd aan de GA-versie 2015-06-01, op 26 september 2016.

Algemene beschikbaarheid (versie 2015-06-01) werd aangekondigd op 24 juni 2015. In deze release zijn de volgende wijzigingen aangebracht:
- Een sleutel verwijderen - veld 'gebruiken' is verwijderd.
- Informatie ophalen over een sleutel - veld 'gebruiken' is verwijderd.
- Een sleutel importeren in een kluis - veld 'gebruiken' is verwijderd.
- Een sleutel herstellen - veld 'gebruiken' is verwijderd.
- RSA_OAEP is gewijzigd in RSA-OAEP voor RSA-algoritmen. Zie [Over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).

De tweede preview-versie (versie 2015-02-01-preview) werd aangekondigd op 20 april 2015. Zie de blogpost [REST API-update](/archive/blogs/kv/rest-api-update) voor meer informatie. De volgende taken zijn bijgewerkt:

- De sleutels in een kluis vermelden - ondersteuning voor paginering toegevoegd aan bewerking.
- De versies van een sleutel vermelden - bewerking toegevoegd voor het vermelden van de versies van een sleutel.
- Geheimen in een kluis vermelden - ondersteuning voor paginering toegevoegd.
- Versies van een geheim vermelden - bewerking toegevoegd voor het vermelden van de versies van een geheim.
- Alle bewerkingen - gemaakt/bijgewerkte tijdstempels toegevoegd aan kenmerken.
- Een geheim maken - inhoudstype toegevoegd aan geheimen.
- Een sleutel maken - tags toegevoegd als optionele informatie.
- Een geheim maken - tags toegevoegd als optionele informatie.
- Een sleutel bijwerken - tags toegevoegd als optionele informatie.
- Een geheim bijwerken - tags toegevoegd als optionele informatie.
- De maximale grootte voor geheimen is gewijzigd van 10 K in 25 kB. Zie [Over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).

## <a name="2014"></a>2014

De eerste preview-versie (versie 2014-12-08-preview) werd aangekondigd op 8 januari 2015.

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende vragen hebt, kunt u contact met ons opnemen via [ondersteuning](https://azure.microsoft.com/support/options/).
