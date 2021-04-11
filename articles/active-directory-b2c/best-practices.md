---
title: Aanbevolen procedures voor Azure AD B2C
titleSuffix: Azure AD B2C
description: Aanbevelingen en aanbevolen procedures waarmee u rekening moet houden wanneer u werkt met Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 2c8a9121d0e36eb51cd02c2c884ddcaa0dd79a79
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226206"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Aanbevelingen en aanbevolen procedures voor Azure Active Directory B2C

De volgende aanbevolen procedures en aanbevelingen omvatten enkele van de belangrijkste aspecten van het integreren van Azure Active Directory (Azure AD) B2C in bestaande of nieuwe toepassings omgevingen.

## <a name="fundamentals"></a>Basisprincipes

| Best practice | Description |
|--|--|
| Gebruikers stromen kiezen voor de meeste scenario's | Het Framework voor identiteits ervaring van Azure AD B2C is de kern sterkte van de service. Het beleid beschrijft volledige identiteits ervaringen, zoals aanmelden, aanmelden of profiel bewerking. Om u te helpen bij het instellen van de meest voorkomende identiteits taken, bevat de Azure AD B2C Portal vooraf gedefinieerde, Configureer bare beleids regels met de naam gebruikers stromen. Met gebruikers stromen kunt u binnen enkele minuten fantastische gebruikers ervaringen maken, met slechts enkele klikken. [Meer informatie over het gebruik van gebruikers stromen versus aangepast beleid](user-flow-overview.md#comparing-user-flows-and-custom-policies).|
| App-registraties | Elke toepassing (Web, native) en API die wordt beveiligd, moeten zijn geregistreerd in Azure AD B2C. Als een app zowel een web-als een systeem eigen versie van iOS en Android heeft, kunt u deze registreren als één toepassing in Azure AD B2C met dezelfde client-ID. Meer informatie over het [registreren van OIDC-, SAML-, Web-en systeem eigen apps](./tutorial-register-applications.md?tabs=applications). Meer informatie over [toepassings typen die kunnen worden gebruikt in azure AD B2C](./application-types.md). |
| Naar facturering van maandelijkse actieve gebruikers verplaatsen | Azure AD B2C is verplaatst van maandelijkse actieve authenticaties naar maandelijkse MAU-facturering (actieve gebruikers). De meeste klanten vinden dit model kosten effectief. Meer [informatie over de facturering van maandelijkse actieve gebruikers](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planning en ontwerp

Definieer uw toepassings-en service architectuur, inventarisatie van de huidige systemen en plan uw migratie naar Azure AD B2C.

| Best practice | Description |
|--|--|
| Een end-to-end oplossing ontwerpen | Neem alle toepassings afhankelijkheden op bij het plannen van een Azure AD B2C-integratie. Overweeg alle services en producten die zich op dit moment in uw omgeving bevinden of die moeten worden toegevoegd aan de oplossing, bijvoorbeeld Azure Functions, CRM-systemen (Customer Relationship Management), Azure API Management Gateway en Storage services. Houd rekening met de beveiliging en schaal baarheid van alle services. |
| De ervaringen van uw gebruikers documenteren | Gedetailleerde informatie over de gebruiker die uw klanten kan ervaren, kan uw toepassing in de weg staan. Voeg elk scherm toe en eventuele vertakkings stromen die ze kunnen tegen komen bij interactie met de identiteits-en profiel aspecten van uw toepassing. Neem gebruiks vriendelijkheid, toegankelijkheid en lokalisatie op in uw planning. |
| Kies het juiste verificatie Protocol |  Zie [scenario's en ondersteunde verificatie stromen](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)voor een uitsplitsing van de verschillende toepassings scenario's en de aanbevolen verificatie stromen. |
| Testen van de end-to-end gebruikers ervaring van een test-of-concept | Begin met onze [micro soft-code voorbeelden](code-samples.md) en community-voor [beelden](https://github.com/azure-ad-b2c/samples). |
| Een migratie plan maken |Het plannen van tevoren kan de migratie soepeler uitvoeren. Meer informatie over [gebruikers migratie](user-migration.md).|
| Bruikbaarheid versus beveiliging | Uw oplossing moet de juiste balans tussen toepassings bruikbaarheid en het acceptabele risico niveau van uw organisatie halen. |
| On-premises afhankelijkheden verplaatsen naar de Cloud | Overweeg om bestaande toepassings afhankelijkheden te verplaatsen naar de cloud om een flexibele oplossing te garanderen. |
| Bestaande apps migreren naar b2clogin.com | De afschaffing van login.microsoftonline.com gaat in van kracht voor alle Azure AD B2C tenants op 04 december 2020. [Meer informatie](b2clogin.md). |
| Identiteits beveiliging en voorwaardelijke toegang gebruiken | Gebruik deze mogelijkheden voor aanzienlijk meer controle over Risk ante authenticatie en toegangs beleid. Azure AD B2C Premium P2 is vereist. [Meer informatie](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementatie

Houd tijdens de implementatie fase rekening met de volgende aanbevelingen.

| Best practice | Description |
|--|--|
| Aangepaste beleids regels bewerken met de extensie Azure AD B2C voor Visual Studio code | Down load Visual Studio code en deze [door de Community ontwikkelde uitbrei ding van de Visual Studio code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Hoewel dit geen officiële micro soft-product is, bevat de Azure AD B2C-extensie voor Visual Studio code verschillende functies waarmee u eenvoudiger aangepaste beleids regels kunt gebruiken. |
| Meer informatie over het oplossen van Azure AD B2C | Meer informatie over het [oplossen van problemen met aangepaste beleids regels](./troubleshoot-custom-policies.md?tabs=applications) tijdens de ontwikkeling. Meer informatie over hoe een normale verificatie stroom eruit ziet en hulpprogram ma's gebruikt voor het detecteren van afwijkingen en fouten. Gebruik bijvoorbeeld [Application Insights](troubleshoot-with-application-insights.md) om de uitvoer logboeken van gebruikers ritten te controleren. |
| Maak gebruik van onze bibliotheek met bewezen aangepaste beleids patronen | Zoek voor [beelden](https://github.com/azure-ad-b2c/samples) voor een aantal verbeterde gebruikers ritten van Azure AD B2C klant identiteit en toegangs beheer (CIAM). |

## <a name="testing"></a>Testen

Uw Azure AD B2C-implementatie testen en automatiseren.

| Best practice | Description |
|--|--|
| Account voor globaal verkeer | Gebruik verkeers bronnen van een ander globaal adres om de vereisten voor prestaties en lokalisatie te testen. Zorg ervoor dat alle HTML-, CSS-en afhankelijkheden voldoen aan uw prestatie behoeften. |
| Functionele en UI-tests | Test de gebruikers die end-to-end stromen. Voeg elke paar minuten synthetische tests toe met behulp van selenium, VS web test, enzovoort. |
| Pen testen | Voordat u aan de slag gaat met uw oplossing, voert u de oefeningen voor indringings tests uit om te controleren of alle onderdelen veilig zijn, inclusief eventuele afhankelijkheden van derden. Controleer of u uw Api's met toegangs tokens hebt beveiligd en het juiste verificatie protocol hebt gebruikt voor uw toepassings scenario. Meer informatie over [Indringings tests](../security/fundamentals/pen-testing.md) en de [Microsoft Cloud gecombineerde indringings test regels van engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B testen | Vlieg uw nieuwe functies met een kleine, wille keurige set gebruikers voordat u de hele populatie uitrollen. Als Java script is ingeschakeld in Azure AD B2C, kunt u integreren met een/B-test programma zoals geoptimaliseerd, duidelijkheid en anderen. |
| Belasting testen | Azure AD B2C kan worden geschaald, maar uw toepassing kan alleen worden geschaald als alle afhankelijkheden kunnen worden geschaald. Laad-test uw Api's en CDN. |
| Beperking |  Azure AD B2C beperkt het verkeer als er te veel aanvragen worden verzonden vanuit dezelfde bron in een korte periode. Gebruik verschillende verkeers bronnen tijdens het testen en handel de `AADB2C90229` fout code probleemloos af in uw toepassingen. |
| Automation | Gebruik pijp lijnen voor continue integratie en levering (CI/CD) om testen en implementaties te automatiseren, bijvoorbeeld [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operations

Uw Azure AD B2C omgeving beheren.

| Best practice | Description |
|--|--|
| Meerdere omgevingen maken | Voor eenvoudigere bewerkingen en implementaties kunt u afzonderlijke omgevingen maken voor ontwikkeling, testen, voor bereiding op productie en productie. Maak voor elke Azure AD B2C tenants. |
| Versie beheer voor uw aangepaste beleids regels gebruiken | Overweeg het gebruik van GitHub, Azure opslag plaatsen of een ander versie beheersysteem op de Cloud voor uw aangepaste beleids Azure AD B2C. |
| Gebruik de Microsoft Graph-API om het beheer van uw B2C-tenants te automatiseren | Microsoft Graph-Api's:<br/>[Framework voor identiteits ervaring](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) beheren (aangepast beleid)<br/>[Sleutels](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Gebruikersstromen](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integreren met Azure DevOps | Met een [CI/cd-pijp lijn](deploy-custom-policies-devops.md) kunt u eenvoudig code verplaatsen tussen verschillende omgevingen en de productie gereedheid op elk moment garanderen.   |
| Integreren met Azure Monitor | [Audit logboek gebeurtenissen](view-audit-logs.md) worden alleen zeven dagen bewaard. [Integreer met Azure monitor](azure-monitor.md) om de logboeken te bewaren voor gebruik op lange termijn, of integreer met hulpprogram ma's voor beveiligings informatie en Event Management van derden om inzicht te krijgen in uw omgeving. |
| Actieve waarschuwingen en bewaking instellen | [Gebruikers gedrag bijhouden](./analytics-with-application-insights.md) in azure AD B2C met behulp van Application Insights. |

## <a name="support-and-status-updates"></a>Ondersteuning en status updates

Blijf op de hoogte van de status van de service en vind ondersteunings opties.

| Best practice | Description |
|--|--|
| [Service-updates](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Blijf op de hoogte van Azure AD B2C product updates en aankondigingen. |
| [Microsoft-ondersteuning](support-options.md) | Een ondersteunings aanvraag indienen voor Azure AD B2C technische problemen. Ondersteuning bij facturering en abonnements beheer is gratis. |
| [Status van Azure](https://status.azure.com/status) | De huidige status van alle Azure-Services weer geven. |