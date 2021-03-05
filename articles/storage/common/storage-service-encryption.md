---
title: Azure Storage-versleuteling voor inactieve gegevens
description: Azure Storage beveiligt uw gegevens door deze automatisch te versleutelen voordat deze in de cloud worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van de gegevens in uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: b2471ccd2a412c7cbae9d4e59412ac055697e3d7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180357"
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

## <a name="encryption-scopes-for-blob-storage-preview"></a>Versleutelings bereik voor Blob-opslag (preview-versie)

Een opslag account wordt standaard versleuteld met een sleutel die is afgestemd op het opslag account. U kunt ervoor kiezen om door micro soft beheerde sleutels of door de klant beheerde sleutels te gebruiken die zijn opgeslagen in Azure Key Vault om de toegang tot de sleutel die uw gegevens versleutelen, te beveiligen en te beheren.

Met versleutelings scopes kunt u optioneel versleuteling beheren op het niveau van de container of een afzonderlijke blob. U kunt versleutelings scopes gebruiken om beveiligde grenzen te maken tussen gegevens die zich in hetzelfde opslag account bevinden, maar deel uitmaakt van verschillende klanten.

U kunt een of meer versleutelings zoekbereiken maken voor een opslag account met behulp van de resource provider Azure Storage. Wanneer u een versleutelings bereik maakt, geeft u op of het bereik is beveiligd met een door micro soft beheerde sleutel of met een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault. Verschillende versleutelings bereiken voor hetzelfde opslag account kunnen gebruikmaken van door micro soft beheerde of door de klant beheerde sleutels.

Nadat u een versleutelings bereik hebt gemaakt, kunt u het versleutelings bereik opgeven voor een aanvraag voor het maken van een container of BLOB. Zie [encrypties maken en beheren (preview)](../blobs/encryption-scope-manage.md)voor meer informatie over het maken van een versleutelings bereik.

> [!NOTE]
> Versleutelings bereiken worden niet ondersteund met geografisch redundante opslag met lees toegang (RA-GRS) en accounts met geozone-redundante opslag (RA-GZRS) met lees toegang tijdens de preview-versie.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!IMPORTANT]
> De preview-versie van het versleutelings bereik is alleen bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.
>
> Om onverwachte kosten te voor komen, moet u alle versleutelings scopes uitschakelen die u momenteel niet nodig hebt.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Een container of Blob maken met een versleutelings bereik

Blobs die zijn gemaakt onder een versleutelings bereik, worden versleuteld met de sleutel die voor dat bereik is opgegeven. U kunt een versleutelings bereik opgeven voor een afzonderlijke BLOB wanneer u de BLOB maakt, of u kunt een standaard versleutelings bereik opgeven wanneer u een container maakt. Wanneer een standaard versleutelings bereik is opgegeven op het niveau van een container, worden alle blobs in die container versleuteld met de sleutel die is gekoppeld aan het standaard bereik.

Wanneer u een BLOB maakt in een container met een standaard versleutelings bereik, kunt u een versleutelings bereik opgeven dat het standaard versleutelings bereik overschrijft als de container zo is geconfigureerd dat onderdrukkingen van het standaard versleutelings bereik worden toegestaan. Als u onderdrukkingen van het standaard versleutelings bereik wilt voor komen, configureert u de container voor het weigeren van onderdrukkingen voor een afzonderlijke blob.

Lees bewerkingen op een blob die deel uitmaakt van een versleutelings bereik, worden op transparante wijze uitgevoerd, zolang het versleutelings bereik niet is uitgeschakeld.

### <a name="disable-an-encryption-scope"></a>Een versleutelings bereik uitschakelen

Wanneer u een versleutelings bereik uitschakelt, mislukken alle volgende Lees-of schrijf bewerkingen die zijn gemaakt met het versleutelings bereik met de HTTP-fout code 403 (verboden). Als u het versleutelings bereik opnieuw inschakelt, zullen lees-en schrijf bewerkingen weer normaal gesp roken gewoon door gaan.

Wanneer een versleutelings bereik is uitgeschakeld, wordt dit niet meer in rekening gebracht. Schakel eventuele versleutelings scopes uit die niet nodig zijn om onnodige kosten te voor komen.

Als uw versleutelings bereik is beveiligd met door de klant beheerde sleutels voor Azure Key Vault, kunt u ook de bijbehorende sleutel in de sleutel kluis verwijderen om het versleutelings bereik uit te scha kelen. Houd er rekening mee dat door de klant beheerde sleutels in Azure Key Vault worden beveiligd door zacht verwijderen en de beveiliging op te schonen, en dat een verwijderde sleutel onderhevig is aan het gedrag dat is gedefinieerd door die eigenschappen. Zie een van de volgende onderwerpen in de Azure Key Vault-documentatie voor meer informatie:

- [Voorlopig verwijderen gebruiken met PowerShell](../../key-vault/general/key-vault-recovery.md)
- [Voorlopig verwijderen gebruiken met CLI](../../key-vault/general/key-vault-recovery.md)

> [!NOTE]
> Het is niet mogelijk om een versleutelings bereik te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
- [Versleutelings bereik voor Blob-opslag (preview-versie)](../blobs/encryption-scope-overview.md)
