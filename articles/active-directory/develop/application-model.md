---
title: Toepassings model | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het proces van het registreren van uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795655"
---
# <a name="application-model"></a>Toepassingsmodel

Toepassingen kunnen gebruikers zelf aanmelden of zich aanmelden bij een id-provider. In dit artikel worden de stappen beschreven die nodig zijn voor het registreren van een toepassing met het micro soft Identity-platform.

## <a name="register-an-application"></a>Een toepassing registreren

Als een id-provider weet dat een gebruiker toegang heeft tot een bepaalde app, moeten zowel de gebruiker als de toepassing zijn geregistreerd bij de ID-provider. Wanneer u uw toepassing registreert met Azure Active Directory (Azure AD), geeft u een identiteits configuratie op voor uw toepassing, zodat deze kan worden geïntegreerd met het micro soft Identity-platform. Door de app te registreren, kunt u het volgende doen:

* Pas de huis stijl van uw toepassing aan in het dialoog venster voor aanmelden. Het is belang rijk dat u zich aanmeldt in de eerste ervaring die een gebruiker heeft met uw app.
* Bepaal of u wilt toestaan dat gebruikers zich alleen kunnen aanmelden als ze deel uitmaken van uw organisatie. Deze architectuur wordt een toepassing met één Tenant genoemd. U kunt ook gebruikers toestaan zich aan te melden met een werk-of school account dat een multi tenant-toepassing wordt genoemd. U kunt ook persoonlijke micro soft-accounts of een sociaal account toestaan van LinkedIn, Google, enzovoort.
* Machtigingen voor het bereik van aanvragen. U kunt bijvoorbeeld het bereik ' gebruiker. read ' aanvragen, dat toestemming geeft om het profiel van de aangemelde gebruiker te lezen.
* Definieer bereiken waarmee de toegang tot uw web-API wordt gedefinieerd. Wanneer een app toegang wil krijgen tot uw API, moet dit doorgaans machtigingen aanvragen voor de bereiken die u definieert.
* Deel een geheim met het micro soft-identiteits platform waarmee de identiteit van de app wordt bewezen. Het gebruik van een geheim is relevant in het geval waarin de app een vertrouwelijke client toepassing is. Een vertrouwelijke client toepassing is een toepassing die referenties veilig kan bevatten. Er is een vertrouwde back-endserver vereist om de referenties op te slaan.

Nadat de app is geregistreerd, krijgt deze een unieke id die wordt gedeeld met het micro soft-identiteits platform wanneer het tokens aanvraagt. Als de app een [vertrouwelijke client toepassing](developer-glossary.md#client-application)is, deelt deze ook het geheim of de open bare sleutel, afhankelijk van het feit of er certificaten of geheimen zijn gebruikt.

Het micro soft Identity-platform vertegenwoordigt toepassingen met behulp van een model dat voldoet aan twee belang rijke functies:

* Identificeer de app aan de hand van de verificatie protocollen die worden ondersteund.
* Geef alle id's, Url's, geheimen en gerelateerde informatie op die nodig zijn voor de verificatie.

Het micro soft Identity-platform:

* Bevat alle gegevens die nodig zijn voor de ondersteuning van verificatie tijdens runtime.
* Bevat alle gegevens om te bepalen welke resources een app nodig heeft voor toegang en onder welke omstandigheden een bepaalde aanvraag moet worden afgehandeld.
* Biedt een infra structuur voor het implementeren van app-inrichting in de Tenant van de app-ontwikkelaar en op elke andere Azure AD-Tenant.
* Verwerkt de toestemming van de gebruiker tijdens de tijd van de token aanvraag en vereenvoudigt de dynamische inrichting van apps op tenants.

*Toestemming* is het proces van een resource-eigenaar die toestemming verleent voor een client toepassing om toegang te krijgen tot beveiligde bronnen, onder specifieke machtigingen, namens de eigenaar van de resource. Met het micro soft Identity platform kunt u:

* Gebruikers en beheerders voor het dynamisch verlenen of weigeren van toestemming voor de app om namens hen toegang te krijgen tot resources.
* Beheerders bepalen uiteindelijk welke apps kunnen worden uitgevoerd en welke gebruikers specifieke apps mogen gebruiken en hoe de Directory-bronnen worden geopend.

## <a name="multi-tenant-apps"></a>Apps met meerdere tenants

In het micro soft Identity-platform beschrijft een [toepassings object](developer-glossary.md#application-object) een toepassing. Tijdens de implementatie gebruikt het micro soft Identity-platform het object van de toepassing als een blauw druk om een [Service-Principal](developer-glossary.md#service-principal-object)te maken, die een concreet exemplaar van een toepassing in een directory of Tenant vertegenwoordigt. De Service-Principal definieert wat de app daad werkelijk kan doen in een specifieke doel directory, wie deze kan gebruiken, tot welke resources toegang heeft, enzovoort. Met het micro soft Identity-platform maakt u via [toestemming](developer-glossary.md#consent)een service-principal van een toepassings object.

Het volgende diagram toont een vereenvoudigd micro soft Identity platform inrichtings stroom op basis van toestemming. Er worden twee tenants weer gegeven: *A* en *B*.

* *Tenant A* is eigenaar van de toepassing.
* *Tenant B* is een instantiëring van de toepassing via een service-principal.

![Diagram waarin een vereenvoudigde inrichtings stroom wordt weer gegeven die door de toestemming wordt aangestuurd.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

De inrichtingsstroom verloopt als volgt:

1. Een gebruiker van Tenant B probeert zich aan te melden bij de app. Het autorisatie-eind punt vraagt een token voor de toepassing aan.
1. De gebruikers referenties zijn verkregen en geverifieerd voor authenticatie.
1. De gebruiker wordt gevraagd toestemming te geven voor de app om toegang te krijgen tot Tenant B.
1. Het micro soft Identity-platform gebruikt het toepassings object in Tenant A als een blauw druk voor het maken van een Service-Principal in Tenant B.
1. De gebruiker ontvangt het aangevraagde token.

U kunt dit proces herhalen voor meer tenants. Tenant A behoudt de blauw druk voor de app (toepassings object). Gebruikers en beheerders van alle andere tenants waar de app toestemming krijgt, houden de controle over wat de toepassing mag doen via het bijbehorende service-principal-object in elke Tenant. Zie [toepassings-en Service-Principal-objecten in het micro soft Identity-platform](app-objects-and-service-principals.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over verificatie en autorisatie in het micro soft-identiteits platform:

* Zie [Verificatie versus autorisatie](authentication-vs-authorization.md)voor meer informatie over de basis beginselen van verificatie en autorisatie.
* Zie [beveiligings tokens](security-tokens.md)voor meer informatie over het gebruik van toegangs tokens, het vernieuwen van tokens en id-tokens in verificatie en autorisatie.
* Zie [app-aanmeldings flow](app-sign-in-flow.md)voor meer informatie over de aanmeldings stroom van web-, desktop-en Mobile-apps.

Raadpleeg de volgende artikelen voor meer informatie over het toepassings model:

* Zie [hoe en waarom toepassingen worden toegevoegd aan Azure AD](active-directory-how-applications-are-added.md)voor meer informatie over toepassings objecten en service-principals in het micro soft-identiteits platform.
* Zie voor meer informatie over apps met één Tenant en apps met meerdere tenants [pacht in azure Active Directory](single-and-multi-tenant-apps.md).
* Zie [Azure Active Directory B2C-documentatie](../../active-directory-b2c/index.yml)voor meer informatie over hoe Azure AD ook voorziet in azure Active Directory B2C zodat organisaties gebruikers kunnen aanmelden, meestal klanten, met behulp van sociale identiteiten, zoals een Google-account.