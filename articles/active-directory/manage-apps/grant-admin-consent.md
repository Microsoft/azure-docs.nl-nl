---
title: Beheerder van de hele Tenant toestemming geven voor een toepassing-Azure AD
description: Meer informatie over het verlenen van toestemming op Tenant niveau voor een toepassing, zodat eind gebruikers niet om toestemming wordt gevraagd wanneer ze zich aanmelden bij een toepassing.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaf8f92ec2c7d9653595b3249521582620670aaf
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089949"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Een toepassing beheerderstoestemming verlenen voor de hele tenant

Meer informatie over hoe u de gebruikers ervaring kunt vereenvoudigen door de beheerder van de hele Tenant toestemming te verlenen voor een toepassing. In dit artikel worden de verschillende manieren beschreven waarop u dit kunt doen. De methoden zijn van toepassing op alle eind gebruikers in uw Azure Active Directory-Tenant (Azure AD).

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor het verlenen van toestemming voor de beheerder van de Tenant moet u zich aanmelden als [globale beheerder](../roles/permissions-reference.md#global-administrator), een [toepassings beheerder](../roles/permissions-reference.md#application-administrator)of een [Cloud toepassings beheerder](../roles/permissions-reference.md#cloud-application-administrator).

> [!IMPORTANT]
> Wanneer een toepassing toestemming heeft gekregen voor Tenant beheer, kunnen alle gebruikers zich aanmelden bij de app, tenzij deze is geconfigureerd voor het vereisen van een gebruikers toewijzing. Als u wilt beperken welke gebruikers zich kunnen aanmelden bij een toepassing, moet u de gebruikers toewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing. Zie [methoden voor het toewijzen van gebruikers en groepen](./assign-user-or-group-access-portal.md)voor meer informatie.
>
> De rol van globale beheerder is vereist om toestemming van de beheerder te bieden voor toepassings machtigingen voor de Microsoft Graph-API.

> [!WARNING]
> Voor het verlenen van een door de Tenant verleende beheerder toestemming voor een toepassing wordt de app en de uitgever van de app toegang verleend tot de gegevens van uw organisatie. Controleer zorgvuldig de machtigingen die de toepassing aanvraagt voordat u toestemming verleent.
>
> De rol van globale beheerder is vereist om toestemming van de beheerder te bieden voor toepassings machtigingen voor de Microsoft Graph-API.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Beheerder toestemming geven van de Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Beheerders toestemming verlenen in bedrijfs-apps

U kunt toestemming van de beheerder voor de hele Tenant verlenen via *bedrijfs toepassingen* als de toepassing al is ingericht in uw Tenant. Een app kan bijvoorbeeld worden ingericht in uw Tenant als ten minste één gebruiker al heeft ingestemd met de toepassing. Zie [hoe en waarom toepassingen worden toegevoegd aan Azure Active Directory](../develop/active-directory-how-applications-are-added.md)voor meer informatie.

Toestemming geven voor de hele Tenant beheerder voor een app die wordt vermeld in **bedrijfs toepassingen**:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een [globale beheerder](../roles/permissions-reference.md#global-administrator), een [toepassings beheerder](../roles/permissions-reference.md#application-administrator)of een [Cloud toepassings beheerder](../roles/permissions-reference.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** **bedrijfs toepassingen**.
3. Selecteer de toepassing waaraan u de machtiging voor de hele Tenant beheerder wilt verlenen.
4. Selecteer **machtigingen** en klik vervolgens op **toestemming beheerder verlenen**.
5. Controleer zorgvuldig de machtigingen die nodig zijn voor de toepassing.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, toestemming verlenen. Als dat niet het geval is, klikt u op **Annuleren** of sluit u het venster.

> [!WARNING]
> **Door beheerders** toestemming voor de hele Tenant te verlenen, worden alle machtigingen ingetrokken die eerder zijn verleend voor tenants. Machtigingen die eerder door gebruikers uit hun eigen naam zijn verleend, worden niet beïnvloed. 

### <a name="grant-admin-consent-in-app-registrations"></a>Toestemming van beheerder verlenen in App-registraties

Voor toepassingen die uw organisatie heeft ontwikkeld of die rechtstreeks zijn geregistreerd in uw Azure AD-Tenant, kunt u ook toestemming geven voor de beheerder van de hele Tenant via **app-registraties** in de Azure Portal.

Toestemming van de beheerder voor de hele Tenant verlenen van **app-registraties**:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een [globale beheerder](../roles/permissions-reference.md#global-administrator), een [toepassings beheerder](../roles/permissions-reference.md#application-administrator)of een [Cloud toepassings beheerder](../roles/permissions-reference.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** vervolgens **app-registraties**.
3. Selecteer de toepassing waaraan u de machtiging voor de hele Tenant beheerder wilt verlenen.
4. Selecteer **API-machtigingen** en klik vervolgens op **toestemming beheerder verlenen**.
5. Controleer zorgvuldig de machtigingen die nodig zijn voor de toepassing.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, toestemming verlenen. Als dat niet het geval is, klikt u op **Annuleren** of sluit u het venster.

> [!WARNING]
> **App-registraties** door beheerders toestemming voor de hele Tenant te verlenen, worden alle machtigingen ingetrokken die eerder zijn verleend voor de Tenant. Machtigingen die eerder door gebruikers uit hun eigen naam zijn verleend, worden niet beïnvloed. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>De URL maken voor het verlenen van toestemming voor de beheerder voor de hele Tenant

Bij het verlenen van beheerders toestemming voor de hele Tenant met behulp van een van de hierboven beschreven methoden, wordt een venster geopend van de Azure Portal om te vragen om toestemming van de beheerder voor de hele Tenant. Als u de client-ID (ook wel de toepassings-ID) van de toepassing kent, kunt u dezelfde URL maken om toestemming te geven voor de beheerder van de hele Tenant.

De URL voor de toestemming van de beheerder voor tenants volgt de volgende indeling:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

Hierbij

* `{client-id}` is de client-ID van de toepassing (ook wel App-ID genoemd).
* `{tenant-id}` is de Tenant-ID van uw organisatie of een geverifieerde domein naam.

Zo altijd moet u de machtigingen voor een toepassings aanvraag zorgvuldig controleren voordat u toestemming verleent.

> [!WARNING]
> Door de beheerder toestemming voor de hele Tenant te verlenen via deze URL worden alle machtigingen ingetrokken die eerder zijn verleend voor tenants. Machtigingen die eerder door gebruikers uit hun eigen naam zijn verleend, worden niet beïnvloed. 

## <a name="next-steps"></a>Volgende stappen

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)

[Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)

[Azure AD op micro soft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)