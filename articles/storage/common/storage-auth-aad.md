---
title: Toegang verlenen tot blobs en wacht rijen met behulp van Active Directory
titleSuffix: Azure Storage
description: Toegang tot Azure-blobs en-wacht rijen toestaan met Azure Active Directory (Azure AD). Wijs Azure-rollen toe voor toegangs rechten. Toegang tot gegevens met een Azure AD-account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9d03496634c5d30d30b23a76b5b47b1e810af288
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94635395"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Toegang verlenen tot blobs en wacht rijen met behulp van Azure Active Directory

Azure Storage ondersteunt het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor Blob-en wachtrij opslag. Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een beveiligingsprincipal, wat een gebruiker, groep of toepassings service-principal kan zijn. De beveiligingsprincipal wordt door Azure AD geverifieerd om een OAuth 2,0-token te retour neren. Het token kan vervolgens worden gebruikt voor het autoriseren van een aanvraag voor BLOB-of wachtrij opslag.

Het autoriseren van aanvragen voor Azure Storage met Azure AD biedt een superieure beveiliging en gebruiks gemak voor de verificatie van gedeelde sleutels. Micro soft raadt u aan Azure AD-autorisatie te gebruiken met uw Blob-en wachtrij toepassingen wanneer dat mogelijk is om mogelijke beveiligings problemen die inherent zijn aan de gedeelde sleutel, te minimaliseren.

Verificatie met Azure AD is beschikbaar voor alle accounts voor algemeen gebruik en Blob Storage in alle open bare regio's en nationale Clouds. Alleen opslag accounts die zijn gemaakt met het Azure Resource Manager implementatie model ondersteunen Azure AD-autorisatie.

Blob-opslag biedt ook ondersteuning voor het maken van Shared Access signatures (SAS) die zijn ondertekend met Azure AD-referenties. Zie [beperkte toegang verlenen tot gegevens met hand tekeningen voor gedeelde toegang](storage-sas-overview.md)voor meer informatie.

Azure Files ondersteunt alleen autorisatie met AD (preview) of Azure AD DS (GA) via SMB voor Vm's die zijn toegevoegd aan een domein. Zie [overzicht van Azure files op identiteit gebaseerde verificatie voor SMB-toegang](../files/storage-files-active-directory-overview.md)voor meer informatie over het gebruik van AD (preview) of Azure AD DS (ga) over smb voor Azure files.

Autorisatie met Azure AD wordt niet ondersteund voor Azure-tabel opslag. Gedeelde sleutel gebruiken om aanvragen voor Table-opslag te autoriseren.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Overzicht van Azure AD voor blobs en wacht rijen

Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een BLOB of wachtrij resource, moet de aanvraag worden geautoriseerd, tenzij het een blob is die beschikbaar is voor anonieme toegang. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de BLOB of Queue-service en door de service wordt gebruikt om toegang te verlenen tot de opgegeven resource.

De verificatie stap vereist dat een toepassing tijdens runtime een OAuth 2,0-toegangs token opvraagt. Als een toepassing wordt uitgevoerd vanuit een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure Functions-app, kan deze een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) gebruiken om toegang te krijgen tot blobs of wacht rijen. Zie [toegang verlenen aan blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)voor meer informatie over het autoriseren van aanvragen die door een beheerde identiteit worden door gegeven aan de Azure-Blob of de Queue-service.

De autorisatie stap vereist dat er een of meer Azure-rollen aan de beveiligingsprincipal worden toegewezen. Azure Storage biedt Azure-rollen die algemene sets machtigingen voor Blob-en wachtrij gegevens omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Voor meer informatie over het toewijzen van Azure-functies voor Azure Storage raadpleegt [u toegangs rechten voor opslag gegevens beheren met Azure RBAC](./storage-auth-aad-rbac-portal.md).

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij de Azure Blob of Queue-service, kunnen ook toegang verlenen met Azure AD. Zie [toegang tot Azure Storage met Azure AD toestaan vanuit een Azure Storage toepassing](storage-auth-aad-app.md)voor meer informatie over het aanvragen van een toegangs token en het gebruik ervan om aanvragen voor BLOB-of wachtrij gegevens te autoriseren.

## <a name="assign-azure-roles-for-access-rights"></a>Azure-rollen toewijzen voor toegangs rechten

Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definieert een set ingebouwde rollen van Azure die algemene sets machtigingen omvatten voor toegang tot Blob-en wachtrij gegevens. U kunt ook aangepaste rollen definiëren voor toegang tot Blob-en wachtrij gegevens.

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, het opslag account of een afzonderlijke container of wachtrij. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Ingebouwde rollen van Azure voor blobs en wacht rijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Zie een van de volgende artikelen voor meer informatie over het toewijzen van een ingebouwde Azure-rol aan een beveiligingsprincipal:

- [Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-portal.md)
- [De Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-cli.md)
- [De Azure PowerShell-module gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-powershell.md)

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd voor Azure Storage [begrijpen functie definities](../../role-based-access-control/role-definitions.md#management-and-data-operations). Zie [aangepaste rollen in azure](../../role-based-access-control/custom-roles.md)voor meer informatie over het maken van aangepaste Azure-rollen.

### <a name="access-permissions-for-data-operations"></a>Toegangs machtigingen voor gegevens bewerkingen

Zie voor meer informatie over de vereiste machtigingen voor het aanroepen van specifieke BLOB-of Queue-service bewerkingen [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

## <a name="resource-scope"></a>Resourcebereik

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Toegang tot gegevens met een Azure AD-account

Toegang tot BLOB-of wachtrij gegevens via de Azure Portal, Power shell of Azure CLI kan worden geautoriseerd door gebruik te maken van het Azure AD-account van de gebruiker of door gebruik te maken van de toegangs sleutels voor het account (gedeelde sleutel autorisatie).

### <a name="data-access-from-the-azure-portal"></a>Toegang tot gegevens vanuit de Azure Portal

De Azure Portal kunnen uw Azure AD-account of de toegangs sleutels voor het account gebruiken om toegang te krijgen tot Blob-en wachtrij gegevens in een Azure-opslag account. Welk verificatie schema het Azure Portal gebruikt, is afhankelijk van de Azure-functies die aan u zijn toegewezen.

Wanneer u probeert toegang te krijgen tot BLOB-of wachtrij gegevens, controleert de Azure Portal eerst of aan u een Azure-rol is toegewezen met **micro soft. Storage/Storage accounts/listkeys ophalen/Action**. Als aan u een rol is toegewezen met deze actie, gebruikt de Azure Portal de account sleutel voor toegang tot Blob-en wachtrij gegevens via gedeelde sleutel autorisatie. Als u geen rol aan deze actie hebt toegewezen, probeert de Azure Portal toegang te krijgen tot gegevens met uw Azure AD-account.

Als u toegang wilt krijgen tot BLOB-of wachtrij gegevens van de Azure Portal met uw Azure AD-account, hebt u machtigingen nodig voor toegang tot de BLOB-en wachtrij gegevens en hebt u ook machtigingen nodig om te navigeren door de resources van het opslag account in de Azure Portal. De ingebouwde rollen die worden geboden in Azure Storage, verlenen toegang tot blobs en wachtrijen, maar ze verlenen geen toegangsmachtigingen voor opslagaccounts. Daarom moet voor toegang tot de portal ook een Azure Resource Manager-rol zijn toegewezen, zoals de rol [Lezer](../../role-based-access-control/built-in-roles.md#reader), toegepast op opslagaccountniveau of hoger. De rol van **lezer** verleent de meeste beperkte machtigingen, maar een andere Azure Resource Manager rol die toegang verleent tot bronnen voor het beheer van opslag accounts is ook aanvaardbaar. Zie voor meer informatie over het toewijzen van machtigingen aan gebruikers voor toegang tot gegevens in de Azure Portal met een Azure AD-account, [de Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-portal.md).

De Azure Portal geeft aan welk verificatie schema wordt gebruikt wanneer u naar een container of wachtrij navigeert. Zie voor meer informatie over toegang tot de gegevens in de portal [kiezen hoe u de toegang tot blobgegevens in de Azure Portal autoriseren](../blobs/authorize-data-operations-portal.md) en [Kies hoe u de toegang tot de wachtrij gegevens in de Azure Portal wilt autoriseren](../queues/authorize-data-operations-portal.md).

### <a name="data-access-from-powershell-or-azure-cli"></a>Gegevens toegang vanuit Power shell of Azure CLI

Azure CLI en Power shell ondersteunen het aanmelden met Azure AD-referenties. Nadat u zich hebt aangemeld, wordt uw sessie uitgevoerd onder deze referenties. Zie voor meer informatie [Azure CLI of Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens](../blobs/authorize-data-operations-powershell.md).

## <a name="next-steps"></a>Volgende stappen

- [Toegang verlenen tot blobs en wacht rijen met Azure Active Directory en beheerde identiteiten voor Azure-resources](storage-auth-aad-msi.md)
- [Azure Active Directory van een toepassing autoriseren voor toegang tot blobs en wacht rijen](storage-auth-aad-app.md)
- [Azure Storage ondersteuning voor toegangs beheer op basis van Azure Active Directory algemeen beschikbaar](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)