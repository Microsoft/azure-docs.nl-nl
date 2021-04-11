---
title: Door de klant beheerde sleutels voor account versleuteling
titleSuffix: Azure Storage
description: U kunt uw eigen versleutelings sleutel gebruiken om de gegevens in uw opslag account te beveiligen. Wanneer u een door klant beheerde sleutel opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de sleutel waarmee uw gegevens worden versleuteld. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbeheer te beheren.
services: storage
author: tamram
ms.service: storage
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 07f8faf503bdea6be8263afa6240594956b61391
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059442"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Door de klant beheerde sleutels voor Azure Storage versleuteling

U kunt uw eigen versleutelings sleutel gebruiken om de gegevens in uw opslag account te beveiligen. Wanneer u een door klant beheerde sleutel opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de sleutel waarmee uw gegevens worden versleuteld. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbeheer te beheren.

U moet een van de volgende Azure-sleutel archieven gebruiken om uw door de klant beheerde sleutels op te slaan:

- [Azure Key Vault](../../key-vault/general/overview.md)
- [Azure Key Vault beheerde Hardware Security module (HSM) (preview-versie)](../../key-vault/managed-hsm/overview.md)

U kunt een eigen sleutel maken en deze opslaan in de sleutel kluis of beheerde HSM, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis of beheerde HSM moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden.

> [!IMPORTANT]
>
> Versleuteling met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.
>
> Azure Key Vault en Azure Key Vault beheerde HSM ondersteunen dezelfde Api's en beheer interfaces voor configuratie.

## <a name="about-customer-managed-keys"></a>Over door de klant beheerde sleutels

In het volgende diagram ziet u hoe Azure Storage Azure Active Directory en een sleutel kluis of beheerde HSM gebruikt om aanvragen te doen met de door de klant beheerde sleutel:

![Diagram waarin wordt getoond hoe door de klant beheerde sleutels in Azure Storage werken](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault beheerder verleent machtigingen voor de versleutelings sleutels aan de beheerde identiteit die is gekoppeld aan het opslag account.
2. Een Azure Storage-beheerder configureert versleuteling met een door de klant beheerde sleutel voor het opslag account.
3. Azure Storage gebruikt de beheerde identiteit die aan het opslag account is gekoppeld om de toegang tot Azure Key Vault via Azure Active Directory te verifiëren.
4. Azure Storage verloopt de versleutelings sleutel van het account met de klant sleutel in Azure Key Vault.
5. Voor lees-en schrijf bewerkingen verzendt Azure Storage aanvragen naar Azure Key Vault om de versleutelings sleutel van het account te decoderen om bewerkingen voor versleuteling en ontsleuteling uit te voeren.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Door de klant beheerde sleutels voor wacht rijen en tabellen

Gegevens die zijn opgeslagen in de wachtrij-en tabel opslag, worden niet automatisch beveiligd door een door de klant beheerde sleutel wanneer door de klant beheerde sleutels zijn ingeschakeld voor het opslag account. U kunt deze services eventueel configureren om te worden opgenomen in deze beveiliging op het moment dat u het opslag account maakt.

Zie voor meer informatie over het maken van een opslag account dat door de klant beheerde sleutels voor wacht rijen en tabellen ondersteunt [een account maken dat door de klant beheerde sleutels ondersteunt voor tabellen en wacht rijen](account-encryption-key-create.md).

Gegevens in Blob Storage en Azure Files worden altijd beveiligd door door de klant beheerde sleutels wanneer door de klant beheerde sleutels zijn geconfigureerd voor het opslag account.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Door de klant beheerde sleutels voor een opslag account inschakelen

Wanneer u een door de klant beheerde sleutel configureert, wordt in Azure Storage de hoofd gegevens versleutelings sleutel voor het account gepakt met de door de klant beheerde sleutel in de bijbehorende sleutel kluis of beheerde HSM. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en is direct van kracht.

Wanneer u door de klant beheerde sleutels in-of uitschakelt of wanneer u de sleutel of de sleutel versie wijzigt, wordt de beveiliging van de hoofd versleutelings sleutel gewijzigd, maar de gegevens in uw Azure Storage-account hoeven niet opnieuw te worden versleuteld.

Door de klant beheerde sleutels kunnen alleen worden ingeschakeld op bestaande opslag accounts. De sleutel kluis of beheerde HSM moet worden geconfigureerd om machtigingen te verlenen aan de beheerde identiteit die aan het opslag account is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat het opslag account is gemaakt.

U kunt op elk gewenst moment scha kelen tussen door de klant beheerde sleutels en door micro soft beheerde sleutels. Zie [about Encryption Key Management](storage-service-encryption.md#about-encryption-key-management)(Engelstalig) voor meer informatie over door micro soft beheerde sleutels.

Zie [Configure Encryption with door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault](customer-managed-keys-configure-key-vault.md)voor meer informatie over het configureren van Azure Storage versleuteling met door de klant beheerde sleutels in een sleutel kluis. Zie [versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault beheerde HSM (preview)](customer-managed-keys-configure-key-vault-hsm.md)voor informatie over het configureren van door de klant beheerde sleutels in een beheerde HSM.

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure AD. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw opslag account. Als u het abonnement, de resource groep of het opslag account vervolgens verplaatst van de ene Azure AD-Directory naar een andere, wordt de beheerde identiteit die aan het opslag account is gekoppeld, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

Azure Storage Encryption ondersteunt RSA-en RSA-HSM-sleutels met een grootte van 2048, 3072 en 4096. Zie [about Keys](../../key-vault/keys/about-keys.md)(Engelstalig) voor meer informatie over sleutels.

Er zijn kosten verbonden aan het gebruik van een sleutel kluis of beheerde HSM. Zie [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie.

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u versleuteling configureert met door de klant beheerde sleutels, hebt u twee opties voor het bijwerken van de sleutel versie:

- **De sleutel versie automatisch bijwerken:** Als u een door de klant beheerde sleutel automatisch wilt bijwerken wanneer er een nieuwe versie beschikbaar is, laat u de sleutel versie weg wanneer u versleuteling inschakelt met door de klant beheerde sleutels voor het opslag account. Als de sleutel versie wordt wegge laten, wordt Azure Storage de sleutel kluis of beheerde HSM Daily dagelijks gecontroleerd voor een nieuwe versie van een door de klant beheerde sleutel. Azure Storage maakt automatisch gebruik van de meest recente versie van de sleutel.
- **De sleutel versie hand matig bijwerken:** Als u een specifieke versie van een sleutel voor Azure Storage versleuteling wilt gebruiken, geeft u die sleutel versie op wanneer u versleuteling inschakelt met door de klant beheerde sleutels voor het opslag account. Als u de sleutel versie opgeeft, gebruikt Azure Storage die versie voor versleuteling totdat u de sleutel versie hand matig bijwerkt.

    Wanneer de sleutel versie expliciet is opgegeven, moet u het opslag account hand matig bijwerken om de nieuwe sleutel versie-URI te gebruiken wanneer een nieuwe versie wordt gemaakt. Raadpleeg [Configure Encryption with door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault](customer-managed-keys-configure-key-vault.md) of [Configureer versleuteling met door de klant beheerde sleutels die zijn opgeslagen in azure Key Vault beheerde HSM (preview)](customer-managed-keys-configure-key-vault-hsm.md)voor meer informatie over het bijwerken van het opslag account om een nieuwe versie van de sleutel te gebruiken.

Wanneer u de sleutel versie bijwerkt, wordt de beveiliging van de hoofd versleutelings sleutel gewijzigd, maar de gegevens in uw Azure Storage-account worden niet opnieuw versleuteld. Er is geen verdere actie vereist van de gebruiker.

> [!NOTE]
> Als u een sleutel wilt draaien, maakt u een nieuwe versie van de sleutel in de sleutel kluis of beheerde HSM volgens uw nalevings beleid. U kunt uw sleutel hand matig draaien of een functie maken om deze te draaien volgens een planning.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

U kunt de toegang tot het opslag account op elk gewenst moment intrekken voor de door de klant beheerde sleutel. Nadat de toegang tot door de klant beheerde sleutels is ingetrokken of nadat de sleutel is uitgeschakeld of verwijderd, kunnen clients geen bewerkingen aanroepen die lezen van of schrijven naar een BLOB of de meta gegevens ervan. Pogingen om een van de volgende bewerkingen aan te roepen, mislukken met fout code 403 (verboden) voor alle gebruikers:

- [Blobs vermelden](/rest/api/storageservices/list-blobs), indien aangeroepen met de `include=metadata` para meter voor de aanvraag-URI
- [BLOB ophalen](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blob-eigenschappen ophalen)
- [BLOB-meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Moment opname-BLOB](/rest/api/storageservices/snapshot-blob), wanneer aangeroepen met de `x-ms-meta-name` aanvraag header
- [Blob kopiëren](/rest/api/storageservices/copy-blob)
- [BLOB kopiëren van URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blob-laag instellen)
- [Blok keren](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [Blok van URL toevoegen](/rest/api/storageservices/append-block-from-url)
- [Blob plaatsen](/rest/api/storageservices/put-blob)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [BLOB voor incrementele kopie](/rest/api/storageservices/incremental-copy-blob)

Als u deze bewerkingen opnieuw wilt aanroepen, moet u de toegang tot de door de klant beheerde sleutel herstellen.

Alle gegevens bewerkingen die niet in deze sectie worden vermeld, kunnen door gaan nadat door de klant beheerde sleutels zijn ingetrokken of een sleutel wordt uitgeschakeld of verwijderd.

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u [Power shell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) of [Azure cli](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Door de klant beheerde sleutels voor Azure Managed disks

Door de klant beheerde sleutels zijn ook beschikbaar voor het beheren van versleuteling van Azure Managed disks. Door de klant beheerde sleutels gedragen zich anders voor Managed disks dan voor Azure Storage resources. Zie versleuteling aan de [server zijde van Azure Managed disks](../../virtual-machines/disk-encryption.md) voor Windows of [server side Encryption of Azure Managed disks](../../virtual-machines/disk-encryption.md) for Linux voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Versleuteling configureren met door de klant beheerde sleutels die zijn opgeslagen in Azure Key Vault beheerde HSM (preview-versie)](customer-managed-keys-configure-key-vault-hsm.md)
