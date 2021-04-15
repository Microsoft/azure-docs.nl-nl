---
title: Tenantbrede beheerders toestemming geven voor een toepassing - Azure AD
description: Meer informatie over het verlenen van tenantbrede toestemming aan een toepassing, zodat eindgebruikers niet om toestemming wordt gevraagd bij het aanmelden bij een toepassing.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374179"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Een toepassing beheerderstoestemming verlenen voor de hele tenant

  Meer informatie over het verlenen van beheerdersmachtiging voor de hele tenant aan een toepassing. In dit artikel worden de verschillende manieren beschreven om dit te bereiken.

Zie toestemmingskader voor Azure Active Directory voor meer informatie over het [verlenen van toestemming aan toepassingen.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Vereisten

Voor het verlenen van beheerdersmachtiging voor de hele tenant moet u zich aanmelden als een gebruiker die gemachtigd is om toestemming te geven namens de organisatie. Dit omvat [Globale beheerder](../roles/permissions-reference.md#global-administrator) en Beheerder [met bevoorrechte](../roles/permissions-reference.md#privileged-role-administrator)rol en, voor sommige [toepassingen,](../roles/permissions-reference.md#application-administrator) Toepassingsbeheerder en [Cloudtoepassingsbeheerder.](../roles/permissions-reference.md#cloud-application-administrator) Een gebruiker kan ook worden gemachtigd om toestemming voor de hele tenant te verlenen als aan deze gebruiker een aangepaste [directoryrol](../roles/custom-create.md) is toegewezen die de machtiging bevat om machtigingen te verlenen [aan toepassingen](../roles/custom-consent-permissions.md).

> [!WARNING]
> Als u tenantbrede beheerderstoegang verleent aan een toepassing, worden de app en de uitgever van de app toegang verleend tot de gegevens van uw organisatie. Controleer zorgvuldig de machtigingen die de toepassing aanvraagt voordat u toestemming verleent.

> [!IMPORTANT]
> Wanneer aan een toepassing beheerderstoewijzing voor de hele tenant is verleend, kunnen alle gebruikers zich aanmelden bij de app, tenzij deze is geconfigureerd om gebruikerstoewijzing te vereisen. Als u wilt beperken welke gebruikers zich kunnen aanmelden bij een toepassing, moet u gebruikerstoewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing. Zie Methoden voor het [toewijzen van gebruikers en groepen voor meer informatie.](./assign-user-or-group-access-portal.md)

## <a name="grant-admin-consent-from-the-azure-portal"></a>Beheerders toestemming geven van de Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Beheerdersmachtiging verlenen in Enterprise-apps

U kunt tenantbrede beheerdersmachtiging verlenen via *Bedrijfstoepassingen* als de toepassing al is ingericht in uw tenant. Een app kan bijvoorbeeld worden ingericht in uw tenant als ten minste één gebruiker al toestemming heeft gegeven voor de toepassing. Zie Hoe en waarom toepassingen worden toegevoegd aan Azure Active Directory [voor meer Azure Active Directory.](../develop/active-directory-how-applications-are-added.md)

Als u tenantbrede beheerdersmachtiging wilt verlenen aan een app die wordt vermeld in **Bedrijfstoepassingen:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale [beheerder,](../roles/permissions-reference.md#global-administrator) [toepassingsbeheerder](../roles/permissions-reference.md#application-administrator)of [cloudtoepassingsbeheerder.](../roles/permissions-reference.md#cloud-application-administrator)
2. Selecteer **Azure Active Directory** vervolgens **Bedrijfstoepassingen.**
3. Selecteer de toepassing waarvoor u beheerdersmachtiging voor de hele tenant wilt verlenen.
4. Selecteer **Machtigingen en** klik vervolgens op **Beheerders toestemming verlenen.**
5. Controleer zorgvuldig de machtigingen die de toepassing nodig heeft.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, verleent u toestemming. Als dat niet het beste is, **klikt** u op Annuleren of sluit u het venster.

> [!WARNING]
> Als u tenantbrede beheerdersmachtigingen verleent via **Enterprise-apps,** worden alle machtigingen ingetrokken die eerder voor de hele tenant waren verleend. Machtigingen die eerder namens gebruikers zijn verleend, worden niet beïnvloed. 

### <a name="grant-admin-consent-in-app-registrations"></a>Beheerdersmachtiging verlenen in App-registraties

Voor toepassingen die uw organisatie heeft ontwikkeld of die rechtstreeks zijn geregistreerd in uw Azure AD-tenant, kunt u ook tenantbrede beheerdersmachtiging verlenen vanuit App-registraties **in** de Azure Portal.

Als u tenantbrede beheerdersmachtiging wilt verlenen vanuit **App-registraties**:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale [beheerder,](../roles/permissions-reference.md#global-administrator) [toepassingsbeheerder](../roles/permissions-reference.md#application-administrator)of [cloudtoepassingsbeheerder.](../roles/permissions-reference.md#cloud-application-administrator)
2. Selecteer **Azure Active Directory** selecteer **App-registraties**.
3. Selecteer de toepassing waarvoor u beheerdersmachtiging voor de hele tenant wilt verlenen.
4. Selecteer **API-machtigingen** en klik vervolgens op **Beheerders toestemming verlenen.**
5. Controleer zorgvuldig de machtigingen die de toepassing nodig heeft.
6. Als u akkoord gaat met de machtigingen die de toepassing vereist, verleent u toestemming. Als dat niet het beste is, **klikt** u op Annuleren of sluit u het venster.

> [!WARNING]
> Door tenantbrede beheerders toestemming te verlenen **via App-registraties** worden alle machtigingen ingetrokken die eerder voor de hele tenant waren verleend. Machtigingen die eerder door gebruikers zelf zijn verleend, worden niet beïnvloed. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>De URL maken voor het verlenen van beheerdersmachtiging voor de hele tenant

Wanneer u tenantbrede beheerders toestemming verleent met behulp van een van de hierboven beschreven methoden, wordt er een venster geopend vanuit de Azure Portal om te vragen om toestemming van de beheerder voor de hele tenant. Als u de client-id (ook wel de toepassings-id genoemd) van de toepassing kent, kunt u dezelfde URL bouwen om toestemming te verlenen aan de tenantbrede beheerder.

De URL voor beheerdersmachtigingen voor de hele tenant heeft de volgende indeling:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

Hierbij

* `{client-id}` is de client-id van de toepassing (ook wel app-id genoemd).
* `{tenant-id}` is de tenant-id van uw organisatie of een geverifieerde domeinnaam.

Controleer zoals altijd zorgvuldig de machtigingen die een toepassing aanvraagt voordat u toestemming verleent.

> [!WARNING]
> Als u tenantbrede beheerders toestemming verleent via deze URL, worden alle machtigingen ingetrokken die eerder voor de hele tenant waren verleend. Machtigingen die eerder door gebruikers zelf zijn verleend, worden niet beïnvloed. 

## <a name="next-steps"></a>Volgende stappen

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)

[Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD in Microsoft Q&A](/answers/topics/azure-active-directory.html)
