---
title: Tenant beperkingen gebruiken voor het beheren van de toegang tot SaaS-apps-Azure AD
description: Tenant beperkingen gebruiken om te beheren welke gebruikers toegang hebben tot apps op basis van hun Azure AD-Tenant.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 2/23/2021
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa025f7e21f76b4dde547ccabf675511e9156359
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589324"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Tenant beperkingen gebruiken om de toegang tot SaaS-Cloud toepassingen te beheren

Grote organisaties die de beveiliging benadrukken, willen de overstap naar Cloud Services zoals Microsoft 365, maar moeten weten dat hun gebruikers alleen toegang hebben tot goedgekeurde resources. Traditioneel beperken bedrijven domein namen of IP-adressen wanneer ze de toegang willen beheren. Deze aanpak mislukt in een wereld waar software as a Service (of SaaS)-apps worden gehost in een open bare Cloud, die wordt uitgevoerd op gedeelde domein namen als [Outlook.Office.com](https://outlook.office.com/) en [login.microsoftonline.com](https://login.microsoftonline.com/). Door deze adressen te blok keren, hebben gebruikers geen toegang meer tot Outlook op internet, in plaats van ze alleen te beperken tot goedgekeurde identiteiten en resources.

De oplossing voor de Azure Active Directory (Azure AD) voor deze uitdaging is een functie die Tenant beperkingen heet. Met Tenant beperkingen kunnen organisaties de toegang tot SaaS-Cloud toepassingen beheren op basis van de Azure AD-Tenant die de toepassingen gebruiken voor eenmalige aanmelding. U kunt bijvoorbeeld toegang verlenen tot de Microsoft 365 toepassingen van uw organisatie, terwijl u geen toegang meer hebt tot instanties van deze zelfde toepassingen van andere organisaties.  

Met Tenant beperkingen kunnen organisaties de lijst met tenants opgeven waartoe hun gebruikers toegang mogen hebben. Azure AD verleent vervolgens alleen toegang tot deze toegestane tenants.

Dit artikel richt zich op de Tenant beperkingen voor Microsoft 365, maar de functie beveiligt alle apps die de gebruiker naar Azure AD verzenden voor eenmalige aanmelding. Als u SaaS-apps met een andere Azure AD-Tenant gebruikt vanuit de Tenant die door uw Microsoft 365 wordt gebruikt, moet u ervoor zorgen dat alle vereiste tenants zijn toegestaan (bijvoorbeeld in B2B-samenwerkings scenario's). Zie de [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)voor meer informatie over SaaS-Cloud-apps.

Daarnaast biedt de functie voor Tenant beperkingen nu ondersteuning [voor het blok keren van het gebruik van alle micro soft-consumenten toepassingen](#blocking-consumer-applications-public-preview) (MSA-apps), zoals OneDrive, Hotmail en Xbox.com.  Dit maakt gebruik van een afzonderlijke koptekst voor het `login.live.com` eind punt en wordt aan het einde van het document beschreven.

## <a name="how-it-works"></a>Uitleg

De algemene oplossing bestaat uit de volgende onderdelen:

1. **Azure AD**: als de `Restrict-Access-To-Tenants: <permitted tenant list>` header aanwezig is, verleent Azure AD alleen beveiligings tokens voor de toegestane tenants.

2. **On-premises proxy server-infra structuur**: deze infra structuur is een proxy-apparaat dat kan Transport Layer Security (TLS)-inspectie. U moet de proxy configureren om de header met de lijst met toegestane tenants in te voegen in verkeer dat is bestemd voor Azure AD.

3. **Client software**: ter ondersteuning van Tenant beperkingen, moet client software tokens rechtstreeks aanvragen bij Azure AD, zodat de proxy infrastructuur verkeer kan onderscheppen. Microsoft 365 toepassingen op basis van een browser ondersteunen momenteel Tenant beperkingen, zoals Office-clients die moderne authenticatie gebruiken (zoals OAuth 2,0).

4. **Moderne authenticatie**: Cloud Services moeten moderne verificatie gebruiken om Tenant beperkingen te gebruiken en de toegang tot alle niet-toegestane tenants te blok keren. U moet Microsoft 365 Cloud Services configureren om standaard moderne verificatie protocollen te gebruiken. Voor de meest recente informatie over Microsoft 365 ondersteuning voor moderne verificatie, lees de [bijgewerkte Office 365 moderne-verificatie](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

In het volgende diagram ziet u de verkeers stroom op hoog niveau. Voor Tenant beperkingen is TLS-inspectie alleen vereist voor verkeer naar Azure AD, niet voor de Microsoft 365 Cloud Services. Dit onderscheid is belang rijk, omdat het verkeers volume voor verificatie bij Azure AD doorgaans veel lager is dan het verkeers volume voor SaaS-toepassingen, zoals Exchange Online en share point online.

![Stroom voor het verkeer van Tenant beperkingen-diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Tenant beperkingen instellen

Er zijn twee stappen om aan de slag te gaan met Tenant beperkingen. Zorg er eerst voor dat uw clients verbinding kunnen maken met de juiste adressen. Configureer vervolgens uw proxy-infra structuur.

### <a name="urls-and-ip-addresses"></a>Url's en IP-adressen

Als u Tenant beperkingen wilt gebruiken, moeten uw clients verbinding kunnen maken met de volgende Azure AD-Url's om te verifiëren: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)en [login.Windows.net](https://login.windows.net/). Om toegang te krijgen tot Office 365 moeten uw clients ook verbinding kunnen maken met de FQDN-namen (FULLy Qualified Domain names), Url's en IP-adressen die zijn gedefinieerd in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken. 

### <a name="proxy-configuration-and-requirements"></a>Proxy configuratie en-vereisten

De volgende configuratie is vereist om Tenant beperkingen via uw proxy-infra structuur in te scha kelen. Deze richt lijnen zijn algemeen. Raadpleeg daarom de documentatie van de leverancier van uw proxy voor specifieke implementaties tappen.

#### <a name="prerequisites"></a>Vereisten

- De proxy moet TLS-interceptie, invoeging van HTTP-headers en het filteren van doelen met FQDN-Url's kunnen uitvoeren.

- Clients moeten de certificaat keten vertrouwen die door de proxy voor TLS-communicatie wordt weer gegeven. Als er bijvoorbeeld certificaten van een interne [open bare-sleutel infrastructuur (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) worden gebruikt, moet het interne certificaat van de basis certificerings instantie worden vertrouwd.

- Azure AD Premium 1 licenties zijn vereist voor het gebruik van Tenant beperkingen.

#### <a name="configuration"></a>Configuratie

Plaats voor elke uitgaande aanvraag voor login.microsoftonline.com, login.microsoft.com en login.windows.net twee HTTP-headers: *beperken tot tenants* en *beperkende toegang-context*.

> [!NOTE]
> Neem geen subdomeinen onder `*.login.microsoftonline.com` in uw proxy configuratie. Dit omvat device.login.microsoftonline.com en verstoort de verificatie van client certificaten, die wordt gebruikt bij apparaatregistratie en scenario's voor voorwaardelijke toegang op basis van apparaten. Configureer uw proxy server om device.login.microsoftonline.com uit te sluiten van TLS-uitzonde ring en inspectie en koptekst injectie.

De headers moeten de volgende elementen bevatten:

- Gebruik voor *beperkende toegang tot tenants* een waarde van \<permitted tenant list\> , een door komma's gescheiden lijst met tenants waartoe u gebruikers toegang wilt geven. Elk domein dat is geregistreerd bij een Tenant kan worden gebruikt om de Tenant in deze lijst te identificeren, evenals de Directory-ID zelf. Voor een voor beeld van de drie manieren waarop een Tenant wordt beschreven, is dit de naam/waarde-paar waarmee contoso, Fabrikam en micro soft het volgende kan zien: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Voor *Restrict-Access-context* gebruikt u een waarde van één map-id en declareert u welke Tenant de Tenant beperkingen instellen. Als u bijvoorbeeld Contoso wilt declareren als de Tenant die het beleid voor Tenant beperkingen instelt, ziet het naam/waarde-paar er als volgt uit: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  U **moet** uw eigen directory-id in deze plaats gebruiken om logboeken voor deze verificaties op te halen.

> [!TIP]
> U kunt uw directory-ID vinden in de [Azure Active Directory Portal](https://aad.portal.azure.com/). Meld u aan als beheerder, selecteer **Azure Active Directory** en selecteer vervolgens **Eigenschappen**. 
>
> Als u wilt controleren of een directory-ID of domein naam naar dezelfde Tenant verwijst, gebruikt u die ID of het domein in plaats van <tenant> in deze URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Als de resultaten met het domein en de ID hetzelfde zijn, verwijzen ze naar dezelfde Tenant. 

Als u wilt voor komen dat gebruikers hun eigen HTTP-header invoegen met niet-goedgekeurde tenants, moet de proxy de header voor het *beperken van toegang tot tenants* vervangen als deze al aanwezig is in de binnenkomende aanvraag.

Clients moeten worden gedwongen de proxy te gebruiken voor alle aanvragen van login.microsoftonline.com, login.microsoft.com en login.windows.net. Als er bijvoorbeeld een PAC-bestand wordt gebruikt om clients te leiden om de proxy te gebruiken, moeten eind gebruikers de PAC-bestanden niet kunnen bewerken of uitschakelen.

## <a name="the-user-experience"></a>De gebruikers ervaring

In deze sectie wordt de ervaring voor zowel eind gebruikers als beheerders beschreven.

### <a name="end-user-experience"></a>Ervaring voor de eindgebruiker

Een voor beeld van een gebruiker is het contoso-netwerk, maar probeert toegang te krijgen tot het fabrikam-exemplaar van een gedeelde SaaS-toepassing, zoals Outlook online. Als Fabrikam een niet-toegestane Tenant is voor de contoso-instantie, ziet de gebruiker een bericht over toegang geweigerd. Dit geeft aan dat u probeert toegang te krijgen tot een resource die tot een organisatie behoort die niet is goedgekeurd door uw IT-afdeling.

### <a name="admin-experience"></a>Beheerervaring

Hoewel de configuratie van Tenant beperkingen wordt uitgevoerd op de bedrijfs proxy-infra structuur, hebben beheerders rechtstreeks toegang tot de rapporten met Tenant beperkingen in de Azure Portal. De rapporten weer geven:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). Het dash board **Azure Active Directory-beheer centrum** wordt weer gegeven.

2. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. De pagina overzicht van Azure Active Directory wordt weer gegeven.

3. Selecteer op de pagina overzicht de optie **Tenant beperkingen**.

De beheerder voor de Tenant die is opgegeven als de beperkte-toegangs context Tenant kan dit rapport gebruiken om te zien of aanmeldingen zijn geblokkeerd vanwege het beleid voor Tenant beperkingen, met inbegrip van de identiteit die wordt gebruikt en de doel directory-ID. Aanmeldingen worden opgenomen als de Tenant instelling die de beperking heeft, ofwel de Tenant van de gebruiker of de resource Tenant voor de aanmelding is.

Het rapport kan beperkte informatie bevatten, zoals de doelmap-ID, wanneer een gebruiker die zich in een andere Tenant bevindt dan de niet-toegankelijke context-aanmelding. In dit geval wordt de door de gebruiker geïdentificeerde informatie, zoals naam en user principal name, gemaskeerd om gebruikers gegevens in andere tenants te beveiligen ({PII verwijderd} @domain.com of 00000000-0000-0000-0000-000000000000 in plaats van de gebruikers namen en object-id's waar van toepassing). 

Net als bij andere rapporten in de Azure Portal, kunt u filters gebruiken om het bereik van uw rapport op te geven. U kunt filteren op een bepaald tijds interval, gebruiker, toepassing, client of status. Als u de knop **kolommen** selecteert, kunt u ervoor kiezen om gegevens weer te geven met een combi natie van de volgende velden:

- **Gebruiker** : in dit veld kunnen persoonlijke gegevens worden verwijderd, waar deze worden ingesteld op `00000000-0000-0000-0000-000000000000` . 
- **Toepassing**
- **Status**
- **Datum**
- **Datum (UTC)** : waar UTC Coordinated Universal Time is
- **IP-adres**
- **Client**
- **Gebruikers naam** : in dit veld kunnen persoonlijke gegevens worden verwijderd, waar deze worden ingesteld op `{PII Removed}@domain.com`
- **Locatie**
- **Doel-Tenant-ID**

## <a name="microsoft-365-support"></a>Microsoft 365-ondersteuning

Microsoft 365 toepassingen moeten voldoen aan twee criteria om Tenant beperkingen volledig te ondersteunen:

1. De client die wordt gebruikt voor ondersteuning van moderne verificatie.
2. Moderne authenticatie is ingeschakeld als het standaard verificatie protocol voor de Cloud service.

Raadpleeg [bijgewerkte office 365 moderne-verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) voor de meest recente informatie over welke Office-clients momenteel moderne authenticatie ondersteunen. Deze pagina bevat ook koppelingen naar instructies voor het inschakelen van moderne verificatie op specifieke Exchange Online-en Skype voor bedrijven online-tenants. In share point online is standaard moderne verificatie ingeschakeld.

Microsoft 365 op browser gebaseerde toepassingen (de Office-Portal, Yammer, share point-sites, Outlook op internet en meer) ondersteunen momenteel Tenant beperkingen. Op brede clients (Outlook, Skype voor bedrijven, Word, Excel, Power Point en meer) kunnen alleen Tenant beperkingen worden afgedwongen wanneer moderne verificatie wordt gebruikt.  

Outlook en Skype voor bedrijven-clients die ondersteuning bieden voor moderne verificatie kunnen nog steeds verouderde protocollen gebruiken voor tenants waar moderne authenticatie niet is ingeschakeld, waardoor Tenant beperkingen effectief worden omzeild. Met Tenant beperkingen kunnen toepassingen die gebruikmaken van verouderde protocollen, worden geblokkeerd als ze tijdens de verificatie contact opnemen met login.microsoftonline.com, login.microsoft.com of login.windows.net.

Voor Outlook in Windows kunnen klanten ervoor kiezen om beperkingen te implementeren die voor komen dat eind gebruikers niet-goedgekeurde e-mail accounts toevoegen aan hun profielen. Zie bijvoorbeeld de beleids instelling [voor het toevoegen van niet-standaard groep voor Exchange-accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testen

Als u Tenant beperkingen wilt proberen voordat u deze voor uw hele organisatie implementeert, hebt u twee opties: een op een host gebaseerde benadering van een hulp programma zoals Fiddler of een gefaseerde implementatie van proxy-instellingen.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler voor een op een host gebaseerde aanpak

Fiddler is een gratis proxy voor webfoutopsporing die kan worden gebruikt om HTTP/HTTPS-verkeer vast te leggen en te wijzigen, inclusief het invoegen van HTTP-headers. Voer de volgende stappen uit om Fiddler te configureren om Tenant beperkingen te testen:

1. [Down load en installeer Fiddler](https://www.telerik.com/fiddler).

2. Configureer Fiddler voor het ontsleutelen van HTTPS-verkeer, per [Help-documentatie van Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configureer Fiddler voor het invoegen van de *beperkende toegang tot tenants* en *beperkende toegang-context* headers met aangepaste regels:

   1. In het hulp programma Fiddler Web debugger selecteert u het menu **regels** en selecteert u **regels aanpassen...** om het CustomRules-bestand te openen.

   2. Voeg de volgende regels toe aan het begin van de `OnBeforeRequest` functie. Vervang door \<List of tenant identifiers\> een domein dat is geregistreerd bij uw Tenant (bijvoorbeeld `contoso.onmicrosoft.com` ). Vervang door \<directory ID\> de Azure AD GUID-id van uw Tenant.  U **moet** de juiste GUID-id opnemen zodat de logboeken in uw Tenant worden weer gegeven. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Als u meerdere tenants wilt toestaan, gebruikt u een komma om de namen van de tenants van elkaar te scheiden. Bijvoorbeeld:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Sla het CustomRules-bestand op en sluit het.

Nadat u Fiddler hebt geconfigureerd, kunt u verkeer vastleggen door naar het menu **bestand** te gaan en **verkeer vastleggen** te selecteren.

### <a name="staged-rollout-of-proxy-settings"></a>Gefaseerde implementatie van proxy-instellingen

Afhankelijk van de mogelijkheden van uw proxy-infra structuur kunt u de implementatie van instellingen voor uw gebruikers in de praktijk faseren. Hier volgen een paar opties op hoog niveau voor overweging:

1. Gebruik PAC-bestanden om gebruikers te laten verwijzen naar een test proxy infrastructuur, terwijl normale gebruikers de infra structuur van de productie proxy blijven gebruiken.
2. Sommige proxy servers ondersteunen mogelijk verschillende configuraties met behulp van groepen.

Raadpleeg de documentatie van uw proxy server voor specifieke informatie.

## <a name="blocking-consumer-applications-public-preview"></a>Consumenten toepassingen blok keren (open bare preview)

Toepassingen van micro soft die ondersteuning bieden voor zowel consumenten accounts als organisatie accounts, zoals [OneDrive](https://onedrive.live.com/) of [Microsoft Learn](/learn/), kunnen soms worden gehost op dezelfde URL.  Dit betekent dat gebruikers die toegang moeten hebben tot deze URL voor werk, ook toegang hebben tot het persoonlijk gebruik. Dit kan niet worden toegestaan volgens de richt lijnen van uw bedrijf.

Sommige organisaties proberen dit op te lossen door te blok keren `login.live.com` om te voor komen dat persoonlijke accounts worden geverifieerd.  Dit heeft een aantal neerwaartse nadeel:

1. Blokkerend `login.live.com` blokkeert het gebruik van persoonlijke accounts in B2B-gast scenario's, die kunnen intrude voor bezoekers en samen werking.
1. [Voor auto pilot is het gebruik `login.live.com` van vereist](/mem/autopilot/networking-requirements) om te implementeren. InTune-en auto pilot-scenario's kunnen mislukken wanneer `login.live.com` geblokkeerd.
1. Organisatie-telemetrie en Windows-updates die afhankelijk zijn van de login.live.com-service voor apparaat-Id's [, zullen niet langer werken](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>Configuratie voor consumenten-apps

Terwijl de `Restrict-Access-To-Tenants` header fungeert als een acceptatie lijst, werkt het Microsoft-account (MSA)-blok als een signaal voor weigeren, waarmee wordt aangegeven dat de Microsoft-account platform niet toestaat dat gebruikers zich kunnen aanmelden bij consumenten toepassingen. Als u dit signaal wilt verzenden, `sec-Restrict-Tenant-Access-Policy` wordt de header geïnjecteerd naar verkeer dat wordt bezocht `login.live.com` met dezelfde bedrijfs proxy of firewall als [hierboven](#proxy-configuration-and-requirements). De waarde van de header moet zijn `restrict-msa` . Wanneer de header aanwezig is en een consumenten-app zich rechtstreeks probeert aan te melden bij een gebruiker, wordt dat aanmelden geblokkeerd.

Op dit moment wordt verificatie voor consumenten toepassingen niet weer gegeven in de [beheer logboeken](#admin-experience), omdat login.live.com onafhankelijk van Azure AD wordt gehost.

### <a name="what-the-header-does-and-does-not-block"></a>Wat de header doet en niet blokkeert

Het `restrict-msa` beleid blokkeert het gebruik van consumenten toepassingen, maar maakt via verschillende andere typen verkeer en verificatie mogelijk:

1. Gebruiker-minder verkeer voor apparaten.  Dit omvat het verkeer voor auto pilot, Windows Update en telemetrie van de organisatie.
1. B2B-verificatie van consumenten accounts. Gebruikers met micro soft-accounts die worden [uitgenodigd om samen te werken met een Tenant](../external-identities/redemption-experience.md#invitation-redemption-flow) verificatie voor login.live.com om toegang te krijgen tot een resource Tenant.
    1. Deze toegang wordt beheerd met behulp van de- `Restrict-Access-To-Tenants` header om toegang tot de bron Tenant toe te staan of te weigeren.
1. ' Passthrough ' authenticatie, gebruikt door veel Azure-apps en Office.com, waarbij apps Azure AD gebruiken voor het aanmelden van consumenten gebruikers in een consument context.
    1. Deze toegang wordt ook beheerd met behulp van de- `Restrict-Access-To-Tenants` header om toegang tot de speciale Tenant (' passthrough ') toe te staan of te weigeren `f8cdef31-a31e-4b4a-93e4-5f571e91255a` .  Als deze Tenant niet wordt weer gegeven in de `Restrict-Access-To-Tenants` lijst met toegestane domeinen, worden consumenten accounts geblokkeerd door Azure AD, zodat ze zich niet kunnen aanmelden bij deze apps.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bijgewerkte Office 365 modern-verificatie](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- De [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken controleren