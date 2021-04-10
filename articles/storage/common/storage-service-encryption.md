---
title: Azure Storage-versleuteling voor inactieve gegevens
description: Azure Storage beveiligt uw gegevens door deze automatisch te versleutelen voordat deze in de cloud worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van de gegevens in uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641123"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-versleuteling voor inactieve gegevens

Azure Storage gebruikt SSE (server side Encryption) om uw gegevens automatisch te versleutelen wanneer deze in de cloud worden bewaard. Azure Storage versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

## <a name="about-azure-storage-encryption"></a>Over Azure Storage versleuteling

Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Azure Storage versleuteling is ingeschakeld voor alle opslag accounts, met inbegrip van Resource Manager en klassieke opslag accounts. Azure Storage versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard worden beveiligd, hoeft u uw code of toepassingen niet te wijzigen om Azure Storage versleuteling te benutten.

Gegevens in een opslag account worden versleuteld, ongeacht de standaardlaag (Standard of Premium), de Access-laag (Hot of cool) of het implementatie model (Azure Resource Manager of klassiek). Alle blobs in de archief laag worden ook versleuteld. Alle Azure Storage redundantie opties ondersteunen versleuteling en alle gegevens in de primaire en secundaire regio's worden versleuteld wanneer geo-replicatie is ingeschakeld. Alle Azure Storage bronnen worden versleuteld, waaronder blobs, schijven, bestanden, wacht rijen en tabellen. Alle meta gegevens van objecten worden ook versleuteld. Er zijn geen extra kosten verbonden aan Azure Storage versleuteling.

Elke blok-blob, een toevoeg-BLOB of een pagina-blob die na 20 oktober 2017 is geschreven naar Azure Storage, is versleuteld. De blobs die vóór deze datum zijn gemaakt, worden versleuteld met een achtergrond proces. Als u de versleuteling wilt afdwingen van een blob die is gemaakt vóór 20 oktober 2017, kunt u de BLOB herschrijven. Zie [de versleutelings status van een BLOB controleren](../blobs/storage-blob-encryption-status.md)voor meer informatie over het controleren van de versleutelings status van een blob.

Zie [crypto GRAFIE API: Next Generation](/windows/desktop/seccng/cng-portal)(Engelstalig) voor meer informatie over de onderliggende cryptografische modules Azure Storage versleuteling.

Zie [server-side Encryption of Azure Managed disks](../../virtual-machines/disk-encryption.md)(Engelstalig) voor meer informatie over versleuteling en sleutel beheer voor Azure Managed disks.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Gegevens in een nieuw opslag account worden standaard versleuteld met door micro soft beheerde sleutels. U kunt door micro soft beheerde sleutels blijven gebruiken voor het versleutelen van uw gegevens of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling te beheren met uw eigen sleutels, hebt u twee opties. U kunt een van beide typen sleutel beheer of beide gebruiken:

- U kunt een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van gegevens in Blob Storage en in azure files. <sup>1, 2 door</sup> de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault of Azure Key Vault beheerde hardware security model (hsm) (preview). Zie door de [klant beheerde sleutels gebruiken voor Azure Storage versleuteling](./customer-managed-keys-overview.md)voor meer informatie over door de klant beheerde sleutels.
- U kunt een door de *klant opgegeven sleutel* voor Blob-opslag bewerkingen opgeven. Een client die een lees-of schrijf aanvraag uitvoert voor Blob Storage, kan een versleutelings sleutel bevatten op de aanvraag voor gedetailleerde controle over hoe BLOB-gegevens worden versleuteld en ontsleuteld. Zie [een versleutelings sleutel voor een aanvraag voor Blob-opslag bieden](../blobs/encryption-customer-provided-keys.md)voor meer informatie over door de klant geleverde sleutels.

De volgende tabel vergelijkt de opties voor sleutel beheer voor Azure Storage versleuteling.

| Para meter voor sleutel beheer | Door Microsoft beheerde sleutels | Door klant beheerde sleutels | Door de klant verschafte sleutels |
|--|--|--|--|
| Bewerkingen voor versleuteling/ontsleuteling | Azure | Azure | Azure |
| Azure Storage services ondersteund | Alles | Blob-opslag, Azure Files<sup>1, 2</sup> | Blob Storage |
| Sleutel opslag | Micro soft-sleutel archief | Azure Key Vault of Key Vault HSM | Eigen sleutel archief van de klant |
| Verantwoordelijkheid voor sleutel rotatie | Microsoft | Klant | Klant |
| Sleutel besturings element | Microsoft | Klant | Klant |

<sup>1</sup> Zie [een account maken dat door de klant beheerde sleutels voor wacht rijen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met behulp van een wachtrij opslag.<br />
<sup>2</sup> Zie voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met table-opslag [een account maken dat door de klant beheerde sleutels voor tabellen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Door micro soft beheerde sleutels worden naar behoren geroteerd per nalevings vereisten. Als u specifieke vereisten voor sleutel rotatie hebt, raadt micro soft u aan om over te stappen op door de klant beheerde sleutels zodat u de draaiing zelf kunt beheren en controleren.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Dubbele gegevens versleutelen met infrastructuur versleuteling

Klanten die behoefte hebben aan een hoge mate van zekerheid dat hun gegevens veilig zijn, kunnen ook 256-bits AES-versleuteling inschakelen op het niveau van de Azure Storage-infra structuur. Wanneer infrastructuur versleuteling is ingeschakeld, worden gegevens in een opslag account twee maal versleuteld &mdash; op het niveau van de service en eenmaal op het niveau van de infra structuur &mdash; met twee verschillende versleutelings algoritmen en twee verschillende sleutels. Dubbele versleuteling van Azure Storage gegevens beveiligt tegen een scenario waarbij een van de versleutelings algoritmen of-sleutels kan worden aangetast. In dit scenario blijft de extra laag versleuteling uw gegevens beveiligen.

Versleuteling op service niveau ondersteunt het gebruik van door micro soft beheerde sleutels of door de klant beheerde sleutels met Azure Key Vault. Versleuteling op infrastructuur niveau is afhankelijk van door micro soft beheerde sleutels en maakt altijd gebruik van een afzonderlijke sleutel.

Zie voor meer informatie over het maken van een opslag account dat infrastructuur versleuteling maakt [een opslag account maken waarvoor infrastructuur versleuteling is ingeschakeld voor dubbele versleuteling van gegevens](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
- [Versleutelings bereik voor Blob Storage](../blobs/encryption-scope-overview.md)
- [Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag](../blobs/encryption-customer-provided-keys.md)
