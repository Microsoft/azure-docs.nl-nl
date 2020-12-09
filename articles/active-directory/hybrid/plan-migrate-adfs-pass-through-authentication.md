---
title: 'Azure AD Connect: migreren van Federatie naar PTA voor Azure AD'
description: Dit artikel bevat informatie over het verplaatsen van uw hybride identiteits omgeving van Federatie naar Pass-Through-verificatie.
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
ms.openlocfilehash: a10c4c0e6e40636e4803e054155d6fdaa12a9366
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858549"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migreren van Federatie naar Pass-Through-verificatie voor Azure Active Directory

In dit artikel wordt beschreven hoe u uw organisatie domeinen kunt verplaatsen van Active Directory Federation Services (AD FS) naar Pass-Through-verificatie.

> [!NOTE]
> Het wijzigen van de verificatie methode vereist planning, testen en mogelijk downtime. [Gefaseerde implementatie](how-to-connect-staged-rollout.md) biedt een alternatieve manier om te testen en geleidelijk te migreren van Federatie naar Cloud verificatie met behulp van Pass-Through-verificatie.
> 
> Als u van plan bent gefaseerde implementatie te gebruiken, moet u de functies voor gefaseerde implementatie uitschakelen zodra u klaar bent met knippen.  Zie voor meer informatie [migreren naar Cloud verificatie met behulp van gefaseerde implementatie](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Vereisten voor het migreren naar Pass-Through-verificatie

De volgende vereisten zijn vereist voor de migratie van met behulp van AD FS om Pass-Through-verificatie te gebruiken.

### <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

Voor het volt ooien van de stappen die nodig zijn om te migreren naar met behulp van Pass-Through-verificatie, moet u [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 of een latere versie hebben. In Azure AD Connect 1.1.819.0 worden de manier waarop de aanmeldings conversie wordt uitgevoerd, aanzienlijk gewijzigd. De totale tijd voor het migreren van AD FS naar Cloud authenticatie in deze versie is beperkt van potentieel uur tot minuten.

> [!IMPORTANT]
> U kunt de verouderde documentatie, hulpprogram ma's en blogs lezen die gebruikers conversie vereist wanneer u domeinen van federatieve identiteit naar beheerde identiteit converteert. Het is niet meer nodig om *gebruikers te converteren* . Micro soft werkt met het bijwerken van documentatie en hulpprogram ma's om deze wijziging weer te geven.

Volg de stappen in Azure AD Connect om Azure AD Connect bij te werken: Voer een [upgrade uit naar de nieuwste versie](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Het nummer en de plaatsing van de verificatie agent plannen

Pass-Through-verificatie vereist het implementeren van Lightweight-agents op de Azure AD Connect-server en op uw on-premises computer waarop Windows Server wordt uitgevoerd. Als u de latentie wilt beperken, installeert u de agents zo dicht mogelijk bij uw Active Directory-domein controllers.

Voor de meeste klanten zijn twee of drie verificatie agenten voldoende om hoge Beschik baarheid en de vereiste capaciteit te bieden. Voor een Tenant kunnen Maxi maal 12 geregistreerde agents worden geregistreerd. De eerste agent wordt altijd geïnstalleerd op de Azure AD Connect-server zelf. Zie [Azure AD Pass-Through-verificatie: huidige beperkingen](./how-to-connect-pta-current-limitations.md)voor meer informatie over agent beperkingen en opties voor het implementeren van agents.

### <a name="plan-the-migration-method"></a>De migratie methode plannen

U kunt kiezen uit twee methoden voor het migreren van federatieve identiteits beheer naar Pass-Through-verificatie en naadloze eenmalige aanmelding (SSO). De methode die u gebruikt, is afhankelijk van de manier waarop uw AD FS-exemplaar oorspronkelijk is geconfigureerd.

* **Azure AD Connect**. Als u AD FS oorspronkelijk hebt geconfigureerd met behulp van Azure AD Connect, *moet* u de Azure AD Connect wizard gebruiken om door te gaan met Pass-Through-verificatie.

   Azure AD Connect wordt automatisch de cmdlet **set-MsolDomainAuthentication** uitgevoerd wanneer u de aanmeldings methode voor gebruikers wijzigt. Azure AD Connect automatisch alle geverifieerde federatieve domeinen in uw Azure AD-Tenant unfederates.

   > [!NOTE]
   > Als u op dit moment Azure AD Connect hebt gebruikt om AD FS te configureren, kunt u niet voor komen dat alle domeinen in uw Tenant unfederating wanneer u de aanmelding van de gebruiker wijzigt in Pass-Through-verificatie.
‎
* **Azure AD Connect met Power shell**. U kunt deze methode alleen gebruiken als u AD FS niet oorspronkelijk hebt geconfigureerd met behulp van Azure AD Connect. Voor deze optie moet u de aanmeldings methode voor gebruikers nog steeds wijzigen via de wizard Azure AD Connect. Het belangrijkste verschil met deze optie is dat de wizard de cmdlet **set-MsolDomainAuthentication** niet automatisch uitvoert. Met deze optie hebt u volledige controle over welke domeinen worden geconverteerd en in welke volg orde.

Volg de stappen in de volgende secties om te begrijpen welke methode u moet gebruiken.

#### <a name="verify-current-user-sign-in-settings"></a>Instellingen voor de aanmelding van de huidige gebruiker controleren

1. Meld u aan bij de [Azure AD-Portal](https://aad.portal.azure.com/) met een account voor globale beheerders.
2. Controleer de volgende instellingen in de sectie **gebruikers aanmelding** :
   * **Federatie** is ingesteld op **ingeschakeld**.
   * **Naadloze eenmalige aanmelding** is ingesteld op **uitgeschakeld**.
   * **Pass Through-verificatie** is ingesteld op **uitgeschakeld**.

   ![Scherm opname van de instellingen in de sectie Azure AD Connect gebruiker aanmelden](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Controleren hoe Federatie is geconfigureerd

1. Open Azure AD Connect op uw Azure AD Connect-server. Selecteer **Configureren**.
2. Selecteer op de pagina **extra taken** de optie **huidige configuratie weer geven** en selecteer vervolgens **volgende**.<br />
 
   ![Scherm afbeelding van de optie huidige configuratie weer geven op de pagina extra taken](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Ga onder **aanvullende taken > Federatie beheren** naar **Active Directory Federation Services (AD FS)**.<br />

   * Als de AD FS configuratie in deze sectie wordt weer gegeven, kunt u veilig aannemen dat AD FS oorspronkelijk is geconfigureerd met behulp van Azure AD Connect. U kunt uw domeinen van federatieve identiteiten naar een beheerde identiteit converteren met behulp van de aanmeldings optie Azure AD Connect **gebruiker wijzigen** . Zie de sectie **optie A: Pass-Through-verificatie configureren met behulp van Azure AD Connect** voor meer informatie over het proces.
   * Als AD FS niet in de huidige instellingen wordt vermeld, moet u uw domeinen hand matig met behulp van Power shell converteren van federatieve identiteit naar beheerde identiteit. Zie de sectie **optie B: overschakelen van Federatie naar Pass-Through-verificatie met behulp van Azure AD Connect en Power shell** voor meer informatie over dit proces.

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
> Als **SupportsMfa** is ingesteld op **True**, gebruikt u een on-premises multi-factor Authentication-oplossing om een tweede factor Challenge in te voeren in de verificatie stroom van de gebruiker. Deze installatie werkt niet meer voor Azure AD-verificatie scenario's. 
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

AD FS wordt de **InsideCorporateNetwork** -claim uitgegeven als de gebruiker die de verificatie uitvoert, zich in het bedrijfs netwerk bevindt. Deze claim kan vervolgens worden door gegeven aan Azure AD. De claim wordt gebruikt om multi-factor Authentication over te slaan op basis van de netwerk locatie van de gebruiker. Zie voor meer informatie over hoe u kunt bepalen of deze functionaliteit momenteel beschikbaar is in AD FS [vertrouwde IP-adressen voor federatieve gebruikers](../authentication/howto-mfa-adfs.md).

De **InsideCorporateNetwork** -claim is niet beschikbaar nadat uw domeinen zijn geconverteerd naar Pass-Through-verificatie. U kunt de [benoemde locaties in azure AD](../reports-monitoring/quickstart-configure-named-locations.md) gebruiken om deze functionaliteit te vervangen.

Nadat u benoemde locaties hebt geconfigureerd, moet u alle beleids regels voor voorwaardelijke toegang bijwerken die zijn geconfigureerd voor het opnemen of uitsluiten van **alle vertrouwde locaties** of door **MFA vertrouwde IP-adressen** om de nieuwe benoemde locaties weer te geven.

Zie [Active Directory locaties voor voorwaardelijke toegang](../conditional-access/location-condition.md)voor meer informatie over de **locatie** voorwaarde in voorwaardelijke toegang.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybride apparaten die deel uitmaken van Azure AD

Wanneer u een apparaat toevoegt aan Azure AD, kunt u regels voor voorwaardelijke toegang maken waarmee wordt afgedwongen dat apparaten voldoen aan uw toegangs normen voor beveiliging en naleving. Gebruikers kunnen zich ook bij een apparaat aanmelden met een werk-of school account van een organisatie in plaats van een persoonlijk account. Wanneer u hybride apparaten die deel uitmaken van Azure AD gebruikt, kunt u uw Active Directory apparaten die lid zijn van het domein toevoegen aan Azure AD. Uw federatieve omgeving is mogelijk ingesteld om deze functie te gebruiken.

Om ervoor te zorgen dat hybride samen werking blijft werken voor apparaten die lid zijn van het domein nadat uw domeinen zijn geconverteerd naar Pass-Through-verificatie, voor Windows 10-clients, moet u Azure AD Connect gebruiken om Active Directory computer accounts te synchroniseren met Azure AD.

Voor Windows 8-en Windows 7-computer accounts gebruikt hybride deelname naadloze SSO om de computer te registreren in azure AD. U hoeft geen Windows 8-en Windows 7-computer accounts te synchroniseren zoals u dat voor Windows 10-apparaten kunt doen. U moet echter een bijgewerkt workplacejoin.exe-bestand (via een. msi-bestand) implementeren op Windows 8-en Windows 7-clients zodat ze zich kunnen registreren met naadloze SSO. [Down load het MSI-bestand](https://www.microsoft.com/download/details.aspx?id=53554).

Zie [Configure Hybrid Azure AD-joined devices](../devices/hybrid-azuread-join-plan.md)(Engelstalig) voor meer informatie.

#### <a name="branding"></a>Branding

Als uw organisatie [uw AD FS aanmeld pagina's heeft aangepast](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) om informatie weer te geven die relevant is voor de organisatie, kunt u overwegen vergelijk bare [aanpassingen aan te brengen op de aanmeldings pagina van Azure AD](../fundamentals/customize-branding.md).

Hoewel soort gelijke aanpassingen beschikbaar zijn, moeten sommige visuele wijzigingen op aanmeldings pagina's na de conversie worden verwacht. Mogelijk wilt u informatie geven over verwachte wijzigingen in uw communicatie met gebruikers.

> [!NOTE]
> De huis stijl van de organisatie is alleen beschikbaar als u de Premium-of basis licentie voor Azure Active Directory aanschaft of als u een Microsoft 365 licentie hebt.

## <a name="plan-for-smart-lockout"></a>Slimme vergren deling plannen

Azure AD slim vergren delen beschermt tegen aanvallen met een felle wacht woord. Slim vergren delen voor komt dat een on-premises Active Directory account wordt vergrendeld wanneer Pass-Through-verificatie wordt gebruikt en een account vergrendelings groeps beleid is ingesteld in Active Directory.

Zie [Azure Active Directory slim vergren delen](../authentication/howto-password-smart-lockout.md)voor meer informatie.

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

Nadat de Pass-Through-verificatie en naadloze SSO zijn geïmplementeerd, wordt de gebruikers aanmelding voor toegang tot Microsoft 365 en andere bronnen die worden geverifieerd via Azure AD-wijzigingen. Gebruikers die zich buiten het netwerk bevinden, zien alleen de aanmeldings pagina van Azure AD. Deze gebruikers worden niet omgeleid naar de op formulieren gebaseerde pagina die wordt gepresenteerd door externe Web Application proxy-servers.

Neem de volgende elementen op in uw communicatie strategie:

* Gebruikers op de hoogte stellen van aanstaande en uitgebrachte functionaliteit met behulp van:
   * E-mail en andere interne communicatie kanalen.
   * Visuele elementen, zoals posters.
   * Executive, Live of andere communicatie.
* Bepaal wie de communicatie zal aanpassen en wie de communicatie gaat verzenden en wanneer.

## <a name="implement-your-solution"></a>Uw oplossing implementeren

U hebt uw oplossing gepland. Nu kunt u het implementeren. Implementatie omvat de volgende onderdelen:

* De voor bereiding op naadloze SSO.
* Het wijzigen van de aanmeldings methode voor Pass-Through-verificatie en het inschakelen van naadloze SSO.

### <a name="step-1-prepare-for-seamless-sso"></a>Stap 1: bereid u voor op naadloze SSO

Als u wilt dat uw apparaten naadloze SSO gebruiken, moet u een Azure AD-URL toevoegen aan de intranet zone-instellingen van gebruikers met behulp van een groeps beleid in Active Directory.

Webbrowsers berekenen standaard automatisch de juiste zone, Internet of intranet, van een URL. Bijvoorbeeld **http: \/ \/ Contoso/** wijst toe aan de intranet zone en **http: \/ \/ intranet.contoso.com** wordt toegewezen aan de zone Internet (omdat de URL een punt bevat). Browsers verzenden van Kerberos-tickets naar een Cloud eindpunt, zoals de Azure AD-URL, alleen als u de URL expliciet toevoegt aan de intranet zone van de browser.

Voer de stappen uit om de vereiste wijzigingen op uw apparaten [uit](./how-to-connect-sso-quick-start.md) te voeren.

> [!IMPORTANT]
> Als u deze wijziging aanbrengt, wordt de manier waarop uw gebruikers zich aanmelden bij Azure AD, niet gewijzigd. Het is echter belang rijk dat u deze configuratie toepast op al uw apparaten voordat u verdergaat. Gebruikers die zich aanmelden op apparaten die deze configuratie nog niet hebben ontvangen, moeten een gebruikers naam en wacht woord invoeren om zich aan te melden bij Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Stap 2: Wijzig de aanmeldings methode in Pass-Through-verificatie en schakel naadloze SSO in

U hebt twee opties voor het wijzigen van de aanmeldings methode voor Pass-Through-verificatie en het inschakelen van naadloze SSO.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Optie A: Pass-Through-verificatie configureren met behulp van Azure AD Connect

Gebruik deze methode als u uw AD FS-omgeving voor het eerst hebt geconfigureerd met behulp van Azure AD Connect. U kunt deze methode niet gebruiken als u uw AD FS-omgeving *niet eerder hebt* geconfigureerd met behulp van Azure AD Connect.

> [!IMPORTANT]
> Nadat u de volgende stappen hebt uitgevoerd, worden alle domeinen van de federatieve identiteit geconverteerd naar een beheerde identiteit. Raadpleeg [de migratie methode plannen](#plan-the-migration-method)voor meer informatie.

Wijzig eerst de aanmeldings methode:

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **gebruiker aanmelden wijzigen** en selecteer **volgende**. 
3. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van een account voor globale beheerders.
4. Selecteer op de **aanmeldings** pagina voor gebruikers de knop **Pass-Through-verificatie** , selecteer **eenmalige aanmelding inschakelen** en selecteer **volgende**.
5. Op de pagina **eenmalige aanmelding inschakelen** voert u de referenties in van een domein beheerders account en selecteert u **volgende**.

   > [!NOTE]
   > Referenties voor het domein beheerders account zijn vereist om naadloze SSO in te scha kelen. Het proces is voltooid met de volgende acties, waarvoor deze verhoogde machtigingen zijn vereist. De referenties van het domein beheerders account worden niet opgeslagen in Azure AD Connect of in azure AD. De referenties van het domein beheerders account worden alleen gebruikt om de functie in te scha kelen. De referenties worden verwijderd wanneer het proces is voltooid.
   >
   > 1. Een computer account met de naam AZUREADSSOACC (waarmee Azure AD wordt aangeduid) wordt gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-ontsleutelings sleutel van het computer account wordt veilig gedeeld met Azure AD.
   > 3. Er zijn twee Kerberos Service Principal Names (Spn's) gemaakt om twee Url's voor te stellen die worden gebruikt tijdens de aanmelding van Azure AD.

6. Controleer op de pagina **gereed voor configuratie** of het selectie vakje **synchronisatie proces starten wanneer de configuratie is voltooid** is ingeschakeld. Selecteer vervolgens **configureren**.<br />

   ![Scherm afbeelding van de pagina gereed voor configuratie](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Selecteer in de Azure AD-Portal **Azure Active Directory** en selecteer vervolgens **Azure AD Connect**.
8. Controleer de volgende instellingen:
   * **Federatie** is ingesteld op **uitgeschakeld**.
   * **Naadloze eenmalige aanmelding** is **ingeschakeld**.
   * **Pass Through-verificatie** is ingesteld op **ingeschakeld**.<br />

   ![Scherm afbeelding met de instellingen in de sectie gebruikers aanmelding](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Volgende. aanvullende verificatie methoden implementeren:

1. Ga in het Azure Portal naar **Azure Active Directory**  >  **Azure AD CONNECT** en selecteer vervolgens **Pass-Through-verificatie**.
2. Selecteer op de pagina **Pass-Through-verificatie** de knop **downloaden** .
3. Selecteer op de pagina **agent downloaden** de optie **voor waarden accepteren en downloaden**.

   Extra verificatie agenten worden nu gedownload. Installeer de secundaire verificatie agent op een server die lid is van een domein. 

   > [!NOTE]
   > De eerste agent wordt altijd geïnstalleerd op de Azure AD Connect server zelf als onderdeel van de configuratie wijzigingen die zijn aangebracht in de **gebruikers aanmelding** van het hulp programma Azure AD Connect. Installeer extra verificatie agenten op een afzonderlijke server. U wordt aangeraden twee of drie extra verificatie agenten beschikbaar te stellen. 

4. De verificatie agent-installatie uitvoeren. Tijdens de installatie moet u de referenties van een algemeen Administrator-account invoeren.

   ![Scherm opname van de knop die u gebruikt voor het uitvoeren van het Microsoft Azure AD-pakket voor verbindings verificatie.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Scherm opname van de micro soft-aanmeldings pagina.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Wanneer de verificatie agent is geïnstalleerd, kunt u teruggaan naar de status pagina Pass-Through-verificatie agent om de status van de extra agents te controleren.

Ga verder met [testen en volgende stappen](#testing-and-next-steps).

> [!IMPORTANT]
> Overs Laan **van de sectie optie B: overschakelen van Federatie naar Pass-Through-verificatie met behulp van Azure AD Connect en Power shell**. De stappen in deze sectie zijn niet van toepassing als u optie A hebt gekozen om de aanmeldings methode te wijzigen in Pass-Through-verificatie en naadloze SSO in te scha kelen. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Optie B: overschakelen van Federatie naar Pass-Through-verificatie met behulp van Azure AD Connect en Power shell

Gebruik deze optie als u uw federatieve domeinen niet eerst hebt geconfigureerd met behulp van Azure AD Connect.

Schakel eerst Pass-Through-verificatie in:

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **gebruiker aanmelden wijzigen** en selecteer **volgende**.
3. Voer op de pagina **verbinding maken met Azure AD** de gebruikers naam en het wacht woord in van een account voor globale beheerders.
4. Selecteer op de aanmeldings pagina van de **gebruiker** de knop **Pass-Through-verificatie** . Selecteer **eenmalige aanmelding inschakelen** en selecteer **volgende**.
5. Op de pagina **eenmalige aanmelding inschakelen** voert u de referenties in van een domein beheerders account en selecteert u **volgende**.

   > [!NOTE]
   > Referenties voor het domein beheerders account zijn vereist om naadloze SSO in te scha kelen. Het proces is voltooid met de volgende acties, waarvoor deze verhoogde machtigingen zijn vereist. De referenties van het domein beheerders account worden niet opgeslagen in Azure AD Connect of in azure AD. De referenties van het domein beheerders account worden alleen gebruikt om de functie in te scha kelen. De referenties worden verwijderd wanneer het proces is voltooid.
   > 
   > 1. Een computer account met de naam AZUREADSSOACC (waarmee Azure AD wordt aangeduid) wordt gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-ontsleutelings sleutel van het computer account wordt veilig gedeeld met Azure AD.
   > 3. Er zijn twee Kerberos Service Principal Names (Spn's) gemaakt om twee Url's voor te stellen die worden gebruikt tijdens de aanmelding van Azure AD.

6. Controleer op de pagina **gereed voor configuratie** of het selectie vakje **synchronisatie proces starten wanneer de configuratie is voltooid** is ingeschakeld. Selecteer vervolgens **configureren**.<br />

   ![Scherm opname van de pagina gereed voor configuratie en de knop configureren](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   De volgende stappen worden uitgevoerd wanneer u **configureren** selecteert:

   1. De eerste pass-through-verificatie agent is geïnstalleerd.
   2. De Pass-Through-functie is ingeschakeld.
   3. Naadloze eenmalige aanmelding is ingeschakeld.

7. Controleer de volgende instellingen:
   * **Federatie** is ingesteld op **ingeschakeld**.
   * **Naadloze eenmalige aanmelding** is **ingeschakeld**.
   * **Pass Through-verificatie** is ingesteld op **ingeschakeld**.
   
   ![Scherm afbeelding met de instellingen die moeten worden gecontroleerd in het gedeelte aanmelden bij de gebruiker.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Selecteer **Pass-Through-verificatie** en controleer of de status **actief** is.<br />
   
   Als de verificatie agent niet actief is, voltooit u de [stappen voor probleem oplossing](./tshoot-connect-pass-through-authentication.md) voordat u verdergaat met het domein conversie proces in de volgende stap. U kunt een verificatie storing veroorzaken als u uw domeinen converteert voordat u valideert dat uw Pass Through-verificatie agenten zijn geïnstalleerd en dat de status ervan **actief** is in de Azure Portal.

Implementeer vervolgens extra verificatie agenten:

1. Ga in het Azure Portal naar **Azure Active Directory**  >  **Azure AD CONNECT** en selecteer vervolgens **Pass-Through-verificatie**.
2. Selecteer op de pagina **Pass-Through-verificatie** de knop **downloaden** . 
3. Selecteer op de pagina **agent downloaden** de optie **voor waarden accepteren en downloaden**.
 
   De verificatie agent wordt gedownload. Installeer de secundaire verificatie agent op een server die lid is van een domein.

   > [!NOTE]
   > De eerste agent wordt altijd geïnstalleerd op de Azure AD Connect server zelf als onderdeel van de configuratie wijzigingen die zijn aangebracht in de **gebruikers aanmelding** van het hulp programma Azure AD Connect. Installeer extra verificatie agenten op een afzonderlijke server. U wordt aangeraden twee of drie extra verificatie agenten beschikbaar te stellen.
 
4. De verificatie agent-installatie uitvoeren. Tijdens de installatie moet u de referenties van een algemeen Administrator-account invoeren.<br />

   ![Scherm opname van de knop installeren op de pagina Microsoft Azure AD-verbindings agent-pakket](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Scherm opname van de aanmeldings pagina](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Nadat de verificatie agent is geïnstalleerd, kunt u teruggaan naar de status pagina Pass-Through-verificatie agent om de status van de extra agents te controleren.

Op dit moment is Federated Authentication nog steeds actief en operationeel voor uw domeinen. Als u wilt door gaan met de implementatie, moet u elk domein van federatieve identiteit converteren naar een beheerde identiteit, zodat Pass-Through-verificatie begint met het uitvoeren van verificatie aanvragen voor het domein.

U hoeft niet alle domeinen tegelijk te converteren. U kunt ervoor kiezen om te beginnen met een test domein in uw productie Tenant of te beginnen met het domein met het laagste aantal gebruikers.

De conversie volt ooien met behulp van de Azure AD Power shell-module:

1. Meld u in Power shell aan bij Azure AD met behulp van een account voor globale beheerders.
2. Voer de volgende opdracht uit om het eerste domein te converteren:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Selecteer in de Azure AD-Portal **Azure Active Directory**  >  **Azure AD CONNECT**.
4. Nadat u alle federatieve domeinen hebt geconverteerd, controleert u deze instellingen:
   * **Federatie** is ingesteld op **uitgeschakeld**.
   * **Naadloze eenmalige aanmelding** is **ingeschakeld**.
   * **Pass Through-verificatie** is ingesteld op **ingeschakeld**.<br />

   ![Scherm afbeelding met de instellingen in de sectie gebruikers aanmelding in de Azure AD-Portal.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testen en volgende stappen

Voer de volgende taken uit om Pass-Through-verificatie te verifiëren en om het conversie proces te volt ooien.

### <a name="test-pass-through-authentication"></a>Pass-Through-verificatie testen 

Wanneer uw Tenant federatieve identiteiten heeft gebruikt, werden gebruikers omgeleid van de aanmeldings pagina van Azure AD naar uw AD FS-omgeving. Nu de Tenant is geconfigureerd voor het gebruik van Pass-Through-verificatie in plaats van Federated Authentication, worden gebruikers niet omgeleid naar AD FS. Gebruikers melden zich in plaats daarvan rechtstreeks aan op de aanmeldings pagina van Azure AD.

Pass-Through-verificatie testen:

1. Open Internet Explorer in de InPrivate-modus, zodat naadloze SSO niet automatisch wordt aangemeld.
2. Ga naar de Office 365-aanmeldings pagina ( [https://portal.office.com](https://portal.office.com/) ).
3. Voer een UPN van de gebruiker in en selecteer **volgende**. Zorg ervoor dat u de UPN van een hybride gebruiker invoert die is gesynchroniseerd vanaf uw on-premises Active Directory-exemplaar en die eerder Federated Authentication heeft gebruikt. Een pagina waarop u de gebruikers naam en het wacht woord opgeeft, wordt weer gegeven:

   ![Scherm opname van de aanmeldings pagina waarin u een gebruikers naam invoert](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Scherm opname van de aanmeldings pagina waarin u een wacht woord invoert](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Nadat u het wacht woord hebt ingevoerd en **Aanmelden** hebt geselecteerd, wordt u omgeleid naar de Office 365-Portal.

   ![Scherm afbeelding van de Office 365-Portal](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Naadloze SSO testen

Naadloze SSO testen:

1. Meld u aan bij een computer die lid is van een domein en die is verbonden met het bedrijfs netwerk.
2. Ga in Internet Explorer of Chrome naar een van de volgende Url's (Vervang ' Contoso ' door uw domein):

   * https: \/ \/ MyApps.Microsoft.com/contoso.com
   * https: \/ \/ MyApps.Microsoft.com/contoso.onmicrosoft.com

   De gebruiker wordt kort omgeleid naar de aanmeldings pagina van Azure AD, waarin het bericht wordt weer gegeven dat u zich probeert aan te melden. De gebruiker wordt niet gevraagd om een gebruikers naam of wacht woord.<br />

   ![Scherm afbeelding van de aanmeldings pagina en het bericht van Azure AD](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
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

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rolt over de naadloze SSO Kerberos-ontsleutelings sleutel

Het is belang rijk om regel matig over te brengen van de Kerberos-ontsleutelings sleutel van het AZUREADSSOACC-computer account (dat staat voor Azure AD). Het AZUREADSSOACC-computer account wordt gemaakt in uw on-premises Active Directory-forest. We raden u ten zeerste aan de Kerberos-ontsleutelingssleutel ten minste elke 30 dagen uit te voeren om te uitlijnen met de manier waarop Active Directory domein leden wachtwoord wijzigingen indienen. Er is geen gekoppeld apparaat gekoppeld aan het AZUREADSSOACC-computer account object. Daarom moet u de rollover hand matig uitvoeren.

Start de overschakeling van de versleutelings sleutel voor naadloze SSO Kerberos op de on-premises server waarop Azure AD Connect wordt uitgevoerd.

Zie [Hoe kan ik roll over de Kerberos-ontsleutelings sleutel van het computer account van de AZUREADSSOACC?](./how-to-connect-sso-faq.md)voor meer informatie.

## <a name="monitoring-and-logging"></a>Bewaking en registratie

Bewaak de servers waarop de verificatie agenten worden uitgevoerd om de beschik baarheid van de oplossing te behouden. Naast de algemene server prestatie meter items, bieden de verificatie agenten prestatie objecten die u kunnen helpen bij het begrijpen van verificatie statistieken en-fouten.

Verificatie agenten logboek bewerkingen naar de Windows-gebeurtenis logboeken die zich bevinden onder Application and service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

U kunt logboek registratie ook inschakelen voor het oplossen van problemen.

Zie [problemen oplossen Azure Active Directory Pass-Through-verificatie](./tshoot-connect-pass-through-authentication.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure AD Connect ontwerp concepten](plan-connect-design-concepts.md).
* Kies de [juiste verificatie](./choose-ad-authn.md).
* Meer informatie over [ondersteunde topologieën](plan-connect-design-concepts.md).
