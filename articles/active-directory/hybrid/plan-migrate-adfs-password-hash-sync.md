---
title: 'Azure AD Connect: migreren van Federatie naar PHS voor Azure AD | Microsoft Docs'
description: Dit artikel bevat informatie over het verplaatsen van uw hybride identiteits omgeving van Federatie naar wachtwoord hash-synchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861813"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migreren van Federatie naar wacht woord hash synchronisatie voor Azure Active Directory

In dit artikel wordt beschreven hoe u uw organisatie domeinen kunt verplaatsen van Active Directory Federation Services (AD FS) naar de synchronisatie van wacht woord-hashes.

> [!NOTE]
> Het wijzigen van de verificatie methode vereist planning, testen en mogelijk downtime. [Gefaseerde implementatie](how-to-connect-staged-rollout.md) biedt een alternatieve manier om te testen en geleidelijk te migreren van Federatie naar Cloud verificatie met behulp van wachtwoord-hash-synchronisatie.
>
> Als u van plan bent gefaseerde implementatie te gebruiken, moet u de functies voor gefaseerde implementatie uitschakelen zodra u klaar bent met knippen.  Zie voor meer informatie [migreren naar Cloud verificatie met behulp van gefaseerde implementatie](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Vereisten voor het migreren naar wachtwoord-hash-synchronisatie

De volgende vereisten zijn vereist voor de migratie van met behulp van AD FS om wachtwoord-hash-synchronisatie te gebruiken.


### <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

Als u Mini maal de stappen voor het migreren naar wachtwoord-hash-synchronisatie wilt uitvoeren, moet u [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 hebben. Deze versie bevat belang rijke wijzigingen in de manier waarop de aanmeldings conversie wordt uitgevoerd en vermindert de totale tijd voor het migreren van Federatie naar Cloud verificatie van potentieel uur naar een andere periode.


> [!IMPORTANT]
> U kunt de verouderde documentatie, hulpprogram ma's en blogs lezen die gebruikers conversie vereist wanneer u domeinen van federatieve identiteit naar beheerde identiteit converteert. Het is niet meer nodig om *gebruikers te converteren* . Micro soft werkt met het bijwerken van documentatie en hulpprogram ma's om deze wijziging weer te geven.

Volg de stappen in Azure AD Connect om Azure AD Connect bij te werken: Voer een [upgrade uit naar de nieuwste versie](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Synchronisatie van wacht woord-hash vereist

U kunt Azure AD Connect configureren met behulp van snelle instellingen of een aangepaste installatie. Als u de optie voor aangepaste installatie hebt gebruikt, zijn de [vereiste machtigingen](./reference-connect-accounts-permissions.md) voor wachtwoord hash-synchronisatie mogelijk niet aanwezig.

De Azure AD Connect-service account Active Directory Domain Services (AD DS) vereist de volgende machtigingen voor het synchroniseren van wacht woord-hashes:

* Directory wijzigingen repliceren
* Wijzigingen in Directory repliceren

Het is nu een goed moment om te controleren of deze machtigingen aanwezig zijn voor alle domeinen in het forest.

### <a name="plan-the-migration-method"></a>De migratie methode plannen

U kunt kiezen uit twee methoden voor het migreren van federatief identiteits beheer naar wachtwoord hash-synchronisatie en naadloze eenmalige aanmelding (SSO). De methode die u gebruikt, is afhankelijk van de manier waarop uw AD FS-exemplaar oorspronkelijk is geconfigureerd.

* **Azure AD Connect**. Als u AD FS oorspronkelijk hebt geconfigureerd met behulp van Azure AD Connect, *moet* u de Azure AD Connect wizard gebruiken om de wachtwoord hash-synchronisatie te wijzigen.

   Azure AD Connect wordt automatisch de cmdlet **set-MsolDomainAuthentication** uitgevoerd wanneer u de aanmeldings methode voor gebruikers wijzigt. Azure AD Connect automatisch alle geverifieerde federatieve domeinen in uw Azure AD-Tenant unfederates.

   > [!NOTE]
   > Als u op dit moment Azure AD Connect hebt gebruikt om AD FS te configureren, kunt u niet voor komen dat alle domeinen in uw Tenant unfederating wanneer u de aanmelding van de gebruiker wijzigt in een wacht woord-hash-synchronisatie. ‎
* **Azure AD Connect met Power shell**. U kunt deze methode alleen gebruiken als u AD FS niet oorspronkelijk hebt geconfigureerd met behulp van Azure AD Connect. Voor deze optie moet u de aanmeldings methode voor gebruikers nog steeds wijzigen via de wizard Azure AD Connect. Het belangrijkste verschil met deze optie is dat de wizard de cmdlet **set-MsolDomainAuthentication** niet automatisch uitvoert. Met deze optie hebt u volledige controle over welke domeinen worden geconverteerd en in welke volg orde.

Volg de stappen in de volgende secties om te begrijpen welke methode u moet gebruiken.

#### <a name="verify-current-user-sign-in-settings"></a>Instellingen voor de aanmelding van de huidige gebruiker controleren

Controleren of uw huidige gebruikers instellingen voor aanmelden:

1. Meld u aan bij de [Azure AD-Portal](https://aad.portal.azure.com/) met een account voor globale beheerders.
2. Controleer de volgende instellingen in de sectie **gebruikers aanmelding** :
   * **Federatie** is ingesteld op **ingeschakeld**.
   * **Naadloze eenmalige aanmelding** is ingesteld op **uitgeschakeld**.
   * **Pass Through-verificatie** is ingesteld op **uitgeschakeld**.

   ![Scherm opname van de instellingen in de sectie Azure AD Connect gebruiker aanmelden](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>De configuratie van de Azure AD Connect controleren

1. Open Azure AD Connect op uw Azure AD Connect-server. Selecteer **Configureren**.
2. Selecteer op de pagina **extra taken** de optie **huidige configuratie weer geven** en selecteer vervolgens **volgende**.<br />

   ![Scherm afbeelding van de optie huidige configuratie weer geven geselecteerd op de pagina extra taken](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Op de pagina **uw oplossing controleren ziet u** de **synchronisatie** status van de wachtwoord-hash.<br /> 

   * Als **wachtwoord hash-synchronisatie** is ingesteld op **uitgeschakeld**, voert u de stappen in dit artikel uit om deze functie in te scha kelen.
   * Als **wacht woord-hash-synchronisatie** is ingesteld op **ingeschakeld**, kunt u de sectie **stap 1: wachtwoord hash-synchronisatie inschakelen** in dit artikel overs Laan.
4. Ga op de pagina **uw oplossing controleren** naar **Active Directory Federation Services (AD FS)**.<br />

   * Als de AD FS configuratie in deze sectie wordt weer gegeven, kunt u veilig aannemen dat AD FS oorspronkelijk is geconfigureerd met behulp van Azure AD Connect. U kunt uw domeinen van federatieve identiteiten naar een beheerde identiteit converteren met behulp van de aanmeldings optie Azure AD Connect **gebruiker wijzigen** . Het proces wordt beschreven in de sectie **optie A: overschakelen van Federatie naar wachtwoord-hash-synchronisatie met behulp van Azure AD Connect**.
   * Als AD FS niet in de huidige instellingen wordt vermeld, moet u uw domeinen hand matig met behulp van Power shell converteren van federatieve identiteit naar beheerde identiteit. Zie voor meer informatie over dit proces de sectie **optie B: overschakelen van Federatie naar wachtwoord-hash-synchronisatie met behulp van Azure AD Connect en Power shell**.

### <a name="document-current-federation-settings"></a>Huidige Federatie-instellingen documenteren

Als u de huidige Federatie-instellingen wilt vinden, voert u de cmdlet **Get-MsolDomainFederationSettings** uit:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Voorbeeld:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Controleer alle instellingen die mogelijk zijn aangepast voor uw Federatie-ontwerp en implementatie documentatie. Zoek in het bijzonder naar aanpassingen in **PreferredAuthenticationProtocol**, **SupportsMfa** en **PromptLoginBehavior**.

Raadpleeg deze artikelen voor meer informatie:

* [AD FS prompt = ondersteuning voor aanmeld parameters](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Als **SupportsMfa** is ingesteld op **True**, gebruikt u een on-premises multi-factor Authentication-oplossing om een tweede factor Challenge in te voeren in de verificatie stroom van de gebruiker. Deze installatie werkt niet meer voor Azure AD-verificatie scenario's nadat dit domein is geconverteerd van federatieve naar beheerde authenticatie. Nadat u Federatie hebt uitgeschakeld, neemt u de relatie met uw on-premises Federatie op. Dit omvat ook on-premises MFA-adapters. 
>
> Gebruik in plaats daarvan de Cloud service van Azure AD Multi-Factor Authentication om dezelfde functie uit te voeren. Evalueer zorgvuldig uw multi-factor Authentication-vereisten voordat u doorgaat. Zorg ervoor dat u weet hoe u Azure AD Multi-Factor Authentication, de implicaties van licenties en het gebruikers registratie proces kunt gebruiken voordat u uw domeinen converteert.

#### <a name="back-up-federation-settings"></a>Back-up van Federatie-instellingen

Hoewel er geen wijzigingen worden aangebracht aan andere relying party's in uw AD FS-Farm tijdens de processen die in dit artikel worden beschreven, wordt u aangeraden een geldige back-up te maken van uw AD FS-Farm waarvan u kunt herstellen. U kunt een huidige geldige back-up maken met behulp van het gratis [hulp programma micro soft AD FS Rapid Restore](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). U kunt het hulp programma gebruiken om een back-up te maken van AD FS en een bestaande farm te herstellen of een nieuwe farm te maken.

Als u ervoor kiest om de AD FS Rapid Restore-hulp programma te gebruiken, moet u Mini maal het Microsoft 365 identiteits platform Relying Party vertrouwen exporteren en alle gekoppelde aangepaste claim regels die u hebt toegevoegd. U kunt de Relying Party vertrouwens-en gekoppelde claim regels exporteren met behulp van het volgende Power shell-voor beeld:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Overwegingen bij de implementatie en het gebruik van AD FS

In deze sectie worden de overwegingen voor implementatie en informatie over het gebruik van AD FS beschreven.

### <a name="current-ad-fs-use"></a>Huidig AD FS gebruik

Voordat u de federatieve identiteit omzet in een beheerde identiteit, kijkt u goed op hoe u momenteel AD FS voor Azure AD, Microsoft 365 en andere toepassingen (Relying Party vertrouwens relaties) gebruikt. Bekijk in het bijzonder de scenario's die in de volgende tabel worden beschreven:

| Als | Kies |
|-|-|
| U wilt AD FS blijven gebruiken met andere toepassingen (met uitzonde ring van Azure AD en Microsoft 365). | Nadat u uw domeinen hebt geconverteerd, gebruikt u zowel AD FS als Azure AD. Denk aan de gebruikers ervaring. In sommige gevallen moeten gebruikers mogelijk twee maal worden geverifieerd: eenmaal naar Azure AD (waarbij een gebruiker SSO-toegang krijgt tot andere toepassingen, zoals Microsoft 365) en opnieuw voor alle toepassingen die nog steeds zijn gebonden aan AD FS als een Relying Party-vertrouwens relatie. |
| Uw AD FS-exemplaar is sterk aangepast en is afhankelijk van specifieke aanpassings instellingen in het onload.js-bestand (bijvoorbeeld als u de aanmeldings ervaring hebt gewijzigd, zodat gebruikers alleen een **sAMAccountName** -indeling voor hun gebruikers naam gebruiken in plaats van een UPN (User Principal Name), of uw organisatie de aanmeldings ervaring sterk merkt). Het onload.js bestand kan niet worden gedupliceerd in azure AD. | Voordat u doorgaat, moet u controleren of Azure AD kan voldoen aan uw huidige aanpassings vereisten. Zie de secties over AD FS huis stijl en AD FS aanpassen voor meer informatie en voor hulp.|
| U gebruikt AD FS om eerdere versies van Authentication-clients te blok keren.| Overweeg AD FS besturings elementen te vervangen die eerdere versies van authenticatie-clients blok keren door gebruik te maken van een combi natie van [besturings elementen voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-conditions.md) en [Exchange Online-regels voor client toegang](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| U wilt dat gebruikers multi-factor Authentication uitvoeren op een on-premises multi-factor Authentication-Server oplossing wanneer gebruikers zich verifiëren bij AD FS.| U kunt in een beheerd identiteits domein geen multi-factor Authentication-Challenge injecteren via de on-premises multi-factor Authentication-oplossing in de verificatie stroom. U kunt echter de Azure AD Multi-Factor Authentication-Service voor multi-factor Authentication gebruiken nadat het domein is geconverteerd.<br /><br /> Als uw gebruikers momenteel geen Azure AD-Multi-Factor Authentication gebruiken, is een eenmalige-gebruikers registratie-stap vereist. U moet de geplande registratie voorbereiden en aan uw gebruikers door geven. |
| U gebruikt momenteel toegangs beheer beleid (AuthZ-regels) in AD FS om de toegang tot Microsoft 365 te beheren.| Overweeg om het beleid te vervangen door het equivalente [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) van Azure AD en de [Exchange Online-regels voor client toegang](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Algemene AD FS aanpassingen

In deze sectie worden algemene AD FS aanpassingen beschreven.

#### <a name="insidecorporatenetwork-claim"></a>Claim InsideCorporateNetwork

AD FS wordt de **InsideCorporateNetwork** -claim uitgegeven als de gebruiker die de verificatie uitvoert, zich in het bedrijfs netwerk bevindt. Deze claim kan vervolgens worden door gegeven aan Azure AD. De claim wordt gebruikt om multi-factor Authentication over te slaan op basis van de netwerk locatie van de gebruiker. Zie [vertrouwde IP-adressen voor federatieve gebruikers](../authentication/howto-mfa-adfs.md)voor meer informatie over het bepalen of deze functionaliteit momenteel is ingeschakeld in AD FS.

De **InsideCorporateNetwork** -claim is niet beschikbaar nadat uw domeinen zijn geconverteerd naar wachtwoord-hash-synchronisatie. U kunt de [benoemde locaties in azure AD](../reports-monitoring/quickstart-configure-named-locations.md) gebruiken om deze functionaliteit te vervangen.

Nadat u benoemde locaties hebt geconfigureerd, moet u alle beleids regels voor voorwaardelijke toegang bijwerken die zijn geconfigureerd voor het opnemen of uitsluiten van **alle vertrouwde locaties** of door **MFA vertrouwde IP-adressen** om de nieuwe benoemde locaties weer te geven.

Zie [Active Directory locaties voor voorwaardelijke toegang](../conditional-access/location-condition.md)voor meer informatie over de **locatie** voorwaarde in voorwaardelijke toegang.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybride apparaten die deel uitmaken van Azure AD

Wanneer u een apparaat toevoegt aan Azure AD, kunt u regels voor voorwaardelijke toegang maken waarmee wordt afgedwongen dat apparaten voldoen aan uw toegangs normen voor beveiliging en naleving. Gebruikers kunnen zich ook bij een apparaat aanmelden met een werk-of school account van een organisatie in plaats van een persoonlijk account. Wanneer u hybride apparaten die deel uitmaken van Azure AD gebruikt, kunt u uw Active Directory apparaten die lid zijn van het domein toevoegen aan Azure AD. Uw federatieve omgeving is mogelijk ingesteld om deze functie te gebruiken.

Om ervoor te zorgen dat hybride samen werking blijft werken voor apparaten die lid zijn van het domein nadat uw domeinen zijn geconverteerd naar een wachtwoord-hash-synchronisatie, moet u voor Windows 10-clients Azure AD Connect-Apparaatinstellingen gebruiken om Active Directory computer accounts te synchroniseren met Azure AD. 

Voor Windows 8-en Windows 7-computer accounts gebruikt hybride deelname naadloze SSO om de computer te registreren in azure AD. U hoeft geen Windows 8-en Windows 7-computer accounts te synchroniseren zoals u dat voor Windows 10-apparaten kunt doen. U moet echter een bijgewerkt workplacejoin.exe-bestand (via een. msi-bestand) implementeren op Windows 8-en Windows 7-clients zodat ze zich kunnen registreren met naadloze SSO. [Down load het MSI-bestand](https://www.microsoft.com/download/details.aspx?id=53554).

Zie [Configure Hybrid Azure AD-joined devices](../devices/hybrid-azuread-join-plan.md)(Engelstalig) voor meer informatie.

#### <a name="branding"></a>Branding

Als uw organisatie [uw AD FS aanmeld pagina's heeft aangepast](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) om informatie weer te geven die relevant is voor de organisatie, kunt u overwegen vergelijk bare [aanpassingen aan te brengen op de aanmeldings pagina van Azure AD](../fundamentals/customize-branding.md).

Hoewel soort gelijke aanpassingen beschikbaar zijn, moeten sommige visuele wijzigingen op aanmeldings pagina's na de conversie worden verwacht. Mogelijk wilt u informatie geven over verwachte wijzigingen in uw communicatie met gebruikers.

> [!NOTE]
> De huis stijl van de organisatie is alleen beschikbaar als u de Premium-of basis licentie voor Azure Active Directory aanschaft of als u een Microsoft 365 licentie hebt.

## <a name="plan-deployment-and-support"></a>Implementatie en ondersteuning plannen

Voer de taken uit die worden beschreven in deze sectie om u te helpen bij het plannen van de implementatie en ondersteuning.

### <a name="plan-the-maintenance-window"></a>Het onderhouds venster plannen

Hoewel het domein conversie relatief snel is, kan Azure AD gedurende Maxi maal vier uur na het volt ooien van de domein conversie enkele verificatie aanvragen naar uw AD FS-servers verzenden. In dit venster van vier uur en afhankelijk van verschillende service-caches kan Azure AD deze verificaties niet accepteren. Gebruikers kunnen een fout melding ontvangen. De gebruiker kan nog steeds met AD FS verifiëren, maar Azure AD accepteert het uitgegeven token van de gebruiker niet meer, omdat deze Federation-vertrouwens relatie nu is verwijderd.

Dit geldt alleen voor gebruikers die toegang hebben tot de services via een webbrowser tijdens dit post-conversie venster voordat de cache aan de service zijde wordt gewist. Oudere clients (Exchange ActiveSync, Outlook 2010/2013) worden niet verwacht, omdat Exchange Online een cache van hun referenties voor een bepaalde periode houdt. De cache wordt gebruikt om de gebruiker op de achtergrond opnieuw te verifiëren. De gebruiker hoeft niet terug te gaan naar AD FS. Referenties die zijn opgeslagen op het apparaat voor deze clients worden gebruikt om zichzelf opnieuw te verifiëren nadat deze cache is gewist. Als gevolg van het proces voor domein conversie worden gebruikers niet verwacht dat ze een wacht woord hoeven te ontvangen. 

Moderne verificatie-clients (Office 2016-en Office 2013-, iOS-en Android-apps) gebruiken een geldig vernieuwings token om nieuwe toegangs tokens te verkrijgen voor verdere toegang tot resources, in plaats van terug te gaan naar AD FS. Deze clients zijn ongevoelig voor wachtwoord prompts die het resultaat zijn van het domein conversie proces. De clients blijven werken zonder dat er aanvullende configuratie is.

> [!IMPORTANT]
> Sluit uw AD FS omgeving niet af of verwijder de Microsoft 365 Relying Party-vertrouwens relatie totdat u hebt gecontroleerd of alle gebruikers kunnen verifiëren met behulp van Cloud verificatie.

### <a name="plan-for-rollback"></a>Ongedaan maken plannen

Als u een belang rijk probleem ondervindt dat niet snel kan worden opgelost, kunt u besluiten om de oplossing terug te zetten naar de Federatie. Het is belang rijk om te plannen wat u moet doen als uw implementatie niet zo bedoeld is. Als de conversie van het domein of de gebruikers tijdens de implementatie mislukt, of als u de Federatie wilt terugdraaien, moet u weten hoe u de uitval kunt verhelpen en het effect van uw gebruikers kunt verminderen.

#### <a name="to-roll-back"></a>Terug te draaien

Als u wilt terugdraaien, raadpleegt u de federatieve ontwerp-en implementatie documentatie voor uw specifieke implementatie gegevens. Het proces moet deze taken bevatten:

* Het converteren van beheerde domeinen naar federatieve domeinen met behulp van de cmdlet **Convert-MSOLDomainToFederated** .
* Als dat nodig is, kunt u aanvullende claim regels configureren.

### <a name="plan-communications"></a>De communicatie plannen

Een belang rijk onderdeel van de implementatie en ondersteuning van de planning zorgt ervoor dat uw gebruikers proactief op de hoogte worden gebracht van aanstaande wijzigingen. Gebruikers moeten vooraf weten wat ze kunnen ervaren en wat er nodig is. 

Nadat de synchronisatie van wacht woord-hash en naadloze SSO zijn geïmplementeerd, kan de gebruiker zich aanmelden om toegang te krijgen tot Microsoft 365 en andere bronnen die worden geverifieerd via Azure AD-wijzigingen. Gebruikers die zich buiten het netwerk bevinden, zien alleen de aanmeldings pagina van Azure AD. Deze gebruikers worden niet omgeleid naar de op formulieren gebaseerde pagina die wordt gepresenteerd door externe Web Application proxy-servers.

Neem de volgende elementen op in uw communicatie strategie:

* Gebruikers op de hoogte stellen van aanstaande en uitgebrachte functionaliteit met behulp van:
   * E-mail en andere interne communicatie kanalen.
   * Visuele elementen, zoals posters.
   * Executive, Live of andere communicatie.
* Bepaal wie de communicatie zal aanpassen en wie de communicatie gaat verzenden en wanneer.

## <a name="implement-your-solution"></a>Uw oplossing implementeren

U hebt uw oplossing gepland. Nu kunt u het implementeren. Implementatie omvat de volgende onderdelen:

* Synchronisatie van wacht woord-hash inschakelen.
* De voor bereiding op naadloze SSO.
* Het wijzigen van de aanmeldings methode voor wachtwoord hash synchronisatie en het inschakelen van naadloze SSO.

### <a name="step-1-enable-password-hash-synchronization"></a>Stap 1: wacht woord-hash-synchronisatie inschakelen

De eerste stap voor het implementeren van deze oplossing is het inschakelen van wachtwoord hash-synchronisatie met behulp van de wizard Azure AD Connect. Wachtwoord hash-synchronisatie is een optionele functie die u kunt inschakelen in omgevingen die gebruikmaken van Federatie. Er is geen effect op de verificatie stroom. In dit geval wordt Azure AD Connect het synchroniseren van wacht woord-hashes gestart zonder dat dit van invloed is op gebruikers die zich aanmelden met behulp van Federatie.

Daarom wordt u aangeraden deze stap te volt ooien als een voorbereidings taak voordat u de aanmeldings methode van uw domein wijzigt. Vervolgens hebt u ruim genoeg tijd om te controleren of de synchronisatie van wacht woord-hash correct werkt.

Hash-synchronisatie van wacht woord inschakelen:

1. Open de wizard Azure AD Connect op de Azure AD Connect-server en selecteer vervolgens **configureren**.
2. Selecteer **synchronisatie opties aanpassen** en selecteer **volgende**.
3. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van een account voor globale beheerders.
4. Selecteer **volgende** op de pagina **verbinding maken met uw adres lijsten** .
5. Selecteer op de pagina **domein en OE filteren** de optie **volgende**.
6. Op de pagina **optionele functies** selecteert u **wachtwoord synchronisatie** en selecteert u vervolgens **volgende**.
 
   ![Scherm afbeelding van de optie voor wachtwoord synchronisatie geselecteerd op de pagina optionele functies](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecteer **volgende** op de resterende pagina's. Selecteer **configureren** op de laatste pagina.
8. Azure AD Connect begint met het synchroniseren van wacht woord-hashes bij de volgende synchronisatie.

Nadat de synchronisatie van wacht woord-hash is ingeschakeld, worden de wacht woord-hashes voor alle gebruikers in het Azure AD Connect synchronisatie bereik opnieuw gehasht en naar Azure AD geschreven. Afhankelijk van het aantal gebruikers kan deze bewerking enkele minuten of enkele uren duren.

Voor plannings doeleinden moet u ramen dat ongeveer 20.000 gebruikers in één uur worden verwerkt.

Als u wilt controleren of de synchronisatie van wacht woord-hash correct werkt, voltooit u de taak voor **probleem oplossing** in de Azure AD Connect wizard:

1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect server met behulp van de optie als administrator uitvoeren.
2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .
3. Start de wizard Azure AD Connect.
4. Ga naar de pagina **extra taken** , selecteer **problemen oplossen** en selecteer **volgende**.
5. Selecteer op de pagina **probleem oplossing** de optie **starten** om het menu probleem oplossing in Power shell te starten.
6. Selecteer in het hoofd menu de optie **problemen met wachtwoord-hash-synchronisatie oplossen**.
7. Selecteer in het submenu de optie **wacht woord-hash synchronisatie werkt helemaal niet**.

Zie problemen met wachtwoord- [hash-synchronisatie met Azure AD Connect synchronisatie oplossen](./tshoot-connect-password-hash-synchronization.md)voor problemen oplossen.

### <a name="step-2-prepare-for-seamless-sso"></a>Stap 2: bereid u voor op naadloze SSO

Als u wilt dat uw apparaten naadloze SSO gebruiken, moet u een Azure AD-URL toevoegen aan de intranet zone-instellingen van gebruikers met behulp van een groeps beleid in Active Directory.

Webbrowsers berekenen standaard automatisch de juiste zone, Internet of intranet, van een URL. Bijvoorbeeld **http: \/ \/ Contoso/** wijst toe aan de intranet zone en **http: \/ \/ intranet.contoso.com** wordt toegewezen aan de zone Internet (omdat de URL een punt bevat). Browsers verzenden van Kerberos-tickets naar een Cloud eindpunt, zoals de Azure AD-URL, alleen als u de URL expliciet toevoegt aan de intranet zone van de browser.

Voer de stappen uit om de vereiste wijzigingen op uw apparaten [uit](./how-to-connect-sso-quick-start.md) te voeren.

> [!IMPORTANT]
> Als u deze wijziging aanbrengt, wordt de manier waarop uw gebruikers zich aanmelden bij Azure AD, niet gewijzigd. Het is echter belang rijk dat u deze configuratie toepast op al uw apparaten voordat u verdergaat. Gebruikers die zich aanmelden op apparaten die deze configuratie nog niet hebben ontvangen, moeten een gebruikers naam en wacht woord invoeren om zich aan te melden bij Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Stap 3: Wijzig de aanmeldings methode in wachtwoord hash synchronisatie en schakel naadloze SSO in

U hebt twee opties voor het wijzigen van de aanmeldings methode voor wachtwoord hash synchronisatie en het inschakelen van naadloze SSO.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Optie A: overschakelen van Federatie naar hash-synchronisatie met wacht woord met behulp van Azure AD Connect

Gebruik deze methode als u uw AD FS-omgeving voor het eerst hebt geconfigureerd met behulp van Azure AD Connect. U kunt deze methode niet gebruiken als u uw AD FS-omgeving *niet eerder hebt* geconfigureerd met behulp van Azure AD Connect.

Wijzig eerst de aanmeldings methode:

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **gebruiker aanmelden wijzigen** en selecteer **volgende**. 

   ![Scherm afbeelding van de optie gebruiker aanmelden wijzigen op de pagina extra taken](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van een account voor globale beheerders.
4. Op de **aanmeldings** pagina van de gebruiker selecteert u de **knop wachtwoord hash-synchronisatie**. Zorg ervoor dat u het selectie vakje **gebruikers accounts niet converteren** selecteert. De optie is afgeschaft. Selecteer **eenmalige aanmelding inschakelen** en selecteer **volgende**.

   ![Scherm afbeelding van de pagina eenmalige aanmelding inschakelen](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Als u begint met Azure AD Connect versie 1.1.880.0, is het selectie vakje **naadloze eenmalige aanmelding** standaard ingeschakeld.

   > [!IMPORTANT]
   > U kunt de waarschuwingen die aangeven dat gebruikers conversie en volledige wachtwoord-hash-synchronisatie, de vereiste stappen voor het converteren van Federatie naar Cloud authenticatie negeren. Houd er rekening mee dat deze stappen niet meer nodig zijn. Als deze waarschuwingen nog steeds worden weer gegeven, moet u ervoor zorgen dat u de nieuwste versie van Azure AD Connect gebruikt en dat u de meest recente versie van deze hand leiding hebt. Zie de sectie [Update Azure AD Connect](#update-azure-ad-connect)voor meer informatie.

5. Op de pagina **eenmalige aanmelding inschakelen** voert u de referenties van het domein beheerders account in en selecteert u vervolgens **volgende**.

   ![Scherm afbeelding van de pagina eenmalige aanmelding inschakelen waar u de referenties voor het domein beheerders account kunt invoeren.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Referenties voor het domein beheerders account zijn vereist om naadloze SSO in te scha kelen. Het proces is voltooid met de volgende acties, waarvoor deze verhoogde machtigingen zijn vereist. De referenties van het domein beheerders account worden niet opgeslagen in Azure AD Connect of in azure AD. De referenties van het domein beheerders account worden alleen gebruikt om de functie in te scha kelen. De referenties worden verwijderd wanneer het proces is voltooid.
   >
   > 1. Een computer account met de naam AZUREADSSOACC (waarmee Azure AD wordt aangeduid) wordt gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-ontsleutelings sleutel van het computer account wordt veilig gedeeld met Azure AD.
   > 3. Er zijn twee Kerberos Service Principal Names (Spn's) gemaakt om twee Url's voor te stellen die worden gebruikt tijdens de aanmelding van Azure AD.

6. Controleer op de pagina **gereed voor configuratie** of het selectie vakje **synchronisatie proces starten wanneer de configuratie is voltooid** is ingeschakeld. Selecteer vervolgens **configureren**.

      ![Scherm afbeelding van de pagina gereed voor configuratie](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Op dit moment worden alle federatieve domeinen gewijzigd in beheerde authenticatie. Wachtwoord hash-synchronisatie is de nieuwe verificatie methode.

7. Selecteer in de Azure AD-Portal **Azure Active Directory**  >  **Azure AD CONNECT**.
8. Controleer de volgende instellingen:
   * **Federatie** is ingesteld op **uitgeschakeld**.
   * **Naadloze eenmalige aanmelding** is **ingeschakeld**.
   * **Wachtwoord synchronisatie** is ingesteld op **ingeschakeld**.<br /> 

   ![Scherm afbeelding met de instellingen in de aanmeldings sectie van de gebruiker van de Azure AD-Portal.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Ga verder met [testen en volgende stappen](#testing-and-next-steps).

   > [!IMPORTANT]
   > Overs Laan **van de sectie optie B: overschakelen van Federatie naar wachtwoord-hash-synchronisatie met behulp van Azure AD Connect en Power shell**. De stappen in deze sectie zijn niet van toepassing als u optie A hebt gekozen om de aanmeldings methode te wijzigen in wachtwoord hash synchronisatie en naadloze SSO in te scha kelen.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Optie B: overschakelen van Federatie naar wachtwoord hash synchronisatie met Azure AD Connect en Power shell

Gebruik deze optie als u uw federatieve domeinen niet eerst hebt geconfigureerd met behulp van Azure AD Connect. Tijdens dit proces schakelt u naadloze SSO in en activeert u uw domeinen van federatieve naar beheerd.

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **gebruiker aanmelden wijzigen** en selecteer **volgende**.
3. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in voor een account voor globale beheerders.
4. Op de **aanmeldings** pagina van de gebruiker selecteert u de knop **wachtwoord hash-synchronisatie** . Selecteer **eenmalige aanmelding inschakelen** en selecteer **volgende**.

   Voordat u wachtwoord hash-synchronisatie inschakelt: ![ scherm opname met de optie niet configureren op de aanmeldings pagina van de gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Nadat u wachtwoord hash-synchronisatie hebt ingeschakeld: ![ scherm opname met nieuwe opties op de aanmeldings pagina van de gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Als u begint met Azure AD Connect versie 1.1.880.0, is het selectie vakje **naadloze eenmalige aanmelding** standaard ingeschakeld.

5. Op de pagina **eenmalige aanmelding inschakelen** voert u de referenties in voor een domein beheerders account en selecteert u **volgende**.

   > [!NOTE]
   > Referenties voor het domein beheerders account zijn vereist om naadloze SSO in te scha kelen. Het proces is voltooid met de volgende acties, waarvoor deze verhoogde machtigingen zijn vereist. De referenties van het domein beheerders account worden niet opgeslagen in Azure AD Connect of in azure AD. De referenties van het domein beheerders account worden alleen gebruikt om de functie in te scha kelen. De referenties worden verwijderd wanneer het proces is voltooid.
   >
   > 1. Een computer account met de naam AZUREADSSOACC (waarmee Azure AD wordt aangeduid) wordt gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-ontsleutelings sleutel van het computer account wordt veilig gedeeld met Azure AD.
   > 3. Er zijn twee Kerberos Service Principal Names (Spn's) gemaakt om twee Url's voor te stellen die worden gebruikt tijdens de aanmelding van Azure AD.

6. Controleer op de pagina **gereed voor configuratie** of het selectie vakje **synchronisatie proces starten wanneer de configuratie is voltooid** is ingeschakeld. Selecteer vervolgens **configureren**.

   ![Scherm afbeelding met de knop configureren op de pagina gereed om te configureren](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Wanneer u de knop **configureren** selecteert, wordt naadloze SSO geconfigureerd zoals aangegeven in de vorige stap. De synchronisatie configuratie voor wacht woord-hashes is niet gewijzigd omdat deze eerder is ingeschakeld.

   > [!IMPORTANT]
   > Er zijn geen wijzigingen aangebracht in de manier waarop gebruikers zich op dit moment aanmelden.

7. Controleer de volgende instellingen in de Azure AD-portal:
   * **Federatie** is ingesteld op **ingeschakeld**.
   * **Naadloze eenmalige aanmelding** is **ingeschakeld**.
   * **Wachtwoord synchronisatie** is ingesteld op **ingeschakeld**.

   ![Scherm afbeelding met de instellingen in de sectie gebruikers aanmelding](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Domeinen van federatieve naar beheerd converteren

Op dit moment is Federatie nog steeds ingeschakeld en operationeel voor uw domeinen. Om door te gaan met de implementatie moet elk domein worden geconverteerd van federatieve naar beheerd om gebruikers verificatie te forceren via wachtwoord-hash-synchronisatie.

> [!IMPORTANT]
> U hoeft niet alle domeinen tegelijk te converteren. U kunt ervoor kiezen om te beginnen met een test domein in uw productie Tenant of te beginnen met het domein met het laagste aantal gebruikers.

De conversie volt ooien met behulp van de Azure AD Power shell-module:

1. Meld u in Power shell aan bij Azure AD met behulp van een account voor globale beheerders.
2. Voer de volgende opdracht uit om het eerste domein te converteren:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Selecteer in de Azure AD-Portal **Azure Active Directory**  >  **Azure AD CONNECT**.
4. Controleer of het domein is geconverteerd naar beheerd door de volgende opdracht uit te voeren:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testen en volgende stappen

Voer de volgende taken uit om de wachtwoord-hash-synchronisatie te controleren en om het conversie proces te volt ooien.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Verificatie testen met wachtwoord-hash-synchronisatie 

Wanneer uw Tenant federatieve identiteiten heeft gebruikt, werden gebruikers omgeleid van de aanmeldings pagina van Azure AD naar uw AD FS-omgeving. Nu de Tenant is geconfigureerd voor het gebruik van wachtwoord hash-synchronisatie in plaats van Federated Authentication, worden gebruikers niet omgeleid naar AD FS. Gebruikers melden zich in plaats daarvan rechtstreeks aan op de aanmeldings pagina van Azure AD.

De synchronisatie van wacht woord-hashes testen:

1. Open Internet Explorer in de InPrivate-modus, zodat naadloze SSO niet automatisch wordt aangemeld.
2. Ga naar de Office 365-aanmeldings pagina ( [https://portal.office.com](https://portal.office.com/) ).
3. Voer een UPN van de gebruiker in en selecteer **volgende**. Zorg ervoor dat u de UPN van een hybride gebruiker invoert die is gesynchroniseerd vanaf uw on-premises Active Directory-exemplaar en die eerder Federated Authentication heeft gebruikt. Een pagina waarop u de gebruikers naam en het wacht woord opgeeft, wordt weer gegeven:

   ![Scherm opname van de aanmeldings pagina waarin u een gebruikers naam invoert](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Scherm opname van de aanmeldings pagina waarin u een wacht woord invoert](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Nadat u het wacht woord hebt ingevoerd en **Aanmelden** hebt geselecteerd, wordt u omgeleid naar de Office 365-Portal.

   ![Scherm afbeelding van de Office 365-Portal](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Naadloze SSO testen

1. Meld u aan bij een computer die lid is van een domein en die is verbonden met het bedrijfs netwerk.
2. Ga in Internet Explorer of Chrome naar een van de volgende Url's (Vervang ' Contoso ' door uw domein):

   * https: \/ \/ MyApps.Microsoft.com/contoso.com
   * https: \/ \/ MyApps.Microsoft.com/contoso.onmicrosoft.com

   De gebruiker wordt kort omgeleid naar de aanmeldings pagina van Azure AD, waarin het bericht wordt weer gegeven dat u zich probeert aan te melden. De gebruiker wordt niet gevraagd om een gebruikers naam of wacht woord.<br />

   ![Scherm afbeelding van de aanmeldings pagina en het bericht van Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. De gebruiker wordt omgeleid en is aangemeld bij het toegangs venster:

   > [!NOTE]
   > Naadloze SSO werkt op Microsoft 365 services die ondersteuning bieden voor domein hints (bijvoorbeeld myapps.microsoft.com/contoso.com). De Microsoft 365-Portal (portal.office.com) biedt momenteel geen ondersteuning voor domein hints. Gebruikers moeten een UPN opgeven. Nadat een UPN is ingevoerd, haalt naadloze SSO het Kerberos-ticket namens de gebruiker. De gebruiker is aangemeld zonder een wacht woord in te voeren.

   > [!TIP]
   > Overweeg [Azure AD Hybrid join's te implementeren op Windows 10](../devices/overview.md) voor een verbeterde SSO-ervaring.

### <a name="remove-the-relying-party-trust"></a>De vertrouwens relatie van Relying Party verwijderen

Nadat u hebt gecontroleerd of alle gebruikers en clients zijn geverifieerd via Azure AD, is het veilig om de Microsoft 365 Relying Party-vertrouwens relatie te verwijderen.

Als u AD FS niet gebruikt voor andere doel einden (dat wil zeggen, voor andere Relying Party-vertrouwens relaties), is het veilig om AD FS op dit punt uit te voeren.

### <a name="rollback"></a>Terugdraaiactie

Als u een belang rijk probleem ontdekt en dit niet snel kunt oplossen, kunt u ervoor kiezen om de oplossing terug te zetten naar Federatie.

Raadpleeg de Federatie-ontwerp-en implementatie documentatie voor uw specifieke implementatie details. Het proces moet deze taken omvatten:

* Converteer beheerde domeinen naar Federated Authentication met behulp van de cmdlet **Convert-MSOLDomainToFederated** .
* Configureer indien nodig aanvullende claim regels.

### <a name="sync-userprincipalname-updates"></a>Synchronisatie userPrincipalName-updates

In het verleden worden updates van het kenmerk **userPrincipalName** , dat gebruikmaakt van de synchronisatie service van de on-premises omgeving, geblokkeerd, tenzij aan beide voor waarden wordt voldaan:

* De gebruiker bevindt zich in een beheerd (niet-gefedereerde) identiteits domein.
* Er is geen licentie toegewezen aan de gebruiker.

Zie [Sync userPrincipalName updates](./how-to-connect-syncservice-features.md)(Engelstalig) voor meer informatie over het controleren of inschakelen van deze functie.

### <a name="troubleshooting"></a>Problemen oplossen

Uw ondersteunings team moet weten hoe u problemen met verificatie kunt oplossen die zich voordoen tijdens of na de wijziging van Federatie naar beheerd. Gebruik de volgende documentatie voor probleem oplossing om uw ondersteunings team te helpen vertrouwd te raken met de veelvoorkomende stappen voor probleem oplossing en de juiste acties waarmee u het probleem kunt isoleren en oplossen.

[Problemen met Azure Active Directory wachtwoord-hash-synchronisatie oplossen](./tshoot-connect-password-hash-synchronization.md)

[Problemen met Azure Active Directory naadloze eenmalige aanmelding oplossen](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rolt over de naadloze SSO Kerberos-ontsleutelings sleutel

Het is belang rijk om regel matig over te brengen van de Kerberos-ontsleutelings sleutel van het AZUREADSSOACC-computer account (dat staat voor Azure AD). Het AZUREADSSOACC-computer account wordt gemaakt in uw on-premises Active Directory-forest. We raden u ten zeerste aan de Kerberos-ontsleutelingssleutel ten minste elke 30 dagen uit te voeren om te uitlijnen met de manier waarop Active Directory domein leden wachtwoord wijzigingen indienen. Er is geen gekoppeld apparaat gekoppeld aan het AZUREADSSOACC-computer account object. Daarom moet u de rollover hand matig uitvoeren.

Start de overschakeling van de versleutelings sleutel voor naadloze SSO Kerberos op de on-premises server waarop Azure AD Connect wordt uitgevoerd.

Zie [Hoe kan ik roll over de Kerberos-ontsleutelings sleutel van het computer account van de AZUREADSSOACC?](./how-to-connect-sso-faq.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure AD Connect ontwerp concepten](plan-connect-design-concepts.md).
* Kies de [juiste verificatie](./choose-ad-authn.md).
* Meer informatie over [ondersteunde topologieën](plan-connect-design-concepts.md).
