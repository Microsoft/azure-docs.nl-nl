---
title: Apps & service-principals in azure AD | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de relatie tussen toepassings-en Service-Principal-objecten in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 7fe48cf28ab3004087e2b022d7a9eca69a1e58e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169627"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Toepassings- en service-principal-objecten in Azure Active Directory

In dit artikel worden de registratie van toepassingen, toepassings objecten en service-principals in Azure Active Directory beschreven: wat het zijn, hoe ze worden gebruikt en hoe ze met elkaar zijn verbonden. Er wordt ook een scenario met meerdere tenants weer gegeven ter illustratie van de relatie tussen het toepassings object van een toepassing en de bijbehorende service-principal-objecten.

## <a name="application-registration"></a>Een toepassing registreren
Als u identiteits-en toegangs beheer functies wilt overdragen aan Azure AD, moet een toepassing zijn geregistreerd bij een Azure AD- [Tenant](developer-glossary.md#tenant). Wanneer u uw toepassing registreert bij Azure AD, maakt u een identiteits configuratie voor uw toepassing waarmee deze kan worden geïntegreerd met Azure AD. Wanneer u een app registreert in de [Azure Portal][AZURE-Portal], kiest u of deze één Tenant is (alleen toegankelijk in uw Tenant) of multi tenant (toegankelijk in andere tenants) en kunt u eventueel een omleidings-URI instellen (waarbij het toegangs token wordt verzonden naar).

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="Scherm afbeelding van het Azure Portal een toepassings venster registreren":::

Wanneer u de app-registratie hebt voltooid, hebt u een wereld wijd unieke instantie van de app (het toepassings object) die zich in uw thuis Tenant of-map bevindt.  U hebt ook een wereld wijd unieke ID voor uw app (de app of client-ID).  In de portal kunt u geheimen of certificaten en scopes toevoegen om uw app te laten werken, de huis stijl van uw app aan te passen in het dialoog venster voor aanmelden en nog veel meer.

Als u een toepassing registreert in de portal, worden er automatisch een toepassings object en een Service-Principal-object gemaakt in uw thuis Tenant.  Als u een toepassing registreert/maakt met behulp van de Microsoft Graph Api's, is het maken van het Service-Principal-object een afzonderlijke stap.

## <a name="application-object"></a>Toepassings object
Een Azure AD-toepassing wordt gedefinieerd door de ene en enige toepassings object, die zich bevindt in de Azure AD-Tenant waar de toepassing is geregistreerd (bekend als de ' Home '-Tenant van de toepassing).  Een toepassings object wordt gebruikt als sjabloon of blauw druk om een of meer Service-Principal-objecten te maken.  Een service-principal wordt gemaakt in elke Tenant waar de toepassing wordt gebruikt. Net als bij een klasse in objectgeoriënteerd Program meren heeft het toepassings object enkele statische eigenschappen die worden toegepast op alle gemaakte service-principals (of exemplaren van de toepassing).

In het toepassings object worden drie aspecten van een toepassing beschreven: hoe de service tokens kan uitgeven om toegang te krijgen tot de toepassing, de resources waartoe de toepassing toegang moet hebben en de acties die de toepassing kan uitvoeren.

De **app-registraties** Blade in de [Azure Portal][AZURE-Portal] wordt gebruikt om de toepassings objecten in uw thuis Tenant weer te geven en te beheren.

![App-registraties Blade](./media/app-objects-and-service-principals/app-registrations-blade.png)

De entiteit van de Microsoft Graph- [toepassing][MS-Graph-App-Entity] definieert het schema voor de eigenschappen van een toepassings object.

## <a name="service-principal-object"></a>Service-Principal-object
Om toegang te krijgen tot bronnen die worden beveiligd door een Azure AD-Tenant, moet de entiteit die toegang vereist, worden vertegenwoordigd door een beveiligingsprincipal. Deze vereiste geldt voor zowel gebruikers (gebruikers-principal) als toepassingen (Service-Principal). De beveiligingsprincipal definieert het toegangs beleid en de machtigingen voor de gebruiker/toepassing in de Azure AD-Tenant. Hierdoor kunnen kern functies, zoals verificatie van de gebruiker/toepassing tijdens het aanmelden, en autorisatie tijdens de toegang tot bronnen worden ingeschakeld.

Een Service-Principal is de lokale weer gave of het toepassings exemplaar van een globaal toepassings object in één Tenant of directory. Een Service-Principal is een concreet exemplaar dat is gemaakt op basis van het toepassings object en die bepaalde eigenschappen overneemt van dat toepassings object.  Een service-principal wordt gemaakt in elke Tenant waar de toepassing wordt gebruikt en verwijst naar het wereld wijde unieke app-object.  Het Service-Principal-object definieert wat de app daad werkelijk in de specifieke Tenant kan doen, wie toegang heeft tot de app en welke resources de app kan gebruiken.

Wanneer een toepassing toestemming krijgt om toegang te krijgen tot bronnen in een Tenant (na registratie of [toestemming](developer-glossary.md#consent)), wordt er een Service-Principal-object gemaakt. U kunt ook een Service Principal-object maken in een Tenant met behulp van [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http), de [Azure Portal][AZURE-Portal]en andere hulpprogram ma's.  Wanneer u de portal gebruikt, wordt automatisch een service-principal gemaakt wanneer u een toepassing registreert.

De Blade **bedrijfs toepassingen** in de portal wordt gebruikt om de service-principals in een Tenant weer te geven en te beheren. U kunt de machtigingen van de Service-Principal zien, machtigingen voor de gebruiker die hiervoor zijn gemachtigd, de gebruikers die toestemming hebben gegeven, aanmeldings gegevens en meer.

![Blade Enter prise-apps](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

De [entiteit Microsoft Graph ServicePrincipal][MS-Graph-Sp-Entity] definieert het schema voor de eigenschappen van een Service Principal-object.

## <a name="relationship-between-application-objects-and-service-principals"></a>Relatie tussen toepassings objecten en service-principals

Het toepassings object is de *wereld wijde* weer gave van uw toepassing voor gebruik in alle tenants en de Service-Principal is de *lokale* weer gave voor gebruik in een specifieke Tenant.

Het toepassingsobject fungeert als de sjabloon waaruit gemeenschappelijke en standaardeigenschappen worden *afgeleid* voor gebruik bij het maken van de bijbehorende service-principal-objecten. Een toepassings object heeft daarom een 1:1-relatie met de software toepassing en een 1: veel-relatie met de bijbehorende service-principal-object (en).

Een Service-Principal moet worden gemaakt in elke Tenant waar de toepassing wordt gebruikt, waardoor er een identiteit kan worden ingesteld voor aanmelding en/of toegang tot bronnen die worden beveiligd door de Tenant. Een toepassing in één tenant heeft slechts één service-principal (in de starttenant) die is gemaakt en waarvoor toestemming is gegeven voor gebruik tijdens de toepassingsregistratie. Een webtoepassing/API met meerdere tenants bevat ook een service-principal die is gemaakt in elke Tenant, waar een gebruiker van die Tenant toestemming heeft gegeven om het te gebruiken.

> [!NOTE]
> Alle wijzigingen die u aanbrengt in het toepassings object, worden ook weer gegeven in het Service-Principal-object in de thuis Tenant van de toepassing (de Tenant waar deze is geregistreerd). Voor multi tenant-toepassingen worden wijzigingen aan het toepassings object niet weer gegeven in de Service-Principal-objecten van een consumenten Tenant, totdat de toegang wordt verwijderd via het [toegangs venster](https://myapps.microsoft.com) van de toepassing en opnieuw wordt toegewezen.
>
> Houd er rekening mee dat systeem eigen toepassingen standaard worden geregistreerd als multi tenant.

## <a name="example"></a>Voorbeeld

In het volgende diagram ziet u de relatie tussen het toepassings object van een toepassing en de bijbehorende service-principal-objecten, in de context van een voor beeld van een multi tenant-toepassing met de naam **HR-app**. Er zijn drie Azure AD-tenants in dit voorbeeld scenario:

- **Adatum** : de Tenant die wordt gebruikt door het bedrijf dat de **HR-app** heeft ontwikkeld
- **Contoso** : de Tenant die wordt gebruikt door de contoso-organisatie, die een consument is van de **HR-app**
- **Fabrikam** : de Tenant die wordt gebruikt door de fabrikam-organisatie, die ook de **HR-app** verbruikt

![Relatie tussen object App-object en Service-Principal](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In dit voorbeeld scenario:

| Stap | Beschrijving |
|------|-------------|
| 1    | Is het proces van het maken van de toepassing en Service-Principal-objecten in de thuis Tenant van de toepassing. |
| 2    | Wanneer Contoso en fabrikam beheerders toestemming geven, wordt een Service-Principal-object gemaakt in de Azure AD-Tenant van het bedrijf en worden de machtigingen toegewezen die de beheerder heeft verleend. Houd er ook rekening mee dat de HR-app kan worden geconfigureerd/ontworpen om toestemming door gebruikers voor individueel gebruik toe te staan. |
| 3    | De tenants van de consument van de HR-toepassing (Contoso en fabrikam) hebben elk een eigen Service-Principal-object. Elk voor beeld hiervan is het gebruik van een exemplaar van de toepassing tijdens runtime, met de machtigingen die de respectieve beheerder heeft ingestemd. |

## <a name="next-steps"></a>Volgende stappen

- U kunt de [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) gebruiken om de toepassing en Service-Principal-objecten op te vragen.
- U kunt toegang krijgen tot het toepassings object van een toepassing met behulp van de Microsoft Graph-API, de manifest-editor [van de Azure Portal][AZURE-Portal] of [Azure AD Power shell-cmdlets](/powershell/azure/?view=azureadps-2.0), zoals wordt vertegenwoordigd door de entiteit van de OData- [toepassing][MS-Graph-App-Entity].
- U kunt toegang krijgen tot het Service-Principal-object van een toepassing via de Microsoft Graph-API of [Azure AD Power shell-cmdlets](/powershell/azure/?view=azureadps-2.0), zoals wordt aangegeven door de OData [ServicePrincipal-entiteit][MS-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
