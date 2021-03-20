---
title: AppSource-certificering ophalen voor Azure Active Directory | Microsoft Docs
description: Meer informatie over hoe u uw toepassing kunt verkrijgen AppSource gecertificeerd voor Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647503"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>AppSource-gecertificeerd voor Azure Active Directory ophalen

[Microsoft AppSource](https://appsource.microsoft.com/) is een doel voor zakelijke gebruikers om line-of-Business SaaS-toepassingen te detecteren, te uitproberen en te beheren (zelfstandige SaaS en invoeg toepassing voor bestaande micro soft SaaS-producten).

Als u een zelfstandige SaaS-toepassing op AppSource wilt weer geven, moet uw toepassing eenmalige aanmelding accepteren van werk accounts van elk bedrijf of organisatie met Azure Active Directory (Azure AD). Het aanmeldings proces moet de protocollen [OpenID Connect Connect](v2-protocols-oidc.md) of [OAuth 2,0](v2-oauth2-auth-code-flow.md) gebruiken. SAML-integratie wordt niet geaccepteerd voor AppSource-certificering.

## <a name="multi-tenant-applications"></a>Toepassingen voor meerdere tenants

Een *toepassing met meerdere tenants* is een toepassing die aanmeldingen accepteert van gebruikers van elk bedrijf of organisatie die Azure AD hebben zonder dat hiervoor een afzonderlijk exemplaar, configuratie of implementatie is vereist. AppSource beveelt aan dat toepassingen een multitenancy implementeren om de gratis proef versie met *één klik* in te scha kelen.

Voer de volgende stappen uit om multitenancy voor uw toepassing in te scha kelen:
1. Stel de `Multi-Tenanted` eigenschap `Yes` in op de gegevens van uw toepassings registratie in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Toepassingen die zijn gemaakt in de Azure Portal, worden standaard geconfigureerd als *[één Tenant](#single-tenant-applications)*.
1. Werk uw code bij om aanvragen naar het `common` eind punt te verzenden. U doet dit door het eind punt bij `https://login.microsoftonline.com/{yourtenant}` te werken van naar `https://login.microsoftonline.com/common*` .
1. Voor sommige platforms, zoals ASP.NET, moet u uw code ook bijwerken om meerdere verleners te accepteren.

Zie voor meer informatie over multitenancy [een Azure Active Directory-gebruiker (Azure AD) aanmelden met het toepassings patroon voor meerdere tenants](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Toepassingen met één Tenant

Een *toepassing met één Tenant* is een toepassing die alleen aanmeldingen accepteert van gebruikers van een gedefinieerd Azure AD-exemplaar. Externe gebruikers (met inbegrip van werk-of school accounts van andere organisaties of persoonlijke accounts) kunnen zich aanmelden bij een toepassing met één Tenant nadat elke gebruiker als gast account is toegevoegd aan het Azure AD-exemplaar dat de toepassing is geregistreerd. 

U kunt gebruikers als gast accounts toevoegen aan Azure AD via de [Azure AD B2B-samen werking](../external-identities/what-is-b2b.md) en u kunt dit op [een programmatische](../../active-directory-b2c/code-samples.md)manier doen. Bij het gebruik van B2B kunnen gebruikers een self-service portal maken waarvoor geen uitnodiging is vereist om zich aan te melden. Zie [Self-Service Portal voor Azure AD B2B-samenwerkings aanmelding](../external-identities/self-service-portal.md)voor meer informatie.

Toepassingen met één Tenant kunnen de *contact* ervaring mogelijk maken, maar als u de proef versie met eenmalige Klik/gratis wilt inschakelen, moet u in plaats daarvan multitenancy in uw toepassing inschakelen.

## <a name="appsource-trial-experiences"></a>AppSource-proef ervaring

### <a name="free-trial-customer-led-trial-experience"></a>Gratis proef versie (door de klant geleide proef ervaring)

De door de klant gestuurde proef versie is de ervaring die AppSource adviseert bij het bieden van één klik toegang tot uw toepassing. In het volgende voor beeld ziet u hoe deze ervaring eruitziet:

1.  Een gebruiker vindt uw toepassing in de AppSource-website en selecteert de optie **gratis proef versie** .

    ![Toont een gratis proef versie voor een proef versie van de klant](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource leidt de gebruiker naar een URL in uw website. Uw website start het proces voor *eenmalige aanmelding* automatisch (bij het laden van de pagina).

    ![Toont hoe de gebruiker wordt omgeleid naar een URL in uw website](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  De gebruiker wordt omgeleid naar de micro soft-aanmeldings pagina en de gebruiker geeft referenties op om zich aan te melden.

    ![Hiermee wordt de micro soft-aanmeldings pagina weer gegeven](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. De gebruiker geeft toestemming voor uw toepassing.

    ![Voor beeld: toestemming pagina voor een toepassing](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  De aanmelding is voltooid en de gebruiker wordt terug omgeleid naar uw website.  De gebruiker start de gratis proef versie.

    ![Toont de ervaring die de gebruiker ziet wanneer deze weer wordt omgeleid naar uw site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Neem contact met me op

U kunt de evaluatie versie van de partner gebruiken wanneer een hand matige of langlopende bewerking moet worden uitgevoerd om de gebruiker/het bedrijf in te richten, bijvoorbeeld uw toepassing moet virtuele machines, data base-exemplaren of bewerkingen inrichten die veel tijd in beslag nemen. Als de gebruiker in dit geval de knop **proef versie** selecteert en een formulier invult, stuurt AppSource u de contact gegevens van de gebruiker. Wanneer u deze informatie ontvangt, moet u de omgeving inrichten en de instructies naar de gebruiker verzenden om toegang te krijgen tot de proef ervaring:<br/><br/>

1. Een gebruiker vindt uw toepassing in de AppSource-website en selecteert vervolgens **contact met mij opnemen**.

    ![Laat contact met mij opnemen voor een door de partner geleide proef ervaring](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. De gebruiker vult een formulier met contact gegevens in.

    ![Toont een voorbeeld formulier met contact gegevens](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. U ontvangt de gegevens van de gebruiker, stelt een proef exemplaar in en stuurt de Hyper Link naar de gebruiker om toegang te krijgen tot uw toepassing.

    ![Tijdelijke aanduiding voor gebruikers gegevens weer geven](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. De gebruiker heeft toegang tot uw toepassing en voltooit het proces voor eenmalige aanmelding.

    ![Het aanmeldings scherm van de toepassing weer geven](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. De gebruiker geeft toestemming voor uw toepassing.

    ![Geeft een voor beeld van een toestemming pagina voor een toepassing](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. De aanmelding is voltooid en de gebruiker wordt terug omgeleid naar uw website. De gebruiker start de gratis proef versie.

    ![Toont de ervaring die de gebruiker ziet wanneer deze weer wordt omgeleid naar uw site](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Meer informatie

Zie [deze video](https://aka.ms/trialexperienceforwebapps)voor meer informatie over de AppSource-proef ervaring. 

## <a name="get-support"></a>Ondersteuning krijgen

Voor Azure AD-integratie gebruiken we [micro soft Q&A](/answers/topics/azure-active-directory.html) met de community om ondersteuning te bieden.

Wij raden u ten zeerste aan om uw vragen te stellen op [micro soft Q&A](/answers/topics/azure-active-directory.html) als eerste en door te bladeren naar bestaande problemen om te zien of iemand uw vraag eerder heeft gesteld. Zorg ervoor dat uw vragen of opmerkingen zijn gelabeld met [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Gebruik de volgende opmerkingen sectie om feedback te geven en ons te helpen onze inhoud te verfijnen en te vormen.

## <a name="next-steps"></a>Volgende stappen

- Zie [verificatie scenario's voor Azure AD](authentication-flows-app-scenarios.md)voor meer informatie over het bouwen van toepassingen die ondersteuning bieden voor Azure AD-aanmeldingen.
- Ga voor meer informatie over het weer geven van uw SaaS-toepassing in AppSource naar [AppSource-partner gegevens](https://appsource.microsoft.com/partners)