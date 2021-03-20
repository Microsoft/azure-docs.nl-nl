---
title: App-toestemmingsmachtigingen voor aangepaste rollen in Azure Active Directory | Microsoft Docs
description: Bekijk een preview van app-toestemmingsmachtigingen voor aangepaste Azure AD-rollen in de Azure Portal, PowerShell of Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740105"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>App-toestemmingsmachtigingen voor aangepaste rollen in Azure Active Directory

Dit artikel bevat de momenteel beschikbare app-toestemmingsmachtingen voor aangepaste roldefinities in Azure Active Directory (Azure AD). In dit artikel vindt u de machtigingen die vereist zijn voor een aantal veelvoorkomende scenario's met betrekking tot app-toestemming en machtigingen.

## <a name="required-license-plan"></a>Vereiste licentieplan

Voor deze functie hebt u een Azure AD Premium P1-licentie nodig voor uw Azure AD organisatie. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory/) als u een licentie zoekt die bij uw vereisten past.

## <a name="app-consent-permissions"></a>Machtigingen voor app-toestemming

Gebruik de in dit artikel vermelde machtigingen voor het beheren van app-toestemmingsbeleid en de machtiging om toestemming te geven aan apps.

> [!NOTE]
> De Azure AD-beheerportal biedt nog geen ondersteuning voor het toevoegen van de machtigingen die in dit artikel worden vermeld aan een aangepaste directory-roldefinitie. U moet [Azure AD PowerShell gebruiken om een aangepaste directory-rol](custom-create.md#create-a-role-using-powershell) te maken met de machtigingen die in dit artikel worden vermeld.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Gedelegeerde machtigingen verlenen aan apps namens zichzelf (toestemming van de gebruiker)

Om gebruikers toe te staan om namens zichzelf toestemming te geven aan toepassingen (toestemming van de gebruiker), afhankelijk van een app-toestemmingsbeleid.

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

Waarbij `{id}` wordt vervangen door de id van een [app-toestemmingsbeleid](../manage-apps/manage-app-consent-policies.md) waarmee de voorwaarden worden ingesteld waaraan moet worden voldaan om deze machtiging actief te maken.

Als u bijvoorbeeld wilt toestaan dat gebruikers toestemming geven namens zichzelf, afhankelijk van het ingebouwde app-toestemmingsbeleid met id `microsoft-user-default-low`, zou u de machtiging `...managePermissionGrantsForSelf.microsoft-user-default-low` gebruiken.

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Machtigingen verlenen aan apps namens iedereen (beheerderstoestemming)

Voor het delegeren van de tenantbrede beheerderstoestemming aan apps, voor zowel gedelegeerde toestemmingen als app-toestemmingen (app-rollen):

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

Waarbij `{id}` wordt vervangen door de id van een [app-toestemmingsbeleid](../manage-apps/manage-app-consent-policies.md) waarmee de voorwaarden worden ingesteld waaraan moet worden voldaan om deze machtiging bruikbaar te maken.

Als u bijvoorbeeld aan een rol toegewezen personen wilt toestaan tenantbrede beheerderstoestemming te geven aan apps, afhankelijk van een aangepast [app-toestemmingsbeleid](../manage-apps/manage-app-consent-policies.md) met id `low-risk-any-app`, zou u de machtiging `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` gebruiken.

### <a name="managing-app-consent-policies"></a>App-toestemmingsbeleid beheren

Voor het delegeren van het maken, bijwerken en verwijderen van [app-toestemmingsbeleid](../manage-apps/manage-app-consent-policies.md).

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>Lijst met alle machtigingen

Machtiging | Beschrijving
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | Geeft de mogelijkheid om apps toestemming te geven namens zichzelf (gebruikerstoestemming), afhankelijk van app-toestemmingsbeleid `{id}`.
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | Verleent de machtiging om apps toestemming te geven namens iedereen (tenantbrede beheerderstoestemming), afhankelijk van app-toestemmingsbeleid `{id}`.
microsoft.directory/permissionGrantPolicies/standard/read | Biedt de mogelijkheid om app-toestemmingbeleid te lezen.
microsoft.directory/permissionGrantPolicies/basic/update | Biedt de mogelijkheid om basiseigenschappen van bestaande app-toestemmingsbeleidsregels bij te werken.
microsoft.directory/permissionGrantPolicies/create | Biedt de mogelijkheid om app-toestemmingbeleid te maken.
microsoft.directory/permissionGrantPolicies/delete | Biedt de mogelijkheid om app-toestemmingbeleid te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste roltoewijzingen maken met behulp van Azure Portal, Azure AD PowerShell en Graph API](custom-create.md)
- [De toewijzingen voor een aangepaste rol bekijken](../roles/view-assignments.md)
