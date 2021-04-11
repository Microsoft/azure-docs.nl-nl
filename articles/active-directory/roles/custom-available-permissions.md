---
title: Aangepaste rolmachtigingen voor app-registratie-Azure AD | Microsoft Docs
description: Gedelegeerde machtigingen voor aangepaste beheerdersrol voor het beheren van app-registraties.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2c1b912afbcf44cefbfb021c592836dbde5b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466283"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Machtigingen voor toepassings registratie voor aangepaste rollen in Azure Active Directory

Dit artikel bevat de momenteel beschik bare app-registratie machtigingen voor aangepaste roldefinities in Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Machtigingen voor het beheren van toepassingen met één Tenant

Wanneer u de machtigingen voor uw aangepaste rol kiest, hebt u de mogelijkheid om alleen toegang te verlenen voor het beheren van toepassingen met één Tenant. toepassingen met één Tenant zijn alleen beschikbaar voor gebruikers in de Azure AD-organisatie waar de toepassing is geregistreerd. toepassingen met één Tenant worden gedefinieerd als alleen **account typen** waarvoor alleen accounts in deze organisatie Directory zijn ingesteld. In de Graph API is voor toepassingen met één Tenant de eigenschap signInAudience ingesteld op ' AzureADMyOrg '.

Als u toegang wilt verlenen om alleen toepassingen met één Tenant te beheren, gebruikt u de onderstaande machtigingen voor de subtype **toepassingen. myOrganization**. Bijvoorbeeld: micro soft. Directory/Applications. myOrganization/Basic/update.

Bekijk het [overzicht van aangepaste rollen](custom-overview.md) voor een uitleg van de algemene voor waarden subtype, machtigingen en eigenschappen sets. De volgende informatie is specifiek voor toepassings registraties.

### <a name="create-and-delete"></a>Maken en verwijderen

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om toepassings registraties te maken, elk met een ander gedrag:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>micro soft. Directory/toepassingen/createAsOwner

Als u deze machtiging toewijst, wordt de maker toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie geteld voor het quotum van 250 gemaakte objecten van de maker.

#### <a name="microsoftdirectoryapplicationscreate"></a>micro soft. Directory/toepassingen/maken

Als u deze machtigingen toewijst, wordt de maker niet toegevoegd als de eerste eigenaar van de gemaakte app-registratie en wordt de gemaakte app-registratie niet meegeteld op basis van het 250 gemaakte object quota van de maker. Gebruik deze machtiging op een verantwoorde manier, omdat de toegewezen persoon app-registraties kan maken totdat de quotum op directoryniveau wordt bereikt. Als beide machtigingen zijn toegewezen, heeft deze machtiging voorrang.

Als beide machtigingen zijn toegewezen, heeft de/Create-machtiging prioriteit. Hoewel de/createAsOwner-machtiging de maker niet automatisch toevoegt als de eerste eigenaar, kunnen eigen aren worden opgegeven tijdens het maken van de app-registratie bij gebruik van Graph Api's of Power shell-cmdlets.

Maak machtigingen verlenen toegang tot de **nieuwe registratie** opdracht.

[Deze machtigingen verlenen toegang tot de nieuwe registratie Portal opdracht](./media/custom-available-permissions/new-custom-role.png)

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om app-registraties te verwijderen:

#### <a name="microsoftdirectoryapplicationsdelete"></a>micro soft. Directory/toepassingen/verwijderen

Biedt de mogelijkheid om app-registraties te verwijderen, ongeacht subtype; dat wil zeggen, zowel single tenant-als multi tenant-toepassingen.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>micro soft. Directory/toepassingen. myOrganization/verwijderen

Biedt de mogelijkheid om app-registraties te verwijderen die alleen toegankelijk zijn voor accounts in uw organisatie of toepassingen met één Tenant (myOrganization subtype).

![Deze machtigingen verlenen toegang tot de opdracht app-registratie verwijderen](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Bij het toewijzen van een rol die machtigingen voor maken bevat, moet de roltoewijzing worden gemaakt in het bereik van de map. Bij een Create-machtiging die is toegewezen aan een resource bereik, wordt niet de mogelijkheid geboden om app-registraties te maken.

### <a name="read"></a>Lezen

Alle leden van de organisatie kunnen standaard app-registratie gegevens lezen. Gast gebruikers en toepassings service-principals kunnen echter niet. Als u van plan bent om een rol toe te wijzen aan een gast gebruiker of-toepassing, moet u de juiste lees machtigingen toevoegen.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>micro soft. Directory/toepassingen/allProperties/lezen

De mogelijkheid om alle eigenschappen van toepassingen met één Tenant en meerdere tenants te lezen buiten eigenschappen die niet in alle gevallen als referenties kunnen worden gelezen.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>micro soft. Directory/toepassingen. myOrganization/allProperties/lezen

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/allProperties/lezen, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationsownersread"></a>micro soft. Directory/toepassingen/eigen aren/lezen

Verleent de mogelijkheid om eigen aren te lezen voor toepassingen met één Tenant en multi tenant. Verleent toegang tot alle velden op de pagina eigen aars van toepassings registratie:

![Deze machtigingen verlenen toegang tot de pagina eigen aars van app-registratie](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>micro soft. Directory/toepassingen/standaard/lezen

Hiermee wordt toegang verleend om de standaard toepassings registratie-eigenschappen te lezen. Dit omvat eigenschappen over de pagina's voor toepassings registratie.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>micro soft. Directory/toepassingen. myOrganization/standaard/lezen

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/standaard/lezen, maar alleen voor toepassingen met één Tenant.

### <a name="update"></a>Bijwerken

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>micro soft. Directory/toepassingen/allProperties/update

De mogelijkheid om alle eigenschappen voor toepassingen met één Tenant en meerdere tenants bij te werken.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>micro soft. Directory/Applications. myOrganization/allProperties/update

Verleent dezelfde machtigingen als micro soft. Directory/Applications/allProperties/update, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>micro soft. Directory/toepassingen/publiek/update

De mogelijkheid om de ondersteunde account type-eigenschap (signInAudience) bij te werken in toepassingen met één Tenant en multi tenant.

![Deze machtiging verleent toegang tot de account type-eigenschap van de app-registratie op de verificatie pagina](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>micro soft. Directory/Applications. myOrganization/Audience/update

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/publiek/update, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

De mogelijkheid om de eigenschappen van de antwoord-URL, afmeldings-URL, impliciete stroom en uitgevers domein bij te werken in toepassingen met één Tenant en multi tenant. Verleent toegang tot alle velden op de pagina verificatie van toepassings registratie, met uitzonde ring van ondersteunde account typen:

![Hiermee wordt toegang verleend tot de authenticatie van de app-registratie, maar niet-ondersteunde account typen](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/verificatie/update, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>micro soft. Directory/toepassingen/basis/bijwerken

De mogelijkheid om de naam, het logo, de URL van de Home page, de URL van de service en de URL voor de privacyverklaring van een enkele Tenant en multi tenant-toepassingen bij te werken. Verleent toegang tot alle velden op de pagina huismerk gegevens van toepassings registratie:

![Deze machtiging verleent toegang tot de pagina huismerk gegevens van de app-registratie](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>micro soft. Directory/Applications. myOrganization/Basic/update

Verleent dezelfde machtigingen als micro soft. Directory/Applications/Basic/update, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

De mogelijkheid om de certificaten en eigenschappen van client geheimen bij te werken in toepassingen met één Tenant en multi tenant. Verleent toegang tot alle velden op de pagina certificaten voor toepassings registratie & geheimen:

![Met deze machtiging wordt toegang verleend tot de & geheimen-pagina van de app-registratie certificaten](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>micro soft. Directory/Applications. myOrganization/credentials/update

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/referenties/update, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>micro soft. Directory/toepassingen/eigen aren/bijwerken

De mogelijkheid om de eigenschap owner bij te werken voor één Tenant en multi tenant. Verleent toegang tot alle velden op de pagina eigen aars van toepassings registratie:

![Deze machtigingen verlenen toegang tot de pagina eigen aars van app-registratie](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>micro soft. Directory/toepassingen. myOrganization/eigen aren/bijwerken

Hiermee worden dezelfde machtigingen verleend als voor micro soft. Directory/toepassingen/eigen aren/bijwerken, maar alleen voor toepassingen met één Tenant.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>micro soft. Directory/toepassingen/machtigingen/bijwerken

De mogelijkheid om de gedelegeerde machtigingen, toepassings machtigingen, geautoriseerde client toepassingen, vereiste machtigingen te wijzigen en toestemming eigenschappen te verlenen voor toepassingen met één Tenant en multi tenant. De mogelijkheid om toestemming uit te voeren wordt niet verleend. Verleent toegang tot alle velden in de API-machtigingen voor toepassings registratie en maakt een API-pagina beschikbaar:

![Met deze machtigingen wordt toegang verleend tot de pagina app-registratie-API-machtigingen](./media/custom-available-permissions/app-registration-api-permissions.png)

![Deze machtigingen geven toegang tot de app-registratie een API-pagina weer](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Verleent dezelfde machtigingen als micro soft. Directory/Applications/permissions/update, maar alleen voor toepassingen met één Tenant.

## <a name="required-license-plan"></a>Vereiste licentieplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Aangepaste rollen maken met behulp van [de Azure Portal, Azure AD Power shell en Graph API](custom-create.md)
- [Lijst met roltoewijzingen weergeven](view-assignments.md)
