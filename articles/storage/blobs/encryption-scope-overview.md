---
title: Versleutelings bereik voor Blob-opslag (preview-versie)
description: Versleutelings bereiken bieden de mogelijkheid om versleuteling te beheren op het niveau van de container of een afzonderlijke blob. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten.
services: storage
author: tamram
ms.service: storage
ms.date: 09/22/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 881a7fc915ab986577599b85f8412fa8107f7902
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017386"
---
# <a name="encryption-scopes-for-blob-storage-preview"></a>Versleutelings bereik voor Blob-opslag (preview-versie)

Versleutelings bereiken bieden de mogelijkheid om versleuteling te beheren op het niveau van de container of een afzonderlijke blob. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten.

Een opslag account wordt standaard versleuteld met een sleutel die is afgestemd op het hele opslag account. Met een versleutelings bereik kunt u opgeven dat een of meer containers moeten worden versleuteld met een sleutel die alleen is gericht op die containers.

U kunt ervoor kiezen om door micro soft beheerde sleutels of door de klant beheerde sleutels te gebruiken die zijn opgeslagen in Azure Key Vault om de toegang tot de sleutel die uw gegevens versleutelen, te beveiligen en te beheren. Verschillende versleutelings bereiken voor hetzelfde opslag account kunnen gebruikmaken van door micro soft beheerde of door de klant beheerde sleutels.

Nadat u een versleutelings bereik hebt gemaakt, kunt u het versleutelings bereik opgeven voor een aanvraag voor het maken van een container of BLOB. Zie [encrypties maken en beheren (preview)](encryption-scope-manage.md)voor meer informatie over het maken van een versleutelings bereik.

> [!NOTE]
> Versleutelings scopes worden niet ondersteund met geografisch redundante opslag met lees toegang (RA-GRS) of met geozone-redundante opslag (RA-GZRS) met lees toegang tijdens de preview-versie.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> De preview-versie van het versleutelings bereik is alleen bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.
>
> Om onverwachte kosten te voor komen, moet u alle versleutelings scopes uitschakelen die u momenteel niet nodig hebt.

## <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Een container of Blob maken met een versleutelings bereik

Blobs die zijn gemaakt onder een versleutelings bereik, worden versleuteld met de sleutel die voor dat bereik is opgegeven. U kunt een versleutelings bereik opgeven voor een afzonderlijke BLOB wanneer u de BLOB maakt, of u kunt een standaard versleutelings bereik opgeven wanneer u een container maakt. Wanneer een standaard versleutelings bereik is opgegeven op het niveau van een container, worden alle blobs in die container versleuteld met de sleutel die is gekoppeld aan het standaard bereik.

Wanneer u een BLOB maakt in een container met een standaard versleutelings bereik, kunt u een versleutelings bereik opgeven dat het standaard versleutelings bereik overschrijft als de container zo is geconfigureerd dat onderdrukkingen van het standaard versleutelings bereik worden toegestaan. Als u onderdrukkingen van het standaard versleutelings bereik wilt voor komen, configureert u de container voor het weigeren van onderdrukkingen voor een afzonderlijke blob.

Lees bewerkingen op een blob die deel uitmaakt van een versleutelings bereik, worden op transparante wijze uitgevoerd, zolang het versleutelings bereik niet is uitgeschakeld.

## <a name="disable-an-encryption-scope"></a>Een versleutelings bereik uitschakelen

Wanneer u een versleutelings bereik uitschakelt, mislukken alle volgende Lees-of schrijf bewerkingen die zijn gemaakt met het versleutelings bereik met de HTTP-fout code 403 (verboden). Als u het versleutelings bereik opnieuw inschakelt, zullen lees-en schrijf bewerkingen weer normaal gesp roken gewoon door gaan.

Wanneer een versleutelings bereik is uitgeschakeld, wordt dit niet meer in rekening gebracht. Schakel eventuele versleutelings scopes uit die niet nodig zijn om onnodige kosten te voor komen.

Als uw versleutelings bereik is beveiligd met door de klant beheerde sleutels, kunt u ook de bijbehorende sleutel in de sleutel kluis verwijderen om het versleutelings bereik uit te scha kelen. Houd er rekening mee dat door de klant beheerde sleutels worden beveiligd door zacht verwijderen en de beveiliging op te schonen in de sleutel kluis en dat een verwijderde sleutel onderhevig is aan het gedrag dat is gedefinieerd door die eigenschappen. Zie een van de volgende onderwerpen in de Azure Key Vault-documentatie voor meer informatie:

- [Voorlopig verwijderen gebruiken met PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Voorlopig verwijderen gebruiken met CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Het is niet mogelijk om een versleutelings bereik te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
- [Versleutelings bereiken maken en beheren (preview-versie)](encryption-scope-manage.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](../common/customer-managed-keys-overview.md)
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)