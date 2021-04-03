---
title: Recovery Services kluis configuratie bijwerken met REST API
description: In dit artikel leert u hoe u de configuratie van de kluis kunt bijwerken met behulp van REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 19a335d17ee0aa5ff9f989556656f5cf20d2b1a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91567822"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Recovery Services kluis configuraties van Azure bijwerken met behulp van REST API

In dit artikel wordt beschreven hoe u back-up-gerelateerde configuraties in azure Recovery Services kluis bijwerkt met behulp van REST API.

## <a name="soft-delete-state"></a>Status voorlopig verwijderen

Het verwijderen van back-ups van een beveiligd item is een belang rijke bewerking die moet worden bewaakt. Ter bescherming tegen onbedoelde verwijderingen heeft Azure Recovery Services kluis een mogelijkheid tot tijdelijke verwijdering. Met deze mogelijkheid kunt u verwijderde back-ups, indien nodig, binnen een bepaalde periode na het verwijderen herstellen.

Maar er zijn scenario's waarin deze mogelijkheid niet vereist is. Een Azure Recovery Services kluis kan niet worden verwijderd als er back-upitems in zijn, zelfs als ze worden verwijderd. Dit kan een probleem vormen als de kluis onmiddellijk moet worden verwijderd. Bijvoorbeeld: implementatie bewerkingen schonen vaak de gemaakte resources in dezelfde werk stroom. Een implementatie kan een kluis maken, back-ups voor een item configureren, een test herstellen en vervolgens door gaan met het verwijderen van de back-upitems en de kluis. Als het verwijderen van de kluis mislukt, kan de volledige implementatie mislukken. Het uitschakelen van zacht verwijderen is de enige manier om direct verwijderen te garanderen.

Daarom moet u zorgvuldig bepalen of u het voorlopig verwijderen van een bepaalde kluis wilt uitschakelen, afhankelijk van het scenario. Zie het [artikel voorlopig verwijderen](backup-azure-security-feature-cloud.md)voor meer informatie.

### <a name="fetch-soft-delete-state-using-rest-api"></a>Status van voorlopig verwijderen ophalen met REST API

De voorlopig verwijderings status wordt standaard ingeschakeld voor een nieuw gemaakte Recovery Services kluis. Als u de status van zacht verwijderen voor een kluis wilt ophalen of bijwerken, gebruikt u het [rest API document](/rest/api/backup/backupresourcevaultconfigs) met betrekking tot configuratie van de back-upkluis

Als u de huidige status van het voorlopig verwijderen van een kluis wilt ophalen, gebruikt u de volgende *Get* -bewerking

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

De Get-URI heeft `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` para meters. In dit voor beeld `{vaultName}` is ' testVault ' en `{vaultresourceGroupName}` is ' testVaultRG '. Aangezien alle vereiste para meters in de URI worden opgegeven, is er geen afzonderlijke aanvraag tekst nodig.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="responses"></a>Antwoorden

Het geslaagde antwoord voor de GET-bewerking wordt hieronder weer gegeven:

|Naam  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Voorbeeld van een antwoord

Zodra de GET-aanvraag is verzonden, wordt een antwoord van 200 (geslaagd) geretourneerd.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Status van voorlopig verwijderen bijwerken met REST API

Als u de voorlopig verwijderings status van de Recovery Services kluis wilt bijwerken met behulp van REST API, gebruikt u de volgende *put* -bewerking

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-06-15
```

De put-URI heeft `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` para meters. In dit voor beeld `{vaultName}` is ' testVault ' en `{vaultresourceGroupName}` is ' testVaultRG '. Als de URI wordt vervangen door de bovenstaande waarden, ziet de URI er als volgt uit.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-06-15
```

#### <a name="create-the-request-body"></a>De aanvraag tekst maken

De volgende algemene definities worden gebruikt voor het maken van een aanvraag tekst

Raadpleeg [de rest API-documentatie](/rest/api/backup/backupresourcevaultconfigs/update#request-body) voor meer informatie.

|Name  |Vereist  |Type  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   Tekenreeks      |  Optionele eTag       |
|location     |  true       |Tekenreeks         |   Resourcelocatie      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Eigenschappen van de kluis       |
|tags     |         | Object        |     Resourcetags    |

#### <a name="example-request-body"></a>Voorbeeld aanvraag tekst

Het volgende voor beeld wordt gebruikt om de status van zacht verwijderen bij te werken naar disabled.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>Reacties voor de PATCH bewerking

Het geslaagde antwoord voor de PATCH bewerking wordt hieronder weer gegeven:

|Naam  |Type  |Description  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response-for-the-patch-operation"></a>Voorbeeld reactie voor de PATCH bewerking

Zodra de PATCH aanvraag is verzonden, wordt een antwoord van 200 (geslaagd) geretourneerd.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

[Maak een back-upbeleid voor het maken van een back-up van een virtuele Azure-machine in deze kluis](backup-azure-arm-userestapi-createorupdatepolicy.md).

Raadpleeg de volgende documenten voor meer informatie over de REST-Api's van Azure:

- [REST API Azure Recovery Services provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
