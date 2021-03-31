---
title: AppSource-certificering verkrijgen voor Azure Active Directory | Microsoft Docs
description: Meer informatie over het verkrijgen van uw toepassing AppSource Certified for Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: d9a4da6fe65fda07609c7399518fa324017ea44c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649342"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>AppSource-certificering verkrijgen voor Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) is een doel voor zakelijke gebruikers om line-of-Business SaaS-toepassingen te detecteren, te uitproberen en te beheren (zelfstandige SaaS en invoeg toepassing voor bestaande micro soft SaaS-producten).

Als u een zelfstandige SaaS-toepassing op AppSource wilt weer geven, moet uw toepassing eenmalige aanmelding accepteren van werk accounts van elk bedrijf of organisatie met Azure Active Directory (Azure AD). Het aanmeldings proces moet de protocollen [OpenID Connect Connect](v1-protocols-openid-connect-code.md) of [OAuth 2,0](v1-protocols-oauth-code.md) gebruiken. SAML-integratie wordt niet geaccepteerd voor AppSource-certificering.

## <a name="guides-and-code-samples"></a>Hand leidingen en code voorbeelden

Als u meer wilt weten over het integreren van uw toepassing met Azure AD met open ID Connect, volgt u onze hand leidingen en code voorbeelden in de [Azure Active Directory hand leiding voor ontwikkel aars](v1-overview.md#get-started "Aan de slag met Azure AD voor ontwikkel aars").

## <a name="multi-tenant-applications"></a>Toepassingen voor meerdere tenants

Een *toepassing met meerdere tenants* is een toepassing die aanmeldingen accepteert van gebruikers van elk bedrijf of organisatie die Azure AD hebben zonder dat hiervoor een afzonderlijk exemplaar, configuratie of implementatie is vereist. AppSource beveelt aan dat toepassingen een multitenancy implementeren om de gratis proef versie met *één klik* in te scha kelen.

Voer de volgende stappen uit om multitenancy voor uw toepassing in te scha kelen:
1. Stel de `Multi-Tenanted` eigenschap `Yes` in op de gegevens van uw toepassings registratie in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Toepassingen die zijn gemaakt in de Azure Portal, worden standaard geconfigureerd als *[één Tenant](#single-tenant-applications)*.
1. Werk uw code bij om aanvragen naar het `common` eind punt te verzenden. U doet dit door het eind punt bij `https://login.microsoftonline.com/{yourtenant}` te werken van naar `https://login.microsoftonline.com/common*` .
1. Voor sommige platforms, zoals ASP .NET, moet u uw code ook bijwerken om meerdere verleners te accepteren.

Zie voor meer informatie over multitenancy [een Azure Active Directory-gebruiker (Azure AD) aanmelden met het toepassings patroon voor meerdere tenants](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Toepassingen met één Tenant

Een *toepassing met één Tenant* is een toepassing die alleen aanmeldingen accepteert van gebruikers van een gedefinieerd Azure AD-exemplaar. Externe gebruikers (met inbegrip van werk-of school accounts van andere organisaties of persoonlijke accounts) kunnen zich aanmelden bij een toepassing met één Tenant nadat elke gebruiker als gast account is toegevoegd aan het Azure AD-exemplaar dat de toepassing is geregistreerd. 

U kunt gebruikers als gast accounts toevoegen aan Azure AD via de [Azure AD B2B-samen werking](../external-identities/what-is-b2b.md) en u kunt dit op [een programmatische](../../active-directory-b2c/code-samples.md)manier doen. Bij het gebruik van B2B kunnen gebruikers een self-service portal maken waarvoor geen uitnodiging is vereist om zich aan te melden. Zie [Self-Service Portal voor Azure AD B2B-samenwerkings aanmelding](../external-identities/self-service-portal.md)voor meer informatie.

Toepassingen met één Tenant kunnen de *contact* ervaring mogelijk maken, maar als u de proef versie met eenmalige Klik/gratis wilt inschakelen, moet u in plaats daarvan multitenancy in uw toepassing inschakelen.

## <a name="appsource-trial-experiences"></a>AppSource-proef ervaring

### <a name="free-trial-customer-led-trial-experience"></a>Gratis proef versie (door de klant geleide proef ervaring)

De door de klant gestuurde proef versie is de ervaring die AppSource adviseert bij het bieden van één klik toegang tot uw toepassing. In het volgende voor beeld ziet u hoe deze ervaring eruitziet:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>Gebruiker vindt uw toepassing in de AppSource-website</li><li>Optie gratis proef versie selecteren</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource stuurt de gebruiker om naar een URL in uw website</li><li>Uw website start het proces voor <i>eenmalige aanmelding</i> automatisch (bij het laden van de pagina)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>De gebruiker wordt omgeleid naar de aanmeldings pagina van micro soft</li><li>Gebruiker verstrekt referenties om zich aan te melden</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>Gebruiker geeft toestemming voor uw toepassing</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>Het aanmelden is voltooid en de gebruiker wordt terug omgeleid naar uw website</li><li>Gebruiker start de gratis proef versie</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Neem contact met me op

U kunt de evaluatie versie van de partner gebruiken wanneer een hand matige of langlopende bewerking moet worden uitgevoerd om de gebruiker/het bedrijf in te richten, bijvoorbeeld uw toepassing moet virtuele machines, data base-exemplaren of bewerkingen inrichten die veel tijd in beslag nemen. Als de gebruiker in dit geval de knop **proef versie** selecteert en een formulier invult, stuurt AppSource u de contact gegevens van de gebruiker. Wanneer u deze informatie ontvangt, moet u de omgeving inrichten en de instructies naar de gebruiker verzenden om toegang te krijgen tot de proef ervaring:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>Gebruiker vindt uw toepassing in de AppSource-website</li><li>Selecteert de optie contact opnemen</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Een formulier invullen met contact gegevens</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>U ontvangt gebruikers gegevens</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Omgeving instellen</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Contact opnemen met de gebruiker met proef informatie</td>
        </tr>
        </table><br/><br/>
        <ul><li>U ontvangt de gegevens van de gebruiker en de evaluatie versie van de installatie</li><li>U stuurt de Hyper Link naar uw toepassing voor toegang tot de gebruiker</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>Gebruiker heeft toegang tot uw toepassing en voert het proces voor eenmalige aanmelding uit</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>Gebruiker geeft toestemming voor uw toepassing</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>Het aanmelden is voltooid en de gebruiker wordt terug omgeleid naar uw website</li><li>Gebruiker start de gratis proef versie</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Meer informatie

Zie [deze video](https://aka.ms/trialexperienceforwebapps)voor meer informatie over de AppSource-proef ervaring. 

## <a name="next-steps"></a>Volgende stappen

- Zie [verificatie scenario's voor Azure AD](./v1-authentication-scenarios.md)voor meer informatie over het bouwen van toepassingen die ondersteuning bieden voor Azure AD-aanmeldingen.
- Ga voor meer informatie over het weer geven van uw SaaS-toepassing in AppSource naar [AppSource-partner gegevens](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Ondersteuning krijgen

Voor Azure AD-integratie gebruiken we [micro soft Q&A](/answers/products/) met de community om ondersteuning te bieden.

Wij raden u ten zeerste aan om uw vragen te stellen op micro soft Q&A als eerste en door te bladeren naar bestaande problemen om te zien of iemand uw vraag eerder heeft gesteld. Zorg ervoor dat uw vragen of opmerkingen zijn gelabeld met [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Gebruik de volgende opmerkingen sectie om feedback te geven en ons te helpen onze inhoud te verfijnen en te vormen.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->