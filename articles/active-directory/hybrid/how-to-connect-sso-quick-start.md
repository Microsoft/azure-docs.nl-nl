---
title: 'Azure AD Connect: naadloze single Sign-On-Quick Start | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Active Directory naadloze single Sign-On
services: active-directory
keywords: Wat is Azure AD Connect, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 349aef1bb9382eec19d9ad9c7f6d4579c82b62de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043934"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory naadloze eenmalige aanmelding: Quick Start

## <a name="deploy-seamless-single-sign-on"></a>Naadloze single Sign-On implementeren

Azure Active Directory (Azure AD) naadloze single Sign-On (naadloze SSO) worden automatisch ondertekend door gebruikers wanneer ze zich op hun bedrijfs Desk tops bevinden die zijn verbonden met uw bedrijfs netwerk. Naadloze SSO biedt uw gebruikers eenvoudige toegang tot uw Cloud toepassingen zonder dat hiervoor extra on-premises onderdelen nodig zijn.

Voer de volgende stappen uit om naadloze SSO te implementeren.

## <a name="step-1-check-the-prerequisites"></a>Stap 1: Controleer de vereisten

Zorg ervoor dat aan de volgende vereisten is voldaan:

* **Uw Azure AD Connect-server instellen**: als u [Pass-Through-verificatie](how-to-connect-pta.md) gebruikt als uw aanmeldings methode, is er geen aanvullende controle op vereisten vereist. Als u [wachtwoord hash-synchronisatie](how-to-connect-password-hash-synchronization.md) gebruikt als uw aanmeldings methode en als er een firewall is tussen Azure AD Connect en Azure AD, moet u ervoor zorgen dat:
   - U gebruikt versie 1.1.644.0 of hoger van Azure AD Connect. 
   - Als uw firewall of proxy toestaat, voegt u de verbindingen toe aan de lijst met toegestane **\* Msappproxy.net** -url's via poort 443. Als u een specifieke URL vereist in plaats van een Joker teken voor proxy configuratie, kunt u **tenantid.registration.msappproxy.net** configureren, waarbij TENANTID de GUID is van de Tenant waar u de functie configureert. Als op URL gebaseerde proxy-uitzonde ringen in uw organisatie niet mogelijk zijn, kunt u in plaats daarvan toegang verlenen tot de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die wekelijks worden bijgewerkt. Deze vereiste is alleen van toepassing wanneer u de functie inschakelt. Het is niet vereist voor de werkelijke gebruikers aanmeldingen.

    >[!NOTE]
    >Azure AD Connect versies 1.1.557.0, 1.1.558.0, 1.1.561.0 en 1.1.614.0 hebben een probleem met de synchronisatie van wacht woord-hashes. Als u de synchronisatie van wacht woord-hash _niet_ wilt gebruiken in combi natie met Pass-Through-verificatie, raadpleegt u de opmerkingen bij de [Azure AD Connect-release](./reference-connect-version-history.md) voor meer informatie.
    
    >[!NOTE]
    >Als u een uitgaande HTTP-proxy hebt, moet u ervoor zorgen dat deze URL, autologon.microsoftazuread-sso.com, voor komt in de lijst toegestaan. U moet deze URL expliciet opgeven omdat het Joker teken mogelijk niet wordt geaccepteerd. 

* **Een ondersteunde Azure AD Connect topologie gebruiken**: Zorg ervoor dat u een van de ondersteunde topologieën van Azure AD Connect gebruikt die [hier](plan-connect-topologies.md)worden beschreven.

    >[!NOTE]
    >Naadloze SSO ondersteunt meerdere AD-forests, ongeacht of er sprake is van AD-vertrouwens relaties.

* **Referenties voor domein beheerder instellen**: u moet referenties voor de domein beheerder hebben voor elk Active Directory forest dat:
    * U synchroniseert met Azure AD via Azure AD Connect.
    * Bevat gebruikers die u wilt inschakelen voor naadloze eenmalige aanmelding.
    
* **Moderne verificatie inschakelen**: u moet [moderne authenticatie](/office365/enterprise/modern-auth-for-office-2013-and-2016) inschakelen voor uw Tenant om deze functie te kunnen gebruiken.

* **Gebruik de nieuwste versies van Microsoft 365-clients**: voor een stille aanmelding met Microsoft 365-clients (Outlook, Word, Excel en anderen) moeten uw gebruikers gebruikmaken van versie 16.0.8730. xxxx of hoger.

## <a name="step-2-enable-the-feature"></a>Stap 2: de functie inschakelen

Schakel naadloze SSO in via [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> U kunt ook [naadloze SSO inschakelen met behulp van Power shell](tshoot-connect-sso.md#manual-reset-of-the-feature) als Azure AD Connect niet voldoet aan uw vereisten. Gebruik deze optie als u meer dan één domein per Active Directory-forest hebt en u meer wilt richten op het domein waarvoor u naadloze SSO wilt inschakelen.

Als u een nieuwe installatie van Azure AD Connect hebt, kiest u het [aangepaste installatiepad](how-to-connect-install-custom.md). Selecteer de optie **eenmalige aanmelding inschakelen** op de aanmeldings pagina van de **gebruiker** .

>[!NOTE]
> De optie is alleen beschikbaar voor selectie als de aanmeldings methode **wachtwoord hash-synchronisatie** of pass through **-verificatie** is.

![Azure AD Connect: aanmelding van gebruiker](./media/how-to-connect-sso-quick-start/sso8.png)

Als u al een installatie van Azure AD Connect hebt, selecteert u de pagina **gebruikers aanmelding wijzigen** in azure AD Connect en selecteert u **volgende**. Als u Azure AD Connect versies 1.1.880.0 of hoger gebruikt, wordt standaard de optie **eenmalige aanmelding inschakelen** geselecteerd. Als u oudere versies van Azure AD Connect gebruikt, selecteert u de optie **eenmalige aanmelding inschakelen** .

![Azure AD Connect: de aanmelding van de gebruiker wijzigen](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Ga door met de wizard totdat u de pagina **eenmalige aanmelding inschakelen hebt ingeschakeld** . Geef referenties voor de domein beheerder op voor elk Active Directory forest dat:

* U synchroniseert met Azure AD via Azure AD Connect.
* Bevat gebruikers die u wilt inschakelen voor naadloze eenmalige aanmelding.

Wanneer de wizard is voltooid, is naadloze SSO ingeschakeld voor uw Tenant.

>[!NOTE]
> De referenties van de domein beheerder worden niet opgeslagen in Azure AD Connect of in azure AD. Ze worden alleen gebruikt om de functie in te scha kelen.

Volg deze instructies om te controleren of naadloze SSO op de juiste wijze is ingeschakeld:

1. Meld u aan bij de [Azure Active Directory Administrative Center](https://aad.portal.azure.com) met de globale beheerders referenties voor uw Tenant.
2. Selecteer **Azure Active Directory** in het linkerdeel venster.
3. Selecteer **Azure AD Connect**.
4. Controleer of de functie **naadloze eenmalige aanmelding** wordt weer gegeven als **ingeschakeld**.

![Azure Portal: Azure AD Connect deel venster](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Met naadloze SSO wordt een computer account gemaakt `AZUREADSSOACC` met de naam in uw on-premises Active Directory (AD) in elk AD-forest. Het `AZUREADSSOACC` computer account moet om veiligheids redenen sterk worden beveiligd. Alleen domein Administrators moeten het computer account kunnen beheren. Zorg ervoor dat Kerberos-delegering op het computer account is uitgeschakeld en dat geen ander account in Active Directory overdrachts machtigingen heeft voor het `AZUREADSSOACC` computer account. Sla het computer account op in een organisatie-eenheid (OE) waar het veilig is tegen onbedoeld verwijderen en waar alleen domein beheerders toegang hebben.

>[!NOTE]
> Als u de architecturen van Pass-the-hash en referentie diefstal wilt gebruiken in uw on-premises omgeving, moet u de juiste wijzigingen aanbrengen om ervoor te zorgen dat het `AZUREADSSOACC` computer account niet in de quarantaine container wordt beëindigd. 

## <a name="step-3-roll-out-the-feature"></a>Stap 3: de functie implementeren

U kunt met behulp van de onderstaande instructies de naadloze SSO voor uw gebruikers geleidelijk uitrollen. U begint met het toevoegen van de volgende Azure AD-URL aan de intranet zone-instellingen van alle of geselecteerde gebruikers met behulp van groepsbeleid in Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Daarnaast moet u de beleids instelling voor intranet zones met de naam **updates toestaan voor de status balk via script** inschakelen via Groepsbeleid. 

>[!NOTE]
> De volgende instructies werken alleen voor Internet Explorer, micro soft Edge en Google Chrome op Windows (als deze een set vertrouwde site-Url's deelt met Internet Explorer). Lees de volgende sectie voor instructies over het instellen van Mozilla Firefox en Google Chrome in macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Waarom moet u de intranet zone-instellingen van gebruikers wijzigen?

De browser berekent standaard automatisch de juiste zone, Internet of intranet, van een specifieke URL. Bijvoorbeeld: is `http://contoso/` toegewezen aan de intranet zone, terwijl wordt `http://intranet.contoso.com/` toegewezen aan de zone Internet (omdat de URL een punt bevat). Browsers verzenden geen Kerberos-tickets naar een Cloud eindpunt, zoals de Azure AD-URL, tenzij u de URL expliciet toevoegt aan de intranet zone van de browser.

Er zijn twee manieren om de intranet zone-instellingen van gebruikers te wijzigen:

| Optie | Beheerder overweging | Gebruikerservaring |
| --- | --- | --- |
| Groepsbeleid | Beheerder vergrendelt het bewerken van intranet zone-instellingen | Gebruikers kunnen hun eigen instellingen niet wijzigen |
| Groeps beleids voorkeur |  De beheerder staat het bewerken op intranet zone-instellingen toe | Gebruikers kunnen hun eigen instellingen wijzigen |

### <a name="group-policy-option---detailed-steps"></a>De optie groeps beleid-gedetailleerde stappen

1. Open het Groepsbeleidsbeheer-editor-hulp programma.
2. Bewerk het groeps beleid dat is toegepast op enkele of al uw gebruikers. In dit voor beeld wordt het **standaard domein beleid** gebruikt.
3. Blader naar **gebruikers configuratie**  >  **beleid**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Internet Explorer** Internet  >  **configuratie scherm**  >  **beveiligings pagina**. Selecteer vervolgens de **lijst site naar zone toewijzing**.
    ![Scherm opname waarin de ' beveiligings pagina ' met de lijst ' site naar zone toewijzing ' is geselecteerd.](./media/how-to-connect-sso-quick-start/sso6.png)
4. Schakel het beleid in en voer de volgende waarden in het dialoog venster in:
   - **Waardenaam**: de Azure AD-URL waar de Kerberos-tickets worden doorgestuurd.
   - **Waarde** (gegevens): **1** geeft de intranet zone aan.

     Het resultaat ziet er als volgt uit:

     Waardenaam: `https://autologon.microsoftazuread-sso.com`
  
     Waarde (gegevens): 1

   >[!NOTE]
   > Als u wilt voor komen dat sommige gebruikers naadloze SSO gebruiken (bijvoorbeeld als deze gebruikers zich aanmelden bij gedeelde kiosken), stelt u de voor gaande waarden in op **4**. Met deze actie wordt de Azure AD-URL toegevoegd aan de zone met beperkte toegang en wordt de hele tijd naadloze SSO afgebroken.
   >

5. Selecteer **OK** en vervolgens weer **OK**.

    ![Scherm opname van het venster inhoud weer geven met een zone toewijzing geselecteerd.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Blader naar **gebruikers configuratie**  >  **beleid**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Internet Explorer** Internet-  >  **configuratie scherm**  >    >  **intranet zone** van beveiligings pagina. Selecteer vervolgens **updates toestaan voor status balk via script**.

    ![Scherm opname van de pagina ' intranet zone ' met ' updates toestaan voor status balk via script ' geselecteerd.](./media/how-to-connect-sso-quick-start/sso11.png)

7. Schakel de beleids instelling in en selecteer **OK**.

    ![Scherm afbeelding met het venster ' updates toestaan voor status balk via script ', waarbij de beleids instelling is ingeschakeld.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>De optie voor keuren voor groeps beleid-gedetailleerde stappen

1. Open het Groepsbeleidsbeheer-editor-hulp programma.
2. Bewerk het groeps beleid dat is toegepast op enkele of al uw gebruikers. In dit voor beeld wordt het **standaard domein beleid** gebruikt.
3. Bladeren naar **gebruikers configuratie**  >  **voor keuren** het  >    >  **REGI ster**  >  **Nieuw**  >  **register item**.

    ![Scherm opname van het geselecteerde "REGI ster" en "register item" geselecteerd.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Voer de volgende waarden in de juiste velden in en klik op **OK**.
   - **Sleutelpad:** **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon_**
   - **Waardenaam**: **_https_**
   - **Waardetype**: **_REG_DWORD_**
   - **Waardegegevens**: **_00000001_**
 
     ![Scherm opname van het venster ' nieuwe register eigenschappen '.](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Eenmalige aanmelding](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Browser overwegingen

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alle platformen)

In Mozilla Firefox wordt niet automatisch Kerberos-verificatie gebruikt. Elke gebruiker moet de URL van Azure AD hand matig toevoegen aan hun Firefox-instellingen door de volgende stappen te volgen:
1. Voer Firefox uit en voer `about:config` in de adres balk in. Sluit alle meldingen die u ziet.
2. Zoek naar de voor keuren **netwerk. Negotiate-auth. Trusted-uri's** . In deze voor keur worden vertrouwde sites van Firefox voor Kerberos-verificatie weer gegeven.
3. Klik met de rechter muisknop en selecteer **wijzigen**.
4. Voer `https://autologon.microsoftazuread-sso.com` in het veld in.
5. Selecteer **OK** en open vervolgens de browser opnieuw.

#### <a name="safari-macos"></a>Safari (macOS)

Zorg ervoor dat de computer waarop het macOS wordt uitgevoerd, is gekoppeld aan AD. Instructies voor AD-joins op uw macOS-apparaat valt buiten het bereik van dit artikel.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Micro soft Edge op basis van chroom (alle platformen)

Als u de [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) of de [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) -beleids instellingen in uw omgeving hebt overschreven, moet u ook de URL () van Azure AD toevoegen `https://autologon.microsoftazuread-sso.com` .

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Micro soft Edge op basis van chroom (macOS en andere niet-Windows-platforms)

Voor micro soft Edge op basis van chroom op macOS en andere niet-Windows-platforms, raadpleegt u [de micro soft Edge op basis van de lijst met chroom beleid](/DeployEdge/microsoft-edge-policies#authserverallowlist) voor informatie over het toevoegen van de Azure AD-URL voor geïntegreerde verificatie voor uw acceptatie lijst.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (alle platformen)

Als u de [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) of de [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) -beleids instellingen in uw omgeving hebt overschreven, moet u ook de URL () van Azure AD toevoegen `https://autologon.microsoftazuread-sso.com` .

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS en andere niet-Windows-platforms)

Voor Google Chrome op macOS en andere niet-Windows-platforms raadpleegt u [de lijst met Chroom project beleid](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) voor informatie over het beheren van de acceptatie lijst voor de Azure AD-URL voor geïntegreerde verificatie.

Het gebruik van groepsbeleid Active Directory uitbrei dingen van derden voor het implementeren van de Azure AD-URL naar Firefox en Google Chrome op Mac-gebruikers valt buiten het bereik van dit artikel.

#### <a name="known-browser-limitations"></a>Bekende browser beperkingen

Naadloze SSO werkt niet in de modus voor persoonlijke navigatie in Firefox-browsers en micro soft Edge (verouderd). Het werkt ook niet in Internet Explorer als de browser wordt uitgevoerd in de uitgebreide beveiligde modus. Naadloze SSO ondersteunt de volgende versie van micro soft Edge op basis van chroom en werkt in de InPrivate-en gast modus door te ontwerpen.

## <a name="step-4-test-the-feature"></a>Stap 4: de functie testen

Als u de functie voor een specifieke gebruiker wilt testen, moet u ervoor zorgen dat alle volgende voor waarden aanwezig zijn:
  - De gebruiker meldt zich aan bij een bedrijfs apparaat.
  - Het apparaat is toegevoegd aan uw Active Directory domein. Het apparaat hoeft _geen_ lid te zijn van [Azure AD](../devices/overview.md).
  - Het apparaat heeft een directe verbinding met uw domein controller (DC), hetzij op het bekabelde of draadloze netwerk van het bedrijf, hetzij via een RAS-verbinding, zoals een VPN-verbinding.
  - U hebt [de functie](#step-3-roll-out-the-feature) door Groepsbeleid naar deze gebruiker samengevouwen.

Het scenario testen waarbij de gebruiker alleen de gebruikers naam invoert, maar niet het wacht woord:
   - Meld u aan bij https://myapps.microsoft.com/ . Zorg ervoor dat u de browser cache wist of een nieuwe persoonlijke browser sessie gebruikt met een van de ondersteunde browsers in de privé modus.

Als u het scenario wilt testen waarbij de gebruiker de gebruikers naam of het wacht woord niet hoeft in te voeren, gebruikt u een van de volgende stappen: 
   - Meld u aan om `https://myapps.microsoft.com/contoso.onmicrosoft.com` ervoor te zorgen dat u de browser cache wist of een nieuwe persoonlijke browser sessie gebruikt met een van de ondersteunde browsers in de persoonlijke modus. Vervang *Contoso* door de naam van uw Tenant.
   - Meld u aan bij `https://myapps.microsoft.com/contoso.com` een nieuwe persoonlijke browser sessie. Vervang *contoso.com* door een geverifieerd domein (niet een federatief domein) in uw Tenant.

## <a name="step-5-roll-over-keys"></a>Stap 5: toetsen met rollen

In stap 2 maakt Azure AD Connect computer accounts (die Azure AD vertegenwoordigen) in alle Active Directory forests waarop u naadloze SSO hebt ingeschakeld. Zie [Azure Active Directory naadloze eenmalige aanmelding: technische grondige](how-to-connect-sso-how-it-works.md)kennis.

>[!IMPORTANT]
>De Kerberos-ontsleutelings sleutel op een computer account kan worden gebruikt voor het genereren van Kerberos-tickets voor elke gebruiker in het AD-forest. Kwaadwillende actoren kunnen vervolgens Azure AD-aanmeldingen imiteren voor verdachte gebruikers. We raden u ten zeerste aan om deze Kerberos-ontsleutelings sleutels regel matig te totaliseren-minstens één keer per 30 dagen.

Zie [Azure Active Directory naadloze eenmalige aanmelding: veelgestelde vragen](how-to-connect-sso-faq.md)voor meer informatie over het overstappen op sleutels. We werken aan een mogelijkheid om automatische roll over sleutels in te voeren.

>[!IMPORTANT]
>U hoeft deze stap niet _onmiddellijk_ uit te voeren nadat u de functie hebt ingeschakeld. Rolt ten minste één keer per 30 dagen over naar de sleutels voor Kerberos-ontsleuteling.

## <a name="next-steps"></a>Volgende stappen

- [Technisch diep gaande](how-to-connect-sso-how-it-works.md): krijg inzicht in hoe de functie naadloze single Sign-On werkt.
- [Veelgestelde vragen](how-to-connect-sso-faq.md): Hier vindt u antwoorden op veelgestelde vragen over naadloze eenmalige aanmelding.
- [Problemen oplossen](tshoot-connect-sso.md): informatie over het oplossen van veelvoorkomende problemen met de functie naadloze single Sign-On.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): gebruik het Azure Active Directory-forum om nieuwe functie aanvragen te verwerken.
