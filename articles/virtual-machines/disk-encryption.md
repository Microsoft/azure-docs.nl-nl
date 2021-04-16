---
title: Versleuteling aan de serverzijde van beheerde Azure-schijven
description: Azure Storage uw gegevens beveiligen door ze at-rest te versleutelen voordat ze worden opgeslagen in Opslagclusters. U kunt door de klant beheerde sleutels gebruiken om versleuteling met uw eigen sleutels te beheren, of u kunt vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw beheerde schijven.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531538"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Versleuteling aan de serverzijde van Azure Disk Storage

De meeste beheerde Azure-schijven worden versleuteld met Azure Storage-versleuteling, die gebruikmaakt van SSE (Server Side Encryption) om uw gegevens te beveiligen en u te helpen voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. Azure Storage versleuteling worden uw gegevens die zijn opgeslagen op beheerde Azure-schijven (besturingssysteem- en gegevensschijven) standaard automatisch versleuteld wanneer ze naar de cloud worden opgeslagen. Schijven met versleuteling op de host ingeschakeld, worden echter niet versleuteld via Azure Storage. Voor schijven met versleuteling op de host ingeschakeld, biedt de server die als host voor uw VM de versleuteling voor uw gegevens host en die versleutelde gegevens naar de Azure Storage.

Gegevens in beheerde Azure-schijven worden transparant versleuteld met [](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)256-bits AES-versleuteling, een van de sterkste beschikbare blokcoderingen en voldoen aan FIPS 140-2. Zie [Cryptography API: Next Generation (Cryptografie-API: volgende](/windows/desktop/seccng/cng-portal) generatie) voor meer informatie over de onderliggende cryptografische modules van beheerde Azure-schijven

Azure Storage versleuteling heeft geen invloed op de prestaties van beheerde schijven en er zijn geen extra kosten. Zie Voor meer informatie over Azure Storage-versleuteling [Azure Storage versleuteling.](/azure/storage/common/storage-service-encryption)

> [!NOTE]
> Tijdelijke schijven zijn geen beheerde schijven en worden niet versleuteld door SSE, tenzij u versleuteling op de host inschakelen.

## <a name="about-encryption-key-management"></a>Over versleutelingssleutelbeheer

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met  uw eigen sleutels te beheren, kunt u een door de klant beheerde sleutel opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in beheerde schijven. 

In de volgende secties worden alle opties voor sleutelbeheer uitgebreider beschreven.

### <a name="platform-managed-keys"></a>Door het platform beheerde sleutels

Beheerde schijven maken standaard gebruik van door het platform beheerde versleutelingssleutels. Alle beheerde schijven, momentopnamen, afbeeldingen en gegevens die naar bestaande beheerde schijven worden geschreven, worden automatisch versleuteld in rust met door het platform beheerde sleutels.

### <a name="customer-managed-keys"></a>Door klant beheerde sleutels

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Beperkingen

Voor nu gelden voor door de klant beheerde sleutels de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omdraaien, moet u alle gegevens met behulp van de [Azure PowerShell-module of](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) [de Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)kopiëren naar een volledig andere beheerde schijf die geen door de klant beheerde sleutels gebruikt.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Ondersteunde regio’s

Door de klant beheerde sleutels zijn beschikbaar in alle regio's waar beheerde schijven beschikbaar zijn.

Automatische sleutelrotatie is in preview en is alleen beschikbaar in de volgende regio's:

- VS - oost
- VS - oost 2
- VS - zuid-centraal
- VS - west
- VS - west 2
- Europa - noord
- Europa -west
- Frankrijk - centraal

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources. Als u het abonnement, de resourcegroep of de beheerde schijf vervolgens van de ene Azure AD-directory naar de andere verplaatst, wordt de beheerde identiteit die is gekoppeld aan beheerde schijven niet overgedragen naar de nieuwe tenant, waardoor door de klant beheerde sleutels mogelijk niet meer werken. Zie Een abonnement overdragen [tussen Azure AD-directories voor meer informatie.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

Als u door de klant beheerde sleutels voor beheerde schijven wilt inschakelen, bekijkt u onze artikelen over het inschakelen ervan met de [Azure PowerShell-module,](windows/disks-enable-customer-managed-keys-powershell.md) [de Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) of de [Azure Portal](disks-enable-customer-managed-keys-portal.md). Zie Een Azure Key Vault en [DiskEncryptionSet](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)instellen met automatische sleutelrotatie (preview) voor meer informatie over het inschakelen van door de klant beheerde sleutels met automatische sleutelrotatie.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Versleuteling op host: end-to-end-versleuteling voor uw VM-gegevens

Wanneer u versleuteling op de host inschakelen, wordt die versleuteling gestart op de VM-host zelf, de Azure-server waar uw VM aan is toegewezen. De gegevens voor uw tijdelijke schijf en os/gegevensschijfcache worden opgeslagen op die VM-host. Nadat versleuteling op de host is inschakelen, worden al deze gegevens 'at rest' versleuteld en worden ze versleuteld naar de Storage-service, waar deze worden opgeslagen. Versleuteling op de host versleutelt uw gegevens van end-to-end. Versleuteling op de host maakt geen gebruik van de CPU van uw VM en heeft geen invloed op de prestaties van uw VM. 

Tijdelijke schijven en tijdelijke besturingssysteemschijven worden in rust versleuteld met door het platform beheerde sleutels wanneer u end-to-end-versleuteling inschakelen. De caches van het besturingssysteem en de gegevensschijf worden in rust versleuteld met door de klant beheerde of door het platform beheerde sleutels, afhankelijk van het geselecteerde type schijfversleuteling. Als een schijf bijvoorbeeld is versleuteld met door de klant beheerde sleutels, wordt de cache voor de schijf versleuteld met door de klant beheerde sleutels en als een schijf is versleuteld met door het platform beheerde sleutels, wordt de cache voor de schijf versleuteld met door het platform beheerde sleutels.

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Mogelijk vindt u de VM-grootten ook programmatisch. Als u wilt weten hoe u deze programmatisch kunt ophalen, raadpleegt u de sectie Ondersteunde VM-grootten zoeken in de artikelen [Azure PowerShell-module](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) of [Azure CLI.](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)

Als u end-to-end-versleuteling wilt inschakelen met behulp van versleuteling op de host, bekijkt u onze artikelen over het inschakelen ervan met de [Azure PowerShell-module,](windows/disks-enable-host-based-encryption-powershell.md) [de Azure CLI](linux/disks-enable-host-based-encryption-cli.md)of de [Azure Portal](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Dubbele versleuteling in rust

Klanten met hoge beveiligingsgevoelige gegevens die zich zorgen maken over het risico dat een bepaald versleutelingsalgoritme, een bepaalde implementatie of sleutel waarmee wordt geklaagd, kunnen nu kiezen voor een extra versleutelingslaag met behulp van een ander versleutelingsalgoritme/-modus op de infrastructuurlaag met behulp van door het platform beheerde versleutelingssleutels. Deze nieuwe laag kan worden toegepast op persistente besturingssysteem- en gegevensschijven, momentopnamen en afbeeldingen, die allemaal in rust worden versleuteld met dubbele versleuteling.

### <a name="supported-regions"></a>Ondersteunde regio’s

Dubbele versleuteling is beschikbaar in alle regio's waar beheerde schijven beschikbaar zijn.

Als u dubbele versleuteling-at-rest voor beheerde schijven wilt inschakelen, zie onze artikelen over het inschakelen ervan met de [Azure PowerShell-module,](windows/disks-enable-double-encryption-at-rest-powershell.md) [de Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) of de [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de serverzijde versus Azure-schijfversleuteling

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [DM-Crypt-functie](https://en.wikipedia.org/wiki/Dm-crypt) van Linux of de [BitLocker-functie](/windows/security/information-protection/bitlocker/bitlocker-overview) van Windows om beheerde schijven te versleutelen met door de klant beheerde sleutels binnen de gast-VM.  Versleuteling aan de serverzijde met door de klant beheerde sleutels verbetert de ADE doordat u besturingssysteemtypen en -afbeeldingen voor uw VM's kunt gebruiken door gegevens in de Storage-service te versleutelen.
> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources. Als u het abonnement, de resourcegroep of de beheerde schijf vervolgens van de ene Azure AD-directory naar een andere verplaatst, wordt de beheerde identiteit die is gekoppeld aan beheerde schijven niet overgedragen naar de nieuwe tenant, waardoor door de klant beheerde sleutels mogelijk niet meer werken. Zie Een abonnement overdragen [tussen Azure AD-directories voor meer informatie.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="next-steps"></a>Volgende stappen

- Schakel end-to-end-versleuteling in met behulp van versleuteling op de host met de [Azure PowerShell-module](windows/disks-enable-host-based-encryption-powershell.md), [de Azure CLI](linux/disks-enable-host-based-encryption-cli.md)of de [Azure Portal](disks-enable-host-based-encryption-portal.md).
- Schakel dubbele versleuteling -at-rest in voor beheerde schijven met de [Azure PowerShell module](windows/disks-enable-double-encryption-at-rest-powershell.md), [de Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) of de [Azure Portal](disks-enable-double-encryption-at-rest-portal.md).
- Door de klant beheerde sleutels voor beheerde schijven inschakelen met de [Azure PowerShell-module](windows/disks-enable-customer-managed-keys-powershell.md), [de Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) of de [Azure Portal](disks-enable-customer-managed-keys-portal.md).
- [Verken de Azure Resource Manager voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../key-vault/general/overview.md)
