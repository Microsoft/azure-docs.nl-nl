---
title: Primair vernieuwings token (PRT) en Azure AD-Azure Active Directory
description: Wat is de rol van en hoe beheren we het primaire vernieuwings token (PRT) in Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644932"
---
# <a name="what-is-a-primary-refresh-token"></a>Wat is een primair vernieuwingstoken?

Een primair vernieuwings token (PRT) is een sleutel artefact van Azure AD-verificatie op Windows 10, Windows Server 2016 en latere versies, iOS en Android-apparaten. Het is een JSON Web Token (JWT) die speciaal is uitgegeven voor token Brokers van de eerste partij van micro soft om eenmalige aanmelding (SSO) in te scha kelen voor de toepassingen die op deze apparaten worden gebruikt. In dit artikel worden details gegeven over de manier waarop een PRT wordt uitgegeven, gebruikt en beveiligd op Windows 10-apparaten.

In dit artikel wordt ervan uitgegaan dat u al bekend bent met de verschillende beschik bare Apparaatstatus in azure AD en hoe eenmalige aanmelding werkt in Windows 10. Zie het artikel [Wat is Apparaatbeheer in azure Active Directory?](overview.md) voor meer informatie over apparaten in azure AD.

## <a name="key-terminology-and-components"></a>Belangrijkste terminologie en onderdelen

De volgende Windows-onderdelen spelen een belang rijke rol bij het aanvragen en gebruiken van een PRT:

* **Cloud authenticatie provider** (CloudAP): CloudAP is de moderne verificatie provider voor Windows-aanmelding, waarmee gebruikers zich kunnen aanmelden bij een Windows 10-apparaat. CloudAP biedt een invoeg toepassings raamwerk waarin id-providers kunnen bouwen om verificatie voor Windows in te scha kelen met behulp van de referenties van de identiteits provider.
* **Web account manager** (WAM): WAM is de standaard token Broker op Windows 10-apparaten. WAM biedt ook een invoeg toepassings raamwerk dat id-providers kunnen bouwen op en inschakelen voor SSO voor hun toepassingen die gebruikmaken van die id-provider.
* **Azure AD CloudAP-invoeg toepassing**: een specifieke Azure AD-invoeg toepassing die is gebouwd op het CloudAP-Framework, waarmee gebruikers referenties met Azure AD worden gecontroleerd tijdens het aanmelden bij Windows.
* **Azure AD WAM-invoeg toepassing**: een specifieke Azure AD-invoeg toepassing die is gebouwd op het WAM-Framework, waarmee SSO kan worden gemaakt voor toepassingen die afhankelijk zijn van Azure AD voor authenticatie.
* **Dsreg**: een specifiek Azure AD-onderdeel in Windows 10 dat het registratie proces voor apparaten voor alle statussen van het apparaat verwerkt.
* **Trusted Platform Module** (TPM): een TPM is een hardware-onderdeel dat is ingebouwd in een apparaat en biedt op hardware gebaseerde beveiligings functies voor gebruikers-en apparaatgegevens. Meer informatie vindt u in het artikel [Trusted Platform Module technologie overzicht](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Wat bevat de PRT?

Een PRT bevat claims die doorgaans zijn opgenomen in een Azure AD-vernieuwings token. Daarnaast zijn er enkele apparaatspecifieke claims opgenomen in de PRT. De verschillen zijn als volgt:

* **Apparaat-id**: er wordt een PRT verleend aan een gebruiker op een specifiek apparaat. De claim apparaat-ID `deviceID` bepaalt het apparaat waar het PRT aan de gebruiker is verleend. Deze claim wordt later verleend aan tokens die zijn verkregen via de PRT. De claim apparaat-ID wordt gebruikt om de autorisatie voor voorwaardelijke toegang te bepalen op basis van de status of naleving van het apparaat.
* **Sessie sleutel**: de sessie sleutel is een versleutelde symmetrische sleutel die wordt gegenereerd door de Azure AD-verificatie service, uitgegeven als onderdeel van de PRT. De sessie sleutel fungeert als bewijs van bezit wanneer een PRT wordt gebruikt om tokens voor andere toepassingen te verkrijgen.

### <a name="can-i-see-whats-in-a-prt"></a>Kan ik zien wat er in een PRT is?

Een PRT is een ondoorzichtige blob die wordt verzonden vanuit Azure AD waarvan de inhoud niet bekend is bij client onderdelen. U kunt niet zien wat er in een PRT is.

## <a name="how-is-a-prt-issued"></a>Hoe wordt een PRT uitgegeven?

Apparaatregistratie is een vereiste voor verificatie op basis van apparaten in azure AD. Een PRT wordt alleen aan gebruikers uitgegeven op geregistreerde apparaten. Zie het artikel [Windows hello voor bedrijven en apparaatregistratie](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)voor meer gedetailleerde informatie over het registreren van apparaten. Tijdens de registratie van het apparaat genereert het onderdeel dsreg twee sets cryptografische sleutel paren:

* Apparaatsoftware (dkpub/dkpriv)
* Transport sleutel (tkpub/tkpriv)

De persoonlijke sleutels zijn gebonden aan de TPM van het apparaat als het apparaat een geldige en werkende TPM heeft, terwijl de open bare sleutels worden verzonden naar Azure AD tijdens het registratie proces voor het apparaat. Deze sleutels worden gebruikt om de status van het apparaat te valideren tijdens PRT-aanvragen.

De PRT wordt uitgegeven tijdens de gebruikers verificatie op een Windows 10-apparaat in twee scenario's:

* **Azure AD join** of **hybride Azure AD join**: er wordt een PRT uitgegeven tijdens Windows-aanmelding wanneer een gebruiker zich aanmeldt met de referenties van de organisatie. Een PRT wordt uitgegeven met alle door Windows 10 ondersteunde referenties, bijvoorbeeld wacht woord en Windows hello voor bedrijven. In dit scenario is Azure AD CloudAP-invoeg toepassing de primaire instantie voor de PRT.
* **Geregistreerd Azure AD-apparaat**: er wordt een PRT uitgegeven wanneer een gebruiker een secundair werk account toevoegt aan hun Windows 10-apparaat. Gebruikers kunnen op twee verschillende manieren een account toevoegen aan Windows 10:  
   * Een account toevoegen via het bericht **dit account overal gebruiken op dit apparaat** na aanmelding bij een app (bijvoorbeeld Outlook)
   * Een account toevoegen vanuit **instellingen**  >  **accounts**  >  **toegang tot werk of school**  >  **verbinden**

In azure AD-scenario's voor geregistreerde apparaten is de Azure AD WAM-invoeg toepassing de primaire instantie voor de PRT, omdat Windows-aanmelding niet is gebeurd met dit Azure AD-account.

> [!NOTE]
> externe ID-providers moeten het WS-Trust-protocol ondersteunen om PRT-uitgifte op Windows 10-apparaten in te scha kelen. Zonder WS-Trust kan PRT niet worden verleend aan gebruikers op hybride Azure AD gekoppelde of Azure AD gekoppelde apparaten. Op ADFS zijn alleen usernamemixed-eind punten vereist. ADFS/Services/Trust/2005/windowstransport en ADFS/Services/Trust/13/windowstransport moeten alleen worden ingeschakeld als intranet gerichte eind punten en **mogen niet worden weer gegeven** als aan een extranet gerichte eind punten via de Web Application proxy

## <a name="what-is-the-lifetime-of-a-prt"></a>Wat is de levens duur van een PRT?

Zodra het apparaat is uitgegeven, is een PRT 14 dagen geldig en wordt het voortdurend vernieuwd, zolang de gebruiker de computer actief gebruikt.  

## <a name="how-is-a-prt-used"></a>Hoe wordt een PRT gebruikt?

Een PRT wordt gebruikt door twee belang rijke onderdelen in Windows:

* **Azure AD CloudAP-invoeg toepassing**: tijdens Windows-aanmelding vraagt de Azure AD CloudAP-invoeg toepassing een PRT aan bij Azure AD met de referenties die door de gebruiker zijn geleverd. Ook wordt de PRT in de cache opgeslagen om aanmelden in cache in te scha kelen als de gebruiker geen toegang heeft tot een Internet verbinding.
* **Azure AD WAM-invoeg toepassing**: wanneer gebruikers toegang proberen te krijgen tot toepassingen, gebruikt de Azure AD WAM-invoeg toepassing de PRT om SSO in te scha kelen in Windows 10. De Azure AD WAM-invoeg toepassing maakt gebruik van de PRT voor het aanvragen van vernieuwings-en toegangs tokens voor toepassingen die afhankelijk zijn van WAM voor token aanvragen. Ook wordt SSO op browsers ingeschakeld door de PRT in browser aanvragen in te voeren. Browser-SSO in Windows 10 wordt ondersteund in micro soft Edge (systeem eigen) en Chrome (via de [Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) of [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) -extensies).

## <a name="how-is-a-prt-renewed"></a>Hoe wordt een PRT vernieuwd?

Een PRT wordt op twee verschillende manieren vernieuwd:

* **Azure AD CloudAP-invoeg toepassing elke 4 uur**: met de CloudAP-invoeg toepassing wordt de PRT elke vier uur vernieuwd tijdens het aanmelden bij Windows. Als de gebruiker tijdens die tijd geen Internet verbinding heeft, wordt de PRT door CloudAP-invoeg toepassing vernieuwd nadat het apparaat is verbonden met internet.
* **Azure AD WAM-invoeg toepassing tijdens app-token aanvragen**: de WAM-invoeg toepassing maakt SSO op Windows 10-apparaten mogelijk door Silent token aanvragen voor toepassingen in te scha kelen. De WAM-invoeg toepassing kan de PRT tijdens deze token aanvragen op twee verschillende manieren vernieuwen:
   * Een app vraagt WAM voor een toegangs token op de achtergrond, maar er is geen vernieuwings token beschikbaar voor die app. In dit geval gebruikt WAM de PRT om een token voor de app aan te vragen en wordt er een nieuwe PRT in het antwoord teruggestuurd.
   * Een app vraagt WAM aan voor een toegangs token, maar de PRT is ongeldig of er is extra autorisatie vereist voor Azure AD (bijvoorbeeld Azure AD Multi-Factor Authentication). In dit scenario initieert WAM een interactieve aanmelding waarbij de gebruiker opnieuw moet worden geverifieerd of dat er aanvullende verificatie wordt gegeven en er een nieuwe PRT wordt uitgegeven voor een geslaagde authenticatie.

In een AD FS-omgeving is het niet nodig om de PRT te vernieuwen. PRT-verlenging vereist alleen/ADFS/Services/Trust/2005/usernamemixed-en/ADFS/Services/Trust/13/usernamemixed-eind punten die zijn ingeschakeld op de proxy met behulp van WS-Trust-protocol.

Windows-transport eindpunten zijn alleen vereist voor wachtwoord verificatie wanneer een wacht woord wordt gewijzigd, niet voor PRT-vernieuwing.

### <a name="key-considerations"></a>Belangrijkste overwegingen

* Er wordt alleen een PRT uitgegeven en vernieuwd tijdens de verificatie van de systeem eigen app. Een PRT wordt niet vernieuwd of uitgegeven tijdens een browser sessie.
* In azure AD gekoppelde en hybride Azure AD gekoppelde apparaten is de CloudAP-invoeg toepassing de primaire instantie voor een PRT. Als een PRT wordt vernieuwd tijdens een token aanvraag op basis van een WAM, wordt de PRT teruggestuurd naar de CloudAP-invoeg toepassing, die de geldigheid van de PRT met Azure AD verifieert voordat deze wordt geaccepteerd.

## <a name="how-is-the-prt-protected"></a>Hoe wordt de PRT beschermd?

Een PRT wordt beveiligd door deze te binden aan het apparaat waarmee de gebruiker zich heeft aangemeld. Azure AD en Windows 10 bieden PRT-beveiliging via de volgende methoden:

* **Tijdens de eerste aanmelding**: tijdens de eerste aanmelding wordt een PRT uitgegeven door aanvragen te ondertekenen met behulp van de apparaatcode die cryptografisch is gegenereerd tijdens de apparaatregistratie. Op een apparaat met een geldige en werkende TPM wordt de apparaatcode beveiligd door de TPM, waardoor er geen kwaad aardige toegang is. Er wordt geen PRT uitgegeven als de bijbehorende hand tekening van de sleutel van het apparaat niet kan worden gevalideerd.
* **Tijdens token aanvragen en-vernieuwing**: wanneer er een PRT wordt uitgegeven, geeft Azure AD ook een versleutelde sessie sleutel aan het apparaat door. Het is versleuteld met de open bare transport sleutel (tkpub) die wordt gegenereerd en verzonden naar Azure AD als onderdeel van de registratie van het apparaat. Deze sessie sleutel kan alleen worden ontsleuteld door de privé transport sleutel (tkpriv) die wordt beveiligd door de TPM. De sessie sleutel is de toets-of-eigendoms sleutel (POP) voor alle aanvragen die worden verzonden naar Azure AD.  De sessie sleutel wordt ook beveiligd door de TPM en geen ander OS-onderdeel heeft hiervoor toegang. Token aanvragen of PRT-vernieuwings aanvragen worden door deze sessie sleutel via de TPM beveiligd en kunnen daarom niet worden gewijzigd. Azure AD valideert aanvragen van het apparaat dat niet is ondertekend door de bijbehorende sessie sleutel.

Door deze sleutels met de TPM te beveiligen, verbeteren we de beveiliging voor PRT van kwaad aardige Actors die proberen de sleutels te stelen of de PRT opnieuw af te spelen.  Met een TPM wordt de beveiliging van Azure AD join, hybride Azure AD toegevoegd en geregistreerde Azure AD-apparaten aanzienlijk uitgebreid met referentie diefstal. Voor prestaties en betrouw baarheid is TPM 2,0 de aanbevolen versie voor alle scenario's voor het registreren van Azure AD-apparaten in Windows 10. Het starten van Windows 10, 1903 update, gebruikt Azure AD geen TPM 1,2 voor een van de bovenstaande sleutels als gevolg van problemen met de betrouw baarheid. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Hoe worden app-tokens en browser cookies beveiligd?

**App-tokens**: wanneer een app token aanvragen via WAM, verzendt Azure AD een vernieuwings token en een toegangs token. WAM retourneert echter alleen het toegangs token voor de app en beveiligt het vernieuwings token in de cache door het te versleutelen met de DPAPI-sleutel (Data Protection Application Programming Interface) van de gebruiker. WAM maakt veilig gebruik van het vernieuwings token door aanvragen te ondertekenen met de sessie sleutel om verdere toegangs tokens uit te geven. De DPAPI-sleutel wordt beveiligd door een op Azure AD gebaseerde symmetrische sleutel in azure AD zelf. Wanneer het apparaat het gebruikers profiel moet ontsleutelen met de DPAPI-sleutel, biedt Azure AD de DPAPI-sleutel die is versleuteld met de sessie sleutel, die CloudAP-invoeg toepassing TPM vraagt om te ontsleutelen. Deze functionaliteit zorgt voor consistentie bij het beveiligen van vernieuwings tokens en voor komt dat toepassingen die hun eigen beveiligings mechanismen implementeren.  

**Browser cookies**: in Windows 10 ondersteunt Azure AD de browser-SSO in Internet Explorer en micro soft Edge zelf of in Google Chrome via de uitbrei ding voor Windows 10-accounts. De beveiliging is niet alleen ontworpen om de cookies te beveiligen, maar ook de eind punten waarnaar de cookies worden verzonden. Browser cookies zijn op dezelfde manier beveiligd als een PRT, door gebruik te maken van de sessie sleutel om de cookies te ondertekenen en te beveiligen.

Wanneer een gebruiker een browser interactie initieert, roept de browser (of extensie) een COM native client-host aan. De native client-host zorgt ervoor dat de pagina afkomstig is uit een van de toegestane domeinen. De browser kan andere para meters naar de native client-host verzenden, met inbegrip van een nonce, maar de host van de native client garandeert de validatie van de hostnaam. De host van de native client vraagt een PRT-cookie op uit CloudAP-invoeg toepassing, waarmee deze wordt gemaakt en ondertekend met de met TPM beveiligde sessie sleutel. Omdat de PRT-cookie is ondertekend door de sessie sleutel, is het moeilijk te knoeien met. Deze PRT cookie is opgenomen in de aanvraag header voor Azure AD om te controleren of het apparaat afkomstig is van. Als u de Chrome-browser gebruikt, kan alleen de extensie die expliciet is gedefinieerd in het manifest van de native client, deze aanroepen om te voor komen dat een wille keurige uitbrei ding deze aanvragen doet. Zodra Azure AD de PRT cookie heeft gevalideerd, wordt een sessie cookie op de browser uitgegeven. Deze sessie cookie bevat ook dezelfde sessie sleutel die is uitgegeven met een PRT. Tijdens de volgende aanvragen wordt de sessie sleutel gevalideerd, waarbij de cookie op het apparaat daad werkelijk wordt gebonden en wordt voor komen dat er elders wordt afgespeeld.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Wanneer krijgt een PRT een MFA-claim?

Een PRT kan een MFA-claim (multi-factor Authentication) verkrijgen in specifieke scenario's. Wanneer een op MFA gebaseerde PRT wordt gebruikt voor het aanvragen van tokens voor toepassingen, wordt de MFA-claim overgebracht naar die app-tokens. Deze functionaliteit biedt gebruikers een naadloze ervaring door te voor komen dat een MFA-uitdaging voor elke app waarvoor deze vereist is. Een PRT kan op de volgende manieren een MFA-claim krijgen:

* **Meld u aan met Windows hello voor bedrijven**: Windows hello voor bedrijven vervangt wacht woorden en maakt gebruik van cryptografische sleutels om een sterke twee ledige verificatie te bieden. Windows hello voor bedrijven is specifiek voor een gebruiker op een apparaat, en voor zichzelf moet MFA worden ingericht. Wanneer een gebruiker zich aanmeldt met Windows hello voor bedrijven, ontvangt de PRT van de gebruiker een MFA-claim. Dit scenario geldt ook voor gebruikers die zich aanmelden met Smart Cards als smartcard verificatie een MFA-claim van ADFS produceert.
   * Als Windows hello voor bedrijven wordt beschouwd als multi-factor Authentication, wordt de MFA-claim bijgewerkt wanneer de PRT zelf wordt vernieuwd, waardoor de MFA-duur voortdurend wordt uitgebreid wanneer gebruikers zich aanmelden met WIndows Hello voor bedrijven
* **MFA tijdens het interactief aanmelden van WAM**: tijdens een token aanvraag via WAM, als een gebruiker verplicht is om MFA toegang te geven tot de app, wordt de PRT die tijdens deze interactie is vernieuwd, niet afgedrukt met een MFA-claim.
   * In dit geval wordt de MFA-claim niet voortdurend bijgewerkt, waardoor de MFA-duur is gebaseerd op de levens duur die is ingesteld voor de Directory.
   * Wanneer een vorige bestaande PRT en RT worden gebruikt voor toegang tot een app, worden de PRT en RT beschouwd als eerste verificatie bewijs. Een nieuwe op is vereist met een tweede bewijs en een niet-afdruk bare MFA-claim. Dit geeft ook een nieuwe PRT en RT.
* **MFA tijdens apparaatregistratie**: als een beheerder hun apparaatinstellingen in azure AD heeft geconfigureerd om [MFA te vereisen voor het registreren van apparaten](device-management-azure-portal.md#configure-device-settings), moet de gebruiker MFA uitvoeren om de registratie te volt ooien. Tijdens dit proces heeft de PRT die aan de gebruiker is verleend, de MFA-claim ontvangen tijdens de registratie. Deze functie is alleen van toepassing op de gebruiker die de lid-bewerking heeft uitgevoerd, niet op andere gebruikers die zich aanmelden bij dat apparaat.
   * Net als bij de WAM Interactive Sign in wordt de MFA-claim niet continu bijgewerkt, waardoor de MFA-duur is gebaseerd op de levens duur die is ingesteld voor de Directory.

Windows 10 onderhoudt een gepartitioneerde lijst met PRTs voor elke referentie. Daarom is er een PRT voor elk van Windows hello voor bedrijven, een wacht woord of een Smart Card. Deze partitionering zorgt ervoor dat MFA-claims worden geïsoleerd op basis van de gebruikte referenties en niet worden gemengd tijdens token aanvragen.

## <a name="how-is-a-prt-invalidated"></a>Hoe wordt een PRT ongeldig?

Een PRT is ongeldig in de volgende scenario's:

* **Ongeldige gebruiker**: als een gebruiker wordt verwijderd of uitgeschakeld in azure AD, wordt de PRT ongeldig en kan deze niet worden gebruikt om tokens voor toepassingen te verkrijgen. Als een verwijderde of uitgeschakelde gebruiker zich al eerder heeft aangemeld bij een apparaat, wordt deze in het cache geheugen opgeslagen totdat CloudAP op de hoogte is van hun ongeldige status. Zodra CloudAP bepaalt dat de gebruiker ongeldig is, worden de volgende aanmeldingen geblokkeerd. Een ongeldige gebruiker wordt automatisch geblokkeerd voor het aanmelden bij nieuwe apparaten waarvoor geen referenties in de cache zijn opgeslagen.
* **Ongeldig apparaat**: als een apparaat wordt verwijderd of uitgeschakeld in azure AD, wordt het PRT dat op dat apparaat is opgehaald ongeldig en kan het niet worden gebruikt voor het verkrijgen van tokens voor andere toepassingen. Als een gebruiker al is aangemeld bij een ongeldig apparaat, kan deze door gaan. Maar alle tokens op het apparaat worden ongeldig gemaakt en de gebruiker heeft geen SSO voor bronnen van dat apparaat.
* **Wachtwoord wijziging**: nadat een gebruiker het wacht woord heeft gewijzigd, wordt de PRT die is verkregen met het vorige wacht woord, ongeldig gemaakt door Azure AD. Het wijzigen van het wacht woord leidt ertoe dat de gebruiker een nieuwe PRT krijgt. Deze invalidatie kan op twee verschillende manieren plaatsvinden:
   * Als gebruiker zich met het nieuwe wacht woord aanmeldt bij Windows, CloudAP de oude PRT en vraagt Azure AD om een nieuwe PRT te geven met het nieuwe wacht woord. Als de gebruiker geen Internet verbinding heeft, kan het nieuwe wacht woord niet worden gevalideerd; Windows kan de gebruiker mogelijk verplichten om het oude wacht woord in te voeren.
   * Als een gebruiker zich heeft aangemeld met hun oude wacht woord of het wacht woord heeft gewijzigd na het aanmelden bij Windows, wordt de oude PRT gebruikt voor alle op WAM gebaseerde token aanvragen. In dit scenario wordt de gebruiker gevraagd om opnieuw te verifiëren tijdens de WAM-token aanvraag en wordt er een nieuwe PRT uitgegeven.
* **TPM-problemen**: soms kan de TPM van een apparaat Falter of mislukken, waardoor inactief kan worden uitgevoerd op sleutels die zijn beveiligd door de TPM. In dit geval is het niet mogelijk om een PRT op te halen of tokens aan te vragen met behulp van een bestaande PRT, omdat het apparaat geen toegang kan krijgen tot de cryptografische sleutels. Als gevolg hiervan worden alle bestaande PRT door Azure AD ongeldig gemaakt. Wanneer Windows 10 een fout detecteert, wordt een herstel stroom gestart om het apparaat opnieuw te registreren bij nieuwe cryptografische sleutels. Met hybride Azure AD-deelname, net als bij de eerste registratie, gebeurt het herstel zonder tussen komst van de gebruiker. Voor Azure AD gekoppelde of Azure AD geregistreerde apparaten moet het herstel worden uitgevoerd door een gebruiker met beheerders bevoegdheden op het apparaat. In dit scenario wordt de herstel stroom geïnitieerd door een Windows-prompt waarin de gebruiker wordt begeleid om het apparaat correct te herstellen.

## <a name="detailed-flows"></a>Gedetailleerde stromen

In de volgende diagrammen ziet u de onderliggende details van het uitgeven, vernieuwen en gebruiken van een PRT om een toegangs token voor een toepassing aan te vragen. Daarnaast wordt in deze stappen ook beschreven hoe de bovengenoemde beveiligings mechanismen tijdens deze interacties worden toegepast.

### <a name="prt-issuance-during-first-sign-in"></a>PRT-uitgifte tijdens de eerste aanmelding

![PRT-uitgifte tijdens eerste aanmelding in gedetailleerde stroom](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> In azure AD gekoppelde apparaten gebeurt er synchroon om een PRT uit te geven voordat de gebruiker zich kan aanmelden bij Windows. In hybride Azure AD gekoppelde apparaten is een on-premises Active Directory de primaire instantie. De gebruiker wacht dus totdat er een TGT kan worden verkregen om zich aan te melden, terwijl de PRT-uitgifte asynchroon plaatsvindt. Dit scenario is niet van toepassing op geregistreerde Azure AD-apparaten als aanmelding geen gebruik maakt van Azure AD-referenties.

| Stap | Beschrijving |
| :---: | --- |
| A | De gebruiker voert zijn wacht woord in in de gebruikers interface voor aanmelden. LogonUI geeft de referenties in een verificatie buffer door aan LSA, die in schakelt, intern door gegeven aan CloudAP. CloudAP stuurt deze aanvraag door naar de CloudAP-invoeg toepassing. |
| B | De CloudAP-invoeg toepassing initieert een aanvraag voor realm-detectie om de ID-provider voor de gebruiker te identificeren. Als de Tenant van de gebruiker een Federatie provider heeft ingesteld, retourneert Azure AD het eind punt van het meta gegevens uitwisselings eindpunt (MEX) van de Federation-provider. Als dat niet het geval is, wordt door Azure AD geretourneerd dat de gebruiker wordt beheerd, hetgeen aangeeft dat de gebruiker kan verifiëren met Azure AD. |
| C | Als de gebruiker wordt beheerd, krijgt CloudAP de nonce van Azure AD. Als de gebruiker federatief is, vraagt CloudAP-invoeg toepassing een SAML-token van de Federatie provider aan met de referenties van de gebruiker. Zodra de app is ontvangen, vraagt het SAML-token een nonce aan bij Azure AD. |
| D | Met de CloudAP-invoeg toepassing wordt de verificatie aanvraag met de referenties, nonce en een Broker bereik van de gebruiker gemaakt, de aanvraag ondertekend met de apparaatcode (dkpriv) en verzonden naar Azure AD. In een federatieve omgeving gebruikt CloudAP plugin het SAML-token dat door de Federatie provider wordt geretourneerd in plaats van de referenties van de gebruiker. |
| E | Azure AD valideert de gebruikers referenties, de nonce en hand tekening van het apparaat, controleert of het apparaat geldig is in de Tenant en geeft de versleutelde PRT uit. In combi natie met de PRT heeft Azure AD ook een symmetrische sleutel, een zogenaamde sessie sleutel die door Azure AD is versleuteld, uitgegeven met de transport sleutel (tkpub). Daarnaast wordt de sessie sleutel ook Inge sloten in de PRT. Deze sessie sleutel fungeert als de proef-of-eigendoms sleutel (PoP) voor volgende aanvragen met de PRT. |
| F | De CloudAP-invoeg toepassing geeft de versleutelde PRT en de sessie sleutel door aan CloudAP. CloudAP vraagt de TPM om de sessie sleutel te ontsleutelen met de transport sleutel (tkpriv) en versleutelt deze opnieuw met de eigen sleutel van de TPM. De versleutelde sessie sleutel wordt in de cache van CloudAP opgeslagen samen met de PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT-vernieuwing bij volgende aanmeldingen

![PRT-vernieuwing bij volgende aanmeldingen](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Stap | Beschrijving |
| :---: | --- |
| A | De gebruiker voert zijn wacht woord in in de gebruikers interface voor aanmelden. LogonUI geeft de referenties in een verificatie buffer door aan LSA, die in schakelt, intern door gegeven aan CloudAP. CloudAP stuurt deze aanvraag door naar de CloudAP-invoeg toepassing. |
| B | Als de gebruiker zich eerder bij de gebruiker heeft aangemeld, initieert Windows de aanmelding in de cache en worden referenties gevalideerd om de gebruiker in te loggen. De CloudAP-invoeg toepassing initieert om de vier uur dat de PRT asynchroon wordt vernieuwd. |
| C | De CloudAP-invoeg toepassing initieert een aanvraag voor realm-detectie om de ID-provider voor de gebruiker te identificeren. Als de Tenant van de gebruiker een Federatie provider heeft ingesteld, retourneert Azure AD het eind punt van het meta gegevens uitwisselings eindpunt (MEX) van de Federation-provider. Als dat niet het geval is, wordt door Azure AD geretourneerd dat de gebruiker wordt beheerd, hetgeen aangeeft dat de gebruiker kan verifiëren met Azure AD. |
| D | Als de gebruiker federatief is, vraagt CloudAP-invoeg toepassing een SAML-token van de Federatie provider aan met de referenties van de gebruiker. Zodra de app is ontvangen, vraagt het SAML-token een nonce aan bij Azure AD. Als de gebruiker wordt beheerd, ontvangt CloudAP direct de nonce van Azure AD. |
| E | De CloudAP-invoeg toepassing bouwt de verificatie aanvraag met de referenties van de gebruiker, nonce en de bestaande PRT, ondertekent de aanvraag met de sessie sleutel en verzendt deze naar Azure AD. In een federatieve omgeving gebruikt CloudAP plugin het SAML-token dat door de Federatie provider wordt geretourneerd in plaats van de referenties van de gebruiker. |
| F | Azure AD valideert de hand tekening van de sessie sleutel door deze te vergelijken met de sessie sleutel Inge sloten in de PRT, de nonce te valideren en te controleren of het apparaat geldig is in de Tenant en dat er een nieuwe PRT wordt uitgegeven. Zoals eerder gezien, wordt de PRT opnieuw gecombineerd met de sessie sleutel die is versleuteld met de transport sleutel (tkpub). |
| G | De CloudAP-invoeg toepassing geeft de versleutelde PRT en de sessie sleutel door aan CloudAP. CloudAP vraagt de TPM om de sessie sleutel te ontsleutelen met de transport sleutel (tkpriv) en versleutelt deze opnieuw met de eigen sleutel van de TPM. De versleutelde sessie sleutel wordt in de cache van CloudAP opgeslagen samen met de PRT. |

> [!NOTE]
> Een PRT kan extern worden vernieuwd zonder dat er een VPN-verbinding nodig is wanneer usernamemixed-eind punten extern worden ingeschakeld.

### <a name="prt-usage-during-app-token-requests"></a>PRT-gebruik tijdens app-token aanvragen

![PRT-gebruik tijdens app-token aanvragen](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Stap | Beschrijving |
| :---: | --- |
| A | Een toepassing (bijvoorbeeld Outlook, OneNote, enz.) initieert een token aanvraag naar WAM. WAM vraagt de Azure AD WAM-invoeg toepassing op zijn beurt de token aanvraag te onderhouden. |
| B | Als er al een vernieuwings token voor de toepassing beschikbaar is, gebruikt de Azure AD WAM-invoeg toepassing deze om een toegangs token aan te vragen. Voor het opgeven van een bewijs van een apparaat-binding, wordt de aanvraag door de WAM-invoeg toepassing ondertekend met de sessie sleutel. Azure AD valideert de sessie sleutel en geeft een toegangs token en een nieuw vernieuwings token voor de app uit, versleuteld met de sessie sleutel. De WAM-invoeg toepassing de Cloud-invoeg toepassing voor het ontsleutelen van de tokens die op zijn beurt de TPM aanvragen om te ontsleutelen met behulp van de sessie sleutel, waardoor de WAM-invoeg toepassing de tokens ophaalt. Vervolgens biedt de WAM-invoeg toepassing alleen het toegangs token voor de toepassing, terwijl het vernieuwings token opnieuw versleutelt met DPAPI en opslaat in een eigen cache  |
| C |  Als een vernieuwings token voor de toepassing niet beschikbaar is, gebruikt de Azure AD WAM-invoeg toepassing de PRT om een toegangs token aan te vragen. Voor een bewijs van het bezit van de WAM-invoeg toepassing wordt de aanvraag met de PRT met de sessie sleutel ondertekend. Azure AD valideert de hand tekening van de sessie sleutel door deze te vergelijken met de Inge sloten sessie sleutel in de PRT, controleert of het apparaat geldig is en geeft een toegangs token en een vernieuwings token voor de toepassing. Daarnaast kan Azure AD een nieuwe PRT uitgeven (op basis van de vernieuwings cyclus), die allemaal zijn versleuteld met de sessie sleutel. |
| D | De WAM-invoeg toepassing de Cloud-invoeg toepassing voor het ontsleutelen van de tokens die op zijn beurt de TPM aanvragen om te ontsleutelen met behulp van de sessie sleutel, waardoor de WAM-invoeg toepassing de tokens ophaalt. Vervolgens biedt de WAM-invoeg toepassing alleen het toegangs token voor de toepassing, terwijl het vernieuwings token opnieuw versleutelt met DPAPI en opslaat in een eigen cache. De WAM-invoeg toepassing maakt gebruik van het vernieuwings token dat wordt gebruikt voor deze toepassing. De WAM-invoeg toepassing geeft ook een back-up van de nieuwe PRT naar de Cloud-AP-invoeg toepassing, die de PRT valideert met Azure AD voordat u deze in een eigen cache bijwerkt. Cloud-AP-invoeg toepassing maakt gebruik van de nieuwe PRT. |
| E | WAM biedt het nieuw verleende toegangs token aan WAM, dat op zijn beurt weer beschikbaar is voor de aanroepende toepassing|

### <a name="browser-sso-using-prt"></a>Browser-SSO met PRT

![Browser-SSO met PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Stap | Beschrijving |
| :---: | --- |
| A | De gebruiker meldt zich aan bij Windows met hun referenties voor het verkrijgen van een PRT. Zodra de gebruiker de browser opent, worden de Url's vanuit het REGI ster geladen door de browser (of de extensie). |
| B | Wanneer een gebruiker een aanmeldings-URL voor Azure AD opent, valideert de browser of extensie de URL met de url's die zijn verkregen uit het REGI ster. Als ze overeenkomen, roept de browser de native client host op om een token op te halen. |
| C | De native client-host valideert dat de Url's horen bij de micro soft-identiteits providers (Microsoft-account of Azure AD), extraheert een nonce die vanuit de URL wordt verzonden en maakt een aanroep naar de CloudAP-invoeg toepassing om een PRT-cookie op te halen. |
| D | De CloudAP-invoeg toepassing maakt de PRT-cookie, Meld u aan met de TPM-gebonden sessie sleutel en stuurt deze terug naar de host van de native client. |
| E | De host van de native client retourneert deze PRT-cookie naar de browser, waarin deze wordt opgenomen als onderdeel van de aanvraag header x-MS-RefreshTokenCredential en aanvraag tokens van Azure AD. |
| F | Azure AD valideert de hand tekening van de sessie sleutel op de PRT cookie, valideert de nonce, controleert of het apparaat geldig is in de Tenant en geeft een ID-token voor de webpagina en een versleutelde sessie cookie voor de browser. |

> [!NOTE]
> De browser-SSO-stroom die in de bovenstaande stappen wordt beschreven, is niet van toepassing op sessies in persoonlijke modi, zoals InPrivate in micro soft Edge, of incognito in Google Chrome (wanneer u de micro soft-account uitbreiding gebruikt).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen met PRT, raadpleegt u het artikel [problemen oplossen met hybride Azure Active Directory die deel uitmaakt van Windows 10-en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md).
