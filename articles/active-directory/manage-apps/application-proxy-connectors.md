---
title: Inzicht in azure AD toepassingsproxy-connectors | Microsoft Docs
description: Meer informatie over de Azure AD toepassingsproxy-connectors.
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
ms.openlocfilehash: 6b54e3cb3b4ce7eb8f541755df75e8d6a22eb7c2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575306"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Informatie over Azure AD Application Proxy-connectors

Connectors maken Azure AD-toepassingsproxy mogelijk. Ze zijn eenvoudig, eenvoudig te implementeren en te onderhouden en super krachtig. In dit artikel wordt beschreven wat connectors zijn, hoe ze werken en enkele suggesties voor het optimaliseren van uw implementatie.

## <a name="what-is-an-application-proxy-connector"></a>Wat is een toepassingsproxy-connector?

Connectors zijn lichtgewicht agents die on-premises aanwezig zijn en de uitgaande verbinding met de toepassingsproxy vergemakkelijken. Connectors moeten worden geïnstalleerd op een Windows-server die toegang heeft tot de back-endtoepassing. U kunt connectors organiseren in connectorgroepen, met elke groep die verkeer naar specifieke toepassingen afhandelt. Zie Using Azure AD toepassingsproxy to [publish on-premises apps for](what-is-application-proxy.md#application-proxy-connectors) remote users (Azure AD-toepassingsproxy gebruiken om on-premises apps te publiceren voor externe gebruikers) voor meer informatie over de toepassingsproxy en een diagramweergave van de toepassingsproxyarchitectuur

## <a name="requirements-and-deployment"></a>Vereisten en implementatie

Als u toepassingsproxy implementeert, hebt u ten minste één connector nodig, maar we raden twee of meer aan voor meer tolerantie. Installeer de connector op een computer met Windows Server 2012 R2 of hoger. De connector moet communiceren met de toepassingsproxy service en de on-premises toepassingen die u publiceert.

### <a name="windows-server"></a>Windows-server
U hebt een server met Windows Server 2012 R2 of hoger nodig waarop u de toepassingsproxy installeren. De server moet verbinding maken met de toepassingsproxy-services in Azure en de on-premises toepassingen die u publiceert.

Op Windows Server moet TLS 1.2 zijn ingeschakeld voordat u de connector toepassingsproxy installeren. TLS 1.2 op de server inschakelen:

1. Stel de volgende registersleutels in:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw

Zie Voor meer informatie over de netwerkvereisten voor de connectorserver Aan de slag [toepassingsproxy en een connector installeren.](application-proxy-add-on-premises-application.md)

## <a name="maintenance"></a>Onderhoud

De connectors en de service zorgen voor alle taken voor hoge beschikbaarheid. Ze kunnen dynamisch worden toegevoegd of verwijderd. Telkens als er een nieuwe aanvraag binnenkomt, wordt deze doorgeleid naar een van de connectors die momenteel beschikbaar is. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer.

De connectors zijn staatloos en hebben geen configuratiegegevens op de computer. De enige gegevens die ze opslaan, zijn de instellingen voor het verbinden van de service en het verificatiecertificaat. Wanneer ze verbinding maken met de service, halen ze alle vereiste configuratiegegevens op en vernieuwen ze deze om de paar minuten.

Connectors vragen de server ook om na te gaan of er een nieuwere versie van de connector is. Als er een wordt gevonden, worden de connectors zelf bijgewerkt.

U kunt uw connectors bewaken vanaf de computer waar ze op worden uitgevoerd, met behulp van het gebeurtenislogboek en de prestatiemeters. U kunt de status ook bekijken op de toepassingsproxy pagina van de Azure Portal:

![Voorbeeld: Azure AD toepassingsproxy-connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

U hoeft connectors die niet worden gebruikt, niet handmatig te verwijderen. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Ongebruikte connectors worden gelabeld _als inactief_ en worden na tien dagen inactiviteit verwijderd. Als u echter een connector wilt verwijderen, verwijdert u zowel de Connector-service als de Updater-service van de server. Start de computer opnieuw op om de service volledig te verwijderen.

## <a name="automatic-updates"></a>Automatische updates

Azure AD biedt automatische updates voor alle connectors die u implementeert. Zolang de toepassingsproxy Connector Updater wordt uitgevoerd, worden uw connectors automatisch bijgewerkt met de meest recente belangrijke [connectorversie.](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-) Als u de service Connector Updater server niet ziet, moet u de connector opnieuw [installeren](application-proxy-add-on-premises-application.md) om updates op te halen.

Als u niet wilt wachten op een automatische update voor uw connector, kunt u een handmatige upgrade uitvoeren. Ga naar de [downloadpagina van de connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) op de server waarop de connector zich bevindt en selecteer **Downloaden.** Dit proces start een upgrade voor de lokale connector.

Voor tenants met meerdere connectors, worden de automatische updates per één connector in elke groep uitgevoerd om uitvaltijd in uw omgeving te voorkomen.

Er kan downtime zijn wanneer de connector wordt bijgewerkt als:
  
- U hebt slechts één connector. We raden u aan een tweede connector te installeren en [een connectorgroep te maken.](application-proxy-connector-groups.md) Dit voorkomt uitvaltijd en zorgt voor hogere beschikbaarheid.  
- Een connector was in het midden van een transactie toen de update begon. Hoewel de initiële transactie verloren is gegaan, moet uw browser de bewerking automatisch opnieuw proberen of kunt u de pagina vernieuwen. Wanneer de aanvraag wordt ingediend, wordt het verkeer doorgeleid naar een back-upconnector.

Zie voor informatie over eerder uitgebrachte versies en welke wijzigingen ze bevatten, [toepassingsproxy- versiegeschiedenis.](application-proxy-release-version-history.md)

## <a name="creating-connector-groups"></a>Connectorgroepen maken

Met connectorgroepen kunt u specifieke connectors toewijzen voor specifieke toepassingen. U kunt een aantal connectors groeperen en vervolgens elke toepassing toewijzen aan een groep.

Met connectorgroepen kunt u eenvoudiger grote implementaties beheren. Ze verbeteren ook de latentie voor tenants met toepassingen die worden gehost in verschillende regio's, omdat u op locatie gebaseerde connectorgroepen kunt maken om alleen lokale toepassingen te bedienen.

Zie Toepassingen publiceren op afzonderlijke netwerken en locaties met connectorgroepen voor meer informatie [over connectorgroepen.](application-proxy-connector-groups.md)

## <a name="capacity-planning"></a>Capaciteitsplanning

Het is belangrijk om ervoor te zorgen dat u voldoende capaciteit tussen connectors hebt gepland om het verwachte verkeersvolume te verwerken. We raden u aan dat elke connectorgroep ten minste twee connectors heeft om hoge beschikbaarheid en schaal te bieden. Het gebruik van drie connectors is optimaal voor het geval u een machine op elk moment moet voorzien van service.

Over het algemeen is het zo dat hoe meer gebruikers u hebt, hoe groter de machine die u nodig hebt. Hieronder ziet u een tabel met een overzicht van het volume en de verwachte latentie die verschillende machines kunnen verwerken. Houd er rekening mee dat alles is gebaseerd op verwachte transacties per seconde (TPS) in plaats van door de gebruiker, omdat gebruikspatronen variëren en niet kunnen worden gebruikt om de belasting te voorspellen. Er zijn ook enkele verschillen op basis van de grootte van de antwoorden en de reactietijd van de back-endtoepassing. Grotere antwoordgrootten en tragere reactietijden leiden tot een lagere Max TPS. We raden u ook aan extra machines te gebruiken, zodat de gedistribueerde belasting over de machines altijd een uitgebreide buffer biedt. De extra capaciteit zorgt ervoor dat u hoge beschikbaarheid en tolerantie hebt.

|Kernen|RAM|Verwachte latentie (MS)-P99|Maximum aantal TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Deze computer heeft een aangepaste instelling gebruikt om enkele van de standaardverbindingslimieten te verhogen buiten de aanbevolen .NET-instellingen. We raden u aan om een test met de standaardinstellingen uit te gaan voordat u contact op met de ondersteuning om deze limiet voor uw tenant te wijzigen.

> [!NOTE]
> Er is niet veel verschil in de maximale TPS tussen 4, 8 en 16 kernmachines. Het belangrijkste verschil tussen deze is de verwachte latentie.
>
> Deze tabel is ook gericht op de verwachte prestaties van een connector op basis van het type computer dat erop is geïnstalleerd. Dit staat los van toepassingsproxy beperkingslimieten van de service. Zie [Servicelimieten en -beperkingen.](../enterprise-users/directory-service-limits-restrictions.md)

## <a name="security-and-networking"></a>Beveiliging en netwerken

Connectors kunnen overal in het netwerk worden geïnstalleerd waarmee ze aanvragen kunnen verzenden naar de toepassingsproxy service. Het is belangrijk dat de computer met de connector ook toegang heeft tot uw apps. U kunt connectors installeren in uw bedrijfsnetwerk of op een virtuele machine die wordt uitgevoerd in de cloud. Connectors kunnen worden uitgevoerd binnen een perimeternetwerk, ook wel een gedemilitariseerde zone (DMZ) genoemd, maar dit is niet nodig omdat al het verkeer uitgaand is, zodat uw netwerk veilig blijft.

Connectors verzenden alleen uitgaande aanvragen. Het uitgaande verkeer wordt verzonden naar de toepassingsproxy service en naar de gepubliceerde toepassingen. U hoeft geen binnenkomende poorten te openen omdat verkeer op beide manieren stroomt zodra een sessie tot stand is gebracht. U hoeft ook geen binnenkomende toegang via uw firewalls te configureren.

Zie Werken met bestaande [on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)voor meer informatie over het configureren van uitgaande firewallregels.

## <a name="performance-and-scalability"></a>Prestaties en schaalbaarheid

Schaal voor de toepassingsproxy-service is transparant, maar schaal is een factor voor connectors. U moet voldoende connectors hebben om piekverkeer af te handelen. Omdat connectors staatloos zijn, worden ze niet beïnvloed door het aantal gebruikers of sessies. In plaats daarvan reageren ze op het aantal aanvragen en de grootte van de nettolading. Met standaardwebverkeer kan een gemiddelde machine een paar duizend aanvragen per seconde verwerken. De specifieke capaciteit is afhankelijk van de exacte kenmerken van de machine.

De prestaties van de connector zijn gebonden aan CPU en netwerken. CPU-prestaties zijn nodig voor TLS-versleuteling en -ontsleuteling, terwijl netwerken belangrijk zijn voor een snelle verbinding met de toepassingen en de onlineservice in Azure.

Geheugen is daarentegen minder een probleem voor connectors. De onlineservice zorgt voor een groot deel van de verwerking en al het niet-geautticeerde verkeer. Alles wat u in de cloud kunt doen, gebeurt in de cloud.

Als om een of andere reden de connector of machine niet meer beschikbaar is, gaat het verkeer naar een andere connector in de groep. Deze tolerantie is ook de reden waarom we meerdere connectors adviseren.

Een andere factor die van invloed is op de prestaties is de kwaliteit van het netwerk tussen de connectors, waaronder:

- **De onlineservice:** trage verbindingen of verbindingen met hoge latentie met de toepassingsproxy service in Azure zijn van invloed op de prestaties van de connector. Voor de beste prestaties verbindt u uw organisatie met Azure via Express Route. Zorg er anders voor dat uw netwerkteam ervoor zorgt dat verbindingen met Azure zo efficiënt mogelijk worden verwerkt.
- **De back-endtoepassingen:** In sommige gevallen zijn er aanvullende proxies tussen de connector en de back-endtoepassingen die verbindingen kunnen vertragen of verhinderen. Als u dit scenario wilt oplossen, opent u een browser vanaf de connectorserver en probeert u toegang te krijgen tot de toepassing. Als u de connectors in Azure hebt uitgevoerd, maar de toepassingen on-premises zijn, is de ervaring mogelijk niet wat uw gebruikers verwachten.
- **De domeincontrollers:** als de connectors eenmalige aanmelding (SSO) uitvoeren met behulp van beperkte Kerberos-delegering, nemen ze contact op met de domeincontrollers voordat ze de aanvraag naar de back-end verzenden. De connectors hebben een cache met Kerberos-tickets, maar in een drukke omgeving kan de reactiesnelheid van de domeincontrollers van invloed zijn op de prestaties. Dit probleem komt vaker voor bij connectors die worden uitgevoerd in Azure, maar communiceren met on-premises domeincontrollers.

Zie Overwegingen voor netwerktopologie bij het gebruik van Azure Active Directory toepassingsproxy voor meer informatie [over het optimaliseren Azure Active Directory toepassingsproxy.](application-proxy-network-topology.md)

## <a name="domain-joining"></a>Domein toevoegen

Connectors kunnen worden uitgevoerd op een computer die niet lid is van een domein. Als u echter eenmalige aanmelding (SSO) wilt voor toepassingen die gebruikmaken van Geïntegreerde Windows-verificatie (IWA), hebt u een computer nodig die lid is van een domein. In dit geval moeten de connectormachines worden samengevoegd met een domein dat [beperkte Kerberos-delegering](https://web.mit.edu/kerberos) namens de gebruikers voor de gepubliceerde toepassingen kan uitvoeren.

Connectors kunnen ook worden samengevoegd met domeinen in forests die een gedeeltelijke vertrouwensrelatie hebben of aan alleen-lezen domeincontrollers.

## <a name="connector-deployments-on-hardened-environments"></a>Connectorimplementaties in geharde omgevingen

Meestal is de implementatie van de connector eenvoudig en is er geen speciale configuratie vereist. Er zijn echter enkele unieke voorwaarden die in aanmerking moeten worden genomen:

- Organisaties die het uitgaande verkeer beperken, moeten [vereiste poorten openen.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- FIPS-compatibele machines moeten mogelijk hun configuratie wijzigen, zodat de connectorprocessen een certificaat kunnen genereren en opslaan.
- Organisaties die hun omgeving vergrendelen op basis van de processen die de netwerkaanvragen uitgeven, moeten ervoor zorgen dat beide connectorservices toegang hebben tot alle vereiste poorten en IP's.
- In sommige gevallen kunnen uitgaande forward-proxies de verificatie in twee richting breken en ervoor zorgen dat de communicatie mislukt.

## <a name="connector-authentication"></a>Connectorverificatie

Om een beveiligde service te bieden, moeten connectors worden geverifieerd bij de service en moet de service worden geverifieerd bij de connector. Deze verificatie wordt uitgevoerd met behulp van client- en servercertificaten wanneer de connectors de verbinding starten. Op deze manier worden de gebruikersnaam en het wachtwoord van de beheerder niet opgeslagen op de connectormachine.

De gebruikte certificaten zijn specifiek voor de toepassingsproxy service. Ze worden gemaakt tijdens de eerste registratie en worden om de paar maanden automatisch vernieuwd door de connectors.

Na de eerste geslaagde certificaatvernieuwing heeft de Azure AD toepassingsproxy Connector-service (netwerkservice) geen toestemming om het oude certificaat uit het lokale computeropslag te verwijderen. Als het certificaat is verlopen of niet meer wordt gebruikt door de service, kunt u het veilig verwijderen.

Om problemen met het verlengen van certificaten te voorkomen, moet u ervoor zorgen dat de netwerkcommunicatie van de connector naar de [gedocumenteerde bestemmingen](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) is ingeschakeld.

Als een connector een aantal maanden niet is verbonden met de service, zijn de certificaten mogelijk verouderd. In dit geval verwijdert en installeert u de connector opnieuw om registratie te activeren. U kunt de volgende PowerShell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Gebruik voor de `-EnvironmentName "AzureUSGovernment"` overheid. Zie Agent voor de [Azure Government Cloud installeren voor meer informatie.](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)

Zie Ondersteuning voor computer- en back-toepassingsproxy vertrouwenscertificaat voor meer informatie over het verifiëren van het certificaat en het oplossen [van problemen.](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)

## <a name="under-the-hood"></a>Onderhuids

Connectors zijn gebaseerd op Windows Server Web toepassingsproxy, zodat ze de meeste van dezelfde beheerhulpprogramma's hebben, waaronder Windows-gebeurtenislogboeken

![Gebeurtenislogboeken beheren met de Logboeken](./media/application-proxy-connectors/event-view-window.png)

en Windows-prestatiemeters.

![Tellers toevoegen aan de connector met de prestatiemeter](./media/application-proxy-connectors/performance-monitor.png)

De connectors hebben zowel **beheerderslogboeken** als **sessielogboeken.** Het **beheerderslogboek** bevat belangrijke gebeurtenissen en hun fouten. Het **sessielogboek** bevat alle transacties en de verwerkingsgegevens.

Als u de logboeken wilt bekijken, Logboeken **en** gaat u naar Logboeken toepassingen en **services**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector.** Als u het **sessielogboek** zichtbaar wilt maken, selecteert u in het **menu** Weergave de optie Logboeken voor analyse en **foutopsporing weergeven.** Het **sessielogboek** wordt doorgaans gebruikt voor het oplossen van problemen en is standaard uitgeschakeld. Schakel deze functie in om te beginnen met het verzamelen van gebeurtenissen en schakel deze uit wanneer deze niet meer nodig is.

U kunt de status van de service bekijken in het venster Services. De connector bestaat uit twee Windows-services: de werkelijke connector en de updater. Beide moeten altijd worden uitgevoerd.

 ![Voorbeeld: het venster Services met lokale Azure AD-services](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van connectorgroepen](application-proxy-connector-groups.md)
- [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
- [Problemen met toepassingsproxy en connector oplossen](application-proxy-troubleshoot.md)
- [De Azure AD toepassingsproxy-connector op de toepassingsproxy installeren](application-proxy-register-connector-powershell.md)
