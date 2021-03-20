---
title: Versleuteling van beveiligde activa in Azure Automation
description: Dit artikel bevat concepten voor het configureren van het Automation-account voor het gebruik van versleuteling.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 7d59ca60c7f90c227885927086511bd1f8ac7ca1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86185837"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Versleuteling van beveiligde activa in Azure Automation

Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden beveiligd in Azure Automation met behulp van meerdere coderings niveaus. Op basis van de sleutel op het hoogste niveau die wordt gebruikt voor de versleuteling, zijn er twee modellen voor versleuteling:

- Door micro soft beheerde sleutels gebruiken
- Sleutels gebruiken die u beheert

## <a name="microsoft-managed-keys"></a>Door micro soft beheerde sleutels

Uw Azure Automation-account maakt standaard gebruik van door micro soft beheerde sleutels.

Elke beveiligde Asset wordt versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel (gegevens versleutelings sleutel) die voor elk Automation-account wordt gegenereerd. Deze sleutels zijn versleuteld en opgeslagen in Azure Automation met behulp van nog een unieke sleutel die wordt gegenereerd voor elk account dat een account versleutelings sleutel (AEK) wordt genoemd. Deze versleutelings sleutels voor accounts zijn versleuteld en opgeslagen in Azure Automation met door micro soft beheerde sleutels. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Sleutels die u beheert met Key Vault (preview-versie)

U kunt versleuteling van beveiligde assets voor uw Automation-account beheren met uw eigen sleutels. Wanneer u een door de klant beheerde sleutel opgeeft op het niveau van het Automation-account, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de account versleutelings sleutel voor het Automation-account. Dit wordt op zijn beurt gebruikt voor het versleutelen en ontsleutelen van alle beveiligde assets. Door de klant beheerde sleutels bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels voor het beveiligen van uw beveiligde assets controleren.

Gebruik Azure Key Vault om door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren.  Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Azure Key Vault.

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Gebruik van door de klant beheerde sleutels voor een Automation-account

Wanneer u versleuteling gebruikt met door de klant beheerde sleutels voor een Automation-account, Azure Automation de versleutelings sleutel van het account verpakken met de door de klant beheerde sleutel in de bijbehorende sleutel kluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties. het account wordt onmiddellijk met de nieuwe sleutel gecodeerd, zonder enige vertraging.

Een nieuw Automation-account is altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat het account wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan het Automation-account. De beheerde identiteit is alleen beschikbaar nadat het opslag account is gemaakt.

Wanneer u de sleutel die wordt gebruikt voor Azure Automation beveiligde Asset Encryption, wijzigt door door de klant beheerde sleutels in of uit te scha kelen, de sleutel versie bij te werken of een andere sleutel op te geven, wordt de versleuteling van de account versleutelings sleutel gewijzigd, maar de beveiligde assets in uw Azure Automation account hoeven niet opnieuw te worden versleuteld.

> [!NOTE] 
> Als u door de klant beheerde sleutels wilt inschakelen, moet u Azure Automation REST API aanroepen maken met API versie 2020-01-13-preview

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Vereisten voor het gebruik van door de klant beheerde sleutels in Azure Automation

Voordat u door de klant beheerde sleutels voor een Automation-account inschakelt, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan:

 - De beheerd-sleutel van de klant wordt opgeslagen in een Azure Key Vault. 
 - Schakel de opties **voorlopig verwijderen** en **niet wissen** in de sleutel kluis in. Deze functies zijn vereist om sleutels te herstellen in geval van onbedoeld verwijderen.
 - Alleen RSA-sleutels worden ondersteund met Azure Automation versleuteling. Zie [informatie over Azure Key Vault sleutels, geheimen en certificaten](../key-vault/general/about-keys-secrets-certificates.md)voor meer informatie over sleutels.
- Het Automation-account en de sleutel kluis kunnen zich in verschillende abonnementen bevindt, maar moeten zich in dezelfde Tenant beAzure Active Directory.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Toewijzing van een identiteit aan het Automation-account

Als u door de klant beheerde sleutels wilt gebruiken met een Automation-account, moet uw Automation-account worden geverifieerd op basis van de sleutel kluis die door de klant beheerde sleutels opslaat. Azure Automation maakt gebruik van door het systeem toegewezen beheerde identiteiten om het account te verifiëren met Azure Key Vault. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie over beheerde identiteiten?

Configureer een door het systeem toegewezen beheerde identiteit aan het Automation-account met behulp van de volgende REST API-aanroep:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Aanvraagtekst:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

De door het systeem toegewezen identiteit voor het Automation-account wordt geretourneerd in een antwoord dat lijkt op het volgende:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Configuratie van het toegangs beleid voor Key Vault

Zodra een beheerde identiteit is toegewezen aan het Automation-account, configureert u de toegang tot de sleutel kluis die door de klant beheerde sleutels opslaat. Azure Automation vereist **Get**, **rerecover**, **wrapKey**, **sleutel uitpakken** op de door de klant beheerde sleutels.

Een dergelijk toegangs beleid kan worden ingesteld met behulp van de volgende REST API aanroep:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Aanvraagtekst:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> De velden **tenantId** en **objectId** moeten worden voorzien van de waarden **Identity. tenantId** en **Identity. principalId** van het antwoord van de beheerde identiteit voor het Automation-account.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>De configuratie van het Automation-account wijzigen voor het gebruik van door de klant beheerde sleutel

Ten slotte kunt u uw Automation-account overschakelen van door micro soft beheerde sleutels naar door de klant beheerde sleutels, met behulp van de volgende REST API aanroep:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Aanvraagtekst:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Voorbeeldantwoord

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotatie van een door de klant beheerde sleutel

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u het Automation-account bijwerken om de nieuwe sleutel-URI te gebruiken.

Als u de sleutel roteert, wordt het opnieuw versleutelen van beveiligde assets in het Automation-account niet geactiveerd. Er is geen verdere actie vereist.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Intrekking van toegang tot een door de klant beheerde sleutel

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of de Azure CLI. Zie [Azure Key Vault Power shell](/powershell/module/az.keyvault/) of [Azure Key Vault cli](/cli/azure/keyvault)(Engelstalig) voor meer informatie. Toegang intrekken effectief blokkeert de toegang tot alle beveiligde assets in het Automation-account, omdat de versleutelings sleutel niet toegankelijk is voor Azure Automation.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md)voor meer informatie Azure Key Vault.
- Zie [certificaten beheren in azure Automation](shared-resources/certificates.md)om met certificaten te werken.
- Zie [referenties beheren in azure Automation](shared-resources/credentials.md)voor informatie over het afhandelen van referenties.
- Als u Automation-variabelen wilt gebruiken, [beheert u variabelen in azure Automation](shared-resources/variables.md).
- Zie [verbindingen beheren in azure Automation](automation-connections.md)voor hulp bij het werken met verbindingen.
