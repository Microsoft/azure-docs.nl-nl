---
title: Inwisseling van uitnodiging in B2B-samen werking-Azure AD
description: Hierin wordt de uitnodiging voor Azure AD B2B-samen werking voor eind gebruikers beschreven, met inbegrip van de overeenkomst met betrekking tot de privacy-voor waarden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8520afdd05ecce8604ce72596bdf06053217cc2e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173087"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Inwisseling uitnodiging Azure Active Directory B2B-samen werking

In dit artikel worden de manieren beschreven waarop gast gebruikers toegang hebben tot uw resources en het toestemmings proces dat ze tegen komen. Als u een uitnodigings-e-mail naar de gast stuurt, bevat de uitnodiging een koppeling die de gast kan inwisselen om toegang te krijgen tot uw app of portal. Het e-mail bericht is slechts een van de manieren waarop gasten toegang kunnen krijgen tot uw resources. Als alternatief kunt u gasten toevoegen aan uw directory en ze een directe koppeling geven naar de portal of app die u wilt delen. Gasten worden door een proces voor de eerste toestemming begeleid, ongeacht de methode die ze gebruiken. Dit proces zorgt ervoor dat uw gasten akkoord gaan met de privacy-voor waarden en alle [gebruiks voorwaarden](../conditional-access/terms-of-use.md) accepteren die u hebt ingesteld.

Wanneer u een gast gebruiker aan uw Directory toevoegt, heeft het gast gebruikers account een toestemmings status (zichtbaar in Power shell) die in eerste instantie is ingesteld op **PendingAcceptance**. Deze instelling blijft actief totdat de gast uw uitnodiging aanvaardt en akkoord gaat met uw privacybeleid en gebruiks voorwaarden. Daarna wordt de status van de toestemming gewijzigd in **geaccepteerd** en worden de pagina's met toestemming niet meer aan de gast gepresenteerd.

   > [!IMPORTANT]
   > **Vanaf 31 maart 2021** heeft Microsoft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en -tenants voor B2B-samenwerkingsscenario's. In de voorbereiding raden wij klanten aan om te kiezen voor de [verificatie van de eenmalige wachtwoordcode e-mailen](one-time-passcode.md). We waarderen uw feedback over deze openbare preview-functie en willen graag nog meer manieren te maken om samen te werken.

## <a name="redemption-through-the-invitation-email"></a>Inwisselen via e-mail met uitnodiging

Wanneer u een gast gebruiker aan uw Directory toevoegt met [behulp van de Azure Portal](./b2b-quickstart-add-guest-users-portal.md), wordt er een e-mail uitnodiging verzonden naar de gast in het proces. U kunt er ook voor kiezen om e-mail berichten te verzenden wanneer u [Power shell gebruikt](./b2b-quickstart-invite-powershell.md) om gast gebruikers toe te voegen aan uw Directory. Hier volgt een beschrijving van de ervaring van de gast bij het inwisselen van de koppeling in het e-mail bericht.

1. De gast ontvangt een [uitnodigings-e-mail](./invitation-email-elements.md) die wordt verzonden vanuit **uitnodigingen van micro soft**.
2. De gast selecteert **uitnodiging accepteren** in het e-mail bericht.
3. De gast gebruikt hun eigen referenties om u aan te melden bij uw Directory. Als de gast geen account heeft dat federatief kan zijn voor uw directory en de OTP-functie [(one-time wachtwoord code) voor e-mail](./one-time-passcode.md) niet is ingeschakeld; de gast wordt gevraagd om een persoonlijk [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) [-of Azure AD Self-Service-account](../enterprise-users/directory-self-service-signup.md)te maken. Raadpleeg de [uitbestedings stroom voor uitnodigingen](#invitation-redemption-flow) voor meer informatie.
4. De gast wordt geleid door de [toestemming](#consent-experience-for-the-guest) die hieronder wordt beschreven.

## <a name="redemption-through-a-direct-link"></a>Inkopen via een directe koppeling

Als alternatief voor de uitnodigings-e-mail kunt u een gast een rechtstreekse koppeling geven naar uw app of portal. U moet eerst de gast gebruiker toevoegen aan uw directory via de [Azure Portal](./b2b-quickstart-add-guest-users-portal.md) of [Power shell](./b2b-quickstart-invite-powershell.md). Vervolgens kunt u een van de [aanpas bare manieren gebruiken om toepassingen te implementeren voor gebruikers](../manage-apps/end-user-experiences.md), waaronder directe aanmeldings koppelingen. Wanneer een gast gebruikmaakt van een directe koppeling in plaats van het e-mail bericht met de uitnodiging, worden ze nog steeds door gegeven via de eerste toestemmings ervaring.

> [!IMPORTANT]
> De directe koppeling moet Tenant-specifiek zijn. Met andere woorden, het moet een Tenant-ID of geverifieerd domein bevatten zodat de gast kan worden geverifieerd in uw Tenant, waar de gedeelde app zich bevindt. Een gemeen schappelijke URL https://myapps.microsoft.com kan niet worden gebruikt voor een gast omdat deze wordt omgeleid naar hun eigen Tenant voor authenticatie. Hier volgen enkele voor beelden van directe koppelingen met de context van de Tenant:
 > - Toegangs venster voor apps: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Toegangs venster voor apps voor een geverifieerd domein: `https://myapps.microsoft.com/<;verified domain>`
 > - Azure Portal: `https://portal.azure.com/<tenant id>`
 > - Afzonderlijke app: zie een [koppeling voor direct aanmelden](../manage-apps/end-user-experiences.md#direct-sign-on-links) gebruiken

Er zijn enkele gevallen waarin de e-mail uitnodiging wordt aanbevolen via een directe koppeling. Als deze speciale gevallen belang rijk zijn voor uw organisatie, raden we u aan gebruikers uit te nodigen met behulp van methoden die nog steeds de uitnodigings-e-mail verzenden:
 - De gebruiker heeft geen Azure AD-account, een MSA of een e-mail account in een federatieve organisatie. Tenzij u de functie voor eenmalige wachtwoord code gebruikt, moet de gast de uitnodigings-e-mail inwisselen om door de stappen voor het maken van een MSA te worden geleid.
 - Soms heeft het uitgenodigde gebruikers object mogelijk geen e-mail adres vanwege een conflict met een contact object (bijvoorbeeld een Outlook-contact object). In dit geval moet de gebruiker op de opname-URL in het e-mail bericht met de uitnodiging klikken.
 - De gebruiker kan zich aanmelden met een alias van het e-mail adres dat is uitgenodigd. (Een alias is een aanvullend e-mail adres dat is gekoppeld aan een e-mail account.) In dit geval moet de gebruiker op de opname-URL in het e-mail bericht met de uitnodiging klikken.

## <a name="invitation-redemption-flow"></a>Aflossings stroom uitnodiging

Wanneer een gebruiker op de koppeling **uitnodiging accepteren** in een [e-mail uitnodiging](invitation-email-elements.md)klikt, wordt de uitnodiging automatisch door Azure AD ingewisseld op basis van de aflossings stroom zoals hieronder wordt weer gegeven:

![Scherm opname van het aflossings stroom diagram](media/redemption-experience/invitation-redemption-flow.png)

**Als de UPN (User Principle Name) van de gebruiker overeenkomt met een bestaand Azure AD en persoonlijk MSA-account, wordt de gebruiker gevraagd het account te kiezen waarmee ze willen inwisselen.*

1. Azure AD voert detectie op basis van gebruikers uit om te bepalen of de gebruiker bestaat in een [bestaande Azure AD-Tenant](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Als een beheerder [directe Federatie](./direct-federation.md)heeft ingeschakeld, controleert Azure AD of het domein achtervoegsel van de gebruiker overeenkomt met het domein van een geconfigureerde SAML-ID-provider en wordt de gebruiker omgeleid naar de vooraf geconfigureerde ID-provider.

3. Als een beheerder [Google Federation](./google-federation.md)heeft ingeschakeld, controleert Azure AD of het domein achtervoegsel van de gebruiker gmail.com of googlemail.com is en wordt de gebruiker omgeleid naar Google.

4. Bij het inwissel proces wordt gecontroleerd of de gebruiker beschikt over een bestaand persoonlijk [Microsoft-account (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

5. Zodra de **basismap** van de gebruiker is geïdentificeerd, wordt de gebruiker naar de bijbehorende id-provider verzonden om zich aan te melden.  

6. Als de stappen 1 tot en met 4 een basismap voor de uitgenodigde gebruiker niet kunnen vinden, bepaalt Azure AD of de uitnodigende Tenant de OTP-functie [(one-time wachtwoord code)](./one-time-passcode.md) voor de gast heeft ingeschakeld voor gasten.

7. Als [er een eenmalige e-mail voor gasten is ingeschakeld](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), wordt een wachtwoord code verzonden naar de gebruiker via de genodigde e-mail. De gebruiker haalt deze wachtwoord code op en voert deze in op de aanmeldings pagina van Azure AD.

8. Als er een eenmalige e-mail wachtwoord voor gasten is uitgeschakeld, controleert Azure AD het domein achtervoegsel om te bepalen of het hoort bij een Consumer-account. Als dit het geval is, wordt de gebruiker gevraagd een persoonlijke [Microsoft-account](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)te maken. Als dat niet het geval is, wordt de gebruiker gevraagd een [self-service account voor Azure AD](../enterprise-users/directory-self-service-signup.md)te maken.

9. Azure AD probeert een [Azure AD Self-Service-account](../enterprise-users/directory-self-service-signup.md) te maken door de toegang tot het e-mail bericht te controleren. Controleer of het account wordt uitgevoerd door een code naar het e-mail bericht te verzenden en de gebruiker te laten ophalen en verzenden naar Azure AD. Als de Tenant van de uitgenodigde gebruiker echter federatief is of als het veld AllowEmailVerifiedUsers in de Tenant van de uitgenodigde gebruiker is ingesteld op False, kan de gebruiker de terugbetaling niet volt ooien en resulteert de stroom in een fout. Zie [problemen oplossen Azure Active Directory B2B-samen werking](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)voor meer informatie.

10. De gebruiker wordt gevraagd om een persoonlijke [Microsoft-account (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)te maken.

11. Na de verificatie bij de juiste ID-provider, wordt de gebruiker omgeleid naar Azure AD om de [toestemming](#consent-experience-for-the-guest)te volt ooien.  

Voor Just-in-time (JIT)-inwisselingen, waarbij de terugbetaling via een koppeling naar een gestuurde toepassing is, zijn de stappen 8 tot en met 10 niet beschikbaar. Als een gebruiker stap 6 en de functie voor eenmalige wachtwoord code is ingeschakeld, ontvangt de gebruiker een fout bericht en kan hij de uitnodiging niet inwisselen. Beheerders moeten [een eenmalige wachtwoord code voor e-mail inschakelen](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) of ervoor zorgen dat de gebruiker op een uitnodigings koppeling klikt om deze fout te voor komen.

## <a name="consent-experience-for-the-guest"></a>Bestemmings ervaring voor de gast

Wanneer een gast zich voor de eerste keer aanmeldt voor toegang tot resources in een partner organisatie, worden deze door de volgende pagina's geleid. 

1. De gast controleert de pagina **machtigingen controleren** met een beschrijving van de privacyverklaring van de uitnodigende organisatie. Een gebruiker moet het gebruik van hun gegevens in overeenstemming met het privacybeleid van de uitnodigende organisatie **accepteren** om door te gaan.

   ![Schermopname van de pagina Machtigingen controleren](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Voor informatie over hoe u als een Tenant beheerder kunt koppelen aan de privacyverklaring van uw organisatie, raadpleegt u [How to: privacy-informatie van uw organisatie toevoegen in azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Als de gebruiks voorwaarden zijn geconfigureerd, wordt de gast geopend en worden de gebruiks voorwaarden beoordeeld. vervolgens selecteert u **accepteren**. 

   ![Scherm opname met nieuwe gebruiks voorwaarden](media/redemption-experience/terms-of-use-accept.png) 

   U kunt [gebruiks voorwaarden](../conditional-access/terms-of-use.md) configureren in **externe identiteiten**  >  **Gebruiksvoorwaarden**.

3. Tenzij anders vermeld, wordt de gast omgeleid naar het toegangs venster voor apps, met daarin de toepassingen die de gast kan openen.

   ![Scherm opname van het toegangs paneel voor apps](media/redemption-experience/myapps.png) 

In uw Directory wordt de waarde van de **uitnodiging geaccepteerd** van de gast gewijzigd in **Ja**. Als er een MSA is gemaakt, wordt in de **bron** van de gast het **micro soft-account** weer gegeven. Zie [Eigenschappen van een Azure AD B2B-samenwerkings gebruiker](user-properties.md)voor meer informatie over eigenschappen van gast gebruikers accounts. 

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen aan de Azure Portal](add-users-administrator.md)
- [Hoe voegen mede werkers B2B-samenwerkings gebruikers toe aan Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B-samenwerkings gebruikers toevoegen met behulp van Power shell](customize-invitation-api.md#powershell)
- [Een organisatie verlaten als gastgebruiker](leave-the-organization.md)