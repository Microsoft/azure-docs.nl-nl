---
title: 'Azure AD Connect: Cloud verificatie via gefaseerde implementatie | Microsoft Docs'
description: In dit artikel wordt uitgelegd hoe u migreert van Federated Authentication, naar Cloud-verificatie, met behulp van een gefaseerde implementatie.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67018a2ec6b9239661a7022ad7fb9eeb6c9a5f64
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385578"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout"></a>Migreren naar cloudverificatie met gefaseerde implementatie

Met gefaseerde implementatie kunt u groepen gebruikers selectief testen met Cloud verificatie mogelijkheden zoals Azure AD Multi-Factor Authentication (MFA), voorwaardelijke toegang, identiteits beveiliging voor gelekte referenties, identiteits beheer en anderen, voordat u uw domeinen knipt.  In dit artikel wordt beschreven hoe u de switch kunt maken. Voordat u begint met de gefaseerde implementatie, moet u rekening houden met de gevolgen als aan een of meer van de volgende voor waarden wordt voldaan:
    
-  U maakt momenteel gebruik van een on-premises Multi-Factor Authentication-Server. 
-  U gebruikt Smart Cards voor verificatie. 
-  Uw huidige server biedt bepaalde alleen-Federatie functies.

Voordat u deze functie probeert te gebruiken, raden we u aan onze hand leiding te bekijken over het kiezen van de juiste verificatie methode. Zie de tabel ' Compare Methods ' in de [juiste verificatie methode kiezen voor uw Azure Active Directory hybride identiteits oplossing](./choose-ad-authn.md#comparing-methods)voor meer informatie.

Bekijk voor een overzicht van de functie ' Azure Active Directory: wat is gefaseerde implementatie? ' hardware

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Vereisten

-   U hebt een Azure Active Directory-Tenant (Azure AD) met federatieve domeinen.

-   U hebt besloten om over te stappen op een van de volgende twee opties:
    - **Optie A**  -  *synchronisatie van wacht woord-hash (synchronisatie)*  +  *naadloze eenmalige aanmelding (SSO)*.  Zie [Wat is wachtwoord hash Sync](whatis-phs.md) en [Wat is naadloze SSO](how-to-connect-sso.md) ? voor meer informatie.
    - **Optie B**  -  *Pass-Through-verificatie*  +  *naadloze SSO*.  Zie [Wat is Pass-Through-verificatie](how-to-connect-pta.md) ? voor meer informatie.  
    
    Hoewel *naadloze SSO* optioneel is, wordt u aangeraden om deze functie in te scha kelen voor gebruikers die computers die lid zijn van een domein in een bedrijfs netwerk.

-   U hebt alle toepasselijke beleids regels voor Tenant huismerk en voorwaardelijke toegang geconfigureerd die u nodig hebt voor gebruikers die worden gemigreerd naar Cloud authenticatie.

-   Als u van plan bent Azure AD Multi-Factor Authentication te gebruiken, raden we u aan om [gecombineerde registratie te gebruiken voor selfservice voor wachtwoord herstel (SSPR) en multi-factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) zodat uw gebruikers hun verificatie methoden eenmaal moeten registreren. Opmerking: wanneer u SSPR gebruikt om het wacht woord opnieuw in te stellen of het wacht woord te wijzigen met behulp van de MyProfile-pagina tijdens de gefaseerde implementatie, moet Azure AD Connect de nieuwe wacht woord-hash synchroniseren. Dit kan Maxi maal twee minuten duren na de reset.

-   Als u de functie voor gefaseerde implementatie wilt gebruiken, moet u een globale beheerder zijn op uw Tenant.

-   Als u *naadloze SSO* wilt inschakelen op een specifiek Active Directory forest, moet u een domein beheerder zijn.

-  Als u hybride Azure AD of Azure AD-deelname implementeert, moet u een upgrade uitvoeren naar Windows 10 1903 update.


## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende scenario's worden ondersteund voor gefaseerde implementatie. De functie werkt alleen voor:

- Gebruikers die zijn ingericht voor Azure AD met behulp van Azure AD Connect. Het is niet van toepassing op gebruikers met alleen Clouds.

- Aanmeld verkeer van gebruikers bij browsers en *moderne verificatie* -clients. Toepassingen of Cloud Services die gebruikmaken van verouderde verificatie, vallen terug op Federated Authentication stromen. Een voor beeld is mogelijk Exchange Online met moderne verificatie uitgeschakeld of Outlook 2010, dat geen ondersteuning biedt voor moderne verificatie.

- De groeps grootte is momenteel beperkt tot 50.000 gebruikers.  Als u groepen hebt die groter zijn dan 50.000 gebruikers, wordt het aanbevolen deze groep te splitsen over meerdere groepen voor gefaseerde implementatie.

- Windows 10 Hybrid Join's of Azure AD koppelt het primaire vernieuwings token zonder regel te verkrijgen aan de Federatie server voor Windows 10 versie 1903 en hoger, wanneer de UPN van de gebruiker routeerbaar is en domein achtervoegsel wordt gecontroleerd in azure AD.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden niet ondersteund voor gefaseerde implementatie:

- Verouderde verificatie, zoals POP3 en SMTP, wordt niet ondersteund.

- Bepaalde toepassingen verzenden de query parameter ' domain_hint ' naar Azure AD tijdens de verificatie. Deze stromen worden voortgezet en gebruikers die zijn ingeschakeld voor gefaseerde implementatie, blijven Federatie voor verificatie gebruiken.

<!-- -->

- Beheerders kunnen Cloud verificatie implementeren met behulp van beveiligings groepen. Als u wilt voor komen dat synchronisatie latentie wanneer u gebruikmaakt van on-premises Active Directory beveiligings groepen, raden we u aan Cloud beveiligings groepen te gebruiken. De volgende voor waarden zijn van toepassing:

    - U kunt Maxi maal 10 groepen per functie gebruiken. Dat wil zeggen, u kunt elk 10 groepen gebruiken voor de *synchronisatie van wacht woord-hash*, *Pass-Through-verificatie* en *naadloze SSO*.
    - Geneste groepen worden *niet ondersteund*. 
    - Dynamische groepen worden *niet ondersteund* voor gefaseerde implementatie.
    - Met de objecten in de groep wordt de groep geblokkeerd, zodat deze niet meer kan worden toegevoegd.

- Wanneer u voor het eerst een beveiligings groep voor gefaseerde implementatie toevoegt, bent u beperkt tot 200 gebruikers om een UX-time-out te voor komen. Nadat u de groep hebt toegevoegd, kunt u, indien nodig, meer gebruikers toevoegen.

- Wanneer gebruikers zich in de gefaseerde implementatie bevinden, wordt het beleid voor het verlopen van wacht woorden ingesteld op 90 dagen, wanneer EnforceCloudPasswordPolicyForPasswordSyncedUsers is ingeschakeld. 

- Windows 10 hybride koppeling of Azure AD-deelname primair vernieuwings Token ophalen voor Windows 10-versie ouder dan 1903. Dit scenario gaat terug naar het WS-Trust-eind punt van de Federatie server, zelfs als de gebruiker die zich aanmeldt, zich binnen het bereik van de gefaseerde implementatie bevindt.

- Windows 10 Hybrid Join's of Azure AD koppelt het primaire vernieuwings token voor alle versies, wanneer de on-premises UPN van de gebruiker niet routeerbaar is. Dit scenario gaat terug naar het WS-Trust-eind punt in de modus voor gefaseerde implementatie, maar werkt niet meer wanneer de gefaseerde migratie is voltooid en de aanmelding van de gebruiker is niet langer afhankelijk van de Federatie server.

  >[!NOTE]
  >U moet nog steeds de uiteindelijke cutover van federatieve naar Cloud authenticatie maken met behulp van Azure AD Connect of Power shell. Gefaseerde implementatie verwisselt geen domeinen van federatieve naar beheerd.  Zie voor meer informatie over Domain cutover [migreren van Federatie naar wacht woord hash-synchronisatie](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) en [migreren van Federatie naar Pass-Through-verificatie](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
  
## <a name="get-started-with-staged-rollout"></a>Aan de slag met gefaseerde implementatie

Als u het aanmelden voor *wachtwoord hash-synchronisatie* wilt testen met behulp van gefaseerde implementatie, volgt u de voor bereide instructies in de volgende sectie.

Voor informatie over welke Power shell-cmdlets moeten worden gebruikt, raadpleegt u [Azure AD 2,0 Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Vooraf werken voor wachtwoord-hash-synchronisatie

1. Schakel *wacht woord-hash-synchronisatie* in op de pagina [optionele functies](how-to-connect-install-custom.md#optional-features) in azure AD Connect. 

   ![Scherm afbeelding van de pagina optionele functies in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Zorg ervoor dat er een volledige hash-synchronisatie cyclus voor *wacht woorden* is uitgevoerd, zodat alle wacht woorden van de gebruikers zijn gesynchroniseerd met Azure AD. U kunt de status van de *synchronisatie van wacht woord-hashes* controleren met behulp van de diagnostische gegevens van Power shell in [Troubleshooting Password hash sync with Azure AD Connect Sync](tshoot-connect-password-hash-synchronization.md).

   ![Scherm opname van het AADConnect-probleemoplossings logboek](./media/how-to-connect-staged-rollout/sr2.png)

Als u *Pass-Through-verificatie* -aanmelding wilt testen met behulp van gefaseerde implementatie, schakelt u deze in door de pre-werk instructies in de volgende sectie te volgen.

## <a name="pre-work-for-pass-through-authentication"></a>Vooraf-werk voor Pass-Through-verificatie

1. Identificeer een server waarop Windows Server 2012 R2 of hoger wordt uitgevoerd, waar u de *Pass-Through-verificatie* agent wilt uitvoeren. 

   Kies *niet* de Azure AD Connect server. Zorg ervoor dat de server lid is van een domein, geselecteerde gebruikers kan verifiëren met Active Directory en kan communiceren met Azure AD via uitgaande poorten en Url's. Zie de sectie ' stap 1: de vereisten controleren ' in [Quick Start: Azure AD naadloze eenmalige aanmelding](how-to-connect-sso-quick-start.md)voor meer informatie.

1. [Down load de Azure AD Connect-Verificatie agent](https://aka.ms/getauthagent)en installeer deze op de-server. 

1. Als u [maximale Beschik baarheid](how-to-connect-sso-quick-start.md)wilt inschakelen, installeert u extra verificatie agenten op andere servers.

1. Zorg ervoor dat u de instellingen voor [Slim vergren delen](../authentication/howto-password-smart-lockout.md) op de juiste wijze hebt geconfigureerd. Zo zorgt u ervoor dat de on-premises Active Directory accounts van uw gebruikers niet worden vergrendeld door ongeldige actors.

We raden u aan *naadloze SSO* in te scha kelen, onafhankelijk van de aanmeldings methode (*hash voor wachtwoord synchronisatie* of *Pass-Through-verificatie*) die u selecteert voor gefaseerde implementatie. Als u *naadloze SSO* wilt inschakelen, volgt u de instructies voor voor het uitvoeren van de voor bereiding in de volgende sectie.

## <a name="pre-work-for-seamless-sso"></a>Pre-work voor naadloze SSO

Schakel *naadloze SSO* in op de Active Directory-forests met behulp van Power shell. Als u meer dan één Active Directory forest hebt, moet u dit voor elk forest afzonderlijk inschakelen. *Naadloze SSO* wordt alleen geactiveerd voor gebruikers die zijn geselecteerd voor gefaseerde implementatie. Dit heeft geen invloed op uw bestaande federatie-instellingen.

Schakel *naadloze SSO* in door het volgende te doen:

1. Meld u aan bij Azure AD Connect-server.

2. Ga naar de map *% Program Files% \\ Microsoft Azure Active Directory Connect* .

3. Importeer de *naadloze SSO* Power shell-module door de volgende opdracht uit te voeren: 

   `Import-Module .\AzureADSSO.psd1`

4. Voer PowerShell uit als beheerder. Bel in Power shell `New-AzureADSSOAuthenticationContext` . Met deze opdracht wordt een deel venster geopend waarin u de globale beheerders referenties van uw Tenant kunt invoeren.

5. Aanroep `Get-AzureADSSOStatus | ConvertFrom-Json` . Met deze opdracht wordt een lijst van Active Directory forests weer gegeven (Zie de lijst met domeinen) waarop deze functie is ingeschakeld. Standaard is deze ingesteld op False op Tenant niveau.

   ![Voor beeld van de Windows Power shell-uitvoer](./media/how-to-connect-staged-rollout/sr3.png)

6. Aanroep `$creds = Get-Credential` . Voer bij de prompt de domein beheerders referenties in voor het beoogde Active Directory-forest.

7. Aanroep `Enable-AzureADSSOForest -OnPremCredentials $creds` . Met deze opdracht maakt u het AZUREADSSOACC-computer account van de on-premises domein controller voor het Active Directory-forest dat is vereist voor *naadloze SSO*.

8. Voor *naadloze SSO* moeten url's zich in de intranet zone bevindt. Als u deze Url's wilt implementeren met behulp van groeps beleid, raadpleegt u [Quick Start: Azure AD naadloze eenmalige aanmelding](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Voor een volledig overzicht kunt u ook onze [implementatie plannen](https://aka.ms/SeamlessSSODPDownload) voor *naadloze SSO* downloaden.

## <a name="enable-staged-rollout"></a>Gefaseerde implementatie inschakelen

Volg de instructies in de volgende secties als u een specifieke functie (*Pass-Through-verificatie*, *wachtwoord-hash-synchronisatie* of *naadloze SSO*) wilt implementeren voor een geselecteerde set gebruikers in een groep.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Een gefaseerde implementatie van een specifieke functie in uw Tenant inschakelen

U kunt een van de volgende opties implementeren:

- **Optie A**  -  *wachtwoord-hash-synchronisatie*  +  *naadloze SSO*
- **Optie B**  -  *Pass-Through-verificatie*  +  *naadloze SSO*
- **Niet ondersteund**  -  *wachtwoord-hash-synchronisatie*  +  *Pass-Through-verificatie*  +  *naadloze SSO*

Ga als volgt te werk:

1. Meld u aan bij de [Azure AD-Portal](https://aka.ms/stagedrolloutux)om toegang te krijgen tot de UX.

2. Selecteer de koppeling **gefaseerde implementatie voor beheerde gebruiker aanmelden inschakelen** .

   Als u bijvoorbeeld *optie A* wilt inschakelen, kunt u het **wacht woord hash-synchronisatie** en **naadloze eenmalige aanmelding** **instellen op aan, zoals** wordt weer gegeven in de volgende installatie kopieën.

   

  

3. Voeg de groepen toe aan de functie om *Pass Through-verificatie* en *naadloze SSO* in te scha kelen. Als u wilt voor komen dat een UX-time-out optreedt, moet u ervoor zorgen dat de beveiligings groepen in eerste instantie niet meer dan 200 leden bevatten.

   

   >[!NOTE]
   >De leden in een groep worden automatisch ingeschakeld voor gefaseerde implementatie. Geneste en dynamische groepen worden niet ondersteund voor gefaseerde implementatie.
   >Wanneer u een nieuwe groep toevoegt, worden gebruikers in de groep (Maxi maal 200 gebruikers voor een nieuwe groep) bijgewerkt zodat deze direct beheerde verificatie kunnen gebruiken. Het bewerken van een groep (het toevoegen of verwijderen van gebruikers) kan tot 24 uur duren voordat de wijzigingen van kracht worden.
   >Naadloze SSO is alleen van toepassing als gebruikers zich in de naadloze SSO-groep bevinden en ook in een PTA-of PHS-groep.

## <a name="auditing"></a>Controleren

We hebben controle gebeurtenissen ingeschakeld voor de verschillende acties die we voor gefaseerde implementatie uitvoeren:

- Controle gebeurtenis bij het inschakelen van een gefaseerde implementatie voor *wacht woord-hash-synchronisatie*, *Pass Through-verificatie* of *naadloze SSO*.

  >[!NOTE]
  >Er wordt een controle gebeurtenis geregistreerd wanneer *naadloze SSO* wordt ingeschakeld met behulp van gefaseerde implementatie.

  ![Het deel venster implementatie beleid voor functie maken-tabblad activiteit](./media/how-to-connect-staged-rollout/sr7.png)

  ![Het deel venster implementatie beleid maken voor functie-aangepast eigenschappen tabblad](./media/how-to-connect-staged-rollout/sr8.png)

- Controle gebeurtenis wanneer een groep wordt toegevoegd aan de *hash-synchronisatie van wacht woorden*, *Pass Through-verificatie* of *naadloze SSO*.

  >[!NOTE]
  >Er wordt een controle gebeurtenis geregistreerd wanneer een groep wordt toegevoegd aan de *wachtwoord-hash-synchronisatie* voor gefaseerde implementatie.

  ![Het deel venster "een groep toevoegen aan functie-implementatie"-tabblad activiteit](./media/how-to-connect-staged-rollout/sr9.png)

  ![Het deel venster "een groep toevoegen aan de functie-implementatie"-tabblad gewijzigde eigenschappen](./media/how-to-connect-staged-rollout/sr10.png)

- Controle gebeurtenis wanneer een gebruiker die is toegevoegd aan de groep is ingeschakeld voor gefaseerde implementatie.

  ![Het deel venster ' gebruiker toevoegen aan functie-implementatie '-tabblad activiteit](media/how-to-connect-staged-rollout/sr11.png)

  ![Het tabblad ' gebruiker toevoegen aan functie-implementatie ' (venster doel (en))](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validatie

Ga als volgt te werk om de aanmelding met *wachtwoord-hash-synchronisatie* of *Pass-Through-verificatie* (gebruikers naam en wacht woord) te testen:

1. In het extranet gaat u naar de [pagina apps](https://myapps.microsoft.com) in een persoonlijke browser sessie en voert u vervolgens de USERPRINCIPALNAME (UPN) in van het gebruikers account dat is geselecteerd voor gefaseerde implementatie.

   Gebruikers die zijn gericht op gefaseerde implementatie, worden niet omgeleid naar uw federatieve aanmeldings pagina. In plaats daarvan wordt u gevraagd zich aan te melden op de aanmeldings pagina van de Azure AD-Tenant.

1. Zorg ervoor dat de aanmelding correct wordt weer gegeven in het [rapport met aanmeld activiteiten van Azure AD](../reports-monitoring/concept-sign-ins.md) door te filteren met de userPrincipalName.

Aanmelden met *naadloze SSO* testen:

1. Op het intranet gaat u naar de [pagina apps](https://myapps.microsoft.com) in een persoonlijke browser sessie en voert u vervolgens de USERPRINCIPALNAME (UPN) in van het gebruikers account dat is geselecteerd voor gefaseerde implementatie.

   Gebruikers die zijn gericht op gefaseerde implementatie van *naadloze SSO* , worden weer gegeven met een ' poging om u aan te melden... ' bericht voordat ze op de achtergrond zijn aangemeld.

1. Zorg ervoor dat de aanmelding correct wordt weer gegeven in het [rapport met aanmeld activiteiten van Azure AD](../reports-monitoring/concept-sign-ins.md) door te filteren met de userPrincipalName.

   Volg de instructies in [AD FS probleem oplossing: gebeurtenissen en logboek registratie](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)voor het bijhouden van gebruikers aanmeldingen die zich nog steeds voordoen op Active Directory Federation Services (AD FS) voor geselecteerde gebruikers met gefaseerde implementatie. Raadpleeg de documentatie van leveranciers over hoe u dit kunt controleren op Federatie providers van derden.

## <a name="remove-a-user-from-staged-rollout"></a>Een gebruiker verwijderen uit de gefaseerde implementatie

Als u een gebruiker uit de groep verwijdert, wordt de gefaseerde implementatie voor die gebruiker uitgeschakeld. Als u de functie voor gefaseerde implementatie wilt uitschakelen, schuift u het besturings element terug naar **uit**.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: kan ik deze mogelijkheid gebruiken voor productie?**

A: Ja, u kunt deze functie gebruiken in uw productie Tenant, maar u wordt aangeraden om deze eerst te uitproberen in de test Tenant.

**V: kan deze functie worden gebruikt om een permanente ' samen werking ' te behouden, waarbij sommige gebruikers Federated Authentication gebruiken en anderen Cloud verificatie gebruiken?**

A: Nee, deze functie is ontworpen voor het testen van Cloud verificatie. Nadat u een aantal gebruikers groepen hebt getest, moet u zich naar Cloud authenticatie knippen. Het gebruik van een permanente gemengde status wordt niet aanbevolen, omdat deze benadering kan leiden tot onverwachte verificatie stromen.

**V: kan ik Power shell gebruiken voor het uitvoeren van een gefaseerde implementatie?**

A: Ja. Zie [Preview van Azure AD](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout)voor meer informatie over het gebruik van Power shell om gefaseerde implementatie uit te voeren.

## <a name="next-steps"></a>Volgende stappen
- [Azure AD 2,0 Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [De aanmeldings methode voor het synchroniseren van wacht woord-hashes wijzigen](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Aanmeldings methode wijzigen in Pass-Through-verificatie](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
