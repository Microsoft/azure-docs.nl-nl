---
title: Apps & service-principals in Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de relatie tussen toepassings- en service-principalobjecten in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727056"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Toepassings- en service-principal-objecten in Azure Active Directory

In dit artikel worden toepassingsregistratie, toepassingsobjecten en service-principals in Azure Active Directory beschreven: wat ze zijn, hoe ze worden gebruikt en hoe ze aan elkaar zijn gerelateerd. Er wordt ook een voorbeeldscenario met meerdere tenants gepresenteerd om de relatie te illustreren tussen het toepassingsobject van een toepassing en de bijbehorende service-principal-objecten.

## <a name="application-registration"></a>Een toepassing registreren
Als u identiteits- en toegangsbeheerfuncties wilt delegeren aan Azure AD, moet een toepassing worden geregistreerd bij een Azure [AD-tenant.](developer-glossary.md#tenant) Wanneer u uw toepassing registreert bij Azure AD, maakt u een identiteitsconfiguratie voor uw toepassing waarmee deze kan worden geïntegreerd met Azure AD. Wanneer u een app registreert in de [Azure Portal,][AZURE-Portal]kiest u of het een enkele tenant is (alleen toegankelijk in uw tenant) of meerdere tenants (toegankelijk in andere tenants) en kunt u desgewenst een omleidings-URI instellen (waar het toegangs token naar wordt verzonden).

Zie de quickstart voor app-registratie voor stapsgewijse instructies voor het registreren [van een app.](quickstart-register-app.md)

Wanneer u de registratie van de app hebt voltooid, hebt u een wereldwijd uniek exemplaar van de app (het toepassingsobject [)](#application-object)dat zich in uw basis-tenant of -map in de app of map behuist.  U hebt ook een wereldwijd unieke id voor uw app (de app of client-id).  In de portal kunt u vervolgens geheimen of certificaten en scopes toevoegen om uw app te laten werken, de huisstijl van uw app aan te passen in het aanmeldingsdialoogvenster en meer.

Als u een toepassing registreert in de portal, worden er automatisch een toepassingsobject en een service-principal-object gemaakt in uw basis-tenant.  Als u een toepassing registreert/maakt met behulp van Microsoft Graph API's, is het maken van het service-principal-object een afzonderlijke stap.

## <a name="application-object"></a>Toepassingsobject
Een Azure AD-toepassing wordt gedefinieerd door het enige toepassingsobject dat zich in de Azure AD-tenant bevindt waarin de toepassing is geregistreerd (ook wel de 'thuisten tenant' van de toepassing genoemd).  Een toepassingsobject wordt gebruikt als een sjabloon of blauwdruk om een of meer service-principal-objecten te maken.  Er wordt een service-principal gemaakt in elke tenant waarin de toepassing wordt gebruikt. Net als bij een klasse in objectgeoriënteerd programmeren, heeft het toepassingsobject enkele statische eigenschappen die worden toegepast op alle gemaakte service-principals (of toepassings-exemplaren).

Het toepassingsobject beschrijft drie aspecten van een toepassing: hoe de service tokens kan uitgeven om toegang te krijgen tot de toepassing, resources die de toepassing mogelijk moet openen en de acties die de toepassing kan uitvoeren.

De **App-registraties** blade in [de Azure Portal][AZURE-Portal] wordt gebruikt om de toepassingsobjecten in uw thuis-tenant weer te geven en te beheren.

![App-registraties blade](./media/app-objects-and-service-principals/app-registrations-blade.png)

De Microsoft Graph [Application definieert][MS-Graph-App-Entity] het schema voor de eigenschappen van een toepassingsobject.

## <a name="service-principal-object"></a>Service-principal-object
Voor toegang tot resources die worden beveiligd door een Azure AD-tenant, moet de entiteit die toegang vereist worden vertegenwoordigd door een beveiligingsprincipaal. Deze vereiste geldt voor zowel gebruikers (user principal) als toepassingen (service-principal). De beveiligingsprincipaal definieert het toegangsbeleid en de machtigingen voor de gebruiker/toepassing in de Azure AD-tenant. Hierdoor zijn kernfuncties mogelijk, zoals verificatie van de gebruiker/toepassing tijdens het aanmelden en autorisatie tijdens toegang tot resources.

Er zijn drie soorten service-principals: toepassing, beheerde identiteit en verouderd.

Het eerste type service-principal is de lokale representatie, of toepassings-instantie, van een globaal toepassingsobject in één tenant of map. In dit geval is een service-principal een concrete instantie die is gemaakt op basis van het toepassingsobject en bepaalde eigenschappen overgenomen van dat toepassingsobject. Er wordt een service-principal gemaakt in elke tenant waar de toepassing wordt gebruikt en verwijst naar het wereldwijd unieke app-object.  Het service-principal-object definieert wat de app daadwerkelijk kan doen in de specifieke tenant, wie toegang heeft tot de app en tot welke resources de app toegang heeft.

Wanneer een toepassing toegang krijgt tot resources in een tenant (na registratie of [toestemming),](developer-glossary.md#consent)wordt er een service-principal-object gemaakt. U kunt ook service-principalobjecten maken in een tenant [met behulp van Azure PowerShell,](howto-authenticate-service-principal-powershell.md) [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli) [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), [de Azure Portal][AZURE-Portal]en andere hulpprogramma's. Wanneer u de portal gebruikt, wordt er automatisch een service-principal gemaakt wanneer u een toepassing registreert.

Het tweede type service-principal wordt gebruikt om een [beheerde identiteit weer te geven.](/azure/active-directory/managed-identities-azure-resources/overview) Beheerde identiteiten elimineren de noodzaak voor ontwikkelaars om referenties te beheren. Beheerde identiteiten bieden een identiteit die toepassingen kunnen gebruiken bij het maken van verbinding met resources die ondersteuning bieden voor Azure AD-verificatie. Wanneer een beheerde identiteit is ingeschakeld, wordt er een service-principal gemaakt die die beheerde identiteit vertegenwoordigt in uw tenant. Service-principals die beheerde identiteiten vertegenwoordigen, kunnen toegang en machtigingen krijgen, maar kunnen niet rechtstreeks worden bijgewerkt of gewijzigd.

Het derde type service-principal vertegenwoordigt een verouderde app (een app die is gemaakt voordat app-registraties werden geïntroduceerd of gemaakt via verouderde ervaringen). Een verouderde service-principal kan referenties, service-principalnamen, antwoord-URL's en andere eigenschappen hebben die kunnen worden bewerkt door een geautoriseerde gebruiker, maar waaraan geen app-registratie is gekoppeld. De service-principal kan alleen worden gebruikt in de tenant waar deze is gemaakt.

De Microsoft Graph [ServicePrincipal-entiteit][MS-Graph-Sp-Entity] definieert het schema voor de eigenschappen van een service-principal-object.

De **blade Bedrijfstoepassingen** in de portal wordt gebruikt om de service-principals in een tenant weer te bieden en te beheren. U ziet de machtigingen van de service-principal, de machtigingen die door de gebruiker zijn verleend, welke gebruikers die toestemming hebben gegeven, aanmeldingsgegevens hebben gedaan en meer.

![Blade Bedrijfsapps](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Relatie tussen toepassingsobjecten en service-principals

Het toepassingsobject is de *globale* weergave van uw toepassing voor gebruik  in alle tenants en de service-principal is de lokale weergave voor gebruik in een specifieke tenant.

Het toepassingsobject fungeert als de sjabloon waaruit gemeenschappelijke en standaardeigenschappen worden *afgeleid* voor gebruik bij het maken van de bijbehorende service-principal-objecten. Een toepassingsobject heeft daarom een 1:1-relatie met de softwaretoepassing en een 1:many-relatie met de bijbehorende service-principal-object(s).

Er moet een service-principal worden gemaakt in elke tenant waarin de toepassing wordt gebruikt, zodat er een identiteit kan worden gemaakt voor aanmelding en/of toegang tot resources die worden beveiligd door de tenant. Een toepassing in één tenant heeft slechts één service-principal (in de starttenant) die is gemaakt en waarvoor toestemming is gegeven voor gebruik tijdens de toepassingsregistratie. Een toepassing met meerdere tenants heeft ook een service-principal die is gemaakt in elke tenant waar een gebruiker van die tenant toestemming heeft gegeven voor het gebruik ervan.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Gevolgen van het wijzigen en verwijderen van toepassingen
Wijzigingen die u aan uw toepassingsobject aan brengen, worden ook weerspiegeld in het service-principal-object in de basis-tenant van de toepassing (de tenant waar deze is geregistreerd). Dit betekent dat als u een toepassingsobject verwijdert, ook het service-principal-object van de basisten tenant wordt verwijderd.  Als u dat toepassingsobject herstelt, wordt de bijbehorende service-principal echter niet hersteld. Voor toepassingen met meerdere tenants worden wijzigingen in het toepassingsobject niet doorgevoerd in de service-principalobjecten van consumentententensen, totdat de toegang wordt verwijderd via de [Application Toegangsvenster](https://myapps.microsoft.com) en opnieuw wordt verleend.

## <a name="example"></a>Voorbeeld

Het volgende diagram illustreert de relatie tussen het toepassingsobject van een toepassing en de bijbehorende service-principal-objecten, in de context van een voorbeeldtoepassing met meerdere tenants met de naam **HR-app**. Er zijn drie Azure AD-tenants in dit voorbeeldscenario:

- **Adatum:** de tenant die wordt gebruikt door het bedrijf dat de **HR-app heeft ontwikkeld**
- **Contoso:** de tenant die wordt gebruikt door de Contoso-organisatie, een consument van de **HR-app**
- **Fabrikam:** de tenant die wordt gebruikt door de Fabrikam-organisatie, die ook de **HR-app gebruikt**

![Relatie tussen app-object en service-principal-object](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In dit voorbeeldscenario:

| Stap | Beschrijving |
|------|-------------|
| 1    | Is het proces van het maken van de toepassings- en service-principalobjecten in de basis-tenant van de toepassing. |
| 2    | Wanneer Contoso- en Fabrikam-beheerders toestemming geven, wordt er een service-principalobject gemaakt in de Azure AD-tenant van hun bedrijf en worden de machtigingen toegewezen die de beheerder heeft verleend. Houd er ook rekening mee dat de HR-app kan worden geconfigureerd/ontworpen om gebruikers toestemming te geven voor individueel gebruik. |
| 3    | De tenants van consumenten van de HR-toepassing (Contoso en Fabrikam) hebben elk hun eigen service-principal-object. Elk vertegenwoordigt het gebruik van een exemplaar van de toepassing tijdens runtime, dat wordt beheerd door de machtigingen die zijn verleend door de respectieve beheerder. |

## <a name="next-steps"></a>Volgende stappen

- U kunt de Microsoft Graph [Explorer gebruiken om zowel](https://developer.microsoft.com/graph/graph-explorer) de toepassings- als de service-principal-objecten op te vragen.
- U hebt toegang tot het toepassingsobject van een toepassing met behulp van de Microsoft Graph-API, de toepassingsmanifesteditor van de [Azure Portal of][AZURE-Portal] Azure [AD PowerShell-cmdlets,](/powershell/azure/)zoals vertegenwoordigd door de OData [Application-entiteit][MS-Graph-App-Entity].
- U hebt toegang tot het service-principal-object van een toepassing via de Microsoft Graph-API of [Azure AD PowerShell-cmdlets,](/powershell/azure/)zoals vertegenwoordigd door de OData [ServicePrincipal-entiteit.][MS-Graph-Sp-Entity]

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
