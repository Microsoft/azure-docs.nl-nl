---
title: Azure Key Vault als Event Grid bron
description: Hierin worden de eigenschappen en schema's beschreven die voor Azure Key Vault gebeurtenissen worden gegeven met Azure Event Grid
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: ea8821b15000b74a10f28730ccf82b538e7819e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363403"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault als Event Grid bron

In dit artikel vindt u de eigenschappen en het schema voor gebeurtenissen in [Azure Key Vault](../key-vault/index.yml). Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's.


## <a name="available-event-types"></a>Beschik bare gebeurtenis typen

Een Azure Key Vault-account genereert de volgende gebeurtenis typen:

| Volledige naam van de gebeurtenis | Weergave naam van gebeurtenis | Description |
| ---------- | ----------- |---|
| Micro soft. CertificateNewVersionCreated | De nieuwe versie van het certificaat is gemaakt | Wordt geactiveerd wanneer een nieuw certificaat of nieuwe certificaat versie wordt gemaakt. |
| Micro soft. CertificateNearExpiry | Certificaat bijna verlopen | Wordt geactiveerd wanneer de huidige versie van het certificaat bijna verloopt. (De gebeurtenis wordt geactiveerd 30 dagen voor de verval datum.) |
| Micro soft. CertificateExpired | Certificaat is verlopen | Wordt geactiveerd wanneer het certificaat is verlopen. |
| Micro soft. KeyNewVersionCreated | Nieuwe versie van de sleutel gemaakt | Wordt geactiveerd wanneer een nieuwe sleutel of nieuwe sleutel versie wordt gemaakt. |
| Micro soft. KeyNearExpiry | Sleutel bijna verlopen | Wordt geactiveerd wanneer de huidige versie van een sleutel bijna verloopt. (De gebeurtenis wordt geactiveerd 30 dagen voor de verval datum.) |
| Micro soft. de sleutel kluis. | De sleutel is verlopen | Wordt geactiveerd wanneer een sleutel is verlopen. |
| Micro soft. SecretNewVersionCreated | Nieuwe geheime versie gemaakt | Wordt geactiveerd wanneer een nieuw geheim of nieuwe geheime versie wordt gemaakt. |
| Micro soft. SecretNearExpiry | Geheim bijna verlopen | Wordt geactiveerd wanneer de huidige versie van een geheim verloopt. (De gebeurtenis wordt geactiveerd 30 dagen voor de verval datum.) |
| Micro soft. SecretExpired | Geheim verlopen | Wordt geactiveerd wanneer een geheim is verlopen. |
| Micro soft. VaultAccessPolicyChanged | Beleid voor kluis toegang is gewijzigd | Wordt geactiveerd wanneer een toegangs beleid op Key Vault gewijzigd. Het bevat een scenario waarin Key Vault machtigings model is gewijzigd in/van Azure op rollen gebaseerd toegangs beheer.   |

## <a name="event-examples"></a>Gebeurtenis voorbeelden

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)

In het volgende voor beeld wordt schema voor **micro soft. SecretNewVersionCreated** weer gegeven:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

In het volgende voor beeld wordt schema voor **micro soft. SecretNewVersionCreated** weer gegeven:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "source":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "type":"Microsoft.KeyVault.SecretNewVersionCreated",
      "time":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "specversion":"1.0"
   }
]
```

---

### <a name="event-properties"></a>Gebeurtenis eigenschappen

# <a name="event-grid-event-schema"></a>[Event Grid-gebeurtenisschema](#tab/event-grid-event-schema)
Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `topic` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `eventType` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `eventTime` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Gebeurtenis gegevens voor app-configuratie. |
| `dataVersion` | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| `metadataVersion` | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |


# <a name="cloud-event-schema"></a>[Cloudgebeurtenisschema](#tab/cloud-event-schema)

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| `source` | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| `subject` | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| `type` | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| `time` | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| `id` | tekenreeks | De unieke id voor de gebeurtenis. |
| `data` | object | Gebeurtenis gegevens voor app-configuratie. |
| `specversion` | tekenreeks | CloudEvents-schema specificatie versie. |

---
 

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| ---------- | ----------- |---|
| `id` | tekenreeks | De ID van het object dat deze gebeurtenis heeft geactiveerd |
| `vaultName` | tekenreeks | De naam van de sleutel kluis van het object dat deze gebeurtenis heeft geactiveerd |
| `objectType` | tekenreeks | Het type van het object dat deze gebeurtenis heeft geactiveerd |
| `objectName` | tekenreeks | De naam van het object dat deze gebeurtenis heeft geactiveerd |
| `version` | tekenreeks | De versie van het object dat deze gebeurtenis heeft geactiveerd |
| `nbf` | getal | De niet-voor-datum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |
| `exp` | getal | De verval datum in seconden sinds 1970-01-01T00:00:00Z van het object dat deze gebeurtenis heeft geactiveerd |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel  |Beschrijving  |
|---------|---------|
| [Key Vault gebeurtenissen bewaken met Azure Event Grid](../key-vault/general/event-grid-overview.md) | Overzicht van het integreren van Key Vault met Event Grid. |
| [Zelf studie: Key Vault gebeurtenissen maken en bewaken met Event Grid](../key-vault/general/event-grid-logicapps.md) | Meer informatie over het instellen van Event Grid meldingen voor Key Vault. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md)voor een inleiding tot Azure Event grid.
* Zie [Event grid-abonnements schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie over Key Vault.

