---
title: Wijzigingen van Azure User Principle Name (UPN) plannen en oplossen
description: Bekende problemen en oplossingen voor UPN-wijzigingen begrijpen
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46ecc2cba96b07d9105020e69ae3198a3765be7c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172228"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Wijzigingen in de principal-naam van gebruikers plannen en oplossen in Azure Active Directory

Een UPN (User Principal Name) is een kenmerk dat een Internet communicatie standaard vormt voor gebruikers accounts. Een UPN bestaat uit een UPN-voor voegsel (de naam van het gebruikers account) en een UPN-achtervoegsel (een DNS-domein naam). Het voor voegsel wordt toegevoegd aan het achtervoegsel met het @-teken. Bijvoorbeeld someone@example.com. Een UPN moet uniek zijn voor alle SPN-objecten in een Directory-forest. 

**In dit artikel wordt ervan uitgegaan dat u UPN gebruikt als de gebruikers-id. Het behandelt het plannen van UPN-wijzigingen en het herstellen van problemen die kunnen voortvloeien uit UPN-wijzigingen.**

> [!NOTE]
> Voor ontwikkel aars raden we u aan om het objectID van de gebruiker als de onveranderbare id te gebruiken in plaats van de UPN-of e-mail adressen wanneer hun waarden kunnen veranderen.


## <a name="learn-about-upns-and-upn-changes"></a>Meer informatie over Upn's en UPN-wijzigingen
Aanmeldings pagina's vragen gebruikers vaak om hun e-mail adres in te voeren wanneer de vereiste waarde daad werkelijk de UPN is. Daarom moet u de UPN van gebruikers wijzigen op het moment dat het primaire e-mail adres verandert.

Het primaire e-mail adres van gebruikers kan om verschillende redenen worden gewijzigd:

* bedrijf rebranden

* werk nemers die overstappen naar verschillende bedrijfs afdelingen 

* fusies en acquisities

* naamswijzigingen van werk nemers

### <a name="types-of-upn-changes"></a>Typen UPN-wijzigingen

U kunt een UPN wijzigen door het voor voegsel, achtervoegsel of beide te wijzigen.

* **Het voor voegsel wijzigen**.

   *  Als de naam van een persoon bijvoorbeeld is gewijzigd, kunt u de account naam wijzigen:  
BSimon@contoso.comomBJohnson@contoso.com

   * U kunt ook de bedrijfs standaard wijzigen voor voor voegsels:  
Bsimon@contoso.comomBritta.Simon@contoso.com

* **Het achtervoegsel wijzigen**. <br>

    Als een persoon bijvoorbeeld afdelingen heeft gewijzigd, kunt u het domein wijzigen: 

   * Britta.Simon@contoso.com Aan Britta.Simon@contosolabs.com <br>
     of<br>
    * Britta.Simon@corp.contoso.com Aan Britta.Simon@labs.contoso.com 

U wordt aangeraden de UPN van gebruikers elke keer dat het primaire e-mail adres wordt bijgewerkt, te wijzigen.

Zorg er tijdens de eerste synchronisatie van Active Directory naar Azure AD voor dat de e-mail berichten van de gebruikers identiek zijn aan hun Upn's.

### <a name="upns-in-active-directory"></a>Upn's in Active Directory

In Active Directory is het standaard UPN-achtervoegsel de DNS-naam van het domein waarin u het gebruikers account hebt gemaakt. In de meeste gevallen is dit de domein naam die u registreert als het ondernemings domein op internet. Als u het gebruikers account maakt in het domein contoso.com, wordt de standaard-UPN

username@contoso.com

 U kunt echter [meer UPN-achtervoegsels toevoegen](../fundamentals/add-custom-domain.md) met behulp van Active Directory domeinen en vertrouwens relaties. 

U kunt bijvoorbeeld labs.contoso.com toevoegen en de Upn's en het e-mail adres van de gebruikers laten zien. Ze worden vervolgens

username@labs.contoso.com.

>[!IMPORTANT]
> Als u [het achtervoegsel in Active Directory wijzigt](../fundamentals/add-custom-domain.md), moet u ervoor zorgen dat er een overeenkomende aangepaste domein naam is [toegevoegd en geverifieerd in azure AD](../fundamentals/add-custom-domain.md). 

![Een scherm opname van geverifieerde domeinen](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Upn's in Azure Active Directory

Gebruikers melden zich aan bij Azure AD met de waarde in het kenmerk userPrincipalName. 

Wanneer u Azure AD gebruikt in combi natie met uw on-premises Active Directory, worden gebruikers accounts gesynchroniseerd met behulp van de Azure AD Connect-service. Standaard gebruikt de Azure AD Connect wizard het kenmerk userPrincipalName van de on-premises Active Directory als de UPN in azure AD. U kunt dit wijzigen in een ander kenmerk in een aangepaste installatie.

Het is belang rijk dat u een gedefinieerd proces hebt wanneer u een User Principal Name (UPN) van een enkele gebruiker of voor uw hele organisatie bijwerkt. 

Zie de bekende problemen en tijdelijke oplossingen in dit document.

Wanneer u gebruikers accounts synchroniseert van Active Directory naar Azure AD, moet u ervoor zorgen dat de Upn's in Active Directory worden toegewezen aan geverifieerde domeinen in azure AD.

![Scherm afbeelding met voor beelden van Upn's die zijn toegewezen aan geverifieerde Azure A D-domeinen.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Als de waarde van het kenmerk userPrincipalName niet overeenkomt met een geverifieerd domein in azure AD, wordt het achtervoegsel door het synchronisatie proces vervangen door de standaard waarde. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Bulk wijzigingen in de UPN uitrollen

Volg de [Aanbevolen procedures voor een pilot](../fundamentals/active-directory-deployment-plans.md) voor bulk wijzigingen in de UPN. Ook hebt u een getest rollback-plan voor het terugdraaien van Upn's als u problemen ondervindt die niet snel kunnen worden opgelost. Als uw test eenmaal wordt uitgevoerd, kunt u beginnen met het richten op kleine verzamelingen gebruikers met verschillende organisatie rollen en hun specifieke sets apps of apparaten.

Door deze eerste subset van gebruikers te passeren, krijgt u een goed idee van wat gebruikers moeten verwachten als onderdeel van de wijziging. Neem deze informatie op over de communicatie van uw gebruikers.

Maak een gedefinieerde procedure voor het wijzigen van Upn's voor afzonderlijke gebruikers als onderdeel van de normale bewerkingen. We raden u aan een test procedure te hebben die documentatie bevat over bekende problemen en tijdelijke oplossingen.

In de volgende secties worden mogelijke bekende problemen en tijdelijke oplossingen beschreven wanneer Upn's worden gewijzigd.

## <a name="apps-known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen voor apps

[Software as a Service (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) en LOB-toepassingen (line-of-Business) zijn vaak afhankelijk van upn's om gebruikers te vinden en gebruikers profiel gegevens op te slaan, inclusief rollen. Toepassingen die gebruikmaken [van just-in-time-inrichting](../app-provisioning/user-provisioning.md) voor het maken van een gebruikers profiel wanneer gebruikers zich voor de eerste keer aanmelden bij de app, kunnen worden beïnvloed door UPN-wijzigingen.

**Bekend probleem**<br>
Het wijzigen van de UPN van een gebruiker kan de relatie tussen de Azure AD-gebruiker en het gebruikers profiel dat is gemaakt op de toepassing, verstoren. Als de toepassing  [just-in-time-inrichting](../app-provisioning/user-provisioning.md)gebruikt, kan er een gloed nieuw gebruikers profiel worden gemaakt. Hiervoor moet de toepassings beheerder hand matige wijzigingen aanbrengen om deze relatie op te lossen.

**Tijdelijke oplossing**<br>
Met [Azure AD Automated User Provisioning](../app-provisioning/user-provisioning.md) kunt u automatisch uw gebruikers identiteiten maken, onderhouden en verwijderen in ondersteunde Cloud toepassingen. Door automatische gebruikers inrichting in uw toepassingen te configureren, worden de Upn's voor de toepassingen automatisch bijgewerkt. Test de toepassingen als onderdeel van de progressieve implementatie om te valideren dat ze niet worden beïnvloed door UPN-wijzigingen.
Als u een ontwikkelaar bent, kunt u [scim-ondersteuning toevoegen aan uw toepassing](../app-provisioning/use-scim-to-provision-users-and-groups.md) om automatische gebruikers inrichting van Azure Active Directory in te scha kelen. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen voor beheerde apparaten

Door [uw apparaten naar Azure ad te brengen](../devices/overview.md), maximaliseert u de productiviteit van uw gebruikers via eenmalige aanmelding (SSO) in uw Cloud en on-premises resources.

### <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

Aan Azure [ad gekoppelde](../devices/concept-azure-ad-join.md) apparaten worden rechtstreeks toegevoegd aan Azure AD en kunnen gebruikers zich aanmelden bij het apparaat met behulp van de identiteit van de organisatie.

**Bekende problemen** <br>
Gebruikers kunnen problemen ondervinden met eenmalige aanmelding met toepassingen die afhankelijk zijn van Azure AD voor verificatie.

**Oplossing** <br>
De problemen die in deze sectie worden vermeld, zijn vastgesteld op de Windows 10-update van 2020 (2004).

**Tijdelijke oplossing** <br>
Sta voldoende tijd toe om de UPN-wijziging te synchroniseren met Azure AD. Nadat u hebt gecontroleerd of de nieuwe UPN wordt weer gegeven in de Azure AD-Portal, vraagt u de gebruiker om zich aan te melden met de nieuwe UPN. U kunt ook controleren via [Power shell](/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Nadat u zich hebt aangemeld met de nieuwe UPN, kunnen er nog steeds verwijzingen naar de oude UPN worden weer gegeven op de Windows-instelling ' toegang tot werk of school '.

![Scherm opname van geverifieerde domeinen](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-gekoppelde apparaten

[Hybride Azure AD](../devices/concept-azure-ad-join-hybrid.md) -apparaten worden toegevoegd aan Active Directory en Azure AD. U kunt hybride deelname aan Azure AD implementeren als uw omgeving een on-premises Active Directory footprint heeft en u ook wilt profiteren van de mogelijkheden van Azure AD.

**Bekende problemen** 

Windows 10 Hybrid Azure AD gekoppelde apparaten ondervinden waarschijnlijk onverwachte herstarts en toegangs problemen.

Als gebruikers zich aanmelden bij Windows voordat de nieuwe UPN is gesynchroniseerd met Azure AD, of een bestaande Windows-sessie blijft gebruiken, kunnen ze problemen ondervinden met eenmalige aanmelding bij toepassingen die gebruikmaken van Azure AD voor verificatie als voorwaardelijke toegang is geconfigureerd om het gebruik van aan Hybrid gekoppelde apparaten af te dwingen om toegang te krijgen tot bronnen. 

Daarnaast wordt het volgende bericht weer gegeven, waarbij het na één minuut opnieuw opstarten wordt afgedwongen. 

"Uw PC wordt over één minuut automatisch opnieuw opgestart. Er is een probleem opgetreden in Windows en opnieuw moet worden opgestart. Sluit dit bericht nu en sla uw werk op.

**Oplossing** <br>
De problemen die in deze sectie worden vermeld, zijn vastgesteld op de Windows 10-update van 2020 (2004).

**Tijdelijke oplossing** 

Het apparaat moet worden ontkoppeld van Azure AD en opnieuw worden gestart. Nadat het apparaat opnieuw is opgestart, wordt automatisch opnieuw lid van Azure AD en moet de gebruiker zich aanmelden met de nieuwe UPN door de tegel ' andere gebruiker ' te selecteren. Als u een apparaat uit Azure AD wilt ontkoppelen, voert u de volgende opdracht uit vanaf een opdracht prompt:

**dsregcmd /leave**

Als de gebruiker wordt gebruikt, moet deze [opnieuw worden inge schreven](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) voor Windows hello voor bedrijven. Windows 7-en 8,1-apparaten worden niet beïnvloed door dit probleem nadat UPN-wijzigingen zijn aangebracht.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Bekende problemen en tijdelijke oplossingen Microsoft Authenticator

Uw organisatie vereist mogelijk het gebruik van de [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) om u aan te melden en toegang te krijgen tot bedrijfs toepassingen en-gegevens. Hoewel een gebruikers naam in de app kan worden weer gegeven, is het account niet ingesteld om te functioneren als verificatie methode totdat de gebruiker het registratie proces heeft voltooid.

De [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md) heeft vier belang rijke functies:

* Multi-factor Authentication via een push melding of verificatie code

* Fungeren als een verificatie Broker op iOS-en Android-apparaten om eenmalige aanmelding te bieden voor toepassingen die gebruikmaken van [brokered-verificatie](../develop/msal-android-single-sign-on.md)

* Apparaatregistratie (ook bekend als Workplace Join) voor Azure AD. Dit is een vereiste voor andere functies, zoals Intune-app-beveiliging en inschrijving/beheer van het apparaat.

* Aanmelden voor telefoon, waarvoor MFA en apparaatregistratie vereist zijn.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication met Android-apparaten

De Microsoft Authenticator-app biedt een out-of-band-verificatie optie. In plaats van een geautomatiseerde telefoon oproep of SMS-bericht naar de gebruiker tijdens het aanmelden te plaatsen, pusht [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) een melding naar de Microsoft Authenticator-app op de smartphone of Tablet van de gebruiker. De gebruiker tikt alleen op goed keuren (of voert een pincode of biometrische in en tikt op "verifiëren") in de app om de aanmelding te volt ooien.

**Bekende problemen** 

Wanneer u de UPN van een gebruiker wijzigt, wordt de oude UPN nog steeds weer gegeven in het gebruikers account en kan er geen melding worden ontvangen. [Verificatie codes](../user-help/user-help-auth-app-faq.md) blijven werken.

**Tijdelijke oplossing**

Als er een melding wordt ontvangen, geeft u de gebruiker de opdracht om de melding te sluiten, de verificator-app te openen, op de optie controleren op meldingen te tikken en de MFA-prompt goed te keuren. Daarna wordt de UPN die op het account wordt weer gegeven, bijgewerkt. Opmerking: de bijgewerkte UPN kan worden weer gegeven als een nieuw account. Dit is het gevolg van een andere verificator functionaliteit die wordt gebruikt. Raadpleeg de aanvullende bekende problemen in dit artikel voor meer informatie.

### <a name="brokered-authentication"></a>Brokered-verificatie

Op Android-en iOS-brokers als Microsoft Authenticator inschakelen:

* Eenmalige aanmelding (SSO): uw gebruikers hoeven zich niet aan te melden bij elke toepassing.

* Apparaat-id: de Broker heeft toegang tot het apparaat dat is gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek.

* Verificatie van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven en wordt deze door de Broker gecontroleerd.

Daarnaast kunnen toepassingen deel nemen aan meer geavanceerde functies, zoals [voorwaardelijke toegang](../conditional-access/index.yml), en worden [Microsoft intune scenario's](../develop/msal-net-use-brokers-with-xamarin-apps.md)ondersteund.

**Bekende problemen**<br>
Er wordt een gebruiker weer gegeven met meer interactieve verificatie prompts voor nieuwe toepassingen die gebruikmaken van de door Broker ondersteunde aanmelding omdat de login_hint door gegeven door de toepassing en de UPN die is opgeslagen op de Broker.

**Tijdelijke oplossing** <br> De gebruiker moet het account hand matig verwijderen uit Microsoft Authenticator en een nieuwe aanmelding starten vanuit een toepassing met Broker ondersteuning. Het account wordt automatisch toegevoegd na de eerste verificatie.

### <a name="device-registration"></a>Apparaatregistratie

De Microsoft Authenticator-app is verantwoordelijk voor het registreren van het apparaat bij Azure AD. Met apparaatregistratie kan het apparaat worden geverifieerd bij Azure AD en is een vereiste voor de volgende scenario's:

* Intune-app-beveiliging

* Inschrijving van intune-apparaten

* Aanmelden via telefoon

**Bekende problemen**<br>
Wanneer u de UPN wijzigt, wordt er een nieuw account met de nieuwe UPN weer gegeven in de app Microsoft Authenticator, terwijl het account met de oude UPN nog steeds wordt weer gegeven. Daarnaast wordt de oude UPN weer gegeven in het gedeelte apparaatregistratie op de app-instellingen. Er is geen wijziging in de normale functionaliteit van apparaatregistratie of de afhankelijke scenario's.

**Tijdelijke oplossing** <br> Als u alle verwijzingen naar de oude UPN in de Microsoft Authenticator-app wilt verwijderen, geeft u de gebruiker de opdracht om de oude en nieuwe accounts hand matig te verwijderen uit Microsoft Authenticator, moet u zich opnieuw registreren voor MFA en opnieuw lid worden van het apparaat.

### <a name="phone-sign-in"></a>Aanmelding via de telefoon

Met aanmelden via de telefoon kunnen gebruikers zich zonder wacht woord aanmelden bij Azure AD. Als u aanmelding via de telefoon wilt inschakelen, moet de gebruiker zich registreren voor MFA met de verificator-app en vervolgens direct aanmelden via de telefoon inschakelen op verificator. Als onderdeel van de configuratie wordt het apparaat geregistreerd bij Azure AD.

**Bekende problemen** <br>
Gebruikers kunnen zich niet aanmelden via de telefoon omdat ze geen meldingen ontvangen. Als de gebruiker op controleren op meldingen tikt, wordt er een fout bericht weer geven.

**Tijdelijke oplossing**<br>
De gebruiker moet de vervolg keuzelijst selecteren op het account dat is ingeschakeld voor aanmelding via de telefoon en selecteer aanmelding via de telefoon uitschakelen. Indien gewenst, kan de aanmelding van de telefoon opnieuw worden ingeschakeld.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Bekende problemen met de beveiligings sleutel (FIDO2) en tijdelijke oplossingen

**Bekende problemen** <br>
Wanneer meerdere gebruikers op dezelfde sleutel zijn geregistreerd, toont het aanmeldings scherm een pagina voor het selecteren van accounts waarop de oude UPN wordt weer gegeven. Aanmeldingen met behulp van beveiligings sleutels worden niet beïnvloed door UPN-wijzigingen.  

**Tijdelijke oplossing**<br>
Als gebruikers verwijzingen naar oude Upn's wilt verwijderen, moeten ze [de beveiligings sleutel opnieuw instellen en opnieuw registreren](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Bekende problemen met OneDrive en tijdelijke oplossingen

OneDrive-gebruikers zijn bekend om problemen te ondervinden na het wijzigen van UPN. Zie [hoe UPN-wijzigingen van invloed zijn op de onedrive-URL en onedrive-functies](/onedrive/upn-changes)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk deze bronnen:
* [Azure AD Connect: ontwerp concepten](./plan-connect-design-concepts.md)

* [Populatie van Azure AD UserPrincipalName](./plan-connect-userprincipalname.md)

* [Tokens van micro soft Identity platform ID](../develop/id-tokens.md)