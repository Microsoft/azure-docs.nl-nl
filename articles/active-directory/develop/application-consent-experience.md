---
title: Azure AD-App-toestemming
titleSuffix: Microsoft identity platform
description: Meer informatie over de Azure AD-toestemming om te zien hoe u deze kunt gebruiken bij het beheren en ontwikkelen van toepassingen in azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105408"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Inzicht in ervaringen met Azure AD-toepassingtoestemming

Meer informatie over de gebruikers ervaring voor de toepassings instemming van Azure Active Directory (Azure AD). U kunt toepassingen op intelligente wijze beheren voor uw organisatie en/of toepassingen ontwikkelen met een meer naadloze toestemming.

## <a name="consent-and-permissions"></a>Toestemming en machtigingen

Toestemming is het proces van een gebruiker die toestemming verleent voor toegang tot beveiligde resources voor hun naam. Een beheerder of gebruiker kan worden gevraagd om toestemming te geven om toegang tot hun organisatie/individuele gegevens toe te staan.

De daad werkelijke gebruikers ervaring van het verlenen van toestemming varieert afhankelijk van het beleid dat is ingesteld op de Tenant van de gebruiker, het bereik van de instantie van de gebruiker (of rol) en het type [machtigingen](v2-permissions-and-consent.md) dat door de client toepassing wordt aangevraagd. Dit betekent dat toepassings ontwikkelaars en Tenant beheerders enige controle over de toestemming hebben. Beheerders beschikken over de flexibiliteit om beleid in te stellen en uit te scha kelen voor een Tenant of app voor het beheren van de toestemmings ervaring in hun Tenant. Ontwikkel aars van toepassingen kunnen bepalen welke typen machtigingen worden aangevraagd en of ze gebruikers willen begeleiden via de toestemming stroom van de gebruiker of door de beheerder.

- De machtigings stroom van de **gebruiker** is wanneer een toepassings ontwikkelaar gebruikers doorstuurt naar het autorisatie-eind punt met het doel om alleen toestemming voor de huidige gebruiker vast te leggen.
- De **beheerder stuurt toestemming stroom** wanneer een toepassings ontwikkelaar gebruikers doorstuurt naar het eind punt van de beheerder met het doel om toestemming voor de hele Tenant vast te leggen. Ontwikkel aars van toepassingen moeten alle machtigingen in de `RequiredResourceAccess` eigenschap in het manifest van de toepassing weer geven om ervoor te zorgen dat de beheerder toestemming stroom goed werkt. Zie het [toepassings manifest](./reference-app-manifest.md)voor meer informatie.

## <a name="building-blocks-of-the-consent-prompt"></a>Bouw stenen van de toestemming prompt

De prompt voor toestemming is zodanig ontworpen dat gebruikers voldoende informatie hebben om te bepalen of ze de client toepassing kunnen vertrouwen voor toegang tot beveiligde bronnen. Inzicht in de bouw stenen helpt gebruikers bij het verlenen van toestemming om meer weloverwogen beslissingen te nemen en helpt ontwikkel aars bij het bouwen van betere gebruikers ervaring.

Het volgende diagram en deze tabel bevatten informatie over de bouw stenen van de toestemming prompt.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Bouw stenen van de toestemming prompt":::

| # | Onderdeel | Doel |
| ----- | ----- | ----- |
| 1 | Gebruikers-id | Deze id vertegenwoordigt de gebruiker die de client toepassing heeft aangevraagd om toegang te krijgen tot beveiligde bronnen namens. |
| 2 | Titel | De titel wordt gewijzigd op basis van het feit of de gebruikers de toestemming stroom van de gebruiker of beheerder door lopen. In de machtigings stroom van de gebruiker wordt de titel ' machtigingen aangevraagd ' weer gegeven in de stroom van de beheerder toestemming de titel heeft een extra regel ' accepteren voor uw organisatie '. |
| 3 | App-logo | Deze installatie kopie zou gebruikers kunnen helpen een visuele indicatie te krijgen van de vraag of deze app de app is die ze wilde gebruiken. Deze installatie kopie wordt verzorgd door toepassings ontwikkelaars en het eigendom van deze installatie kopie wordt niet gevalideerd. |
| 4 | Naam van app | Deze waarde moet de gebruikers op de hoogte stellen van de toegang tot de gegevens van de toepassing. Opmerking Deze naam wordt verschaft door de ontwikkel aars en het eigendom van deze app-naam wordt niet gevalideerd. |
| 5 | Uitgeversdomein | Deze waarde moet gebruikers hebben van een domein dat ze mogelijk kunnen evalueren voor betrouw baarheid. Dit domein wordt door de ontwikkel aars verschaft en het eigendom van dit domein van de uitgever wordt gevalideerd. |
| 6 | Uitgever gecontroleerd | De blauwe ' geverifieerde ' badge betekent dat de uitgever van de app de identiteit heeft gecontroleerd met behulp van een Microsoft Partner Network account en het verificatie proces heeft voltooid.|
| 7 | Informatie over uitgever  | Hier wordt weer gegeven of de toepassing is gepubliceerd door micro soft of uw organisatie. |
| 8 | Machtigingen | Deze lijst bevat de machtigingen die worden aangevraagd door de client toepassing. Gebruikers moeten altijd de typen machtigingen die worden aangevraagd, evalueren om te begrijpen welke gegevens de client toepassing voor hun naam mag gebruiken als ze deze accepteren. Als ontwikkelaar van toepassingen is het het beste om toegang aan te vragen bij de machtigingen met de minste bevoegdheden. |
| 9 | Beschrijving van machtiging | Deze waarde wordt verschaft door de service die de machtigingen weergeeft. Als u de beschrijvingen van machtigingen wilt zien, moet u de punt haken naast de machtiging scha kelen. |
| 10| App-voor waarden | Deze termen bevatten koppelingen naar de service voorwaarden en de privacyverklaring van de toepassing. De uitgever is verantwoordelijk voor het overzicht van de regels in hun service voorwaarden. Daarnaast is de uitgever verantwoordelijk voor het opheffen van de manier waarop ze gebruikers gegevens in hun privacyverklaring gebruiken en delen. Als de uitgever geen koppelingen naar deze waarden biedt voor toepassingen met meerdere tenants, wordt er een gemarkeerde waarschuwing weer gegeven bij de toestemming prompt. |
| 11 | https://myapps.microsoft.com | Dit is de koppeling waar gebruikers niet-micro soft-toepassingen kunnen controleren en verwijderen die momenteel toegang tot hun gegevens hebben. |
| 12 | Hier een rapport | Deze koppeling wordt gebruikt voor het rapporteren van een verdachte app als u de app niet vertrouwt, als u denkt dat de app een andere app imiteert, als u denkt dat de app uw gegevens kan misbruiken of om een andere reden. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>Voor de app is een machtiging vereist binnen het bereik van de instantie van de gebruiker

Een gemeen schappelijke instemming is dat de gebruiker toegang heeft tot een app waarvoor een machtigingenset is vereist die binnen het bereik van de instantie van de gebruiker valt. De gebruiker wordt omgeleid naar de door de gebruiker toestemmings stroom.

Beheerders krijgen een extra controle over de traditionele toestemming prompt waarmee ze toestemming kunnen geven namens de hele Tenant. Het besturings element wordt standaard uitgeschakeld. alleen wanneer beheerders expliciet het selectie vakje inschakelt, wordt toestemming verleend namens de volledige Tenant. Vanaf nu wordt dit selectie vakje alleen weer gegeven voor de rol van globale beheerder, zodat de beheerder van de Cloud en de app-beheerder dit selectie vakje niet kan zien.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Vraag om toestemming voor scenario 1a":::

Gebruikers krijgen de traditionele toestemming prompt te zien.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Scherm opname van de traditionele toestemming prompt.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>Voor de app is een machtiging vereist buiten het bereik van de bevoegdheid van de gebruiker

Een ander gemeen schappelijke instemming scenario is dat de gebruiker toegang heeft tot een app waarvoor ten minste één machtiging is vereist die buiten het bereik van de instantie van de gebruiker valt.

Beheerders krijgen een extra controle over de traditionele toestemming prompt waarmee ze toestemming kunnen geven namens de hele Tenant.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Vraag om toestemming voor scenario 1a":::

Gebruikers die geen beheerder zijn, worden geblokkeerd voor het verlenen van toestemming voor de toepassing, en ze krijgen de beheerder te vragen om toegang te krijgen tot de app.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Scherm opname van de toestemming vragen aan de gebruiker om een beheerder te vragen om toegang te krijgen tot de app.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>De gebruiker wordt omgeleid naar de beheerder toestemming stroom

Een ander algemeen scenario is wanneer de gebruiker navigeert naar of wordt doorgestuurd naar de toestemmings stroom van de beheerder.

Gebruikers met beheerders rechten krijgen de vraag om toestemming van de beheerder. De titel en de machtigings beschrijvingen worden op deze vraag gewijzigd. de wijzigingen markeren het feit dat het accepteren van deze prompt de app toegang geeft tot de aangevraagde gegevens namens de hele Tenant.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Vragen om toestemming voor scenario 3a":::

Gebruikers die geen beheerder zijn, worden geblokkeerd voor het verlenen van toestemming voor de toepassing, en ze krijgen de beheerder te vragen om toegang te krijgen tot de app.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Scherm opname van de toestemming vragen aan de gebruiker om een beheerder te vragen om toegang te krijgen tot de app.":::

## <a name="next-steps"></a>Volgende stappen

- Een stapsgewijze overzicht van [de manier waarop het Azure AD toestemmings raamwerk toestemming implementeert](./quickstart-register-app.md).
- Meer informatie over [hoe een multi tenant-toepassing het toestemming raamwerk kan gebruiken voor het](./howto-convert-app-to-be-multi-tenant.md) implementeren van de toestemming ' gebruiker ' en ' beheerder ', zodat meer geavanceerde toepassings patronen met meerdere lagen worden ondersteund.
- Meer informatie [over het configureren van het Publisher-domein van de app](howto-configure-publisher-domain.md).