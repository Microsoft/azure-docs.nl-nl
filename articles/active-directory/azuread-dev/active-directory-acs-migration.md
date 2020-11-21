---
title: Migreren vanuit Azure Access Control Service | Microsoft Docs
description: Meer informatie over de opties voor het verplaatsen van apps en services van Azure Access Control Service (ACS).
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
ms.openlocfilehash: eda648a4d00a0ab4a51c66510060ce16421972ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020007"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Procedure: migreren vanuit Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), een service van Azure Active Directory (Azure AD), wordt buiten gebruik gesteld op 7 november 2018. Toepassingen en services die momenteel gebruikmaken van Access Control, moeten door vervolgens volledig worden gemigreerd naar een ander verificatie mechanisme. In dit artikel worden aanbevelingen beschreven voor huidige klanten, wanneer u van plan bent om uw gebruik van Access Control af te laten. Als u Access Control momenteel niet gebruikt, hoeft u geen actie te ondernemen.

## <a name="overview"></a>Overzicht

Access Control is een Cloud verificatie service die een eenvoudige manier biedt om gebruikers te verifiëren en te autoriseren voor toegang tot uw webtoepassingen en-services. Hiermee kunnen veel functies van verificatie en autorisatie worden geledigd uit uw code. Access Control wordt voornamelijk gebruikt door ontwikkel aars en architecten van Microsoft .NET-clients, webtoepassingen en Windows Communication Foundation WCF-webservices (ASP.NET).

Gebruiks voorbeelden voor Access Control kunnen worden onderverdeeld in drie hoofd categorieën:

- Verifiëren bij bepaalde micro soft-Cloud Services, waaronder Azure Service Bus en Dynamics CRM. Client toepassingen verkrijgen tokens van Access Control om te verifiëren bij deze services om verschillende acties uit te voeren.
- Verificatie toevoegen aan webtoepassingen, zowel aangepast als vooraf verpakt (zoals share point). Door gebruik te maken van Access Control passieve verificatie kunnen webtoepassingen ondersteuning bieden voor aanmelding met een Microsoft-account (voorheen Live ID), en met accounts van Google, Facebook, Yahoo, Azure AD en Active Directory Federation Services (AD FS).
- Aangepaste webservices beveiligen met tokens die zijn uitgegeven door Access Control. Met behulp van ' Active '-verificatie kunnen webservices ervoor zorgen dat ze alleen toegang toestaan tot bekende clients die zijn geverifieerd met Access Control.

Elk van deze use-cases en de aanbevolen migratie strategieën worden besproken in de volgende secties.

> [!WARNING]
> In de meeste gevallen zijn belang rijke code wijzigingen vereist voor het migreren van bestaande apps en services naar nieuwere technologieën. U kunt het beste meteen beginnen met het plannen en uitvoeren van uw migratie om potentiële storingen of downtime te voor komen.

Access Control heeft de volgende onderdelen:

- Een Secure token service (STS), die verificatie aanvragen ontvangt en beveiligings tokens als resultaat geeft.
- De klassieke Azure-Portal, waar u Access Control naam ruimten maakt, verwijdert en uitschakelt.
- Een afzonderlijke Access Control-Beheer Portal, waar u Access Control naam ruimten kunt aanpassen en configureren.
- Een beheer service, die u kunt gebruiken voor het automatiseren van de functies van de portals.
- Een regel engine voor token transformatie, die u kunt gebruiken om complexe logica te definiëren voor het bewerken van de uitvoer indeling van tokens die Access Control problemen.

Als u deze onderdelen wilt gebruiken, moet u een of meer Access Control-naam ruimten maken. Een *naam ruimte* is een toegewezen exemplaar van Access Control waarmee uw toepassingen en services communiceren. Een naam ruimte is geïsoleerd van alle andere Access Control-klanten. Andere Access Control-klanten gebruiken hun eigen naam ruimten. Een naam ruimte in Access Control heeft een toegewezen URL die er als volgt uitziet:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Alle communicatie met de STS-en beheer bewerkingen wordt uitgevoerd op deze URL. U gebruikt verschillende paden voor verschillende doel einden. Als u wilt bepalen of uw toepassingen of services gebruikmaken van Access Control, controleert u op alle verkeer naar https:// &lt; namespace &gt; . accesscontrol.Windows.net. Elk verkeer naar deze URL wordt verwerkt door Access Control en moet worden stopgezet. 

De uitzonde ring hierop is het verkeer naar `https://accounts.accesscontrol.windows.net` . Verkeer naar deze URL wordt al verwerkt door een andere service en **wordt niet** beïnvloed door de Access Control afschaffing. 

Zie [Access Control Service 2,0 (gearchiveerd)](/previous-versions/azure/azure-services/hh147631(v=azure.100))voor meer informatie over Access Control.

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Meer informatie over welke apps worden beïnvloed

Volg de stappen in deze sectie om erachter te komen welke van uw apps van invloed zijn op de buiten gebruiks telling van ACS.

### <a name="download-and-install-acs-powershell"></a>ACS Power shell downloaden en installeren

1. Ga naar het PowerShell Gallery en down load [ACS. namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. De module installeren door uit te voeren

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Een lijst met alle mogelijke opdrachten ophalen door uit te voeren

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Voer het volgende uit om hulp te krijgen voor een specifieke opdracht:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    waarbij `[Command-Name]` de naam is van de ACS-opdracht.

### <a name="list-your-acs-namespaces"></a>Uw ACS-naam ruimten weer geven

1. Maak verbinding met ACS met de cmdlet **Connect-AcsAccount** .
  
    Mogelijk moet u uitvoeren `Set-ExecutionPolicy -ExecutionPolicy Bypass` voordat u opdrachten kunt uitvoeren en de beheerder van deze abonnementen moet zijn om de opdrachten uit te voeren.

2. Vermeld uw beschik bare Azure-abonnementen met de cmdlet **Get-AcsSubscription** .
3. Vermeld uw ACS-naam ruimten met behulp van de cmdlet **Get-AcsNamespace** .

### <a name="check-which-applications-will-be-impacted"></a>Controleren welke toepassingen worden beïnvloed

1. Gebruik de naam ruimte uit de vorige stap en ga naar `https://<namespace>.accesscontrol.windows.net`

    Als een van de naam ruimten bijvoorbeeld contoso-test is, gaat u naar `https://contoso-test.accesscontrol.windows.net`

2. Onder **vertrouwens relaties** selecteert u **toepassingen voor relying** Party om de lijst met apps weer te geven die van invloed zijn op de uittreding van ACS.
3. Herhaal stap 1-2 voor elke andere ACS-naam ruimte die u hebt.

## <a name="retirement-schedule"></a>Pensioen planning

Vanaf november 2017 worden alle Access Control onderdelen volledig ondersteund en operationeel. De enige beperking is dat u [geen nieuwe Access Control-naam ruimten kunt maken via de klassieke Azure-Portal](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Dit is de planning voor het afAccess Controlen van onderdelen:

- **November 2017**: de Azure AD-beheer ervaring in de klassieke Azure-Portal [is buiten gebruik gesteld](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Op dit punt is naam ruimte beheer voor Access Control beschikbaar op een nieuwe, toegewezen URL: `https://manage.windowsazure.com?restoreClassic=true` . Gebruik deze URl om uw bestaande naam ruimten weer te geven, naam ruimten in te scha kelen en uit te scha kelen en om naam ruimten te verwijderen, indien gewenst.
- **2 April 2018**: de klassieke Azure-Portal is volledig buiten gebruik gesteld, wat betekent dat Access Control naam ruimte beheer niet meer beschikbaar is via een URL. Op dit moment kunt u uw Access Control-naam ruimten niet in-of uitschakelen, verwijderen of opsommen. De Access Control Beheer Portal is echter volledig functioneel en bevindt zich op `https://\<namespace\>.accesscontrol.windows.net` . Alle andere onderdelen van Access Control blijven normaal functioneren.
- **7 November 2018**: alle Access Control onderdelen worden permanent afgesloten. Dit omvat de Access Control Beheer Portal, de beheer service, STS en de token transformatie regel engine. Op dit moment mislukken alle aanvragen die worden verzonden naar Access Control (te vinden op \<namespace\> . accesscontrol.Windows.net). U moet alle bestaande apps en services voor deze periode goed hebben gemigreerd naar andere technologieën.

> [!NOTE]
> Een beleid schakelt naam ruimten uit die gedurende een bepaalde tijd geen token hebben aangevraagd. Vanaf begin september 2018 is deze periode op dit moment al 14 dagen inactief, maar dit wordt in de komende weken verkort tot 7 dagen van inactiviteit. Als u Access Control naam ruimten die momenteel zijn uitgeschakeld, kunt u [ACS Power shell downloaden en installeren](#download-and-install-acs-powershell) om de naam ruimte (n) weer in te scha kelen.

## <a name="migration-strategies"></a>Migratiestrategieën

In de volgende secties worden de aanbevelingen op hoog niveau voor de migratie van Access Control naar andere micro soft-technologieën beschreven.

### <a name="clients-of-microsoft-cloud-services"></a>Clients van micro soft-Cloud Services

Elke micro soft-Cloud service die tokens accepteert die zijn uitgegeven door Access Control, ondersteunt nu ten minste één alternatieve vorm van verificatie. Het juiste verificatie mechanisme varieert voor elke service. U wordt aangeraden om te verwijzen naar de specifieke documentatie voor elke service voor officiële richt lijnen. Voor het gemak wordt elke set van documentatie hier beschreven:

| Service | Hulp |
| ------- | -------- |
| Azure Service Bus | [Migreren naar hand tekeningen voor gedeelde toegang](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus relay | [Migreren naar hand tekeningen voor gedeelde toegang](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed cache | [Migreren naar Azure Cache voor Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure data Market | [Migreren naar het Cognitive Services-API's](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migreren naar de Logic Apps-functie van Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migreren naar Azure AD-verificatie](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [De Azure Backup-Agent bijwerken](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Share point-klanten

Share point 2013-, 2016-en share point online-klanten hebben in de Cloud, on-premises en hybride scenario's een langdurige ACS gebruikt voor verificatie doeleinden. Sommige share point-functies en-use-cases worden beïnvloed door ACS-uittreding, terwijl anderen dat niet doen. De onderstaande tabel bevat een overzicht van de migratie richtlijnen voor een aantal van de populairste share point-functies die gebruikmaken van ACS:

| Functie | Hulp |
| ------- | -------- |
| Gebruikers verifiëren vanuit Azure AD | Voorheen heeft Azure AD geen ondersteuning voor SAML 1,1-tokens die vereist zijn door share point voor verificatie, en ACS werd gebruikt als een tussen persoon die share point compatibel met Azure AD-token indelingen heeft gemaakt. Nu kunt u [rechtstreeks verbinding maken met share point met Azure AD met behulp van Azure AD-App galerie share point op de on-premises app](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [App-verificatie & server-naar-Server-verificatie in share point on-premises](/SharePoint/security-for-sharepoint-server/authentication-overview) | Niet beïnvloed door ACS-uittredingen; u hoeft niets te wijzigen. | 
| [Beperkte verificatie van de vertrouwens relatie voor share point-invoeg toepassingen (door de provider gehoste en share point gehost)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Niet beïnvloed door ACS-uittredingen; u hoeft niets te wijzigen. |
| [Zoeken in hybride share point-Cloud](/archive/blogs/spses/cloud-hybrid-search-service-application) | Niet beïnvloed door ACS-uittredingen; u hoeft niets te wijzigen. |

### <a name="web-applications-that-use-passive-authentication"></a>Webtoepassingen die gebruikmaken van passieve verificatie

Access Control biedt de volgende functies en mogelijkheden voor webtoepassingen die gebruikmaken van Access Control voor gebruikers verificatie:

- Diep gaande integratie met Windows Identity Foundation (WIF).
- Federatie met Google-, Facebook-, Yahoo-, Azure Active Directory-en AD FS-accounts en micro soft-accounts.
- Ondersteuning voor de volgende verificatie protocollen: OAuth 2,0 Draft 13, WS-Trust en Webservices-federatie (WS-Federation).
- Ondersteuning voor de volgende token indelingen: JSON Web Token (JWT), SAML 1,1, SAML 2,0 en Simple Web token (SWT).
- Een thuis realm-detectie-ervaring die is geïntegreerd in WIF, waarmee gebruikers het type account kunnen kiezen dat ze gebruiken om zich aan te melden. Deze ervaring wordt gehost door de webtoepassing en is volledig aanpasbaar.
- Token transformatie waarmee de door de webtoepassing ontvangen claims van Access Control uitgebreid kunnen worden aangepast, waaronder:
    - Claims van id-providers door geven.
    - Extra aangepaste claims toevoegen.
    - Eenvoudige if-then-logica voor het uitgeven van claims onder bepaalde voor waarden.

Helaas is er geen service die al deze gelijkwaardige mogelijkheden biedt. U moet evalueren welke mogelijkheden van Access Control u nodig hebt en vervolgens kiezen tussen het gebruik van [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) of een andere Cloud verificatie service.

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

Een pad om te overwegen is uw apps en services rechtstreeks te integreren met Azure AD. Azure AD is de Cloud-ID-provider voor werk-of school accounts van micro soft. Azure AD is de ID-provider voor Microsoft 365, Azure en nog veel meer. Het biedt soort gelijke Federated Authentication mogelijkheden voor Access Control, maar biedt geen ondersteuning voor alle Access Control-functies. 

Het primaire voor beeld is Federatie met sociale id-providers, zoals Facebook, Google en Yahoo. Als uw gebruikers zich aanmelden met deze typen referenties, is Azure AD niet de oplossing voor u. 

Azure AD biedt ook niet noodzakelijkerwijs dezelfde verificatie protocollen als Access Control. Hoewel zowel Access Control als Azure AD ondersteuning voor OAuth zijn, zijn er bijvoorbeeld subtiele verschillen tussen elke implementatie. Voor verschillende implementaties moet u code wijzigen als onderdeel van een migratie.

Azure AD biedt echter verschillende potentiële voor delen om klanten te Access Controlen. De service ondersteunt eigen micro soft-werk-of school accounts die worden gehost in de Cloud. deze worden vaak gebruikt door Access Control klanten. 

Een Azure AD-Tenant kan ook federatief zijn voor een of meer exemplaren van on-premises Active Directory via AD FS. Op deze manier kan uw app gebruikers van de Cloud verifiëren en gebruikers die lokaal worden gehost. Het ondersteunt ook het WS-Federation-protocol, waardoor het relatief eenvoudig kan worden geïntegreerd met een webtoepassing met behulp van WIF.

In de volgende tabel worden de functies van Access Control die relevant zijn voor webtoepassingen vergeleken met de functies die beschikbaar zijn in azure AD. 

Op hoog niveau *is Azure Active Directory waarschijnlijk de beste keuze voor uw migratie als u gebruikers alleen kunt aanmelden met hun micro soft-werk-of school account*.

| Mogelijkheid | Ondersteuning voor Access Control | Ondersteuning voor Azure AD |
| ---------- | ----------- | ---------------- |
| **Typen accounts** | | |
| Werk-of school accounts van micro soft | Ondersteund | Ondersteund |
| Accounts van Windows Server Active Directory en AD FS |-Ondersteund via federatie met een Azure AD-Tenant <br />-Ondersteund via directe Federatie met AD FS | Alleen ondersteund via federatie met een Azure AD-Tenant | 
| Accounts van andere bedrijfsidentiteits beheersystemen |-Mogelijk via federatie met een Azure AD-Tenant <br />-Ondersteund via directe Federatie | Mogelijk via federatie met een Azure AD-Tenant |
| Micro soft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund via het OAuth-Protocol van Azure AD v 2.0, maar niet via andere protocollen | 
| Facebook-, Google-, Yahoo-accounts | Ondersteund | Niet ondersteund |
| **Protocollen en SDK-compatibiliteit** | | |
| WIF | Ondersteund | Ondersteund, maar er zijn beperkte instructies beschikbaar |
| Webservices-federatie | Ondersteund | Ondersteund |
| OAuth 2.0 | Ondersteuning voor concept 13 | Ondersteuning voor RFC 6749, de meest moderne specificatie |
| WS-Trust | Ondersteund | Niet ondersteund |
| **Token indelingen** | | |
| JWT | Ondersteund in bèta | Ondersteund |
| SAML 1,1 | Ondersteund | Preview |
| SAML 2.0 | Ondersteund | Ondersteund |
| SWT | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpas bare thuis realm detectie/account-gebruikers interface voor orderverzamelen | Download bare code die kan worden opgenomen in apps | Niet ondersteund |
| Aangepaste certificaten voor token-ondertekening uploaden | Ondersteund | Ondersteund |
| Claims in tokens aanpassen |-Invoer claims door geven van id-providers<br />-Toegangs token van ID-provider als claim ophalen<br />-Uitvoer claims uitgeven op basis van de waarden van invoer claims<br />-Uitvoer claims met constante waarden uitgeven |-Kan geen claims door geven van federatieve id-providers<br />-Kan geen toegangs Token ophalen van de identiteits provider als een claim<br />-Kan geen uitvoer claims uitgeven op basis van de waarden van invoer claims<br />-Kan uitvoer claims verzenden met constante waarden<br />-Kan uitvoer claims uitgeven op basis van eigenschappen van gebruikers die zijn gesynchroniseerd met Azure AD |
| **Automation** | | |
| Configuratie-en beheer taken automatiseren | Ondersteund via Access Control Management-service | Ondersteund met behulp van de Microsoft Graph-API |

Als u besluit dat Azure AD het beste migratie traject is voor uw toepassingen en services, moet u rekening houden met twee manieren om uw app te integreren met Azure AD.

Als u WS-Federation of WIF wilt gebruiken om te integreren met Azure AD, kunt u het beste de aanpak volgen die wordt beschreven in [federatieve eenmalige aanmelding configureren voor een toepassing buiten de galerie](../manage-apps/configure-saml-single-sign-on.md). Het artikel verwijst naar het configureren van Azure AD voor op SAML gebaseerde eenmalige aanmelding, maar is ook geschikt voor het configureren van WS-Federation. Als u deze aanpak volgt, is een Azure AD Premium-licentie vereist. Deze aanpak heeft twee voor delen:

- U profiteert van de volledige flexibiliteit van het aanpassen van Azure AD-tokens. U kunt de claims die zijn uitgegeven door Azure AD, aanpassen om te voldoen aan de claims die worden uitgegeven door Access Control. Dit omvat met name de claim van de gebruikers-ID of de naam-id. Als u consistente gebruikers-Id's voor uw gebruikers wilt blijven ontvangen nadat u de technologieën hebt gewijzigd, moet u ervoor zorgen dat de gebruikers-Id's die zijn uitgegeven door Azure AD overeenkomen met die van Access Control.
- U kunt een certificaat voor token-ondertekening configureren dat specifiek is voor uw toepassing en met een levens duur die u beheert.

> [!NOTE]
> Voor deze benadering is een Azure AD Premium-licentie vereist. Als u een Access Control klant bent en u een Premium-licentie nodig hebt voor het instellen van eenmalige aanmelding voor een toepassing, neemt u contact met ons op. We zullen graag Ontwikkel licenties bieden die u kunt gebruiken.

Een alternatieve methode is het volgen van [Dit code voorbeeld](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), waardoor er enigszins verschillende instructies worden geboden voor het instellen van WS-Federation. Dit code voorbeeld maakt geen gebruik van WIF, maar in plaats daarvan de ASP.NET 4,5 OWIN middleware. De instructies voor app-registratie zijn echter geldig voor apps die gebruikmaken van WIF. u hebt geen Azure AD Premium-licentie nodig. 

Als u deze aanpak kiest, moet u inzicht krijgen [in de rollover van de handtekening sleutel in azure AD](../develop/active-directory-signing-key-rollover.md). Deze benadering maakt gebruik van de globale Azure AD-handtekening sleutel voor het uitgeven van tokens. WIF wordt standaard niet automatisch vernieuwd. Wanneer Azure AD de globale ondertekeningssleutel draait, moet uw WIF-implementatie worden voor bereid om de wijzigingen te accepteren. Zie [belang rijke informatie over de rollover van de handtekening sleutel in azure AD](/previous-versions/azure/dn641920(v=azure.100))voor meer informatie.

Als u kunt integreren met Azure AD via de OpenID Connect Connect-of OAuth-protocollen, wordt u aangeraden dit te doen. We hebben uitgebreide documentatie en richt lijnen over hoe u Azure AD integreert in uw webtoepassing die beschikbaar is in onze [Azure AD-ontwikkelaars handleiding](../develop/index.yml).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migreren naar Azure Active Directory B2C

Het andere migratie traject dat moet worden overwogen, is Azure AD B2C. Azure AD B2C is een Cloud verificatie service waarmee ontwikkel aars hun verificatie-en identiteits beheer logica kunnen uitbesteden aan een Cloud service, zoals Access Control. Het is een betaalde service (met gratis en Premium-lagen) die is ontworpen voor consument gerichte toepassingen die mogelijk Maxi maal miljoenen actieve gebruikers hebben.

Net als Access Control, een van de meest aantrekkelijke functies van Azure AD B2C is dat deze veel verschillende soorten accounts ondersteunt. Met Azure AD B2C kunt u zich aanmelden bij gebruikers met behulp van hun Microsoft-account of Facebook-, Google-, LinkedIn-, GitHub-of Yahoo-accounts. Azure AD B2C biedt ook ondersteuning voor "lokale accounts" of gebruikers naam en wacht woorden die gebruikers specifiek voor uw toepassing maken. Azure AD B2C biedt ook uitgebreide uitbreid baarheid die u kunt gebruiken om uw aanmeldings stromen aan te passen. 

Azure AD B2C biedt echter geen ondersteuning voor de breedte van verificatie protocollen en-Token indelingen die Access Control klanten mogelijk vereist. U kunt Azure AD B2C ook niet gebruiken voor het ophalen van tokens en query's voor aanvullende informatie over de gebruiker van de ID-provider, micro soft of anderszins.

In de volgende tabel worden de functies van Access Control vergeleken die relevant zijn voor webtoepassingen met die beschikbaar zijn in Azure AD B2C. Op hoog niveau *is Azure AD B2C waarschijnlijk de juiste keuze voor uw migratie als uw toepassing is gericht op de consument, of als het veel verschillende soorten accounts ondersteunt.*

| Mogelijkheid | Ondersteuning voor Access Control | Ondersteuning voor Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typen accounts** | | |
| Werk-of school accounts van micro soft | Ondersteund | Ondersteund via aangepast beleid  |
| Accounts van Windows Server Active Directory en AD FS | Ondersteund via directe Federatie met AD FS | Ondersteund via SAML Federation door gebruik te maken van aangepaste beleids regels |
| Accounts van andere bedrijfsidentiteits beheersystemen | Ondersteund via directe Federatie via WS-Federation | Ondersteund via SAML Federation door gebruik te maken van aangepaste beleids regels |
| Micro soft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund | 
| Facebook-, Google-, Yahoo-accounts | Ondersteund | Facebook en Google worden systeem eigen ondersteund, Yahoo ondersteund via OpenID Connect Connect Federatie met aangepaste beleids regels |
| **Protocollen en SDK-compatibiliteit** | | |
| Windows Identity Foundation (WIF) | Ondersteund | Niet ondersteund |
| Webservices-federatie | Ondersteund | Niet ondersteund |
| OAuth 2.0 | Ondersteuning voor concept 13 | Ondersteuning voor RFC 6749, de meest moderne specificatie |
| WS-Trust | Ondersteund | Niet ondersteund |
| **Token indelingen** | | |
| JWT | Ondersteund in bèta | Ondersteund |
| SAML 1,1 | Ondersteund | Niet ondersteund |
| SAML 2.0 | Ondersteund | Niet ondersteund |
| SWT | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpas bare thuis realm detectie/account-gebruikers interface voor orderverzamelen | Download bare code die kan worden opgenomen in apps | Volledig aanpas bare gebruikers interface via aangepaste CSS |
| Aangepaste certificaten voor token-ondertekening uploaden | Ondersteund | Aangepaste handtekening sleutels, geen certificaten, ondersteund via aangepast beleid |
| Claims in tokens aanpassen |-Invoer claims door geven van id-providers<br />-Toegangs token van ID-provider als claim ophalen<br />-Uitvoer claims uitgeven op basis van de waarden van invoer claims<br />-Uitvoer claims met constante waarden uitgeven |-Kan claims van id-providers passeren; aangepast beleid dat is vereist voor sommige claims<br />-Kan geen toegangs Token ophalen van de identiteits provider als een claim<br />-Kan uitvoer claims uitgeven op basis van de waarden van invoer claims via aangepaste beleids regels<br />-Kan uitvoer claims met constante waarden uitgeven via aangepaste beleids regels |
| **Automation** | | |
| Configuratie-en beheer taken automatiseren | Ondersteund via Access Control Management-service |-Maken van gebruikers die zijn toegestaan met de Microsoft Graph-API<br />-Kan geen B2C-tenants,-toepassingen of-beleid maken via een programma |

Als u besluit dat Azure AD B2C het beste migratie traject is voor uw toepassingen en services, begint u met de volgende bronnen:

- [Documentatie over Azure AD B2C](../../active-directory-b2c/overview.md)
- [Aangepast Azure AD B2C-beleid](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar ping-identiteit of Auth0

In sommige gevallen kan het voor komen dat Azure AD en Azure AD B2C niet voldoende zijn om Access Control te vervangen in uw webtoepassingen zonder dat belang rijke code wijzigingen worden aangebracht. Enkele algemene voor beelden hiervan zijn:

- Webtoepassingen die gebruikmaken van WIF of WS-Federation voor aanmelding met sociale id-providers zoals Google of Facebook.
- Webtoepassingen die direct Federatie uitvoeren op een ondernemings-id-provider via het WS-Federation-protocol.
- Webtoepassingen die het toegangs token vereisen dat is uitgegeven door een sociale ID-provider (zoals Google of Facebook) als een claim in de tokens die zijn uitgegeven door Access Control.
- Webtoepassingen met complexe token transformatie regels die niet kunnen worden gereproduceerd door Azure AD of Azure AD B2C.
- Webtoepassingen voor meerdere tenants die ACS gebruiken om de Federatie centraal te beheren voor veel verschillende id-providers

In dergelijke gevallen kunt u overwegen om uw webtoepassing te migreren naar een andere Cloud Authentication service. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt vergelijk bare mogelijkheden als Access Control:

![In deze afbeelding wordt het Auth0-logo weer gegeven](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) is een flexibele cloud-identiteits service die [voor klanten van Access Control op hoog niveau migratie richtlijnen](https://auth0.com/acs)heeft gemaakt en die bijna elke functie ondersteunt die ACS ondersteunt.

![Deze afbeelding toont het ping Identity-logo](./media/active-directory-acs-migration/rsz-ping.png)

De [ping-identiteit](https://www.pingidentity.com) biedt twee oplossingen die vergelijkbaar zijn met ACS. PingOne is een Cloud-identiteits service die veel van de functies van ACS ondersteunt, en PingFederate is een soort gelijke oplossing voor het identiteits product dat meer flexibiliteit biedt. Raadpleeg voor meer informatie over [het gebruik van](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) deze producten.

Onze doel stellingen voor het werken met ping Identity en Auth0 is ervoor te zorgen dat alle Access Control klanten over een migratie traject beschikken voor hun apps en services die de hoeveelheid werk die nodig is om van Access Control te verplaatsen, minimaliseert.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webservices die gebruikmaken van actieve verificatie

Voor webservices die zijn beveiligd met tokens die zijn uitgegeven door Access Control, Access Control biedt de volgende functies en mogelijkheden:

- De mogelijkheid om een of meer *service-identiteiten* in uw Access Control naam ruimte te registreren. Service-identiteiten kunnen worden gebruikt om tokens aan te vragen.
- Ondersteuning voor de OAuth-TERUGLOOP en OAuth 2,0 Draft 13-protocollen voor het aanvragen van tokens, door gebruik te maken van de volgende typen referenties:
    - Een eenvoudig wacht woord dat is gemaakt voor de service-identiteit
    - Een ondertekende SWT met behulp van een symmetrische sleutel of een x509-certificaat
    - Een SAML-token dat is uitgegeven door een vertrouwde id-provider (meestal een AD FS-exemplaar)
- Ondersteuning voor de volgende token indelingen: JWT, SAML 1,1, SAML 2,0 en SWT.
- Eenvoudige token transformatie regels.

Service-identiteiten in Access Control worden doorgaans gebruikt voor het implementeren van server-naar-Server-verificatie. 

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

Onze aanbeveling voor dit type verificatie stroom is om te migreren naar [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD is de Cloud-ID-provider voor werk-of school accounts van micro soft. Azure AD is de ID-provider voor Microsoft 365, Azure en nog veel meer. 

U kunt ook Azure AD gebruiken voor server-naar-Server-verificatie met behulp van de Azure AD-implementatie van de OAuth-client referenties toewijzen. De volgende tabel vergelijkt de mogelijkheden van Access Control in server-naar-Server-verificatie met de functies die beschikbaar zijn in azure AD.

| Mogelijkheid | Ondersteuning voor Access Control | Ondersteuning voor Azure AD |
| ---------- | ----------- | ---------------- |
| Een webservice registreren | Een Relying Party maken in de Access Control Beheer Portal | Een Azure AD-webtoepassing maken in de Azure Portal |
| Een client registreren | Een service-identiteit maken in Access Control Beheer Portal | Een andere Azure AD-webtoepassing maken in de Azure Portal |
| Gebruikte protocol |-OAuth-TERUGLOOP Protocol<br />-OAuth 2,0 Draft 13-client referenties toewijzen | Referenties voor OAuth 2.0-client verlenen |
| Client verificatie methoden |-Eenvoudig wacht woord<br />-Ondertekende SWT<br />-SAML-token van een federatieve id-provider |-Eenvoudig wacht woord<br />-Ondertekende JWT |
| Token indelingen |-JWT<br />-SAML 1,1<br />-SAML 2,0<br />-SWT<br /> | Alleen JWT |
| Token transformatie |-Aangepaste claims toevoegen<br />-Eenvoudig als-dan claim de uitgifte logica | Aangepaste claims toevoegen | 
| Configuratie-en beheer taken automatiseren | Ondersteund via Access Control Management-service | Ondersteund met behulp van de Microsoft Graph-API |

Raadpleeg de volgende bronnen voor hulp bij het implementeren van server-naar-server-scenario's:

- Service-to-service-sectie van de [hand leiding voor Azure AD-ontwikkel aars](../develop/index.yml)
- [Voor beeld van een daemon-code met eenvoudige wacht woord-client referenties](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Voor beeld van een daemon-code met behulp van de certificaat-client referenties](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar ping-identiteit of Auth0

In sommige gevallen kan het voor komen dat de Azure AD-client referenties en de OAuth-toekennings implementatie niet voldoende zijn om Access Control in uw architectuur te vervangen zonder dat de belangrijkste code wijzigingen worden aangebracht. Enkele algemene voor beelden hiervan zijn:

- Server-naar-Server-verificatie met andere token indelingen dan JWTs.
- Server-naar-Server-verificatie met behulp van een invoer token van een externe ID-provider.
- Server-naar-Server-verificatie met token transformatie regels die Azure AD niet kan reproduceren.

In dergelijke gevallen kunt u overwegen om uw webtoepassing te migreren naar een andere Cloud Authentication service. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt vergelijk bare mogelijkheden als Access Control:

![In deze afbeelding wordt het Auth0-logo weer gegeven](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) is een flexibele cloud-identiteits service die [voor klanten van Access Control op hoog niveau migratie richtlijnen](https://auth0.com/acs)heeft gemaakt en die bijna elke functie ondersteunt die ACS ondersteunt.

![Deze afbeelding toont het ping Identity-logo ](./media/active-directory-acs-migration/rsz-ping.png)
 [ping identiteit](https://www.pingidentity.com) biedt twee oplossingen die VERGELIJKBAAR zijn met ACS. PingOne is een Cloud-identiteits service die veel van de functies van ACS ondersteunt, en PingFederate is een soort gelijke oplossing voor het identiteits product dat meer flexibiliteit biedt. Raadpleeg voor meer informatie over [het gebruik van](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) deze producten.

Onze doel stellingen voor het werken met ping Identity en Auth0 is ervoor te zorgen dat alle Access Control klanten over een migratie traject beschikken voor hun apps en services die de hoeveelheid werk die nodig is om van Access Control te verplaatsen, minimaliseert.

#### <a name="passthrough-authentication"></a>Passthrough-verificatie

Voor Passthrough-verificatie met wille keurige token transformatie is er geen equivalente micro soft-technologie aan ACS. Als dat niet het geval is, is Auth0 mogelijk degene die de dichtstbijzijnde oplossing biedt.

## <a name="questions-concerns-and-feedback"></a>Vragen, aandachtspunten en feedback

We begrijpen dat veel Access Control-klanten geen gewist migratie traject vinden na het lezen van dit artikel. Mogelijk hebt u hulp of richt lijnen nodig bij het bepalen van het juiste abonnement. Als u uw migratie scenario's en vragen wilt bespreken, laat u een opmerking op deze pagina staan.