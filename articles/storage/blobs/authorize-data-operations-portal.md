---
title: Kies hoe u de toegang tot BLOB-gegevens in de Azure Portal wilt autoriseren
titleSuffix: Azure Storage
description: Wanneer u BLOB-gegevens opent met behulp van de Azure Portal, maakt de portal aanvragen voor Azure Storage onder de voor vallen. Deze aanvragen voor Azure Storage kunnen worden geverifieerd en geautoriseerd met uw Azure AD-account of de toegangs sleutel voor het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: a12936f8f9f84dacfab4850253df665ae7758be1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102613240"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Kies hoe u de toegang tot BLOB-gegevens in de Azure Portal wilt autoriseren

Wanneer u BLOB-gegevens opent met behulp van de [Azure Portal](https://portal.azure.com), maakt de portal aanvragen voor Azure Storage onder de voor vallen. Een aanvraag voor Azure Storage kan worden geautoriseerd met behulp van uw Azure AD-account of de toegangs sleutel voor het opslag account. De portal geeft aan welke methode u gebruikt en stelt u in staat om tussen de twee te scha kelen als u de juiste machtigingen hebt.  

U kunt ook opgeven hoe u een afzonderlijke BLOB-upload bewerking in de Azure Portal wilt autoriseren. De portal gebruikt standaard de methode die u al gebruikt voor het autoriseren van een BLOB-upload bewerking, maar u hebt de mogelijkheid om deze instelling te wijzigen wanneer u een BLOB uploadt.

## <a name="permissions-needed-to-access-blob-data"></a>Benodigde machtigingen voor toegang tot BLOB-gegevens

Afhankelijk van hoe u de toegang tot BLOB-gegevens in de Azure Portal wilt autoriseren, hebt u specifieke machtigingen nodig. In de meeste gevallen worden deze machtigingen gegeven via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Zie [Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC)?](../../role-based-access-control/overview.md)voor meer informatie over Azure RBAC.

### <a name="use-the-account-access-key"></a>De toegangs sleutel voor het account gebruiken

Als u BLOB-gegevens wilt openen met de toegangs sleutel voor het account, moet u een Azure-rol aan u hebben toegewezen die de Azure RBAC-actie **micro soft. Storage/Storage accounts/listkeys ophalen/Action** bevat. Deze Azure-rol is mogelijk een ingebouwde of aangepaste rol. Ingebouwde rollen die **micro soft. Storage/Storage accounts/listkeys ophalen/Action** ondersteunen zijn onder andere:

- De rol van Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner)
- De rol [inzender](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- De rol [Inzender voor opslag accounts](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wanneer u probeert toegang te krijgen tot BLOB-gegevens in de Azure Portal, controleert de portal eerst of aan u een rol is toegewezen met **micro soft. Storage/Storage accounts/listkeys ophalen/Action**. Als aan u een rol is toegewezen met deze actie, gebruikt de Portal de account sleutel voor toegang tot BLOB-gegevens. Als u geen rol aan deze actie hebt toegewezen, probeert de portal toegang te krijgen tot gegevens met uw Azure AD-account.

> [!IMPORTANT]
> Wanneer een opslag account is vergrendeld met een Azure Resource Manager **alleen-lezen** vergrendeling, is de bewerking [lijst sleutels](/rest/api/storagerp/storageaccounts/listkeys) niet toegestaan voor dat opslag account. **Lijst sleutels** is een post-bewerking en alle post-bewerkingen worden voor komen wanneer een **alleen-lezen** vergrendeling voor het account is geconfigureerd. Daarom moeten gebruikers, wanneer het account is vergrendeld met een **alleen-lezen** vergrendeling, Azure AD-referenties gebruiken om toegang te krijgen tot BLOB-gegevens in de portal. Zie [uw Azure ad-account gebruiken](#use-your-azure-ad-account)voor meer informatie over het openen van BLOB-gegevens in de portal met Azure AD.

> [!NOTE]
> De service beheerder rollen van de klassieke abonnements beheerder en Co-Administrator omvatten het equivalent van de Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner) van de rol. De rol **eigenaar** omvat alle acties, waaronder **micro soft. Storage/Storage accounts/listkeys ophalen/Action**, zodat een gebruiker met een van deze beheerders rollen ook toegang heeft tot BLOB-gegevens met de account sleutel. Zie [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) voor meer informatie.

### <a name="use-your-azure-ad-account"></a>Uw Azure AD-account gebruiken

Als u toegang wilt krijgen tot BLOB-gegevens van de Azure Portal met uw Azure AD-account, moeten beide volgende instructies waar zijn:

- U hebt de rol van Azure Resource Manager [lezer](../../role-based-access-control/built-in-roles.md#reader) ten minste toegewezen aan het niveau van het opslag account of hoger. De rol van **lezer** verleent de meeste beperkte machtigingen, maar een andere Azure Resource Manager rol die toegang verleent tot bronnen voor het beheer van opslag accounts is ook aanvaardbaar.
- U hebt een ingebouwde of aangepaste rol toegewezen die toegang biedt tot BLOB-gegevens.

De rol van **lezer** of een andere Azure Resource Manager roltoewijzing is nodig, zodat de gebruiker beheer bronnen voor opslag accounts in de Azure Portal kan bekijken en navigeren. De Azure-rollen die toegang verlenen tot BLOB-gegevens, verlenen geen toegang tot bronnen voor het beheer van opslag accounts. Om toegang te krijgen tot BLOB-gegevens in de portal, heeft de gebruiker machtigingen nodig om te navigeren in de resources van het opslag account. Zie [de rol van lezer toewijzen voor toegang tot de portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)voor meer informatie over deze vereiste.

De ingebouwde rollen die toegang bieden tot uw BLOB-gegevens zijn onder andere:

- [Eigenaar](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)van de opslag-BLOB-gegevens: voor POSIX-toegangs beheer voor Azure data Lake Storage Gen2.
- [Inzender voor Storage BLOB-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): machtigingen voor lezen/schrijven/verwijderen voor blobs.
- [Gegevens lezer](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)van de opslag-blob: alleen-lezen machtigingen voor blobs.

Aangepaste rollen kunnen verschillende combi Naties van dezelfde machtigingen ondersteunen die worden geboden door de ingebouwde rollen. Zie voor meer informatie over het maken van aangepaste rollen van Azure [Azure aangepaste rollen](../../role-based-access-control/custom-roles.md) en [informatie over roldefinities voor Azure-resources](../../role-based-access-control/role-definitions.md).

> [!IMPORTANT]
> De preview-versie van Storage Explorer in het Azure Portal biedt geen ondersteuning voor het gebruik van Azure AD-referenties om BLOB-gegevens weer te geven en te wijzigen. Storage Explorer in de Azure Portal maakt altijd gebruik van de account sleutels om toegang te krijgen tot gegevens. Als u Storage Explorer in het Azure Portal wilt gebruiken, moet u een rol toewijzen die **micro soft. Storage/Storage accounts/listkeys ophalen/Action** bevat.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Navigeer naar blobs in de Azure Portal

Als u BLOB-gegevens in de portal wilt weer geven, gaat u naar het **overzicht** voor uw opslag account en klikt u op de koppelingen voor **blobs**. U kunt ook naar de sectie **containers** in het menu navigeren.

:::image type="content" source="media/authorize-data-operations-portal/blob-access-portal.png" alt-text="Scherm afbeelding die laat zien hoe u navigeert naar BLOB-gegevens in de Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>De huidige verificatie methode bepalen

Wanneer u naar een container navigeert, geeft de Azure Portal aan of u momenteel de toegangs sleutel voor het account of uw Azure AD-account gebruikt om te verifiëren.

### <a name="authenticate-with-the-account-access-key"></a>Verifiëren met de toegangs sleutel voor het account

Als u een verificatie uitvoert met de toegangs sleutel voor het account, ziet u de **toegangs sleutel** die is opgegeven als verificatie methode in de portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Scherm opname van de gebruiker die momenteel toegang heeft tot containers met de account sleutel":::

Als u wilt overschakelen op het gebruik van een Azure AD-account, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als u over de juiste machtigingen beschikt via de Azure-rollen die aan u zijn toegewezen, kunt u door gaan. Als u echter niet over de juiste machtigingen beschikt, ziet u een fout bericht als het volgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Fout die wordt weer gegeven als het Azure AD-account geen ondersteuning biedt voor toegang":::

U ziet dat er geen blobs worden weer gegeven in de lijst als uw Azure AD-account geen machtigingen heeft om ze weer te geven. Klik op de **Schakel optie om toegang te krijgen** tot de sleutel koppeling om de toegangs sleutel opnieuw te gebruiken voor verificatie.

### <a name="authenticate-with-your-azure-ad-account"></a>Verifiëren met uw Azure AD-account

Als u de verificatie uitvoert met uw Azure AD-account, ziet u de **Azure AD-gebruikers account** die is opgegeven als verificatie methode in de portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Scherm opname van de gebruiker die momenteel toegang heeft tot containers met een Azure AD-account":::

Als u wilt overschakelen op het gebruik van de toegangs sleutel voor het account, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als u toegang hebt tot de account sleutel, kunt u door gaan. Als u echter geen toegang hebt tot de account sleutel, ziet u een fout bericht als het volgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="De fout die wordt weer gegeven als u geen toegang hebt tot de account sleutel":::

U ziet dat er geen blobs worden weer gegeven in de lijst als u geen toegang hebt tot de account sleutels. Klik op de koppeling **overschakelen naar Azure AD-gebruikers account** om uw Azure ad-account opnieuw te gebruiken voor verificatie.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Opgeven hoe een BLOB-upload bewerking moet worden geautoriseerd

Wanneer u een BLOB uploadt uit de Azure Portal, kunt u opgeven of u deze bewerking wilt verifiëren en autoriseren met de toegangs sleutel voor het account of met uw Azure AD-referenties. De portal gebruikt standaard de huidige verificatie methode, zoals wordt weer gegeven in [de huidige verificatie methode bepalen](#determine-the-current-authentication-method).

Voer de volgende stappen uit om op te geven hoe een BLOB-upload bewerking moet worden geautoriseerd:

1. Navigeer in het Azure Portal naar de container waarnaar u een BLOB wilt uploaden.
1. Selecteer de knop **Uploaden**.
1. Vouw de sectie **Geavanceerd** uit om de geavanceerde eigenschappen voor de BLOB weer te geven.
1. Geef in het veld **type verificatie** aan of u de upload bewerking wilt autoriseren met behulp van uw Azure ad-account of met de toegangs sleutel voor het account, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Scherm afbeelding die laat zien hoe u de autorisatie methode wijzigt bij het uploaden van blobs":::

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en-wacht rijen verifiëren met Azure Active Directory](../common/storage-auth-aad.md)
- [Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](../common/storage-auth-aad-rbac-portal.md)
- [De Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-cli.md)
- [De Azure PowerShell-module gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-powershell.md)
