---
title: Migreren vanuit de Azure-Access Control Service | Microsoft Docs
description: Meer informatie over de opties voor het verplaatsen van apps en services vanuit azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: bf50db4c463f5161adcc88d69eb2ae8970526103
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515614"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>How to: Migrate from the Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS) wordt een service van Azure Active Directory (Azure AD) op 7 november 2018 uit gebruik genomen. Toepassingen en services die momenteel gebruikmaken Access Control moeten dan volledig worden gemigreerd naar een ander verificatiemechanisme. In dit artikel worden aanbevelingen voor huidige klanten beschreven, omdat u van plan bent om uw gebruik van Access Control. Als u momenteel geen Access Control, hoeft u geen actie te ondernemen.

## <a name="overview"></a>Overzicht

Access Control is een cloudverificatieservice die een eenvoudige manier biedt om gebruikers te verifiëren en te autoriseren voor toegang tot uw webtoepassingen en -services. Hiermee kunt u veel functies van verificatie en autorisatie in uw code in rekening brengen. Access Control wordt voornamelijk gebruikt door ontwikkelaars en architecten van Microsoft .NET-clients, ASP.NET-webtoepassingen en Windows Communication Foundation (WCF)-webservices.

Gebruiksgevallen voor Access Control kunnen worden onderverdeeld in drie hoofdcategorieën:

- Authenticating to certain Microsoft cloud services, including Azure Service Bus and Dynamics CRM. Clienttoepassingen verkrijgen tokens van Access Control om te verifiëren bij deze services om verschillende acties uit te voeren.
- Verificatie toevoegen aan webtoepassingen, zowel aangepast als vooraf verpakt (zoals SharePoint). Met behulp van Access Control 'passieve' verificatie kunnen webtoepassingen aanmelding ondersteunen met een Microsoft-account (voorheen Live ID) en met accounts van Google, Facebook, Yahoo, Azure AD en Active Directory Federation Services (AD FS).
- Aangepaste webservices beveiligen met tokens die zijn uitgegeven door Access Control. Met behulp van actieve verificatie kunnen webservices ervoor zorgen dat ze alleen toegang toestaan tot bekende clients die zijn geverifieerd met Access Control.

Elk van deze use cases en de aanbevolen migratiestrategieën worden in de volgende secties besproken.

> [!WARNING]
> In de meeste gevallen zijn belangrijke codewijzigingen vereist voor het migreren van bestaande apps en services naar nieuwere technologieën. We raden u aan om onmiddellijk te beginnen met het plannen en uitvoeren van uw migratie om mogelijke storingen of downtime te voorkomen.

Access Control bevat de volgende onderdelen:

- Een beveiligde tokenservice (STS), die verificatieaanvragen ontvangt en beveiligingstokens retourneert.
- De klassieke Azure-portal, waar u naamruimten maakt, verwijdert en Access Control uit.
- Een afzonderlijke Access Control-beheerportal, waar u de naamruimten Access Control configureert.
- Een beheerservice, die u kunt gebruiken om de functies van de portals te automatiseren.
- Een engine voor tokentransformatieregel, die u kunt gebruiken om complexe logica te definiëren voor het bewerken van de uitvoerindeling van tokens die Access Control oplossen.

Als u deze onderdelen wilt gebruiken, moet u een of meer Access Control maken. Een *naamruimte* is een toegewezen exemplaar van Access Control uw toepassingen en services communiceren. Een naamruimte is geïsoleerd van alle andere Access Control klanten. Andere Access Control gebruiken hun eigen naamruimten. Een naamruimte in Access Control heeft een toegewezen URL die er als volgende uitziet:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Alle communicatie met de STS en beheerbewerkingen wordt uitgevoerd op deze URL. U gebruikt verschillende paden voor verschillende doeleinden. Als u wilt bepalen of uw toepassingen of services gebruikmaken van Access Control, controleert u op verkeer https:// &lt; &gt; .accesscontrol.windows.net. Al het verkeer naar deze URL wordt verwerkt door Access Control en moet worden stopgezet. 

De uitzondering hierop is verkeer naar `https://accounts.accesscontrol.windows.net` . Verkeer naar deze URL wordt al verwerkt door een andere service en **wordt niet** beïnvloed door Access Control afschaffing. 

Zie voor meer informatie Access Control [2.0 (gearchiveerd) Access Control Service 2.0.](/previous-versions/azure/azure-services/hh147631(v=azure.100))

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ontdekken welke van uw apps worden beïnvloed

Volg de stappen in deze sectie om erachter te komen welke van uw apps worden beïnvloed door de buiten gebruik stellen van ACS.

### <a name="download-and-install-acs-powershell"></a>ACS PowerShell downloaden en installeren

1. Ga naar de PowerShell Gallery en download [Acs.Namespaces.](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)
2. Installeer de module door het uitvoeren van

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Een lijst met alle mogelijke opdrachten op te halen door uit te voeren

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Voer het volgende uit om hulp te krijgen bij een specifieke opdracht:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    waarbij `[Command-Name]` de naam is van de ACS-opdracht.

### <a name="list-your-acs-namespaces"></a>Uw ACS-naamruimten op een lijst zetten

1. Maak verbinding met ACS met behulp van de cmdlet **Connect-AcsAccount.**
  
    Mogelijk moet u uitvoeren voordat u opdrachten kunt uitvoeren en de beheerder van deze abonnementen kunt zijn om `Set-ExecutionPolicy -ExecutionPolicy Bypass` de opdrachten uit te voeren.

2. Vermeld uw beschikbare Azure-abonnementen met behulp van de cmdlet **Get-AcsSubscription.**
3. Maak een lijst van uw ACS-naamruimten met behulp van de cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Controleren welke toepassingen worden beïnvloed

1. Gebruik de naamruimte uit de vorige stap en ga naar `https://<namespace>.accesscontrol.windows.net`

    Als een van de naamruimten bijvoorbeeld contoso-test is, gaat u naar `https://contoso-test.accesscontrol.windows.net`

2. Selecteer **onder Vertrouwensrelaties** de optie **Relying Party-toepassingen** om de lijst met apps te zien die worden beïnvloed door acs-pensioen.
3. Herhaal stap 1-2 voor alle andere ACS-naamruimten die u hebt.

## <a name="retirement-schedule"></a>Schema voor pensioen

Vanaf november 2017 worden alle Access Control volledig ondersteund en operationeel. De enige beperking is dat u geen nieuwe Access Control maken via de klassieke [Azure-portal.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Dit is het schema voor het afvangen van Access Control onderdelen:

- **November 2017:** De Azure AD-beheerderservaring in de klassieke Azure-portal [is niet meer beschikbaar.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Op dit moment is naamruimtebeheer voor Access Control beschikbaar via een nieuwe, toegewezen URL: `https://manage.windowsazure.com?restoreClassic=true` . Gebruik deze RL om uw bestaande naamruimten weer te geven, naamruimten in en uit te schakelen, en om naamruimten te verwijderen, als u dat wilt.
- **2 april 2018:** de klassieke Azure-portal is volledig uit gebruik genomen, wat betekent Access Control naamruimtebeheer niet meer beschikbaar is via een URL. Op dit moment kunt u uw naamruimten niet uitschakelen, inschakelen, verwijderen of Access Control opsnoemen. De beheerportal Access Control echter volledig functioneel en bevindt zich op `https://<namespace>.accesscontrol.windows.net` . Alle andere onderdelen van Access Control blijven normaal werken.
- **7 november 2018:** alle Access Control onderdelen worden permanent afgesloten. Dit omvat de Access Control-beheerportal, de beheerservice, STS en de engine voor tokentransformatieregel. Op dit moment mislukken alle aanvragen die naar Access Control (op \<namespace\> .accesscontrol.windows.net) worden verzonden. U moet al vóór deze tijd alle bestaande apps en services naar andere technologieën hebben gemigreerd.

> [!NOTE]
> Een beleid schakelt naamruimten uit die een bepaalde tijd geen token hebben aangevraagd. Vanaf begin september 2018 is deze periode momenteel 14 dagen inactief, maar dit wordt in de komende weken ingekort tot 7 dagen inactiviteit. Als u Access Control hebt die momenteel zijn uitgeschakeld, kunt u [ACS PowerShell](#download-and-install-acs-powershell) downloaden en installeren om de naamruimten opnieuw in te stellen.

## <a name="migration-strategies"></a>Migratiestrategieën

In de volgende secties worden aanbevelingen op hoog niveau beschreven voor het migreren van Access Control naar andere Microsoft-technologieën.

### <a name="clients-of-microsoft-cloud-services"></a>Clients van Microsoft-cloudservices

Elke Microsoft-cloudservice die tokens accepteert die zijn uitgegeven door Access Control ondersteunt nu ten minste één alternatieve vorm van verificatie. Het juiste verificatiemechanisme varieert voor elke service. We raden u aan de specifieke documentatie voor elke service te lezen voor officiële richtlijnen. Voor het gemak wordt elke set documentatie hier aangeboden:

| Service | Hulp |
| ------- | -------- |
| Azure Service Bus | [Migreren naar handtekeningen voor gedeelde toegang](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migreren naar handtekeningen voor gedeelde toegang](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migreren naar Azure Cache voor Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migreren naar de Cognitive Services-API's](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migreren naar de Logic Apps-functie van Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migreren naar Azure AD-verificatie](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [De agent Azure Backup upgraden](../../backup/backup-azure-file-folder-backup-faq.yml) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-klanten

Klanten van SharePoint 2013, 2016 en SharePoint Online gebruiken ACS al lang voor verificatiedoeleinden in cloud-, on-premises en hybride scenario's. Sommige SharePoint-functies en gebruiksgevallen worden beïnvloed door acs-pensioen, andere niet. De onderstaande tabel bevat een overzicht van de migratie-richtlijnen voor een aantal van de populairste SharePoint-functies die gebruikmaken van ACS:

| Functie | Hulp |
| ------- | -------- |
| Gebruikers van Azure AD authenticeren | Voorheen ondersteunde Azure AD geen SAML 1.1-tokens die door SharePoint zijn vereist voor verificatie, en ACS werd gebruikt als intermediair die SharePoint compatibel maakte met Azure AD-tokenindelingen. Nu kunt u SharePoint rechtstreeks verbinden met Azure AD met [behulp van Azure AD-app On-premises app Gallery SharePoint](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [App-& server-naar-server-verificatie in SharePoint on-premises](/SharePoint/security-for-sharepoint-server/authentication-overview) | Niet beïnvloed door het uit bedrijf nemen van ACS; geen wijzigingen nodig. | 
| [Autorisatie met lage vertrouwensrelatie voor SharePoint-invoegvoegingen (gehoste en gehoste SharePoint-provider)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Niet beïnvloed door het uit bedrijf nemen van ACS; geen wijzigingen nodig. |
| [Hybride zoekopdrachten in de SharePoint-cloud](/archive/blogs/spses/cloud-hybrid-search-service-application) | Niet beïnvloed door het uit bedrijf nemen van ACS; geen wijzigingen nodig. |

### <a name="web-applications-that-use-passive-authentication"></a>Webtoepassingen die gebruikmaken van passieve verificatie

Voor webtoepassingen die gebruikmaken van Access Control voor gebruikersverificatie, biedt Access Control de volgende functies en mogelijkheden voor ontwikkelaars en architecten van webtoepassingen:

- Diepe integratie met Windows Identity Foundation (WIF).
- Federatie met Google-, Facebook-, Yahoo-, Azure Active Directory- en AD FS-accounts en Microsoft-accounts.
- Ondersteuning voor de volgende verificatieprotocollen: OAuth 2.0 Draft 13, WS-Trust en Webservices-federatie (WS-Federation).
- Ondersteuning voor de volgende tokenindelingen: JSON Web Token (JWT), SAML 1.1, SAML 2.0 en Simple Web Token (SWT).
- Een ervaring voor thuis realmdetectie, geïntegreerd in WIF, waarmee gebruikers het type account kunnen kiezen dat ze gebruiken om zich aan te melden. Deze ervaring wordt gehost door de webtoepassing en kan volledig worden aangepast.
- Tokentransformatie waarmee uitgebreide aanpassingen kunnen worden gemaakt van de claims die door de webtoepassing worden ontvangen van Access Control, waaronder:
    - Claims van id-providers doorgeven.
    - Aanvullende aangepaste claims toevoegen.
    - Eenvoudige if-then-logica voor het uitgeven van claims onder bepaalde voorwaarden.

Er is helaas niet één service die al deze equivalente mogelijkheden biedt. U moet evalueren welke mogelijkheden van Access Control u nodig hebt en vervolgens kiezen tussen het gebruik van [Azure Active Directory,](https://azure.microsoft.com/develop/identity/signin/) [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) of een andere cloudverificatieservice.

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

U kunt uw apps en services rechtstreeks integreren met Azure AD. Azure AD is de cloudidentiteitsprovider voor werk- of schoolaccounts van Microsoft. Azure AD is de id-provider voor Microsoft 365, Azure en nog veel meer. Het biedt vergelijkbare federatief verificatiemogelijkheden voor Access Control, maar ondersteunt niet alle Access Control functies. 

Het primaire voorbeeld is federatie met id-providers voor sociale netwerken, zoals Facebook, Google en Yahoo. Als uw gebruikers zich aanmelden met deze typen referenties, is Azure AD niet de oplossing voor u. 

Azure AD biedt ook niet noodzakelijkerwijs ondersteuning voor exact dezelfde verificatieprotocollen als Access Control. Hoewel zowel Access Control als Azure AD OAuth ondersteunen, zijn er bijvoorbeeld subtiele verschillen tussen elke implementatie. Voor verschillende implementaties moet u code wijzigen als onderdeel van een migratie.

Azure AD biedt echter verschillende potentiële voordelen voor Access Control klanten. Het biedt standaard ondersteuning voor werk- of schoolaccounts van Microsoft die worden gehost in de cloud, die vaak worden gebruikt door Access Control klanten. 

Een Azure AD-tenant kan ook worden ge federeerd naar een of meer exemplaren van on-premises Active Directory via AD FS. Op deze manier kan uw app cloudgebruikers en -gebruikers verifiëren die on-premises worden gehost. Het ondersteunt ook het WS-Federation protocol, waardoor het relatief eenvoudig is om te integreren met een webtoepassing met behulp van WIF.

De volgende tabel vergelijkt de functies van Access Control die relevant zijn voor webtoepassingen met de functies die beschikbaar zijn in Azure AD. 

Op hoog niveau is Azure Active Directory de beste keuze voor uw migratie als u gebruikers zich alleen laat aanmelden met hun Werk- of *schoolaccount van Microsoft.*

| Mogelijkheid | Access Control ondersteuning | Azure AD-ondersteuning |
| ---------- | ----------- | ---------------- |
| **Typen accounts** | | |
| Werk- of schoolaccounts van Microsoft | Ondersteund | Ondersteund |
| Accounts van Windows Server Active Directory en AD FS |- Ondersteund via federatie met een Azure AD-tenant <br />- Ondersteund via directe federatie met AD FS | Alleen ondersteund via federatie met een Azure AD-tenant | 
| Accounts van andere enterprise identity management-systemen |- Mogelijk via federatie met een Azure AD-tenant <br />- Ondersteund via directe federatie | Mogelijk via federatie met een Azure AD-tenant |
| Microsoft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund via het OAuth-protocol van Azure AD v2.0, maar niet via andere protocollen | 
| Facebook-, Google-, Yahoo-accounts | Ondersteund | Helemaal niet ondersteund |
| **Protocollen en SDK-compatibiliteit** | | |
| WIF | Ondersteund | Ondersteunde, maar beperkte instructies zijn beschikbaar |
| Webservices-federatie | Ondersteund | Ondersteund |
| OAuth 2.0 | Ondersteuning voor concept 13 | Ondersteuning voor RFC 6749, de meest moderne specificatie |
| WS-Trust | Ondersteund | Niet ondersteund |
| **Tokenindelingen** | | |
| JWT | Ondersteund in bètaversie | Ondersteund |
| SAML 1.1 | Ondersteund | Preview |
| SAML 2.0 | Ondersteund | Ondersteund |
| Swt | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpasbare gebruikersinterface voor thuis realmdetectie/account kiezen | Downloadbare code die kan worden opgenomen in apps | Niet ondersteund |
| Aangepaste certificaten voor token-ondertekening uploaden | Ondersteund | Ondersteund |
| Claims in tokens aanpassen |- Invoerclaims van id-providers doorgeven<br />- Toegangs token van id-provider als een claim op te halen<br />- Uitvoerclaims uitgeven op basis van waarden van invoerclaims<br />- Uitvoerclaims met constante waarden uitgeven |- Claims van federatieproviders kunnen niet worden door geven<br />- Kan geen toegangs token van id-provider als claim krijgen<br />- Kan geen uitvoerclaims uitgeven op basis van waarden van invoerclaims<br />- Kan uitvoerclaims uitgeven met constante waarden<br />- Kan uitvoerclaims uitgeven op basis van eigenschappen van gebruikers die zijn gesynchroniseerd met Azure AD |
| **Automation** | | |
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service | Ondersteund met behulp van de Microsoft Graph-API |

Als u besluit dat Azure AD het beste migratiepad is voor uw toepassingen en services, moet u rekening houden met twee manieren om uw app te integreren met Azure AD.

Als u WS-Federation of WIF wilt gebruiken om te integreren met Azure AD, wordt u aangeraden de methode te volgen die wordt beschreven in Federatief een aanmelding configureren voor een toepassing buiten [de galerie.](../manage-apps/configure-saml-single-sign-on.md) In dit artikel wordt verwezen naar het configureren van Azure AD voor een aanmelding op basis van SAML, maar het werkt ook voor het configureren van WS-Federation. Voor deze aanpak is een Azure AD Premium vereist. Deze aanpak heeft twee voordelen:

- U krijgt de volledige flexibiliteit van het aanpassen van Het Azure AD-token. U kunt de claims die zijn uitgegeven door Azure AD aanpassen aan de claims die zijn uitgegeven door Access Control. Dit geldt met name voor de gebruikers-id of naam-id-claim. Als u consistente gebruikers-IDentifiers voor uw gebruikers wilt blijven ontvangen nadat u de technologieën hebt gewijzigd, moet u ervoor zorgen dat de gebruikers-ID's die zijn uitgegeven door Azure AD overeenkomen met de gebruikers-Access Control.
- U kunt een certificaat voor token-ondertekening configureren dat specifiek is voor uw toepassing en met een levensduur die u controleert.

> [!NOTE]
> Voor deze aanpak is een Azure AD Premium vereist. Als u een Access Control bent en u een Premium-licentie nodig hebt voor het instellen van een een aanmelding voor een toepassing, neem dan contact met ons op. We bieden u graag ontwikkelaarslicenties die u kunt gebruiken.

Een alternatieve methode is om dit [codevoorbeeld te volgen.](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)Dit biedt iets andere instructies voor het instellen van WS-Federation. Dit codevoorbeeld maakt geen gebruik van WIF, maar van de ASP.NET 4.5 OWIN-middleware. De instructies voor app-registratie zijn echter geldig voor apps die gebruikmaken van WIF en vereisen geen Azure AD Premium licentie. 

Als u deze benadering kiest, moet u inzicht hebben in de rollover van ondertekeningssleutels [in Azure AD.](../develop/active-directory-signing-key-rollover.md) Bij deze benadering wordt de globale ondertekeningssleutel van Azure AD gebruikt om tokens uit te geven. Standaard worden ondertekeningssleutels niet automatisch vernieuwd door WIF. Wanneer Azure AD de globale ondertekeningssleutels roteert, moet uw WIF-implementatie worden voorbereid om de wijzigingen te accepteren. Zie Belangrijke informatie over het [rolloveren van ondertekeningssleutels in Azure AD voor meer informatie.](/previous-versions/azure/dn641920(v=azure.100))

Als u met Azure AD kunt integreren via de OpenID Connect of OAuth-protocollen, raden we u aan dit te doen. We hebben uitgebreide documentatie en richtlijnen voor het integreren van Azure AD in uw webtoepassing die beschikbaar is in onze [Ontwikkelaarshandleiding voor Azure AD.](../develop/index.yml)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migreren naar Azure Active Directory B2C

Het andere migratiepad dat u moet overwegen, is Azure AD B2C. Azure AD B2C is een cloudverificatieservice waarmee ontwikkelaars, zoals Access Control, hun logica voor verificatie en identiteitsbeheer kunnen uitbesteed aan een cloudservice. Het is een betaalde service (met gratis en Premium-lagen) die is ontworpen voor consumententoepassingen die maximaal miljoenen actieve gebruikers kunnen hebben.

Net Access Control is een van de meest aantrekkelijke functies van Azure AD B2C dat het veel verschillende typen accounts ondersteunt. Met Azure AD B2C kunt u gebruikers aanmelden met hun Microsoft-account- of Facebook-, Google-, LinkedIn-, GitHub- of Yahoo-accounts en meer. Azure AD B2C ondersteunt ook 'lokale accounts' of gebruikersnaam en wachtwoorden die gebruikers specifiek voor uw toepassing maken. Azure AD B2C biedt ook uitgebreide extensibility die u kunt gebruiken om uw aanmeldingsstromen aan te passen. 

De Azure AD B2C biedt echter geen ondersteuning voor de verschillende verificatieprotocollen en tokenindelingen die Access Control nodig hebben. U kunt ook geen Azure AD B2C om tokens op te halen en op te vragen naar aanvullende informatie over de gebruiker van de id-provider, Microsoft of anderszins.

In de volgende tabel worden de functies van Access Control die relevant zijn voor webtoepassingen vergeleken met de functies die beschikbaar zijn in Azure AD B2C. Op hoog niveau is Azure AD B2C de juiste keuze voor uw migratie als uw toepassing te maken heeft met consumenten of als deze veel verschillende *typen accounts ondersteunt.*

| Mogelijkheid | Access Control ondersteuning | Azure AD B2C ondersteuning |
| ---------- | ----------- | ---------------- |
| **Typen accounts** | | |
| Werk- of schoolaccounts van Microsoft | Ondersteund | Ondersteund via aangepast beleid  |
| Accounts van Windows Server Active Directory en AD FS | Ondersteund via directe federatie met AD FS | Ondersteund via SAML-federatie met behulp van aangepast beleid |
| Accounts van andere enterprise-systemen voor identiteitsbeheer | Ondersteund via directe federatie via WS-Federation | Ondersteund via SAML-federatie met behulp van aangepast beleid |
| Microsoft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund | 
| Facebook-, Google-, Yahoo-accounts | Ondersteund | Facebook en Google ondersteunden native, Yahoo ondersteund via OpenID Connect federation met behulp van aangepast beleid |
| **Protocollen en SDK-compatibiliteit** | | |
| Windows Identity Foundation (WIF) | Ondersteund | Niet ondersteund |
| Webservices-federatie | Ondersteund | Niet ondersteund |
| OAuth 2.0 | Ondersteuning voor concept 13 | Ondersteuning voor RFC 6749, de meest moderne specificatie |
| WS-Trust | Ondersteund | Niet ondersteund |
| **Tokenindelingen** | | |
| JWT | Ondersteund in bètaversie | Ondersteund |
| SAML 1.1 | Ondersteund | Niet ondersteund |
| SAML 2.0 | Ondersteund | Niet ondersteund |
| Swt | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpasbare gebruikersinterface voor thuis realmdetectie/account kiezen | Downloadbare code die kan worden opgenomen in apps | Volledig aanpasbare gebruikersinterface via aangepaste CSS |
| Aangepaste certificaten voor token-ondertekening uploaden | Ondersteund | Aangepaste ondertekeningssleutels, geen certificaten, die worden ondersteund via aangepast beleid |
| Claims in tokens aanpassen |- Invoerclaims van id-providers doorgeven<br />- Toegangs token van id-provider als een claim op te halen<br />- Uitvoerclaims uitgeven op basis van waarden van invoerclaims<br />- Uitvoerclaims uitgeven met constante waarden |- Kan claims van id-providers doorgeven; aangepast beleid vereist voor sommige claims<br />- Kan geen toegangs token van id-provider als claim krijgen<br />- Kan uitvoerclaims uitgeven op basis van waarden van invoerclaims via aangepast beleid<br />- Kan uitvoerclaims met constante waarden uitgeven via aangepast beleid |
| **Automation** | | |
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service |- Gebruikers maken die zijn toegestaan met behulp van de Microsoft Graph-API<br />- Kan B2C-tenants, -toepassingen of -beleid niet programmatisch maken |

Als u besluit dat Azure AD B2C het beste migratiepad voor uw toepassingen en services is, begint u met de volgende resources:

- [Azure AD B2C documentatie](../../active-directory-b2c/overview.md)
- [Aangepast Azure AD B2C-beleid](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar Ping Identity of Auth0

In sommige gevallen is het mogelijk dat Azure AD en Azure AD B2C niet voldoende zijn om de Access Control in uw webtoepassingen te vervangen zonder belangrijke codewijzigingen aan te brengen. Enkele veelvoorkomende voorbeelden zijn:

- Webtoepassingen die gebruikmaken van WIF of WS-Federation voor aanmelding met id-providers voor sociale netwerken, zoals Google of Facebook.
- Webtoepassingen die directe federatie met een ondernemingsidentiteitsprovider uitvoeren via het WS-Federation protocol.
- Webtoepassingen waarvoor het toegangstoken is vereist dat is uitgegeven door een id-provider voor sociale netwerken (zoals Google of Facebook) als een claim in de tokens die zijn uitgegeven door Access Control.
- Webtoepassingen met complexe tokentransformatieregels die Azure AD of Azure AD B2C niet kunnen reproduceren.
- Webtoepassingen met meerdere tenants die gebruikmaken van ACS om federatie centraal te beheren voor veel verschillende id-providers

In dergelijke gevallen kunt u overwegen om uw webtoepassing te migreren naar een andere cloudverificatieservice. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt mogelijkheden die vergelijkbaar zijn met Access Control:

![In deze afbeelding ziet u het Auth0-logo](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) is een flexibele cloudidentiteitsservice die op hoog niveau migratie-richtlijnen heeft gemaakt voor klanten van [Access Control](https://auth0.com/acs)en die ondersteuning biedt voor vrijwel elke functie die ACS gebruikt.

![In deze afbeelding ziet u het pingidentiteitslogo](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity biedt](https://www.pingidentity.com) twee oplossingen die vergelijkbaar zijn met ACS. PingOne is een cloudidentiteitsservice die veel van dezelfde functies ondersteunt als ACS en PingFederate is een vergelijkbaar on-premises identiteitsproduct dat meer flexibiliteit biedt. Raadpleeg de ACS-pensioeninformatie van Ping voor meer informatie over het gebruik van deze producten.

Ons doel bij het werken met Ping Identity en Auth0 is ervoor te zorgen dat alle Access Control-klanten een migratiepad hebben voor hun apps en services, zodat er minder werk nodig is om van de Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webservices die gebruikmaken van actieve verificatie

Voor webservices die zijn beveiligd met tokens die zijn uitgegeven door Access Control, Access Control de volgende functies en mogelijkheden:

- De mogelijkheid om een of meer *service-identiteiten* in uw Access Control registreren. Service-identiteiten kunnen worden gebruikt om tokens aan te vragen.
- Ondersteuning voor de protocollen OAuth WRAP en OAuth 2.0 Draft 13 voor het aanvragen van tokens, met behulp van de volgende typen referenties:
    - Een eenvoudig wachtwoord dat is gemaakt voor de service-identiteit
    - Een ondertekende SWT met behulp van een symmetrische sleutel of X509-certificaat
    - Een SAML-token dat is uitgegeven door een vertrouwde id-provider (meestal een AD FS-instantie)
- Ondersteuning voor de volgende tokenindelingen: JWT, SAML 1.1, SAML 2.0 en SWT.
- Eenvoudige regels voor tokentransformatie.

Service-identiteiten in Access Control worden doorgaans gebruikt voor het implementeren van server-naar-server-verificatie. 

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

Onze aanbeveling voor dit type verificatiestroom is om te migreren naar [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD is de cloudidentiteitsprovider voor werk- of schoolaccounts van Microsoft. Azure AD is de id-provider voor Microsoft 365, Azure en nog veel meer. 

U kunt Azure AD ook gebruiken voor server-naar-server-verificatie door de Azure AD-implementatie van de OAuth-clientreferenties te verlenen. In de volgende tabel worden de mogelijkheden van Access Control in server-naar-server-verificatie vergeleken met de mogelijkheden die beschikbaar zijn in Azure AD.

| Mogelijkheid | Access Control ondersteuning | Azure AD-ondersteuning |
| ---------- | ----------- | ---------------- |
| Een webservice registreren | Een relying party maken in de Access Control-beheerportal | Een Azure AD-webtoepassing maken in de Azure Portal |
| Een client registreren | Een service-id maken in Access Control-beheerportal | Maak nog een Azure AD-webtoepassing in de Azure Portal |
| Protocol gebruikt |- OAuth WRAP-protocol<br />- OAuth 2.0 Draft 13-clientreferenties verlenen | Referenties voor OAuth 2.0-client verlenen |
| Clientverificatiemethoden |- Eenvoudig wachtwoord<br />- Ondertekende SWT<br />- SAML-token van een federatief id-provider |- Eenvoudig wachtwoord<br />- Ondertekende JWT |
| Tokenindelingen |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Alleen JWT |
| Tokentransformatie |- Aangepaste claims toevoegen<br />- Eenvoudige als-dan claimuitgiftelogica | Aangepaste claims toevoegen | 
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service | Ondersteund met behulp van de Microsoft Graph-API |

Zie de volgende bronnen voor hulp bij het implementeren van server-naar-server-scenario's:

- Service-naar-service-sectie van de [Ontwikkelaarshandleiding voor Azure AD](../develop/index.yml)
- [Daemon-codevoorbeeld met behulp van clientreferenties voor eenvoudig wachtwoord](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemon-codevoorbeeld met behulp van certificaatclientreferenties](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar Ping Identity of Auth0

In sommige gevallen kan het zijn dat de referenties van de Azure AD-client en de implementatie van de OAuth-toekenning niet voldoende zijn om Access Control in uw architectuur te vervangen zonder belangrijke codewijzigingen. Enkele veelvoorkomende voorbeelden zijn:

- Server-naar-server-verificatie met behulp van andere tokenindelingen dan JWT's.
- Server-naar-server-verificatie met behulp van een invoer-token dat wordt geleverd door een externe id-provider.
- Server-naar-server-verificatie met tokentransformatieregels die Azure AD niet kan reproduceren.

In dergelijke gevallen kunt u overwegen om uw webtoepassing te migreren naar een andere cloudverificatieservice. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt mogelijkheden die vergelijkbaar zijn met Access Control:

![In deze afbeelding ziet u het Auth0-logo](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) is een flexibele cloudidentiteitsservice die op hoog niveau migratie-richtlijnen heeft ontwikkeld voor klanten van [Access Control](https://auth0.com/acs)en die ondersteuning biedt voor vrijwel elke functie die ACS gebruikt.

![In deze afbeelding ziet u dat het Ping Identity-logo ](./media/active-directory-acs-migration/rsz-ping.png)
 [Ping Identity](https://www.pingidentity.com) twee oplossingen biedt die vergelijkbaar zijn met ACS. PingOne is een cloudidentiteitsservice die ondersteuning biedt voor veel van dezelfde functies als ACS en PingFederate is een vergelijkbaar on-premises identiteitsproduct dat meer flexibiliteit biedt. Raadpleeg de ACS-pensioeninformatie van Ping voor meer informatie over het gebruik van deze producten.

Ons doel bij het werken met Ping Identity en Auth0 is ervoor te zorgen dat alle Access Control-klanten een migratiepad hebben voor hun apps en services, zodat er minder werk nodig is om van de Access Control.

#### <a name="passthrough-authentication"></a>Passthrough-verificatie

Voor passthrough-verificatie met een willekeurige tokentransformatie is er geen equivalente Microsoft-technologie voor ACS. Als uw klanten dit nodig hebben, is Auth0 mogelijk degene die de dichtstbijzijnde benaderingsoplossing biedt.

## <a name="questions-concerns-and-feedback"></a>Vragen, zorgen en feedback

We begrijpen dat veel Access Control geen duidelijk migratiepad vinden na het lezen van dit artikel. Mogelijk hebt u hulp of hulp nodig bij het bepalen van het juiste plan. Als u uw migratiescenario's en vragen wilt bespreken, kunt u een opmerking plaatsen op deze pagina.