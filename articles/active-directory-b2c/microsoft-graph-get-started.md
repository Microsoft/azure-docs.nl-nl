---
title: Een Microsoft Graph-toepassing registreren
titleSuffix: Azure AD B2C
description: Bereid u voor op het beheer van Azure AD B2C resources met Microsoft Graph door een toepassing te registreren die de vereiste Graph API machtigingen heeft.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675183"
---
# <a name="register-a-microsoft-graph-application"></a>Een Microsoft Graph-toepassing registreren

Met [Microsoft Graph][ms-graph] kunt u veel van de resources in uw Azure AD B2C Tenant beheren, met inbegrip van gebruikers accounts voor klanten en aangepaste beleids regels. Door scripts of toepassingen te schrijven die de [Microsoft Graph-API][ms-graph-api]aanroepen, kunt u de taken voor het beheer van tenants automatiseren, zoals:

* Een bestaande gebruikers opslag migreren naar een Azure AD B2C-Tenant
* Aangepast beleid implementeren met een Azure-pijp lijn in azure DevOps en aangepaste beleids sleutels beheren
* Registratie van de host van de gebruiker op uw eigen pagina en gebruikers accounts maken in uw Azure AD B2C Directory achter de schermen
* Toepassings registratie automatiseren
* Audit logboeken verkrijgen

In de volgende secties wordt beschreven hoe u de Microsoft Graph-API kunt gebruiken om het beheer van resources in uw Azure AD B2C Directory te automatiseren.

## <a name="microsoft-graph-api-interaction-modes"></a>Interactie modi van Microsoft Graph-API

Er zijn twee modi voor communicatie die u kunt gebruiken bij het werken met de Microsoft Graph-API voor het beheren van resources in uw Azure AD B2C Tenant:

* **Interactief** : geschikt voor taken die eenmaal kunnen worden uitgevoerd, gebruikt u een beheerders account in de B2C-Tenant om de beheer taken uit te voeren. Voor deze modus moet een beheerder zich aanmelden met hun referenties voordat de Microsoft Graph-API wordt aangeroepen.

* **Automatisch** : voor geplande of voortdurend uitgevoerde taken gebruikt deze methode een service account dat u configureert met de vereiste machtigingen voor het uitvoeren van beheer taken. U maakt het ' Service account ' in Azure AD B2C door een toepassing te registreren die door uw toepassingen en scripts wordt gebruikt voor verificatie met behulp van de *toepassings-id* en de **OAuth 2,0-client referenties** verlenen. In dit geval fungeert de toepassing als zichzelf om de Microsoft Graph-API aan te roepen, niet de beheerder gebruiker, zoals in de eerder beschreven interactieve methode.

U schakelt het **geautomatiseerde** interactie scenario in door een toepassings registratie te maken die wordt weer gegeven in de volgende secties.

Hoewel de OAuth 2,0-toewijzings stroom voor client referenties momenteel niet rechtstreeks wordt ondersteund door de Azure AD B2C Authentication-Service, kunt u de client referentie stroom instellen met behulp van Azure AD en het micro soft Identity platform/token-eind punt voor een toepassing in uw Azure AD B2C-Tenant. Een Azure AD B2C-Tenant deelt een aantal functies met Azure AD-tenants voor bedrijven.

## <a name="register-management-application"></a>Beheer toepassing registreren

Voordat uw scripts en toepassingen kunnen communiceren met de [Microsoft Graph-API][ms-graph-api] om Azure AD B2C resources te beheren, moet u een toepassings registratie in uw Azure AD B2C-Tenant maken die de vereiste API-machtigingen verleent.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *managementapp1*.
1. Selecteer **Alleen accounts in deze organisatiemap**.
1. Schakel onder **machtigingen** het selectie vakje *verlenen beheerder toestemming voor openid connect-en offline_access machtigingen* uit.
1. Selecteer **Registreren**.
1. Noteer de **toepassings-id** van de toepassing die wordt weer gegeven op de overzichts pagina van de toepassing. U gebruikt deze waarde in een latere stap.

### <a name="grant-api-access"></a>API-toegang verlenen

Verleen vervolgens de geregistreerde toepassings machtigingen voor het bewerken van Tenant bronnen via aanroepen van de Microsoft Graph-API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Client geheim maken

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

U hebt nu een toepassing die gemachtigd is om gebruikers te *maken*, te *lezen*, bij te *werken* en te *verwijderen* in uw Azure AD B2C-Tenant. Ga door naar de volgende sectie om machtigingen voor *wachtwoord updates* toe te voegen.

## <a name="enable-user-delete-and-password-update"></a>Gebruikers verwijderen en wacht woord bijwerken inschakelen

De machtiging voor het *lezen en schrijven van gegevens* bevat **niet** de mogelijkheid om gebruikers te verwijderen of wacht woorden van gebruikers accounts bij te werken.

Als voor uw toepassing of script gebruikers moeten worden verwijderd of hun wacht woord moet worden bijgewerkt, wijst u de rol *gebruikers beheerder* toe aan uw toepassing:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en gebruik het **Directory +-abonnements** filter om over te scha kelen naar uw Azure AD B2C-Tenant.
1. Zoek en selecteer **Azure AD B2C**.
1. Selecteer onder **beheren** de optie **rollen en beheerders**.
1. Selecteer de rol **gebruikers beheerder** .
1. Selecteer **toewijzingen toevoegen**.
1. Voer in het tekstvak **selecteren** de naam in van de toepassing die u eerder hebt geregistreerd, bijvoorbeeld *managementapp1*. Selecteer uw toepassing als deze wordt weer gegeven in de zoek resultaten.
1. Selecteer **Toevoegen**. Het kan enkele minuten duren voordat de machtigingen volledig zijn door gegeven.

## <a name="next-steps"></a>Volgende stappen

Nu u uw beheer toepassing hebt geregistreerd en de vereiste machtigingen hebt verleend, kunnen uw toepassingen en services (bijvoorbeeld Azure-pijp lijnen) de referenties en machtigingen gebruiken om te communiceren met de Microsoft Graph-API. 

* [Een toegangstoken ophalen uit Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Het toegangs token gebruiken om Microsoft Graph aan te roepen](/graph/auth-v2-service#4-get-an-access-token)
* [B2C-bewerkingen die door Microsoft Graph worden ondersteund](microsoft-graph-operations.md)
* [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](microsoft-graph-operations.md)
* [Audit logboeken ophalen met de rapportage-API van Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
