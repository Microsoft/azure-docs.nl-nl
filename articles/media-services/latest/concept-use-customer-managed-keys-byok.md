---
title: Uw eigen sleutel (door de klant beheerde sleutels) meenemen
description: U kunt een door de klant beheerde sleutel gebruiken (dat wil zeggen, uw eigen sleutel meenemen) met Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4564e28f76aebe7f708c2b6f68903fe67bcefe26
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698855"
---
# <a name="bring-your-own-key-customer-managed-keys-with-media-services"></a>Uw eigen sleutel (door de klant beheerde sleutels) meenemen met Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bring Your Own Key (BYOK) is een Azure Wide Initiative om klanten te helpen hun werk belastingen naar de cloud te verplaatsen. Met door de klant beheerde sleutels kunnen klanten zich houden aan de nalevings voorschriften van de industrie en wordt de Tenant isolatie van een service verbeterd. Het beheer van de versleutelings sleutels biedt klanten de mogelijkheid om onnodige toegang en controle en vertrouwen in micro soft-services te minimaliseren.

## <a name="keys-and-key-management"></a>Sleutels en sleutel beheer

U kunt uw eigen sleutel gebruiken met Media Services wanneer u de Media Services 2020-05-01-API gebruikt. Er wordt een standaard account sleutel gemaakt voor alle accounts die zijn versleuteld met een systeem sleutel die eigendom is van Media Services. Wanneer u uw eigen sleutel gebruikt, wordt de account sleutel versleuteld met uw sleutel. Inhouds sleutels worden versleuteld door de account sleutel. JobInputHttp-url's en validatie sleutels voor symmetrische tokens worden ook versleuteld.

:::image type="content" source="./media/customer-managed-key/customer-managed-key.svg" alt-text="Een door de klant beheerde sleutel vervangt een door een systeem beheerde sleutel":::

Media Services gebruikt de beheerde identiteit van het Media Services-account om uw sleutel te lezen uit een Key Vault waarvan u de eigenaar bent. Media Services vereist dat de Key Vault zich in dezelfde regio bevindt als het account en dat deze de functie voor voorlopig verwijderen en leegmaken van de beveiliging heeft ingeschakeld.

Uw sleutel kan een 2048, 3072 of een 4096 RSA-sleutel zijn, en zowel HSM als software sleutels worden ondersteund.

> [!NOTE]
> De EC-sleutels worden niet ondersteund.

U kunt een sleutel naam en sleutel versie opgeven, of alleen een sleutel naam. Wanneer u alleen een sleutel naam gebruikt, gebruikt Media Services de nieuwste sleutel versie. Nieuwe versies van klant sleutels worden automatisch gedetecteerd en de account sleutel wordt opnieuw versleuteld.

> [!WARNING]
> Media Services controleert de toegang tot de sleutel van de klant. Als de klant sleutel ontoegankelijk wordt (bijvoorbeeld omdat de sleutel is verwijderd of de Key Vault is verwijderd of de toegangs machtiging is verwijderd), wordt het account door Media Services overgezet naar de status van de klant sleutel die niet toegankelijk is (effectief uitschakelen van het account). Het account kan echter in deze status worden verwijderd. De enige ondersteunde bewerkingen zijn account GET, LIST en DELETE; alle andere aanvragen (code ring, streaming, enzovoort) zullen mislukken totdat de toegang tot de account sleutel is hersteld.

## <a name="double-encryption"></a>Dubbele versleuteling

Media Services ondersteunt automatisch dubbele versleuteling. Voor Data-at-rest gebruikt de eerste laag van versleuteling een door de klant beheerde sleutel of een door micro soft beheerde sleutel, afhankelijk van de `AccountEncryption` instelling van het account.  De tweede laag versleuteling voor Data-at-rest wordt automatisch met een afzonderlijke beheerde sleutel van micro soft gegeven. Zie voor meer informatie over dubbele versleuteling [Azure Double Encryption](../../security/fundamentals/double-encryption.md)(Engelstalig).

> [!NOTE]
> Dubbele versleuteling wordt automatisch ingeschakeld voor het Media Services-account. U moet de door de klant beheerde sleutel en dubbele versleuteling echter afzonderlijk configureren voor uw opslag account. Zie [Storege-versleuteling](../../storage/common/storage-service-encryption.md).

## <a name="tutorials"></a>Zelfstudies

- [De Azure Portal gebruiken om door klant beheerde sleutels of BYOK met Media Services](tutorial-byok-portal.md)
- [Gebruik door de klant beheerde sleutels of BYOK met Media Services rest API](tutorial-byok-postman.md).

## <a name="next-steps"></a>Volgende stappen

[Uw inhoud beveiligen met Media Services dynamische versleuteling](content-protection-overview.md)