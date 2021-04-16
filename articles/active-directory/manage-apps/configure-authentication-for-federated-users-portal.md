---
title: Automatische versnelling van aanmelding configureren met behulp van Thuis realm-detectie
description: Meer informatie over het configureren van thuisdomeindetectiebeleid voor Azure Active Directory verificatie voor federatief gebruikers, inclusief automatische versnelling en domeinhints.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af80979a4712f6d25d994835128f9d5d2205f42
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534730"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Configureer Azure Active Directory aanmeldingsgedrag voor een toepassing met behulp van een detectiebeleid voor thuis realms

Dit artikel bevat een inleiding tot het configureren Azure Active Directory verificatiegedrag voor federatief gebruikers met behulp van hrd-beleid (Home Realm Discovery).  Het gaat over het gebruik van automatische versnelling om het invoerscherm voor gebruikersnamen over te slaan en automatisch gebruikers door testuren naar federatief aanmeldings-eindpunten.  Microsoft raadt aan automatische versnelling niet langer te configureren, omdat hiermee het gebruik van sterkere verificatiemethoden, zoals FIDO, kan worden voorkomen en samenwerking wordt bemoeilijkt.

## <a name="home-realm-discovery"></a>Home Realm Discovery

Home Realm Discovery (HRD) is het proces waarmee Azure Active Directory (Azure AD) kan bepalen bij welke id-provider ('IdP') een gebruiker zich tijdens het aanmelden moet verifiëren.  Wanneer een gebruiker zich bij een Azure AD-tenant voor toegang tot een resource of op de algemene aanmeldingspagina van Azure AD meldt, typt hij een gebruikersnaam (UPN). Azure AD gebruikt deze om te ontdekken waar de gebruiker zich moet aanmelden.

De gebruiker wordt naar een van de volgende id-providers geauthenticeerd:

- De start-tenant van de gebruiker (kan dezelfde tenant zijn als de resource die de gebruiker probeert te openen).

- Microsoft-account.  De gebruiker is een gast in de resource-tenant die een consumentenaccount gebruikt voor verificatie.

- Een on-premises id-provider zoals Active Directory Federation Services (AD FS).

- Een andere id-provider die is ge federeerd met de Azure AD-tenant.

## <a name="auto-acceleration"></a>Automatische versnelling

Sommige organisaties configureren domeinen in hun Azure Active Directory tenant om te federeren met een andere IdP, zoals AD FS voor gebruikersverificatie.  

Wanneer een gebruiker zich bij een toepassing meldt, krijgt deze eerst een Azure AD-aanmeldingspagina te zien. Nadat ze hun UPN hebben getypt, worden ze, als ze zich in een federatief domein, naar de aanmeldingspagina van de IdP voor dat domein gaan. Onder bepaalde omstandigheden willen beheerders gebruikers mogelijk om leiden naar de aanmeldingspagina wanneer ze zich aanmelden bij specifieke toepassingen.

Als gevolg hiervan kunnen gebruikers de eerste pagina Azure Active Directory overslaan. Dit proces wordt aangeduid als 'automatische versnelling voor aanmelden'.

In gevallen waarin de tenant is ge federeerd naar een andere IdP voor aanmelding, maakt automatische versnelling het aanmelden van gebruikers gestroomlijnder.  U kunt automatische versnelling configureren voor afzonderlijke toepassingen.

>[!NOTE]
>Als u een toepassing configureert voor automatische versnelling, kunnen gebruikers geen beheerde referenties (zoals FIDO) gebruiken en kunnen gastgebruikers zich niet aanmelden. Als u een gebruiker rechtstreeks naar een federatief IdP voor verificatie neemt, is er geen manier om terug te gaan naar de Azure Active Directory aanmeldingspagina. Gastgebruikers, die mogelijk moeten worden omgeleid naar andere tenants of een externe IdP, zoals een Microsoft-account, kunnen zich niet aanmelden bij die toepassing omdat ze de stap Detectie van thuis realm overslaan.  

Er zijn drie manieren om automatische versnelling naar een federatief IdP te bepalen:

- Gebruik een [domeinhint](#domain-hints) voor verificatieaanvragen voor een toepassing.
- Configureer een beleid voor thuis realmdetectie om [automatische versnelling af te dwingen.](#home-realm-discovery-policy-for-auto-acceleration)
- Configureer een thuisdomeindetectiebeleid om [domeinhints van](prevent-domain-hints-with-home-realm-discovery.md) specifieke toepassingen of voor bepaalde domeinen te negeren.

### <a name="domain-hints"></a>Domeinhints

Domeinhints zijn instructies die zijn opgenomen in de verificatieaanvraag van een toepassing. Ze kunnen worden gebruikt om de gebruiker versneld naar hun federatieve IdP-aanmeldingspagina te sturen. Ze kunnen ook door een toepassing met meerdere tenants worden gebruikt om de gebruiker rechtstreeks en versneld naar de gemerkte Azure AD-aanmeldingspagina voor hun tenant te sturen.  

Met de toepassing 'largeapp.com' kunnen klanten bijvoorbeeld toegang krijgen tot de toepassing via een aangepaste URL 'contoso.largeapp.com'. De app kan ook een domeinhint bevatten om contoso.com in de verificatieaanvraag.

De syntaxis van domeinhints is afhankelijk van het protocol dat wordt gebruikt en wordt doorgaans geconfigureerd in de toepassing.

**WS-Federation:** whr=contoso.com in de queryreeks.

**SAML:** een SAML-verificatieaanvraag die een domeinhint of een queryreeks whr=contoso.com bevat.

**Open ID Connect:** een queryreeks domain_hint=contoso.com.

Standaard probeert Azure AD om aanmelding om te leiden naar de IdP  die is geconfigureerd voor een domein als aan beide van de volgende twee wordt gekomen:

- Er is een domeinhint opgenomen in de verificatieaanvraag van de toepassing **en**
- De tenant is federatief met dat domein.

Als de domeinhint niet verwijst naar een geverifieerd federatief domein, wordt dit genegeerd.

Zie de blog voor meer informatie over Enterprise Mobility + Security automatische versnelling met behulp van de domeinhints die door Azure Active Directory worden [ondersteund.](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)

>[!NOTE]
>Als een domeinhint is opgenomen in een verificatieaanvraag en moet worden [gerespecteerd,](#home-realm-discovery-policy-to-prevent-auto-acceleration)overschrijven de aanwezigheid ervan automatische versnelling die is ingesteld voor de toepassing in het HRD-beleid.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Thuis realmdetectiebeleid voor automatische versnelling

Sommige toepassingen bieden geen manier om de verificatieaanvraag te configureren die ze indienen. In dergelijke gevallen is het niet mogelijk om domeinhints te gebruiken om automatische versnelling te beheren. Automatische versnelling kan worden geconfigureerd via het detectiebeleid voor thuis realms om hetzelfde gedrag te bereiken.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Beleid voor thuis realmdetectie om automatische versnelling te voorkomen

Sommige Microsoft- en SaaS-toepassingen bevatten automatisch domain_hints (bijvoorbeeld resulteert in een aanmeldingsaanvraag waaraan is toegevoegd), waardoor de implementatie van beheerde referenties, zoals `https://outlook.com/contoso.com` `&domain_hint=contoso.com` FIDO, kan worden onderbroken.  U kunt [het detectiebeleid voor thuisdomeinen](/graph/api/resources/homeRealmDiscoveryPolicy) gebruiken om domeinhints van bepaalde apps of voor bepaalde domeinen te negeren tijdens de implementatie van beheerde referenties.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Directe ROPC-verificatie van federatief gebruikers inschakelen voor verouderde toepassingen

De best practice is dat toepassingen AAD-bibliotheken en interactieve aanmelding gebruiken om gebruikers te verifiëren. De bibliotheken zorgen voor de federatief gebruikersstromen.  Soms worden verouderde toepassingen, met name toepassingen die ROPC-toekenningen gebruiken, de gebruikersnaam en het wachtwoord rechtstreeks naar Azure AD verzenden en worden ze niet geschreven om federatie te begrijpen. Ze voeren geen thuis realmdetectie uit en werken niet met het juiste federatief eindpunt om een gebruiker te verifiëren. Als u wilt, kunt u HRD-beleid gebruiken om specifieke verouderde toepassingen in te stellen die referenties voor gebruikersnaam en wachtwoord indienen met behulp van de ROPC-toekenning om rechtstreeks te verifiëren met Azure Active Directory. Wachtwoord-hashsynchronisatie moet zijn ingeschakeld.

> [!IMPORTANT]
> Schakel directe verificatie alleen in als Wachtwoord-hashsynchronisatie is ingeschakeld en u weet dat het goed is om deze toepassing te verifiëren zonder beleid dat is geïmplementeerd door uw on-premises IdP. Als u Wachtwoord-hashsynchronisatie uit schakelen of directorysynchronisatie met AD Connect om een of andere reden uitschakelen, moet u dit beleid verwijderen om te voorkomen dat directe verificatie met behulp van een verouderde wachtwoordhash.

## <a name="set-hrd-policy"></a>HRD-beleid instellen

Er zijn drie stappen voor het instellen van HRD-beleid voor een toepassing voor federatief automatisch versnellen van aanmelding of directe cloudtoepassingen:

1. Een HRD-beleid maken.

2. Zoek de service-principal waaraan het beleid moet worden gekoppeld.

3. Koppel het beleid aan de service-principal.

Beleidsregels worden alleen van kracht voor een specifieke toepassing wanneer ze zijn gekoppeld aan een service-principal.

Er kan slechts één HRD-beleid tegelijk actief zijn op een service-principal.  

U kunt de power Azure Active Directory PowerShell-cmdlets gebruiken om HRD-beleid te maken en te beheren.

Hieronder volgt een voorbeeld van een HRD-beleidsdefinitie:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

Het beleidstype is[HomeRealmDiscoveryPolicy.](/graph/api/resources/homeRealmDiscoveryPolicy)

**AccelerateToFederatedDomain** is optioneel. Als **AccelerateToFederatedDomain** onwaar is, heeft het beleid geen effect op automatische versnelling. Als **AccelerateToFederatedDomain** true is en er slechts één geverifieerd en federatief domein in de tenant is, worden gebruikers rechtstreeks naar de federatief IdP om zich aan te melden. Als dit waar is en er meer dan één geverifieerd domein in de tenant is, moet **PreferredDomain** worden opgegeven.

**PreferredDomain** is optioneel. **PreferredDomain moet** een domein aangeven waarop moet worden versneld. Dit kan worden weggelaten als de tenant slechts één federatief domein heeft.  Als dit wordt weggelaten en er meer dan één geverifieerd federatief domein is, heeft het beleid geen effect.

 Als **PreferredDomain** is opgegeven, moet dit overeenkomen met een geverifieerd, federatief domein voor de tenant. Alle gebruikers van de toepassing moeten zich kunnen aanmelden bij dat domein. Gebruikers die zich niet kunnen aanmelden bij het federatief domein, worden ingesloten en kunnen zich niet volledig aanmelden.

**AllowCloudPasswordValidation** is optioneel. Als **AllowCloudPasswordValidation** waar is, kan de toepassing een federatieve gebruiker verifiëren door referenties voor gebruikersnaam en wachtwoord rechtstreeks aan het eindpunt van het Azure Active Directory-token te presenteren. Dit werkt alleen als Wachtwoord-hashsynchronisatie is ingeschakeld.

Daarnaast bestaan er twee HRD-opties op tenantniveau, die niet hierboven worden weergegeven:

- **AlternateIdLogin** is optioneel.  Als deze optie is ingeschakeld, kunnen gebruikers zich op de aanmeldingspagina van Azure AD aanmelden met hun e-mailadressen in plaats van hun [UPN.](../authentication/howto-authentication-use-email-signin.md)  Alternatieve ID's zijn afhankelijk van het niet automatisch versnellen van de gebruiker naar een federatief IDP.

- **DomainHintPolicy** is een optioneel complex object dat voorkomt dat [  domeinhints](prevent-domain-hints-with-home-realm-discovery.md)gebruikers automatisch versnellen naar federatief domeinen. Deze tenantinstelling wordt gebruikt om ervoor te zorgen dat toepassingen die domeinhints verzenden niet voorkomen dat gebruikers zich aanmelden met referenties die in de cloud worden beheerd.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioriteit en evaluatie van HRD-beleid

HRD-beleid kan worden gemaakt en vervolgens worden toegewezen aan specifieke organisaties en service-principals. Dit betekent dat het mogelijk is om meerdere beleidsregels toe te passen op een specifieke toepassing, zodat Azure AD moet bepalen welke beleidsregels prioriteit hebben. Een set regels bepaalt welk HRD-beleid (van vele toegepast) van kracht wordt:

- Als er een domeinhint aanwezig is in de verificatieaanvraag, wordt het HRD-beleid voor de tenant (het beleid ingesteld als de standaard tenant) gecontroleerd om te zien of domeinhints moeten [worden genegeerd.](prevent-domain-hints-with-home-realm-discovery.md) Als domeinhints zijn toegestaan, wordt het gedrag gebruikt dat is opgegeven door de domeinhint.

- Als een beleid expliciet wordt toegewezen aan de service-principal, wordt dit afgedwongen.

- Als er geen domeinhint is en er geen beleid expliciet wordt toegewezen aan de service-principal, wordt een beleid afgedwongen dat expliciet is toegewezen aan de bovenliggende organisatie van de service-principal.

- Als er geen domeinhint is en er geen beleid is toegewezen aan de service-principal of de organisatie, wordt het standaard HRD-gedrag gebruikt.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Zelfstudie voor het instellen van HRD-beleid voor een toepassing

We gebruiken Azure AD PowerShell-cmdlets om een aantal scenario's te bekijken, waaronder:

- Instellen van HRD-beleid voor automatische versnelling voor een toepassing in een tenant met één federatief domein.

- Instellen van HRD-beleid voor automatische versnelling voor een toepassing naar een van de verschillende domeinen die zijn geverifieerd voor uw tenant.

- HET instellen van HRD-beleid om een verouderde toepassing in staat te stellen directe verificatie van gebruikersnaam/wachtwoord uit te voeren Azure Active Directory voor een federatief gebruiker.

- Hiermee worden de toepassingen belijst waarvoor een beleid is geconfigureerd.

### <a name="prerequisites"></a>Vereisten

In de volgende voorbeelden maakt, updatet, koppelt en verwijdert u beleidsregels voor toepassingsservice-principals in Azure AD.

1. Download om te beginnen de nieuwste preview-versie van de Azure AD PowerShell-cmdlet.

2. Nadat u de Azure AD PowerShell-cmdlets hebt gedownload, kunt u de opdracht Connect uitvoeren om u aan te melden bij Azure AD met uw beheerdersaccount:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Voer de volgende opdracht uit om alle beleidsregels in uw organisatie te bekijken:

    ``` powershell
    Get-AzureADPolicy
    ```

Als er niets wordt geretourneerd, betekent dit dat u geen beleid hebt gemaakt in uw tenant.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Voorbeeld: een HRD-beleid instellen voor een toepassing

In dit voorbeeld maakt u een beleid dat wordt toegewezen aan een toepassing:

- Gebruikers worden automatisch versneld naar een AD FS aanmeldingsscherm wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw tenant is.
- Auto-accelerates users to an AD FS sign-in screen there is more than one federated domain in your tenant.
- Hiermee kunt u niet-interactieve gebruikersnaam/wachtwoord rechtstreeks aanmelden bij Azure Active Directory voor federatief gebruikers voor de toepassingen waar het beleid aan is toegewezen.

#### <a name="step-1-create-an-hrd-policy"></a>Stap 1: een HRD-beleid maken

Het volgende beleid versnelt gebruikers automatisch naar een AD FS-aanmeldingsscherm wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw tenant is.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Het volgende beleid versnelt gebruikers automatisch naar een AD FS aanmeldingsscherm dat er meer dan één federatief domein in uw tenant is. Als u meer dan één federatief domein hebt dat gebruikers verifieert voor toepassingen, moet u het domein opgeven dat automatisch moet worden versneld.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Voer de volgende opdracht uit om een beleid te maken om verificatie van gebruikersnaam en wachtwoord rechtstreeks met Azure Active Directory voor specifieke toepassingen in te schakelen:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Voer de volgende opdracht uit om uw nieuwe beleid weer te geven en de **ObjectID** op te halen:

``` powershell
Get-AzureADPolicy
```

Als u het HRD-beleid wilt toepassen nadat u het hebt gemaakt, kunt u het toewijzen aan meerdere toepassingsservice-principals.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Stap 2: de service-principal zoeken waaraan het beleid moet worden toegewezen

U hebt de **ObjectID** nodig van de service-principals waaraan u het beleid wilt toewijzen. Er zijn verschillende manieren om de **ObjectID van** service-principals te vinden.

U kunt de portal gebruiken of een query uitvoeren [op Microsoft Graph.](/graph/api/resources/serviceprincipal) U kunt ook naar het [Graph Explorer-hulpprogramma](https://developer.microsoft.com/graph/graph-explorer) gaan en u aanmelden bij uw Azure AD-account om alle service-principals van uw organisatie te bekijken.

Omdat u PowerShell gebruikt, kunt u de volgende cmdlet gebruiken om de service-principals en hun ID's weer te geven.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Stap 3: het beleid toewijzen aan uw service-principal

Nadat u de **ObjectID hebt** van de service-principal van de toepassing waarvoor u automatische versnelling wilt configureren, moet u de volgende opdracht uitvoeren. Met deze opdracht koppelt u het HRD-beleid dat u in stap 1 hebt gemaakt aan de service-principal die u in stap 2 hebt gevonden.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

U kunt deze opdracht herhalen voor elke service-principal waaraan u het beleid wilt toevoegen.

Als aan een toepassing al een HomeRealmDiscovery-beleid is toegewezen, kunt u geen tweede toevoegen.  In dat geval wijzigt u de definitie van het thuis realmdetectiebeleid dat is toegewezen aan de toepassing om extra parameters toe te voegen.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Stap 4: controleer aan welke toepassingsservice-principals uw HRD-beleid is toegewezen

Als u wilt controleren op welke toepassingen HRD-beleid is geconfigureerd, gebruikt u de cmdlet **Get-AzureADPolicyAppliedObject.** Geef de **ObjectID door** van het beleid dat u wilt controleren.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Stap 5: u bent klaar.

Probeer de toepassing om te controleren of het nieuwe beleid werkt.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Voorbeeld: Een lijst maken met de toepassingen waarvoor HRD-beleid is geconfigureerd

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Stap 1: alle beleidsregels die in uw organisatie zijn gemaakt, opsnissen

``` powershell
Get-AzureADPolicy
```

Noteer **de ObjectID** van het beleid dat u wilt gebruiken om toewijzingen voor weer te geven.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 2: een lijst maken van de service-principals waaraan het beleid is toegewezen  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Voorbeeld: Een HRD-beleid uit een toepassing verwijderen

#### <a name="step-1-get-the-objectid"></a>Stap 1: de ObjectID op te halen

Gebruik het vorige voorbeeld om de **ObjectID** van het beleid op te halen en die van de toepassingsservice-principal waarvan u het wilt verwijderen.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Stap 2: de beleidstoewijzing verwijderen uit de toepassingsservice-principal  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 3: controleer het verwijderen door de service-principals te bekijken waaraan het beleid is toegewezen

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Volgende stappen

- Zie Verificatiescenario's voor Azure AD voor meer informatie over hoe verificatie werkt in [Azure AD.](../develop/authentication-vs-authorization.md)
- Zie Single [sign-on to applications in](what-is-single-sign-on.md)Azure Active Directory voor meer informatie over een Azure Active Directory.
- Ga naar [het Microsoft Identity Platform](../develop/v2-overview.md) voor een overzicht van alle aan ontwikkelaars gerelateerde inhoud.