---
title: Meer informatie over Azure AD-toepassingsproxy-connectors | Microsoft Docs
description: Meer informatie over de Azure AD-toepassingsproxy-connectors.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f57d390ed71cb4e0e76972e02170afde7b13e4ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253456"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Azure AD-toepassingsproxy-connectors begrijpen

Connectors zijn wat Azure AD-toepassingsproxy mogelijk maakt. Ze zijn eenvoudig, eenvoudig te implementeren en te onderhouden, en zeer krachtig. In dit artikel wordt beschreven welke connectors zijn, hoe ze werken en enkele suggesties voor het optimaliseren van uw implementatie.

## <a name="what-is-an-application-proxy-connector"></a>Wat is een Application proxy-connector?

Connectors zijn lichte agents die on-premises zijn en de uitgaande verbinding met de Application proxy-service vergemakkelijken. Connectors moeten worden geïnstalleerd op een Windows-Server die toegang heeft tot de back-end-toepassing. U kunt connectors in connector groepen indelen, waarbij elke groep verkeer naar specifieke toepassingen verwerkt. Zie [Azure AD-toepassingsproxy gebruiken voor het publiceren van on-premises apps voor externe gebruikers](what-is-application-proxy.md#application-proxy-connectors) voor meer informatie over toepassings proxy en een diagram representatie van de toepassings proxy architectuur.

## <a name="requirements-and-deployment"></a>Vereisten en implementatie

Als u de toepassings proxy wilt implementeren, hebt u ten minste één connector nodig, maar we raden u aan twee of meer te gebruiken voor meer flexibiliteit. Installeer de connector op een computer met Windows Server 2012 R2 of hoger. De connector moet communiceren met de service toepassings proxy en de on-premises toepassingen die u publiceert.

### <a name="windows-server"></a>Windows-server
U hebt een server met Windows Server 2012 R2 of later nodig waarop u de Application proxy-connector kunt installeren. De server moet verbinding maken met de toepassings proxy services in Azure en de on-premises toepassingen die u publiceert.

Voor de installatie van de toepassings proxy connector moet TLS 1,2 zijn ingeschakeld op de Windows-Server. TLS 1,2 inschakelen op de server:

1. Stel de volgende registersleutels in:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw

Zie [aan de slag met toepassings proxy en een connector installeren](application-proxy-add-on-premises-application.md)voor meer informatie over de netwerk vereisten voor de connector server.

## <a name="maintenance"></a>Onderhoud

De connectors en de service zorgen voor alle taken met hoge Beschik baarheid. Ze kunnen dynamisch worden toegevoegd of verwijderd. Telkens wanneer een nieuwe aanvraag arriveert, wordt deze doorgestuurd naar een van de connectors die op dit moment beschikbaar zijn. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer.

De connectors zijn stateless en hebben geen configuratie gegevens op de computer. De enige gegevens die worden opgeslagen, zijn de instellingen voor het verbinden van de service en het verificatie certificaat. Wanneer ze verbinding maken met de service, worden alle vereiste configuratie gegevens opgehaald en worden ze elke paar minuten vernieuwd.

Connectors vragen de server ook om te bepalen of er een nieuwere versie van de connector is. Als er een wordt gevonden, worden de connectors zelf bijgewerkt.

U kunt uw connectors bewaken vanaf de computer waarop ze worden uitgevoerd, met behulp van het gebeurtenis logboek en de prestatie meter items. U kunt ook hun status weer geven op de pagina Toepassings proxy van de Azure Portal:

![Voor beeld: Azure AD-toepassingsproxy-connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

U hoeft geen connectors hand matig te verwijderen die niet worden gebruikt. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Ongebruikte connectors zijn gelabeld als _inactief_ en worden verwijderd na 10 dagen van inactiviteit. Als u een connector wilt verwijderen, verwijdert u de connector service en de updater-service van de-server. Start de computer opnieuw op om de service volledig te verwijderen.

## <a name="automatic-updates"></a>Automatische updates

Azure AD biedt automatische updates voor alle connectors die u implementeert. Zolang de toepassings proxy Connector Updater-service wordt uitgevoerd, worden uw connectors automatisch bijgewerkt. Als de Connector Updater-Service niet wordt weer geven op uw server, moet u [de connector opnieuw installeren](application-proxy-add-on-premises-application.md) om eventuele updates op te halen.

Als u niet wilt wachten totdat een automatische update naar uw connector komt, kunt u een hand matige upgrade uitvoeren. Ga naar de [Download pagina](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) voor de connector op de server waarop de connector zich bevindt en selecteer **downloaden**. Dit proces start een upgrade voor de lokale connector.

Voor tenants met meerdere connectors, worden de automatische updates per één connector in elke groep uitgevoerd om uitvaltijd in uw omgeving te voorkomen.

U kunt uitval tijd ondervinden wanneer de connector wordt bijgewerkt als:
  
- U hebt slechts één connector aanbevolen om een tweede connector te installeren en [een connector groep te maken](application-proxy-connector-groups.md). Hierdoor wordt uitval tijd voor komen en wordt er een hogere Beschik baarheid geboden.  
- Een connector bevindt zich in het midden van een trans actie toen de update begon. Hoewel de eerste trans actie verloren gaat, moet de browser de bewerking automatisch opnieuw uitvoeren of u kunt uw pagina vernieuwen. Wanneer de aanvraag opnieuw wordt verzonden, wordt het verkeer doorgestuurd naar een back-upconnector.

Zie [toepassings proxy-versie geschiedenis](application-proxy-release-version-history.md)van de release voor meer informatie over eerder uitgebrachte versies en de wijzigingen die ze bevatten.

## <a name="creating-connector-groups"></a>Connector groepen maken

Met connector groepen kunt u specifieke connectors toewijzen voor het leveren van specifieke toepassingen. U kunt een aantal connectors samen groeperen en vervolgens elke toepassing aan een groep toewijzen.

Met connector groepen kunt u eenvoudig grote implementaties beheren. Ze verbeteren ook de latentie voor tenants met toepassingen die worden gehost in verschillende regio's, omdat u op locatie gebaseerde connector groepen kunt maken voor alleen lokale toepassingen.

Zie [toepassingen op verschillende netwerken en locaties publiceren met connector groepen](application-proxy-connector-groups.md)voor meer informatie over connector groepen.

## <a name="capacity-planning"></a>Capaciteitsplanning

Het is belang rijk om ervoor te zorgen dat u voldoende capaciteit hebt gepland tussen connectors om het verwachte verkeers volume af te handelen. U wordt aangeraden elke connector groep ten minste twee connectors te bieden voor hoge Beschik baarheid en schaal. Wanneer u drie connectors hebt, is het mogelijk dat u op elk gewenst moment een machine moet onderhouden.

Over het algemeen, hoe meer gebruikers u hebt, hoe groter de computer die u nodig hebt. Hieronder volgt een tabel met een overzicht van het volume en de verwachte latentie die verschillende computers kunnen verwerken. Houd er rekening mee dat deze allemaal is gebaseerd op de verwachte trans acties per seconde (TPS) in plaats van door de gebruiker omdat de gebruiks patronen variëren en niet kunnen worden gebruikt om de belasting te voors pellen. Er zijn ook enkele verschillen op basis van de grootte van de reacties en de reactie tijd van de back-end-toepassing-grotere antwoord groottes en tragere reactie tijden, waardoor de maximale TPS wordt berekend. We raden u ook aan extra machines te plaatsen, zodat de gedistribueerde belasting over de machines altijd een ruimere buffer biedt. De extra capaciteit zorgt ervoor dat u beschikt over hoge Beschik baarheid en tolerantie.

|Kernen|RAM|Verwachte latentie (MS)-P99|Maximum aantal TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Op deze computer is een aangepaste instelling gebruikt om een aantal van de standaard verbindings limieten van meer dan .NET aanbevolen instellingen te verhogen. Het is raadzaam om een test uit te voeren met de standaard instellingen voordat u contact opneemt met ondersteuning om deze limiet voor uw Tenant te wijzigen.

> [!NOTE]
> Er is niet veel verschil in de maximale TPS tussen computers met 4, 8 en 16 kernen. Het belangrijkste verschil tussen deze bevindt zich in de verwachte latentie.
>
> Deze tabel is ook gericht op de verwachte prestaties van een connector op basis van het type computer waarop het is geïnstalleerd. Dit is gescheiden van de beperkings limieten voor de toepassings proxy service, Zie [service limieten en beperkingen](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Beveiliging en netwerken

Connectors kunnen overal in het netwerk worden geïnstalleerd waarmee ze aanvragen kunnen verzenden naar de service Application proxy. Belang rijk is dat de computer waarop de connector wordt uitgevoerd ook toegang heeft tot uw apps. U kunt connectors installeren in uw bedrijfs netwerk of op een virtuele machine die wordt uitgevoerd in de Cloud. Connectors kunnen worden uitgevoerd binnen een perimeter netwerk, ook wel bekend als een gedemilitariseerde zone (DMZ), maar dit is niet nodig omdat al het verkeer uitgaand is, zodat uw netwerk beveiligd blijft.

Connectors verzenden alleen uitgaande aanvragen. Het uitgaande verkeer wordt verzonden naar de service toepassings proxy en naar de gepubliceerde toepassingen. U hoeft geen binnenkomende poorten te openen omdat verkeer op beide manieren verloopt zodra een sessie tot stand is gebracht. U hoeft ook geen inkomende toegang via uw firewalls te configureren.

Zie [werken met bestaande on-premises proxy servers](application-proxy-configure-connectors-with-proxy-servers.md)voor meer informatie over het configureren van uitgaande firewall regels.

## <a name="performance-and-scalability"></a>Prestaties en schaalbaarheid

De schaal voor de toepassings proxy service is transparant, maar schalen is een factor voor connectors. U moet over voldoende connectors beschikken om het piek verkeer te kunnen afhandelen. Omdat connectors stateless zijn, worden ze niet beïnvloed door het aantal gebruikers of sessies. In plaats daarvan reageren ze op het aantal aanvragen en de grootte van de payload. Met het standaard webverkeer kan een gemiddelde computer een aantal duizenden aanvragen per seconde verwerken. De specifieke capaciteit is afhankelijk van de exacte computer kenmerken.

De prestaties van de connector zijn afhankelijk van de CPU en het netwerk. CPU-prestaties zijn vereist voor TLS-versleuteling en-ontsleuteling, terwijl netwerken belang rijk zijn om snelle connectiviteit met de toepassingen en de online service in azure te krijgen.

Het geheugen is daarentegen minder van een probleem voor connectors. De online service zorgt voor een groot deel van de verwerking en alle niet-geverifieerde verkeer. Alles wat u kunt doen in de Cloud wordt uitgevoerd in de Cloud.

Als een connector of machine niet beschikbaar is, gaat het verkeer naar een andere connector in de groep. Deze tolerantie is ook een aanbeveling voor het gebruik van meerdere connectors.

Een andere factor die van invloed is op de prestaties is de kwaliteit van het netwerk tussen de connectors, waaronder:

- **De online service**: trage of hoge latentie verbindingen met de Application proxy-service in azure beïnvloeden de prestaties van de connector. Verbind uw organisatie met Azure met Express route voor de beste prestaties. Als dat niet het geval is, moet u ervoor zorgen dat het netwerk team zo efficiënt mogelijk verbindingen met Azure verwerkt.
- **De back-end-toepassingen**: in sommige gevallen zijn er extra proxy's tussen de connector en de back-end-toepassingen die verbindingen kunnen vertragen of verhinderen. Als u dit scenario wilt oplossen, opent u een browser van de connector server en probeert u toegang tot de toepassing te krijgen. Als u de connectors in azure uitvoert, maar de toepassingen on-premises zijn, is de ervaring mogelijk niet wat uw gebruikers verwachten.
- **De domein controllers**: als de connectors eenmalige aanmelding (SSO) uitvoeren met beperkte Kerberos-delegering, nemen ze contact op met de domein controllers voordat ze de aanvraag verzenden naar de back-end. De connectors hebben een cache van Kerberos-tickets, maar in een omgeving die bezet is, kan de reactie snelheid van de domein controllers invloed hebben op de prestaties. Dit probleem komt vaker voor bij connectors die worden uitgevoerd in azure, maar communiceren met domein controllers die on-premises zijn.

Zie [overwegingen voor netwerk topologie bij het gebruik van Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md)voor meer informatie over het optimaliseren van uw netwerk.

## <a name="domain-joining"></a>Domein toevoegen

Connectors kunnen worden uitgevoerd op een computer die geen lid is van een domein. Als u echter eenmalige aanmelding (SSO) wilt gebruiken voor toepassingen die gebruikmaken van geïntegreerde Windows-authenticatie (IWA), hebt u een computer die lid is van een domein. In dit geval moeten de connector computers lid zijn van een domein dat beperkte [Kerberos](https://web.mit.edu/kerberos) -delegering kan uitvoeren namens de gebruikers voor de gepubliceerde toepassingen.

Connectors kunnen ook worden gekoppeld aan domeinen in forests met een gedeeltelijk vertrouwen of op alleen-lezen domein controllers.

## <a name="connector-deployments-on-hardened-environments"></a>Connector implementaties in beveiligde omgevingen

Normaal gesp roken is de implementatie van een connector eenvoudig en is er geen speciale configuratie vereist. Er zijn echter enkele unieke voor waarden die moeten worden overwogen:

- Organisaties die het uitgaande verkeer beperken, moeten de [vereiste poorten openen](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS-compatibele computers kunnen verplicht zijn om hun configuratie te wijzigen zodat de connector-processen een certificaat kunnen genereren en opslaan.
- Organisaties die hun omgeving vergren delen op basis van de processen die de netwerk aanvragen uitgeven, moeten ervoor zorgen dat beide connector Services zijn ingeschakeld voor toegang tot alle vereiste poorten en IP-adressen.
- In sommige gevallen kunnen uitgaande doorstuur servers de verificatie met twee richtings certificaten opheffen, waardoor de communicatie mislukt.

## <a name="connector-authentication"></a>Connector verificatie

Om een beveiligde service te bieden, moeten connectors zich bij de service verifiëren en moet de service zich bij de connector verifiëren. Deze verificatie wordt uitgevoerd met behulp van client-en server certificaten wanneer de connectors de verbinding starten. Op deze manier worden de gebruikers naam en het wacht woord van de beheerder niet opgeslagen op de computer van de connector.

De gebruikte certificaten zijn specifiek voor de Application proxy-service. Ze worden tijdens de eerste registratie gemaakt en worden elke paar maanden automatisch vernieuwd door de connectors.

Na het eerste geslaagde certificaat is het vernieuwen van de Azure AD-toepassingsproxy connector-service (netwerk service) niet gemachtigd om het oude certificaat uit het archief van de lokale computer te verwijderen. Als het certificaat is verlopen of niet meer door de service wordt gebruikt, kunt u het veilig verwijderen.

Als u problemen met het vernieuwen van het certificaat wilt voor komen, moet u ervoor zorgen dat de netwerk communicatie van de connector op de [gedocumenteerde locaties](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) is ingeschakeld.

Als een connector gedurende enkele maanden niet is verbonden met de service, zijn de certificaten mogelijk verouderd. In dit geval moet u de connector verwijderen en opnieuw installeren om de registratie te activeren. U kunt de volgende Power shell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Gebruik voor Government `-EnvironmentName "AzureUSGovernment"` . Zie [agent installeren voor de Azure Government-Cloud](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)voor meer informatie.

Voor meer informatie over het controleren van het certificaat en het oplossen van problemen raadpleegt u de [ondersteuning van machine-en back-endservers voor het vertrouwens certificaat van de toepassings proxy](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Onderhuids

Connectors zijn gebaseerd op Windows Server Web Application proxy, zodat ze de meeste beheer hulpprogramma's, waaronder Windows-gebeurtenis logboeken, hebben.

![Gebeurtenis logboeken beheren met de Logboeken](./media/application-proxy-connectors/event-view-window.png)

en Windows-prestatie meter items.

![Tellers toevoegen aan de connector met de prestatie meter](./media/application-proxy-connectors/performance-monitor.png)

De connectors hebben zowel **beheer** -als **sessie** Logboeken. Het logboek **beheerder** bevat belang rijke gebeurtenissen en fouten. Het **sessie** logboek bevat alle trans acties en de bijbehorende verwerkings gegevens.

Als u de logboeken wilt weer geven, opent u **Logboeken** en gaat u naar **toepassingen en services**  >  **wordt de micro soft**  >  **AadApplicationProxy**-  >  **connector** geregistreerd. Als u het **sessie** logboek zichtbaar wilt maken, selecteert u in het menu **beeld** de optie **analyse logboeken en fout opsporing weer geven**. Het **sessie** logboek wordt doorgaans gebruikt voor het oplossen van problemen en is standaard uitgeschakeld. Schakel deze optie in om het verzamelen van gebeurtenissen te starten en deze uit te scha kelen wanneer deze niet meer nodig is.

U kunt de status van de service controleren in het venster Services. De connector bestaat uit twee Windows-Services: de daad werkelijke connector en de Updater. Beide moeten beide tijd worden uitgevoerd.

 ![Voor beeld: het venster Services met de lokale Azure AD-Services](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren op afzonderlijke netwerken en locaties met connector groepen](application-proxy-connector-groups.md)
- [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
- [Problemen met toepassings proxy en connector fouten oplossen](application-proxy-troubleshoot.md)
- [De Azure AD-toepassingsproxy-connector op de achtergrond installeren](application-proxy-register-connector-powershell.md)
