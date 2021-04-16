---
title: Tenantbeperkingen gebruiken om toegang tot SaaS-apps te beheren - Azure AD
description: Tenantbeperkingen gebruiken om te beheren welke gebruikers toegang hebben tot apps op basis van hun Azure AD-tenant.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376576"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Tenantbeperkingen gebruiken om toegang tot SaaS-cloudtoepassingen te beheren

Grote organisaties die de nadruk leggen op beveiliging, willen over op cloudservices zoals Microsoft 365, maar moeten weten dat hun gebruikers alleen toegang hebben tot goedgekeurde resources. Van oudsher beperken bedrijven domeinnamen of IP-adressen wanneer ze de toegang willen beheren. Deze aanpak mislukt in een wereld waarin Software as a Service-apps (of SaaS)-apps worden gehost in een openbare cloud en worden uitgevoerd op gedeelde domeinnamen zoals [outlook.office.com](https://outlook.office.com/) en [login.microsoftonline.com](https://login.microsoftonline.com/). Als u deze adressen blokkeert, hebben gebruikers geen volledige toegang tot Outlook op het web, in plaats van ze te beperken tot goedgekeurde identiteiten en resources.

De Azure Active Directory (Azure AD) voor deze uitdaging is een functie die tenantbeperkingen wordt genoemd. Met tenantbeperkingen kunnen organisaties de toegang tot SaaS-cloudtoepassingen controleren op basis van de Azure AD-tenant die de toepassingen gebruiken voor een aanmelding. U kunt bijvoorbeeld toegang tot de Microsoft 365-toepassingen van uw organisatie toestaan, terwijl de toegang tot de exemplaren van dezelfde toepassingen door andere organisaties wordt voorkomen.  

Met tenantbeperkingen kunnen organisaties de lijst opgeven met tenants die hun gebruikers mogen openen. Azure AD verleent vervolgens alleen toegang tot deze toegestane tenants.

Dit artikel richt zich op tenantbeperkingen voor Microsoft 365, maar de functie beschermt alle apps die de gebruiker naar Azure AD sturen voor een een aanmelding. Als u SaaS-apps gebruikt met een andere Azure AD-tenant dan de tenant die wordt gebruikt door uw Microsoft 365, moet u ervoor zorgen dat alle vereiste tenants zijn toegestaan (bijvoorbeeld in B2B-samenwerkingsscenario's). Zie de Active Directory Marketplace voor meer informatie over [SaaS-cloud-apps.](https://azuremarketplace.microsoft.com/marketplace/apps)

Daarnaast ondersteunt de functie tenantbeperkingen [](#blocking-consumer-applications-public-preview) nu het blokkeren van het gebruik van alle Microsoft-consumententoepassingen (MSA-apps), zoals OneDrive, Hotmail en Xbox.com.  Hierbij wordt een afzonderlijke header naar `login.live.com` het eindpunt gebruikt en deze wordt aan het einde van het document beschreven.

## <a name="how-it-works"></a>Uitleg

De algehele oplossing bestaat uit de volgende onderdelen:

1. **Azure AD:** als de `Restrict-Access-To-Tenants: <permitted tenant list>` header aanwezig is, geeft Azure AD alleen beveiligingstokens uit voor de toegestane tenants.

2. **On-premises proxyserverinfrastructuur:** deze infrastructuur is een proxyapparaat dat kan Transport Layer Security (TLS)-inspectie. U moet de proxy configureren om de header met de lijst met toegestane tenants in te voegen in verkeer dat is bestemd voor Azure AD.

3. **Clientsoftware:** ter ondersteuning van tenantbeperkingen moet clientsoftware tokens rechtstreeks vanuit Azure AD aanvragen, zodat de proxyinfrastructuur verkeer kan onderscheppen. Browsergebaseerde Microsoft 365 ondersteunen momenteel tenantbeperkingen, net als Office-clients die moderne verificatie gebruiken (zoals OAuth 2.0).

4. **Moderne verificatie:** cloudservices moeten moderne verificatie gebruiken om tenantbeperkingen te gebruiken en de toegang tot alle niet-toegestane tenants te blokkeren. U moet Microsoft 365 cloudservices configureren om standaard moderne verificatieprotocollen te gebruiken. Lees Bijgewerkte moderne Office 365 Microsoft 365 verificatie voor de meest recente informatie over [de ondersteuning voor moderne verificatie.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

Het volgende diagram illustreert de verkeersstroom op hoog niveau. Tenantbeperkingen vereisen TLS-inspectie alleen voor verkeer naar Azure AD, niet voor de Microsoft 365 cloudservices. Dit onderscheid is belangrijk, omdat het verkeersvolume voor verificatie bij Azure AD meestal veel lager is dan het verkeersvolume naar SaaS-toepassingen zoals Exchange Online en SharePoint Online.

![Verkeersstroom met tenantbeperkingen - diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Tenantbeperkingen instellen

Er zijn twee stappen om aan de slag te gaan met tenantbeperkingen. Zorg er eerst voor dat uw clients verbinding kunnen maken met de juiste adressen. Configureer ten tweede uw proxy-infrastructuur.

### <a name="urls-and-ip-addresses"></a>URL's en IP-adressen

Als u tenantbeperkingen wilt gebruiken, moeten uw clients verbinding kunnen maken met de volgende Azure AD-URL's om te verifiëren: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)en [login.windows.net.](https://login.windows.net/) Daarnaast moeten uw clients voor toegang tot Office 365 ook verbinding kunnen maken met de FQDN's (Fully Qualified Domain Names), URL's en IP-adressen die zijn gedefinieerd in Office 365-URL's en [IP-adresbereiken.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Proxyconfiguratie en -vereisten

De volgende configuratie is vereist voor het inschakelen van tenantbeperkingen via uw proxy-infrastructuur. Deze richtlijnen zijn algemeen, dus raadpleeg de documentatie van uw proxyleverancier voor specifieke implementatiestappen.

#### <a name="prerequisites"></a>Vereisten

- De proxy moet TLS-onderschepping, het invoegen van HTTP-headers en filterbestemmingen kunnen uitvoeren met FQDN's/URL's.

- Clients moeten de certificaatketen vertrouwen die door de proxy wordt aangeboden voor TLS-communicatie. Als bijvoorbeeld certificaten van een interne [PKI (Public Key Infrastructure)](/windows/desktop/seccertenroll/public-key-infrastructure) worden gebruikt, moet het certificaat van de interne verlenende basiscertificaatinstantie worden vertrouwd.

- Azure AD Premium 1-licenties zijn vereist voor het gebruik van tenantbeperkingen.

#### <a name="configuration"></a>Configuratie

Voeg voor elke uitgaande aanvraag voor login.microsoftonline.com, login.microsoft.com en login.windows.net twee HTTP-headers in: *Restrict-Access-To-Tenants* en *Restrict-Access-Context.*

> [!NOTE]
> Neem geen subdomeinen onder op `*.login.microsoftonline.com` in uw proxyconfiguratie. Als u dit doet, device.login.microsoftonline.com en heeft dit invloed op de verificatie van clientcertificaten, die wordt gebruikt in scenario's voor apparaatregistratie en voorwaardelijke toegang op basis van apparaten. Configureer uw proxyserver om device.login.microsoftonline.com uit te sluiten van TLS-break-and-inspect en headerinjectie.

De headers moeten de volgende elementen bevatten:

- Gebruik *voor Restrict-Access-To-Tenants* de waarde . Dit is een door komma's gescheiden lijst met tenants die u gebruikers toegang \<permitted tenant list\> wilt geven. Elk domein dat is geregistreerd bij een tenant kan worden gebruikt om de tenant in deze lijst te identificeren, evenals de map-id zelf. Voor een voorbeeld van alle drie de manieren om een tenant te beschrijven, ziet het naam/waarde-paar om Contoso, Fabrikam en Microsoft toe te staan er als volgende uit: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- Gebruik *voor Restrict-Access-Context* een waarde van één directory-id, waarbij wordt aangegeven welke tenant de tenantbeperkingen instelt. Als u bijvoorbeeld Contoso wilt declareer als de tenant die het beleid voor tenantbeperkingen in stelt, ziet het naam/waarde-paar er als voorbeeld uit: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  U **moet** op deze locatie uw eigen map-id gebruiken om logboeken voor deze verificaties op te halen.

> [!TIP]
> U vindt uw directory-id in Azure Active Directory [portal.](https://aad.portal.azure.com/) Meld u aan als beheerder, selecteer **Azure Active Directory** en selecteer vervolgens **Eigenschappen.** 
>
> Als u wilt valideren dat een directory-id of domeinnaam naar dezelfde tenant verwijst, gebruikt u die id of het domein in plaats van <tenant> in deze URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Als de resultaten met het domein en de id hetzelfde zijn, verwijzen ze naar dezelfde tenant. 

Om te voorkomen dat gebruikers hun eigen HTTP-header invoegen met niet-goedgekeurde tenants, moet de proxy de header *Restrict-Access-To-Tenants* vervangen als deze al aanwezig is in de binnenkomende aanvraag.

Clients moeten worden gedwongen om de proxy te gebruiken voor alle aanvragen voor login.microsoftonline.com, login.microsoft.com en login.windows.net. Als PAC-bestanden bijvoorbeeld worden gebruikt om clients om te leiden om de proxy te gebruiken, mogen eindgebruikers de PAC-bestanden niet kunnen bewerken of uitschakelen.

## <a name="the-user-experience"></a>De gebruikerservaring

In deze sectie wordt de ervaring voor zowel eindgebruikers als beheerders beschreven.

### <a name="end-user-experience"></a>Ervaring voor de eindgebruiker

Een voorbeeldgebruiker maakt deel uit van het Contoso-netwerk, maar probeert toegang te krijgen tot het Fabrikam-exemplaar van een gedeelde SaaS-toepassing, zoals Outlook Online. Als Fabrikam een niet-toegestane tenant voor het Contoso-exemplaar is, ziet de gebruiker een bericht over toegangsweeding. Dit bericht geeft aan dat u toegang probeert te krijgen tot een resource die behoort tot een organisatie die niet is goedgekeurd door uw IT-afdeling.

![Foutbericht over tenantbeperkingen, vanaf april 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Beheerervaring

Hoewel de configuratie van tenantbeperkingen wordt uitgevoerd op de proxyinfrastructuur van het bedrijf, hebben beheerders rechtstreeks toegang tot de rapporten met tenantbeperkingen in Azure Portal organisatie. De rapporten weergeven:

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com/). Het **dashboard Azure Active Directory beheercentrum** wordt weergegeven.

2. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster. De Azure Active Directory wordt weergegeven.

3. Selecteer op de pagina Overzicht de **optie Tenantbeperkingen.**

De beheerder van de tenant die is opgegeven als de tenant met beperkte toegangscontext kan dit rapport gebruiken om te zien of aanmeldingen zijn geblokkeerd vanwege het beleid voor tenantbeperkingen, met inbegrip van de identiteit die wordt gebruikt en de map-id van het doel. Aanmeldingen worden opgenomen als de tenantinstelling de beperking de gebruikersten tenant of resourceten tenant voor de aanmelding is.

Het rapport kan beperkte informatie bevatten, zoals de doeldirectory-id, wanneer een gebruiker die zich in een andere tenant dan de tenant Restricted-Access-Context heeft, zich meldt. In dit geval worden gebruikersgegevens, zoals naam en user principal name, gemaskeerd om gebruikersgegevens in andere tenants te beveiligen ({PII removed} of @domain.com 000000000-0000-0000-0000-000000000000000 in plaats van gebruikersnamen en object-ID's, indien van toepassing). 

Net als andere rapporten in Azure Portal kunt u filters gebruiken om het bereik van uw rapport op te geven. U kunt filteren op een specifiek tijdsinterval, gebruiker, toepassing, client of status. Als u de knop **Kolommen** selecteert, kunt u ervoor kiezen om gegevens weer te geven met een combinatie van de volgende velden:

- **Gebruiker:** in dit veld kunnen persoonsgegevens worden verwijderd, waarbij deze worden ingesteld op `00000000-0000-0000-0000-000000000000` . 
- **Toepassing**
- **Status**
- **Datum**
- **Datum (UTC)** - waar UTC is Coordinated Universal Time
- **IP-adres**
- **Client**
- **Gebruikersnaam:** in dit veld kunnen persoonsgegevens worden verwijderd, waar deze worden ingesteld op `{PII Removed}@domain.com`
- **Locatie**
- **Doel-tenant-id**

## <a name="microsoft-365-support"></a>Microsoft 365-ondersteuning

Microsoft 365 moeten voldoen aan twee criteria om tenantbeperkingen volledig te ondersteunen:

1. De gebruikte client ondersteunt moderne verificatie.
2. Moderne verificatie is ingeschakeld als het standaardverificatieprotocol voor de cloudservice.

Raadpleeg [Bijgewerkte moderne Office 365-verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) voor de meest recente informatie over welke Office-clients momenteel moderne verificatie ondersteunen. Deze pagina bevat ook koppelingen naar instructies voor het inschakelen van moderne verificatie op specifieke Exchange Online- en Skype voor Bedrijven Online-tenants. SharePoint Online schakelt standaard al moderne verificatie in.

Microsoft 365 browsertoepassingen (Office Portal, Yammer, SharePoint-sites, Outlook op het web en meer) ondersteunen momenteel tenantbeperkingen. Thick clients (Outlook, Skype voor Bedrijven, Word, Excel, PowerPoint en meer) kunnen tenantbeperkingen alleen afdwingen wanneer moderne verificatie wordt gebruikt.  

Outlook- en Skype voor Bedrijven-clients die moderne verificatie ondersteunen, kunnen mogelijk nog steeds verouderde protocollen gebruiken voor tenants waarbij moderne verificatie niet is ingeschakeld, waardoor tenantbeperkingen effectief worden overgeslagen. Tenantbeperkingen kunnen toepassingen blokkeren die gebruikmaken van verouderde protocollen als ze contact opnemen met login.microsoftonline.com, login.microsoft.com of login.windows.net tijdens de verificatie.

Voor Outlook in Windows kunnen klanten beperkingen implementeren die verhinderen dat eindgebruikers niet-goedgekeurde e-mailaccounts toevoegen aan hun profielen. Zie bijvoorbeeld de groepsbeleidsinstelling [Voorkomen dat niet-standaard Exchange-accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) worden toegevoegd.

## <a name="testing"></a>Testen

Als u tenantbeperkingen wilt uitproberen voordat u deze implementeert voor uw hele organisatie, hebt u twee opties: een op een host gebaseerde benadering met behulp van een hulpprogramma zoals Fiddler of een gefaseerd implementeren van proxy-instellingen.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler voor een op een host gebaseerde benadering

Fiddler is een gratis webdebuggingsproxy die kan worden gebruikt om HTTP-/HTTPS-verkeer vast te leggen en te wijzigen, inclusief het invoegen van HTTP-headers. Voer de volgende stappen uit om Fiddler te configureren voor het testen van tenantbeperkingen:

1. [Download en installeer Fiddler](https://www.telerik.com/fiddler).

2. Configureer Fiddler voor het ontsleutelen van HTTPS-verkeer volgens de [Help-documentatie van Fiddler.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Configureer Fiddler om de *headers Restrict-Access-To-Tenants* en *Restrict-Access-Context* in te voegen met behulp van aangepaste regels:

   1. Selecteer in het hulpprogramma Fiddler Web Debugger het menu **Regels** en selecteer **Regels aanpassen...** om het bestand CustomRules te openen.

   2. Voeg de volgende regels toe aan het begin van de `OnBeforeRequest` functie. Vervang \<List of tenant identifiers\> door een domein dat is geregistreerd bij uw tenant (bijvoorbeeld `contoso.onmicrosoft.com` ). Vervang \<directory ID\> door de Azure AD GUID-id van uw tenant.  U **moet** de juiste GUID-id opnemen om ervoor te kunnen dat de logboeken in uw tenant worden weergegeven. 

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

   Als u meerdere tenants wilt toestaan, gebruikt u een komma om de namen van de tenants te scheiden. Bijvoorbeeld:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Sla het bestand CustomRules op en sluit het.

Nadat u Fiddler hebt geconfigureerd, kunt u verkeer vastleggen door naar het menu **Bestand** te gaan en **Verkeer vastleggen te selecteren.**

### <a name="staged-rollout-of-proxy-settings"></a>Gefaseerd implementatie van proxy-instellingen

Afhankelijk van de mogelijkheden van uw proxy-infrastructuur, kunt u mogelijk de implementatie van instellingen voor uw gebruikers faseeren. Hier zijn enkele algemene opties om rekening mee te houden:

1. Gebruik PAC-bestanden om testgebruikers naar een testproxy-infrastructuur te laten wijzen, terwijl normale gebruikers de productieproxy-infrastructuur blijven gebruiken.
2. Sommige proxyservers ondersteunen mogelijk verschillende configuraties met behulp van groepen.

Raadpleeg de documentatie van uw proxyserver voor specifieke informatie.

## <a name="blocking-consumer-applications-public-preview"></a>Consumententoepassingen blokkeren (openbare preview)

Toepassingen van Microsoft die ondersteuning bieden voor zowel consumentenaccounts als organisatieaccounts, zoals [OneDrive](https://onedrive.live.com/) of [Microsoft Learn,](/learn/)kunnen soms op dezelfde URL worden gehost.  Dit betekent dat gebruikers die voor werkdoeleinden toegang moeten hebben tot deze URL ook toegang hebben voor persoonlijk gebruik, wat mogelijk niet is toegestaan volgens uw bedrijfsrichtlijnen.

Sommige organisaties proberen dit op te lossen door te blokkeren om te voorkomen dat `login.live.com` persoonlijke accounts worden ge authenticeerd.  Dit heeft verschillende nadelen:

1. Blokkeren `login.live.com` blokkeert het gebruik van persoonlijke accounts in B2B-gastscenario's, wat van toepassing kan zijn op bezoekers en samenwerking.
1. [Voor Autopilot is `login.live.com` het gebruik van vereist](/mem/autopilot/networking-requirements) om te implementeren. Intune- en Autopilot-scenario's kunnen mislukken wanneer `login.live.com` is geblokkeerd.
1. Organisatie-telemetrie en Windows-updates die afhankelijk zijn van de login.live.com-service voor apparaat-ID's, [werken niet meer.](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)

### <a name="configuration-for-consumer-apps"></a>Configuratie voor consumenten-apps

Hoewel de header als een allowlist fungeert, werkt het Microsoft-account-blok (MSA) als een weigersignaal, waardoor het Microsoft-account-platform wordt verteld dat gebruikers zich niet mogen aanmelden bij `Restrict-Access-To-Tenants` consumententoepassingen. Om dit signaal te verzenden, wordt de header geïnjecteerd in verkeer dat `sec-Restrict-Tenant-Access-Policy` dezelfde bedrijfsproxy of firewall gebruikt `login.live.com` als [hierboven](#proxy-configuration-and-requirements). De waarde van de header moet `restrict-msa` zijn. Wanneer de header aanwezig is en een consumenten-app probeert een gebruiker rechtstreeks aan te melden, wordt die aanmelding geblokkeerd.

Op dit moment wordt verificatie voor consumententoepassingen niet weergegeven in de beheerlogboeken, [](#admin-experience)omdat login.live.com afzonderlijk van Azure AD wordt gehost.

### <a name="what-the-header-does-and-does-not-block"></a>Wat de header wel en niet blokkeert

Het `restrict-msa` beleid blokkeert het gebruik van consumententoepassingen, maar maakt verschillende andere soorten verkeer en verificatie mogelijk:

1. Gebruikersverkeer voor apparaten.  Dit omvat verkeer voor Autopilot, Windows Update en organisatie-telemetrie.
1. B2B-verificatie van consumentenaccounts. Gebruikers met Microsoft-accounts die worden uitgenodigd om samen te werken met een [tenant,](../external-identities/redemption-experience.md#invitation-redemption-flow) verifiëren zich login.live.com om toegang te krijgen tot een resource-tenant.
    1. Deze toegang wordt beheerd met behulp van `Restrict-Access-To-Tenants` de header om toegang tot die resource-tenant toe te staan of te weigeren.
1. Passthrough-verificatie, die wordt gebruikt door veel Azure-apps en Office.com, waarbij apps Azure AD gebruiken om consumenten in een consumentencontext aan te melden.
    1. Deze toegang wordt ook beheerd met behulp van de header om toegang tot de speciale `Restrict-Access-To-Tenants` passthrough-tenant () toe te staan of te `f8cdef31-a31e-4b4a-93e4-5f571e91255a` weigeren.  Als deze tenant niet wordt weergegeven in de lijst met toegestane domeinen, worden consumentenaccounts geblokkeerd door Azure AD om zich aan te `Restrict-Access-To-Tenants` melden bij deze apps.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bijgewerkte moderne Office 365-verificatie](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- De [Office 365-URL's en IP-adresbereiken controleren](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
