---
title: Versleutelings bereik voor Blob Storage
description: Versleutelings bereiken bieden de mogelijkheid om versleuteling te beheren op het niveau van de container of een afzonderlijke blob. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 16a600d7caf65f880ffb5c2a2abfe5a9774a7795
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640468"
---
# <a name="encryption-scopes-for-blob-storage"></a>Versleutelings bereik voor Blob Storage

Met versleutelings scopes kunt u versleuteling beheren met een sleutel die is afgestemd op een container of een afzonderlijke blob. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten.

Zie voor meer informatie over het werken met versleutelings bereiken [maken en beheren van versleutelings scopes](encryption-scope-manage.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-encryption-scopes-work"></a>Hoe versleutelings bereiken werken

Een opslag account wordt standaard versleuteld met een sleutel die is afgestemd op het hele opslag account. Wanneer u een versleutelings bereik definieert, geeft u een sleutel op die kan worden toegewezen aan een container of een afzonderlijke blob. Wanneer het versleutelings bereik wordt toegepast op een blob, wordt de blob met die sleutel versleuteld. Wanneer het versleutelings bereik wordt toegepast op een container, fungeert dit als het standaard bereik voor blobs in die container, zodat alle blobs die naar die container worden geüpload, kunnen worden versleuteld met dezelfde sleutel. De container kan worden geconfigureerd om het standaard versleutelings bereik voor alle blobs in de container af te dwingen, of om een afzonderlijke BLOB te uploaden naar de container met een ander versleutelings bereik dan de standaard waarde.

Lees bewerkingen op een blob die is gemaakt met een versleutelings bereik, worden op transparante wijze uitgevoerd, zolang het versleutelings bereik niet is uitgeschakeld.

### <a name="key-management"></a>Sleutelbeheer

Wanneer u een versleutelings bereik definieert, kunt u opgeven of het bereik is beveiligd met een door micro soft beheerde sleutel of met een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault. Verschillende versleutelings bereiken voor hetzelfde opslag account kunnen gebruikmaken van door micro soft beheerde of door de klant beheerde sleutels. U kunt ook overschakelen naar het type sleutel dat wordt gebruikt voor het beveiligen van een versleutelings bereik van een door de klant beheerde sleutel naar een door micro soft beheerde sleutel, of andersom, op elk gewenst moment. Zie door de klant [beheerde sleutels voor Azure Storage versleuteling](../common/customer-managed-keys-overview.md)voor meer informatie over door de klant beheerde sleutels. Zie [about Encryption Key Management](../common/storage-service-encryption.md#about-encryption-key-management)(Engelstalig) voor meer informatie over door micro soft beheerde sleutels.

Als u een versleutelings bereik definieert met een door de klant beheerde sleutel, kunt u ervoor kiezen om de sleutel versie automatisch of hand matig bij te werken. Als u ervoor kiest om de sleutel versie automatisch bij te werken, controleert Azure Storage de sleutel kluis of beheerde HSM Daily dagelijks voor een nieuwe versie van de door de klant beheerde sleutel en wordt de sleutel automatisch bijgewerkt naar de nieuwste versie. Zie voor meer informatie over het bijwerken van de sleutel versie voor een door de klant beheerde sleutel [de sleutel versie bijwerken](../common/customer-managed-keys-overview.md#update-the-key-version).

Een opslag account kan Maxi maal 10.000 versleutelings bereiken hebben die zijn beveiligd met door de klant beheerde sleutels waarvoor de sleutel versie automatisch wordt bijgewerkt. Als uw opslag account al 10.000 versleutelings bereiken heeft die zijn beveiligd met door de klant beheerde sleutels die automatisch worden bijgewerkt, moet de sleutel versie hand matig worden bijgewerkt voor eventuele extra versleutelings scopes die zijn beveiligd met door de klant beheerde sleutels.  

### <a name="encryption-scopes-for-containers-and-blobs"></a>Versleutelings bereik voor containers en blobs

Wanneer u een container maakt, kunt u een standaard versleutelings bereik opgeven voor de blobs die vervolgens naar die container worden geüpload. Wanneer u een standaard versleutelings bereik voor een container opgeeft, kunt u bepalen hoe het standaard versleutelings bereik wordt afgedwongen:

- U kunt vereisen dat alle blobs die naar de container worden geüpload, gebruikmaken van het standaard versleutelings bereik. In dit geval wordt elke Blob in de container versleuteld met dezelfde sleutel.
- U kunt een client toestaan het standaard versleutelings bereik voor de container te overschrijven, zodat een BLOB kan worden geüpload met een ander versleutelings bereik dan het standaard bereik. In dit geval kunnen de blobs in de container worden versleuteld met verschillende sleutels.

De volgende tabel bevat een overzicht van het gedrag van een BLOB-upload bewerking, afhankelijk van hoe het standaard versleutelings bereik is geconfigureerd voor de container:

| Het versleutelings bereik dat in de container is gedefinieerd, is... | Een BLOB uploaden met het standaard versleutelings bereik... | Een BLOB uploaden met een ander versleutelings bereik dan het standaard bereik... |
|--|--|--|
| Een standaard versleutelings bereik met onderdrukkingen toegestaan | Slaagt | Slaagt |
| Een standaard versleutelings bereik met onderdrukkingen is niet toegestaan | Slaagt | Fouten |

Er moet een standaard versleutelings bereik worden opgegeven voor een container op het moment dat de container wordt gemaakt.

Als er geen standaard versleutelings bereik is opgegeven voor de container, kunt u een BLOB uploaden met behulp van een versleutelings bereik dat u hebt gedefinieerd voor het opslag account. Het versleutelings bereik moet worden opgegeven op het moment dat de BLOB wordt geüpload.

## <a name="disabling-an-encryption-scope"></a>Een versleutelings bereik uitschakelen

Wanneer u een versleutelings bereik uitschakelt, mislukken alle volgende Lees-of schrijf bewerkingen die zijn gemaakt met het versleutelings bereik met de HTTP-fout code 403 (verboden). Als u het versleutelings bereik opnieuw inschakelt, zullen lees-en schrijf bewerkingen weer normaal gesp roken gewoon door gaan.

Wanneer een versleutelings bereik is uitgeschakeld, wordt dit niet meer in rekening gebracht. Schakel eventuele versleutelings scopes uit die niet nodig zijn om onnodige kosten te voor komen.

Als uw versleutelings bereik is beveiligd met een door de klant beheerde sleutel en u de sleutel in de sleutel kluis verwijdert, worden de gegevens niet meer toegankelijk. Zorg er ook voor dat u het versleutelings bereik uitschakelt om te voor komen dat er wordt gefactureerd.

Houd er rekening mee dat door de klant beheerde sleutels worden beveiligd door zacht verwijderen en de beveiliging op te schonen in de sleutel kluis en dat een verwijderde sleutel onderhevig is aan het gedrag dat is gedefinieerd door die eigenschappen. Zie een van de volgende onderwerpen in de Azure Key Vault-documentatie voor meer informatie:

- [Voorlopig verwijderen gebruiken met PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Voorlopig verwijderen gebruiken met CLI](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> Het is niet mogelijk om een versleutelings bereik te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
- [Versleutelings scopes maken en beheren](encryption-scope-manage.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../common/customer-managed-keys-overview.md)
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)