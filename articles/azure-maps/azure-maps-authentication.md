---
title: Verificatie met Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Meer informatie over twee manieren om aanvragen te verifiëren in Azure Maps: gedeelde sleutel verificatie en Azure Active Directory (Azure AD)-verificatie.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d47e98273e696a4b6e827d8ebbc71a297f2861cb
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905312"
---
# <a name="authentication-with-azure-maps"></a>Verificatie met Azure Maps

Azure Maps ondersteunt twee manieren om aanvragen te verifiëren: verificatie van gedeelde sleutel en [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) . In dit artikel worden beide verificatie methoden uitgelegd om uw implementatie van Azure Maps services te begeleiden.

> [!NOTE]
> Ter verbetering van de veilige communicatie met Azure Maps, ondersteunen we nu Transport Layer Security (TLS) 1,2. de ondersteuning voor TLS 1,0 en 1,1 wordt buiten gebruik gesteld. Als u momenteel TLS 1. x gebruikt, evalueert u uw TLS 1,2-gereedheid en ontwikkelt u een migratie plan met de tests die zijn beschreven in [het oplossen van het TLS 1,0-probleem](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Gedeelde sleutel verificatie

 Primaire en secundaire sleutels worden gegenereerd nadat het Azure Maps-account is gemaakt. U wordt geadviseerd om de primaire sleutel als abonnements sleutel te gebruiken bij het aanroepen van Azure Maps met gedeelde sleutel verificatie. Met gedeelde sleutel verificatie wordt een sleutel door een Azure Maps-account door gegeven aan een Azure Maps-service. Voor elke aanvraag voor het Azure Maps van services, voegt u de *abonnements sleutel* als een para meter toe aan de URL. De secundaire sleutel kan worden gebruikt in scenario's zoals wijzigingen in de Rolling sleutel.  

Zie [verificatie beheren](./how-to-manage-authentication.md#view-authentication-details)voor meer informatie over het weer geven van uw sleutels in de Azure Portal.

> [!TIP]
> Uit veiligheids overwegingen kunt u het beste wisselen tussen de primaire en secundaire sleutels. Als u sleutels wilt roteren, werkt u de app bij om de secundaire sleutel te gebruiken. Vervolgens implementeert u en drukt u op de knop voor cyclus/vernieuwen naast de primaire sleutel om een nieuwe primaire sleutel te genereren. De oude primaire sleutel wordt uitgeschakeld. Zie [Azure Key Vault instellen met wisselen en controleren van sleutels](../key-vault/secrets/tutorial-rotation-dual.md) voor meer informatie over het roteren van sleutels

## <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

Azure-abonnementen worden geleverd met een Azure AD-Tenant om nauw keurig toegangs beheer mogelijk te maken. Azure Maps biedt verificatie voor Azure Maps-Services met behulp van Azure AD. Azure AD biedt verificatie op basis van identiteiten voor gebruikers en toepassingen die zijn geregistreerd in de Azure AD-Tenant.

Azure Maps accepteert **OAuth 2,0** -toegangs tokens voor Azure AD-tenants die zijn gekoppeld aan een Azure-abonnement dat een Azure Maps account bevat. Azure Maps accepteert ook tokens voor:

* Azure AD-gebruikers
* Partner toepassingen die gebruikmaken van machtigingen die worden gedelegeerd door gebruikers
* Beheerde identiteiten voor Azure-resources

Azure Maps genereert een *unieke id (client-id)* voor elk Azure Maps-account. U kunt tokens aanvragen bij Azure AD wanneer u deze client-ID combineert met aanvullende para meters.

Zie [verificatie beheren in azure Maps](./how-to-manage-authentication.md)voor meer informatie over het configureren van Azure AD en het aanvragen van tokens voor Azure Maps.

Zie [Wat is verificatie?](../active-directory/develop/authentication-vs-authorization.md)voor algemene informatie over verificatie met Azure AD.

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Beheerde identiteiten voor Azure-resources en-Azure Maps

[Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) bieden Azure-Services met een automatisch beheerde op toepassingen gebaseerde beveiligingsprincipal die kan worden geverifieerd met Azure AD. Met op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) kan de beveiligings-principal van de beheerde identiteit worden gemachtigd om toegang te krijgen tot Azure Maps Services. Enkele voor beelden van beheerde identiteiten zijn: Azure App Service, Azure Functions en Azure Virtual Machines. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor een lijst met beheerde identiteiten.

### <a name="configuring-application-azure-ad-authentication"></a>Azure AD-verificatie voor toepassingen configureren

Toepassingen worden geverifieerd met de Azure AD-Tenant met behulp van een of meer ondersteunde scenario's van Azure AD. Elk Azure AD-toepassings scenario vertegenwoordigt verschillende vereisten op basis van bedrijfs behoeften. Voor sommige toepassingen zijn mogelijk gebruikers aanmelding vereist en voor andere toepassingen is mogelijk een toepassings aanmelding vereist. Zie [verificatie stromen en toepassings scenario's](../active-directory/develop/authentication-flows-app-scenarios.md)voor meer informatie.

Nadat de toepassing een toegangs token heeft ontvangen, verzendt de SDK en/of toepassing een HTTPS-aanvraag met de volgende set vereiste HTTP-headers naast andere REST API HTTP-headers:

| Header naam    | Waarde               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| Autorisatie  | Bearer-token eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` is de Azure Maps op een account gebaseerde GUID die wordt weer gegeven op de Azure Maps-verificatie pagina.

Hier volgt een voor beeld van een Azure Maps route aanvraag die gebruikmaakt van een Azure AD OAuth Bearer-token:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Zie [verificatie details weer geven](./how-to-manage-authentication.md#view-authentication-details)voor meer informatie over het weer geven van uw client-id.

## <a name="authorization-with-role-based-access-control"></a>Autorisatie met op rollen gebaseerd toegangs beheer

Azure Maps ondersteunt toegang tot alle principal-typen voor [op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md) van Azure, waaronder: individuele Azure AD-gebruikers,-groepen,-toepassingen, Azure-resources en door Azure beheerde identiteiten. Aan principal-typen wordt een set machtigingen verleend, ook wel een roldefinitie genoemd. Een roldefinitie biedt machtigingen voor het REST API acties. Het Toep assen van toegang tot een of meer Azure Maps accounts wordt een bereik genoemd. Bij het Toep assen van een principal, roldefinitie en bereik wordt een roltoewijzing gemaakt. 

In de volgende secties worden de concepten en onderdelen van Azure Maps integratie met Azure RBAC besproken. Als onderdeel van het proces om uw Azure Maps-account in te stellen, wordt een Azure AD-adres lijst gekoppeld aan het Azure-abonnement waarop het Azure Maps-account zich bevindt. 

Wanneer u Azure RBAC configureert, kiest u een beveiligingsprincipal en past u deze toe op een roltoewijzing. Zie [Azure-roltoewijzingen toevoegen of verwijderen](../role-based-access-control/role-assignments-portal.md)voor meer informatie over het toevoegen van roltoewijzingen aan de Azure Portal.

### <a name="picking-a-role-definition"></a>Een roldefinitie kiezen

De volgende roldefinitie typen bestaan ter ondersteuning van toepassings scenario's.

| Azure Role definition       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Gegevens lezer Azure Maps      | Biedt toegang tot onveranderbare Azure Maps REST Api's.                                                       |
| Inzender voor Azure Maps gegevens | Biedt toegang tot onveranderlijke Azure Maps REST-Api's. Veranderlijkheid wordt gedefinieerd door de acties: schrijven en verwijderen. |
| Definitie van aangepaste rol      | Maak een ontwikkelde rol om flexibele beperkte toegang tot Azure Maps REST-Api's in te scha kelen.                      |

Voor sommige Azure Maps Services zijn mogelijk verhoogde bevoegdheden vereist voor het uitvoeren van schrijf-of verwijder acties voor Azure Maps REST-Api's. Azure Maps rol gegevensinzender is vereist voor services die schrijf-of verwijderings acties bieden. In de volgende tabel wordt beschreven welke services Azure Maps contributor van gegevens van toepassing zijn voor het gebruik van schrijf-of verwijderings acties voor de betreffende service. Als er alleen lees acties worden gebruikt voor de service, kan Azure Maps gegevens lezer worden gebruikt in plaats van Azure Maps gegevens bijdrager.

| Azure Maps-service | Roldefinitie Azure Maps  |
| :----------------- | :-------------------------- |
| Gegevens               | Inzender voor Azure Maps gegevens |
| Creator            | Inzender voor Azure Maps gegevens |
| Ruimtelijk            | Inzender voor Azure Maps gegevens |

Zie [Azure RBAC configureren voor Azure Maps](./how-to-manage-authentication.md)voor meer informatie over het weer geven van uw Azure RBAC-instellingen.

#### <a name="custom-role-definitions"></a>Definities voor aangepaste rollen

Een aspect van toepassings beveiliging is het Toep assen van het principe van minimale bevoegdheden. Dit principe impliceert dat de beveiligingsprincipal alleen de vereiste toegang mag hebben en geen extra toegang hebben. Het maken van aangepaste Roldefinities kan use cases ondersteunen waarvoor een grotere nauw keurigheid van toegangs beheer nodig is. Als u een aangepaste roldefinitie wilt maken, kunt u specifieke gegevens acties selecteren om op te nemen of uit te sluiten voor de definitie.

De definitie van de aangepaste rol kan vervolgens worden gebruikt in een roltoewijzing voor elke beveiligings-principal. Zie [aangepaste rollen in azure](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste roldefinities van Azure.

Hier volgen enkele voorbeeld scenario's waarin aangepaste rollen de beveiliging van toepassingen kunnen verbeteren.

| Scenario                                                                                                                                                                                                                 | Aangepaste actie (s) voor de functie gegevens                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Een open bare of interactieve webpagina voor aanmelden met basis kaart tegels en geen andere REST-Api's.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Een toepassing die alleen omgekeerde geocodering en geen andere REST-Api's vereist.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Een rol voor een beveiligingsprincipal die het lezen van Azure Maps op basis van koppelings gegevens en de tegel REST-Api's van een basis kaart opvraagt.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Een rol voor een beveiligingsprincipal waarvoor het lezen, schrijven en verwijderen van op de maker gebaseerde kaart gegevens vereist is. Dit kan worden gedefinieerd als de rol gegevens editor voor kaarten, maar biedt geen toegang tot andere REST-Api's zoals basis kaart tegels. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Wat is bereik?

Wanneer u een roltoewijzing maakt, wordt deze gedefinieerd in de Azure-resource hiërarchie. Boven aan de hiërarchie bevindt zich een [beheer groep](../governance/management-groups/overview.md) en de laagste is een Azure-resource, zoals een Azure Maps-account.
Als u een roltoewijzing aan een resource groep toewijst, kan toegang tot meerdere Azure Maps accounts of bronnen in de groep worden ingeschakeld.

> [!TIP]
> De algemene aanbeveling van micro soft is het toewijzen van toegang aan het Azure Maps-account bereik, omdat hierdoor **onbedoelde toegang tot andere Azure Maps accounts** in hetzelfde Azure-abonnement wordt voor komen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure RBAC.
> [!div class="nextstepaction"]
> [Op rollen gebaseerd toegangsbeheer van Azure](../role-based-access-control/overview.md)

Zie voor meer informatie over het verifiëren van een toepassing met Azure AD en Azure Maps
> [!div class="nextstepaction"]
> [Verificatie in Azure Maps beheren](./how-to-manage-authentication.md)

Zie voor meer informatie over het verifiëren van de Azure Maps Map Control met Azure AD.
> [!div class="nextstepaction"]
> [Azure Maps Map Control gebruiken](./how-to-use-map-control.md)