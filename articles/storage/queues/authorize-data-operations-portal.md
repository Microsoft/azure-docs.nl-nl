---
title: Kies hoe u de toegang tot de wachtrij gegevens in de Azure Portal wilt autoriseren
titleSuffix: Azure Storage
description: Wanneer u toegang krijgt tot wachtrij gegevens met behulp van de Azure Portal, brengt de portal aanvragen naar Azure Storage onder de voor vallen. Deze aanvragen voor Azure Storage kunnen worden geverifieerd en geautoriseerd met uw Azure AD-account of de toegangs sleutel voor het opslag account.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: fbb96fc1d2cb12e1aede07295357abfaa6d6b67f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385010"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Kies hoe u de toegang tot de wachtrij gegevens in de Azure Portal wilt autoriseren

Wanneer u toegang krijgt tot wachtrij gegevens met behulp van de [Azure Portal](https://portal.azure.com), brengt de portal aanvragen naar Azure Storage onder de voor vallen. Een aanvraag voor Azure Storage kan worden geautoriseerd met behulp van uw Azure AD-account of de toegangs sleutel voor het opslag account. De portal geeft aan welke methode u gebruikt en stelt u in staat om tussen de twee te scha kelen als u de juiste machtigingen hebt.

## <a name="permissions-needed-to-access-queue-data"></a>Machtigingen die nodig zijn voor toegang tot de wachtrij gegevens

Afhankelijk van hoe u de toegang tot wachtrij gegevens in de Azure Portal wilt autoriseren, hebt u specifieke machtigingen nodig. In de meeste gevallen worden deze machtigingen gegeven via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Zie [Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC)?](../../role-based-access-control/overview.md)voor meer informatie over Azure RBAC.

### <a name="use-the-account-access-key"></a>De toegangs sleutel voor het account gebruiken

Als u toegang wilt hebben tot wachtrij gegevens met de toegangs sleutel voor het account, moet u een Azure-rol aan u hebben toegewezen die de Azure RBAC-actie **micro soft. Storage/Storage accounts/listkeys ophalen/Action** bevat. Deze Azure-rol is mogelijk een ingebouwde of aangepaste rol. Ingebouwde rollen die **micro soft. Storage/Storage accounts/listkeys ophalen/Action** ondersteunen zijn onder andere:

- De rol van Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner)
- De [rol inzender](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- De [rol Inzender voor opslag accounts](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wanneer u probeert toegang te krijgen tot wachtrij gegevens in de Azure Portal, controleert de portal eerst of aan u een rol is toegewezen met **micro soft. Storage/Storage accounts/listkeys ophalen/Action**. Als aan u een rol is toegewezen met deze actie, gebruikt de Portal de account sleutel voor toegang tot de wachtrij gegevens. Als u geen rol aan deze actie hebt toegewezen, probeert de portal toegang te krijgen tot gegevens met uw Azure AD-account.

> [!IMPORTANT]
> Wanneer een opslag account is vergrendeld met een Azure Resource Manager **alleen-lezen** vergrendeling, is de bewerking [lijst sleutels](/rest/api/storagerp/storageaccounts/listkeys) niet toegestaan voor dat opslag account. **Lijst sleutels** is een post-bewerking en alle post-bewerkingen worden voor komen wanneer een **alleen-lezen** vergrendeling voor het account is geconfigureerd. Daarom moeten gebruikers de Azure AD-referenties gebruiken om toegang te krijgen tot de wachtrij gegevens in de Portal wanneer het account is vergrendeld met een **alleen-lezen** vergrendeling. Zie [uw Azure ad-account gebruiken](#use-your-azure-ad-account)voor meer informatie over het openen van wachtrij gegevens in de portal met Azure AD.

> [!NOTE]
> De klassieke abonnements beheerder rollen **service beheerder** en **mede beheerder** bevatten het equivalent van de Azure Resource Manager [`Owner`](../../role-based-access-control/built-in-roles.md#owner) rol. De rol **eigenaar** omvat alle acties, waaronder **micro soft. Storage/Storage accounts/listkeys ophalen/Action**, zodat een gebruiker met een van deze beheerders rollen ook toegang heeft tot de wachtrij gegevens met de account sleutel. Zie [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) voor meer informatie.

### <a name="use-your-azure-ad-account"></a>Uw Azure AD-account gebruiken

Als u toegang wilt krijgen tot de wachtrij gegevens van de Azure Portal met uw Azure AD-account, moeten beide volgende instructies waar zijn:

- U hebt aan de Azure Resource Manager [`Reader`](../../role-based-access-control/built-in-roles.md#reader) -functie ten minste een bereik toegewezen van het niveau van het opslag account of hoger. De rol van **lezer** verleent de meeste beperkte machtigingen, maar een andere Azure Resource Manager rol die toegang verleent tot bronnen voor het beheer van opslag accounts is ook aanvaardbaar.
- U hebt een ingebouwde of aangepaste rol toegewezen die toegang biedt tot de gegevens in de wachtrij.

De rol van **lezer** of een andere Azure Resource Manager roltoewijzing is nodig, zodat de gebruiker beheer bronnen voor opslag accounts in de Azure Portal kan bekijken en navigeren. De Azure-rollen die toegang tot de wachtrij gegevens verlenen, verlenen geen toegang tot bronnen voor het beheer van opslag accounts. Om toegang te krijgen tot de wachtrij gegevens in de portal, heeft de gebruiker machtigingen nodig om te navigeren in de resources van het opslag account. Zie [de rol van lezer toewijzen voor toegang tot de portal](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)voor meer informatie over deze vereiste.

De ingebouwde rollen die toegang bieden tot uw wachtrij gegevens zijn onder andere:

- [Inzender voor gegevens van de opslag wachtrij](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): machtigingen voor lezen/schrijven/verwijderen voor wacht rijen.
- [Gegevens lezer van de opslag wachtrij](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): alleen-lezen machtigingen voor wacht rijen.

Aangepaste rollen kunnen verschillende combi Naties van dezelfde machtigingen ondersteunen die worden geboden door de ingebouwde rollen. Zie voor meer informatie over het maken van aangepaste rollen van Azure [Azure aangepaste rollen](../../role-based-access-control/custom-roles.md) en [informatie over roldefinities voor Azure-resources](../../role-based-access-control/role-definitions.md).

Het weer geven van wacht rijen met een rol beheerder voor klassieke abonnementen wordt niet ondersteund. Een gebruiker moet een lijst met wacht rijen hebben toegewezen aan de rol van de Azure Resource Manager **lezer** , de rol van de **gegevens lezer van de opslag wachtrij** of de rol van de gegevens Inzender voor de **opslag wachtrij** .

> [!IMPORTANT]
> De preview-versie van Storage Explorer in het Azure Portal biedt geen ondersteuning voor het gebruik van Azure AD-referenties om wachtrij gegevens te bekijken en te wijzigen. Storage Explorer in de Azure Portal maakt altijd gebruik van de account sleutels om toegang te krijgen tot gegevens. Als u Storage Explorer in het Azure Portal wilt gebruiken, moet u een rol toewijzen die **micro soft. Storage/Storage accounts/listkeys ophalen/Action** bevat.

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navigeer naar wacht rijen in de Azure Portal

Als u de wachtrij gegevens in de portal wilt bekijken, gaat u naar het **overzicht** voor uw opslag account en klikt u op de koppelingen voor **wacht rijen**. U kunt ook naar de **Queue-service** sectie in het menu navigeren.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Scherm afbeelding die laat zien hoe u navigeert naar wachtrij gegevens in de Azure Portal":::

## <a name="determine-the-current-authentication-method"></a>De huidige verificatie methode bepalen

Wanneer u naar een wachtrij navigeert, geeft de Azure Portal aan of u momenteel de toegangs sleutel voor het account of uw Azure AD-account gebruikt om te verifiëren.

### <a name="authenticate-with-the-account-access-key"></a>Verifiëren met de toegangs sleutel voor het account

Als u een verificatie uitvoert met de toegangs sleutel voor het account, ziet u de **toegangs sleutel** die is opgegeven als verificatie methode in de portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Scherm opname van de gebruiker die momenteel toegang heeft tot wacht rijen met de account sleutel":::

Als u wilt overschakelen op het gebruik van een Azure AD-account, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als u over de juiste machtigingen beschikt via de Azure-rollen die aan u zijn toegewezen, kunt u door gaan. Als u echter niet over de juiste machtigingen beschikt, ziet u een fout bericht als het volgende:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Fout die wordt weer gegeven als het Azure AD-account geen ondersteuning biedt voor toegang":::

U ziet dat er geen wacht rijen worden weer gegeven in de lijst als uw Azure AD-account geen machtigingen heeft om ze weer te geven. Klik op de **Schakel optie om toegang te krijgen** tot de sleutel koppeling om de toegangs sleutel opnieuw te gebruiken voor verificatie.

### <a name="authenticate-with-your-azure-ad-account"></a>Verifiëren met uw Azure AD-account

Als u de verificatie uitvoert met uw Azure AD-account, ziet u de **Azure AD-gebruikers account** die is opgegeven als verificatie methode in de portal:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Scherm opname van de gebruiker die momenteel toegang heeft tot wacht rijen met een Azure AD-account":::

Als u wilt overschakelen op het gebruik van de toegangs sleutel voor het account, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als u toegang hebt tot de account sleutel, kunt u door gaan. Als u echter geen toegang hebt tot de account sleutel, wordt er een fout bericht weer gegeven in de Azure Portal.

Wacht rijen worden niet weer gegeven in de portal als u geen toegang hebt tot de account sleutels. Klik op de koppeling **overschakelen naar Azure AD-gebruikers account** om uw Azure ad-account opnieuw te gebruiken voor verificatie.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en-wacht rijen verifiëren met Azure Active Directory](../common/storage-auth-aad.md)
- [Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](../common/storage-auth-aad-rbac-portal.md)
- [De Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-cli.md)
- [De Azure PowerShell-module gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](../common/storage-auth-aad-rbac-powershell.md)
