---
title: Aanbevolen procedures voor beveiligde PaaS-implementaties-Microsoft Azure
description: Leer de aanbevolen procedures voor het ontwerpen, bouwen en beheren van veilige Cloud toepassingen op Azure en inzicht te krijgen in de beveiligings voordelen van PaaS tegenover andere Cloud service modellen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 77a2a32b9a6358c39a14cfe37eeb44f7cb90af0a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841985"
---
# <a name="securing-paas-deployments"></a>PaaS-implementaties beveiligen

Dit artikel bevat informatie waarmee u het volgende kunt doen:

- Meer informatie over de beveiligings voordelen van het hosten van toepassingen in de Cloud
- Evalueer de beveiligings voordelen van platform as a Service (PaaS) versus andere Cloud service modellen
- Wijzig de beveiligings focus van een netwerk gericht naar een identiteits gerichte perimeter beveiligings aanpak
- Aanbevelingen voor best practices voor algemene PaaS-beveiliging implementeren

Het [ontwikkelen van beveiligde toepassingen op Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) is een algemene hand leiding voor de beveiligings vragen en-besturings elementen die u moet overwegen bij elke fase van de levens cyclus van software ontwikkeling bij het ontwikkelen van toepassingen voor de Cloud.

## <a name="cloud-security-advantages"></a>Voor delen van Cloud beveiliging
Het is belang rijk om inzicht te krijgen in de [verantwoordelijkheid](shared-responsibility.md) tussen u en micro soft. On-premises is het eigendom van de hele stack, maar wanneer u overstapt naar de Cloud, worden sommige verantwoordelijkheden overgedragen naar micro soft.

Er zijn [beveiligings voordelen die zich in de Cloud](shared-responsibility.md#cloud-security-advantages)bevinden. In een on-premises omgeving hebben organisaties waarschijnlijk unmet verantwoordelijkheden en beperkte bronnen die beschikbaar zijn om te investeren in beveiliging, waardoor een omgeving wordt gemaakt waarin aanvallers zwakke plekken op alle lagen kunnen exploiteren.

Organisaties kunnen hun detectie en reactie tijden voor bedreigingen verbeteren door gebruik te maken van de Cloud beveiligings mogelijkheden van een provider en Cloud Intelligence.  Door verantwoordelijkheden te verplaatsen naar de Cloud provider, kunnen organisaties meer beveiligings dekking krijgen, waardoor ze beveiligings bronnen en budget opnieuw aan andere zakelijke prioriteiten kan toewijzen.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Beveiligings voordelen van een PaaS-Cloud service model
Laten we eens kijken naar de beveiligings voordelen van een Azure PaaS-implementatie versus on-premises.

![Voor delen van de beveiliging van PaaS](./media/paas-deployments/advantages-of-paas.png)

Onder aan de stack, de fysieke infra structuur, verkleint micro soft de algemene Risico's en verantwoordelijkheden. Omdat de micro soft-Cloud voortdurend wordt bewaakt door micro soft, is het moeilijk om een aanval uit te proberen. Het is niet zinvol dat een aanvaller de micro soft-Cloud als doel kan uitvoeren. Tenzij de aanvaller veel geld en resources heeft, gaat de aanvaller waarschijnlijk over naar een ander doel.  

In het midden van de stack is er geen verschil tussen een PaaS-implementatie en on-premises. Op de toepassingslaag en de account-en toegangs beheer laag zijn er vergelijk bare Risico's. In het gedeelte volgende stappen van dit artikel wordt u begeleid bij de aanbevolen procedures voor het elimineren of minimaliseren van deze Risico's.

Boven aan de stack, data governance en Rights Management, neemt u een risico dat kan worden verholpen door sleutel beheer. (Sleutel beheer wordt besproken in Aanbevolen procedures.) Hoewel sleutel beheer een extra verantwoordelijkheid is, hebt u gebieden in een PaaS-implementatie die u niet meer hoeft te beheren, zodat u resources kunt verplaatsen naar sleutel beheer.

Het Azure-platform biedt u ook een krachtige DDoS-bescherming met behulp van verschillende technologieën op basis van het netwerk. Alle typen op het netwerk gebaseerde DDoS-beveiligings methoden hebben echter hun limieten per koppeling en per Data Center. Om de impact van grote DDoS-aanvallen te voor komen, kunt u profiteren van de belangrijkste Cloud mogelijkheden van Azure, zodat u snel en automatisch uitschaalt om DDoS-aanvallen te verdedigen. Meer informatie over hoe u dit kunt doen in de aanbevolen procedures artikelen.

## <a name="modernizing-the-defenders-mindset"></a>De uitgangspunt van het Defender moderniseren
Met PaaS-implementaties kunt u uw algemene benadering van beveiliging verschuiven. U hoeft alleen maar te bepalen wat u nodig hebt om de verantwoordelijkheid van micro soft te delen.

Een ander belang rijk verschil tussen PaaS en traditionele on-premises implementaties is een nieuwe weer gave van wat het primaire beveiligings netwerk definieert. In het verleden was de primaire on-premises beveiligings verbinding uw netwerk en de meeste on-premises beveiligings ontwerpen gebruiken het netwerk als de primaire beveiligings draaiing. Voor PaaS-implementaties kunt u beter worden geholpen door de identiteit te beschouwen als de primaire beveiligings verbinding.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Een beleid met identiteit als de primaire beveiligings verbinding aannemen
Een van de vijf essentiële kenmerken van Cloud Computing is brede netwerk toegang, waardoor het netwerk gericht lijkt op minder relevant. Het doel van de meeste Cloud Computing is gebruikers toe te staan om toegang te krijgen tot bronnen, ongeacht de locatie. De locatie van de meeste gebruikers gaat ergens op internet.

In de volgende afbeelding ziet u hoe de beveiligings verbinding van een netwerk perimeter naar een identiteits perimeter is ontwikkeld. De beveiliging wordt verminderd met het beschermen van uw netwerk en het beschermen van uw gegevens, en het beheren van de beveiliging van uw apps en gebruikers. Het belangrijkste verschil is dat u de beveiliging dichter bij het belang van uw bedrijf wilt pushen.

![Identiteit als nieuwe beveiligings perimeter](./media/paas-deployments/identity-perimeter.png)

In eerste instantie hebben Azure PaaS-Services (bijvoorbeeld webrollen en Azure SQL) weinig of geen traditionele netwerk aantekening. Het is vastgesteld dat het doel van het element moet worden blootgesteld aan Internet (webrole) en dat verificatie de nieuwe verbinding biedt (bijvoorbeeld BLOB of Azure SQL).

Bij moderne beveiligings procedures wordt ervan uitgegaan dat de kwaadwillende persoon de netwerk verbinding heeft geschonden. Daarom zijn moderne veiligheids procedures verplaatst naar de identiteit. Organisaties moeten een op identiteit gebaseerde beveiligings verbinding met sterke authenticatie-en autorisatie hygiëne (aanbevolen procedures) tot stand brengen.

De principes en patronen voor de netwerk verbinding zijn tien tallen beschikbaar. Daarentegen heeft de branche relatief minder ervaring met het gebruik van identiteit als de primaire beveiligings verbinding. We hebben hiervoor voldoende ervaring opgedaan om enkele algemene aanbevelingen te bieden die in het veld zijn bewezen en die van toepassing zijn op bijna alle PaaS-Services.

Hieronder volgen de aanbevolen procedures voor het beheren van het identiteits perimeter netwerk.

**Best Practice**: Beveilig uw sleutels en referenties voor het beveiligen van uw PaaS-implementatie.   
**Details**: verlies van sleutels en referenties is een veelvoorkomend probleem. U kunt een gecentraliseerde oplossing gebruiken waarbij sleutels en geheimen kunnen worden opgeslagen in Hardware Security modules (Hsm's). [Azure Key Vault](../../key-vault/general/overview.md) beveiligt uw sleutels en geheimen door verificatie sleutels, opslag account sleutels, gegevens versleutelings sleutels, pfx-bestanden en wacht woorden te versleutelen met behulp van sleutels die worden beveiligd door hsm's.

**Best Practice**: plaats geen referenties en andere geheimen in de bron code of het github.   
**Details**: het enige wat u nog ergert dan het verlies van uw sleutels en referenties is dat een niet-gemachtigde partij er toegang toe heeft. Aanvallers kunnen gebruikmaken van bot-technologieën om sleutels en geheimen te vinden die zijn opgeslagen in code opslagplaatsen, zoals GitHub. Plaats geen sleutel en geheimen in deze open bare code opslagplaatsen.

**Best Practice**: Beveilig uw VM-beheer interfaces op hybride Paas-en IaaS-Services met behulp van een beheer interface waarmee u de virtuele machines rechtstreeks op afstand kunt beheren.   
**Details**: protocollen voor extern beheer, zoals [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607)en [externe communicatie met Power shell](/powershell/module/microsoft.powershell.core/enable-psremoting) , kunnen worden gebruikt. Over het algemeen is het raadzaam dat u geen rechtstreekse externe toegang tot virtuele machines inschakelt via internet.

Gebruik, indien mogelijk, alternatieve benaderingen zoals het gebruik van virtuele particuliere netwerken in een virtueel Azure-netwerk. Als er geen alternatieve benaderingen beschikbaar zijn, moet u ervoor zorgen dat u complexe wachtwoordzins en twee ledige verificatie gebruikt (zoals [Azure AD multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)).

**Best Practice**: gebruik platforms met sterke verificatie en autorisatie.   
**Details**: gebruik federatieve identiteiten in azure AD in plaats van aangepaste gebruikers archieven. Wanneer u federatieve identiteiten gebruikt, profiteert u van een op platform gebaseerde benadering en delegeert u het beheer van geautoriseerde identiteiten aan uw partners. Een federatieve identiteits benadering is vooral belang rijk wanneer werk nemers worden beëindigd en deze informatie moet worden weer spie geld via meerdere identiteits-en autorisatie systemen.

Gebruik platform verificatie-en autorisatie mechanismen in plaats van aangepaste code. De reden hiervoor is dat het ontwikkelen van aangepaste verificatie code fout gevoelig is. De meeste ontwikkel aars zijn geen beveiligings experts en zijn waarschijnlijk niet op de hoogte van de finesses en de nieuwste ontwikkelingen in verificatie en autorisatie. Commerciële code (bijvoorbeeld van micro soft) wordt vaak uitgebreid gecontroleerd.

Gebruik twee ledige verificatie. Verificatie met twee factoren is de huidige standaard voor verificatie en autorisatie, omdat het voor komt dat de zwakke plekken in de beveiliging van gebruikers naam en wacht woord worden geauthenticeerd. Toegang tot de interfaces van Azure Management (Portal/Remote Power shell) en klant gerichte services moeten zijn ontworpen en geconfigureerd voor het gebruik van [Azure AD-multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Gebruik standaard verificatie protocollen, zoals OAuth2 en Kerberos. Deze protocollen zijn uitgebreid gecontroleerd en zijn waarschijnlijk geïmplementeerd als onderdeel van uw platform bibliotheken voor verificatie en autorisatie.

## <a name="use-threat-modeling-during-application-design"></a>Threatisatie modellen gebruiken tijdens het ontwerpen van toepassingen
De levens cyclus van micro soft [Security Development](https://www.microsoft.com/en-us/sdl) geeft aan dat teams tijdens de ontwerp fase een proces moeten uitvoeren met de naam bedreigingen modellering. Om dit proces te vereenvoudigen, heeft micro soft de [sdl-Threat Modeling tool](../develop/threat-modeling-tool.md)gemaakt. Het model leren van het ontwerp van de toepassing en het opsommen van [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) -bedreigingen voor alle vertrouwens grenzen kunnen ontwerp fouten vroegtijdig op.

De volgende tabel geeft een overzicht van de STRIDE-bedreigingen en biedt enkele voor beelden van oplossingen die gebruikmaken van Azure-functies. Deze oplossingen werken niet in elke situatie.

| Bedreiging | Beveiligings eigenschap | Mogelijke problemen met het Azure-platform |
| --- | --- | --- |
| Adresvervalsing (spoofing) | Verificatie | HTTPS-verbindingen vereisen. |
| Manipulatie | Integriteit | Valideer TLS/SSL-certificaten. |
| Weerlegbaarheid | Niet-weerlegbaarheid | [Bewaking en diagnose](/azure/architecture/best-practices/monitoring)van Azure inschakelen. |
| Openbaarmaking van informatie | Vertrouwelijkheid | Versleutel gevoelige gegevens op rest door gebruik te maken van [service certificaten](/rest/api/appservice/certificates). |
| Denial of Service | Beschikbaarheid | Bewaak de prestatie gegevens voor mogelijke denial-of-service-voor waarden. Verbindings filters implementeren. |
| Verhoging van bevoegdheden | Autorisatie | Gebruik [privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Ontwikkelen op Azure App Service
[Azure app service](../../app-service/overview.md) is een Paas-Aanbieding waarmee u web-en mobiele apps kunt maken voor elk platform of apparaat en overal verbinding kunt maken met gegevens, in de Cloud of on-premises. App Service bevat de web-en mobiele mogelijkheden die eerder afzonderlijk zijn geleverd als Azure-websites en Azure Mobile Services. Deze service bevat ook nieuwe mogelijkheden voor het automatiseren van bedrijfsprocessen en het hosten van cloud-API's. Als één geïntegreerde service biedt App Service een uitgebreide set mogelijkheden voor web-, mobiele en integratie scenario's.

Hieronder vindt u aanbevolen procedures voor het gebruik van App Service.

**Best Practice**: [Verifieer door Azure Active Directory](../../app-service/overview-authentication-authorization.md).   
**Details**: app service biedt een OAuth 2,0-Service voor uw ID-provider. OAuth 2,0 richt zich op de vereenvoudiging van client ontwikkelaars en biedt specifieke autorisatie stromen voor webtoepassingen, desktop toepassingen en mobiele telefoons. Azure AD maakt gebruik van OAuth 2,0 zodat u toegang tot mobiele en webtoepassingen kunt autoriseren.

**Best Practice**: toegang beperken op basis van de beveiligings principes van de nood zaak om te kennen en te voldoen aan de minimale bevoegdheden.   
**Details**: het beperken van de toegang is van cruciaal belang voor organisaties die beveiligings beleid voor gegevens toegang willen afdwingen. U kunt Azure RBAC gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik. Zie aan de [slag met toegangs beheer](../../role-based-access-control/overview.md)voor meer informatie over het verlenen van gebruikers toegang tot toepassingen.

**Aanbevolen procedure**: Beveilig uw sleutels.   
**Details**: Azure Key Vault helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Met Key Vault kunt u sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd door Hardware Security modules (Hsm's). Voor extra zekerheid kunt u de sleutels importeren of genereren in HSM's. Zie [Azure Key Vault](../../key-vault/general/overview.md) voor meer informatie. U kunt Key Vault ook gebruiken om uw TLS-certificaten te beheren met automatische verlenging.

**Best Practice**: IP-adressen van binnenkomende bronnen beperken.   
**Details**: [app service Environment](../../app-service/environment/intro.md) heeft een functie voor de integratie van virtuele netwerken waarmee u binnenkomende bron-IP-adressen kunt beperken via netwerk beveiligings groepen. Met virtuele netwerken kunt u Azure-resources in een niet-Internet Routeer bare netwerk plaatsen waartoe u de toegang beheert. Zie [uw app integreren met een virtueel Azure-netwerk](../../app-service/web-sites-integrate-with-vnet.md)voor meer informatie.

**Best Practice**: Controleer de beveiligings status van uw app service omgevingen.   
**Details**: gebruik Azure Security Center om uw app service omgevingen te bewaken. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er [aanbevelingen](../../security-center/asset-inventory.md) gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden.

> [!NOTE]
> Bewakings App Service is in de preview-fase en is alleen beschikbaar voor de [standaard-laag](../../security-center/security-center-pricing.md) van Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Een Web Application Firewall installeren
Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veelvoorkomende aanvallen zijn hierbij onder andere aanvallen met SQL-injecties en aanvallen via scripting op meerdere sites. Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen en dit kan tevens veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde firewall voor webtoepassingen maakt het beveiligingsbeheer veel eenvoudiger en biedt toepassingsbeheerders meer veiligheid tegen bedreigingen of aanvallen. Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door een patch voor een bekend beveiligingsprobleem toe te passen op een centrale locatie in plaats van elke afzonderlijke webtoepassing te beveiligen. Bestaande toepassingsgateways kunnen eenvoudig worden geconverteerd naar een toepassingsgateway met Web Application Firewall.

[Web Application firewall (WAF)](../../web-application-firewall/afds/afds-overview.md) is een functie van Application Gateway waarmee uw webtoepassingen gecentraliseerd worden beschermd tegen veelvoorkomende aanvallen en beveiligings problemen. WAF is gebaseerd op regels van de kern regel sets 3,0 of 2.2.9 van het [Open Web Application Security project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) .

## <a name="monitor-the-performance-of-your-applications"></a>De prestaties van uw toepassingen bewaken
Bewaking is de handeling van het verzamelen en analyseren van gegevens om de prestaties, de status en beschik baarheid van uw toepassing te bepalen. Een effectieve bewakingsstrategie helpt u de gedetailleerde werking van de onderdelen van uw toepassing te begrijpen. Zo kunt u uw uptime verg Roten door u op de hoogte te stellen van kritieke problemen zodat u deze kunt oplossen voordat ze problemen ondervinden. Het helpt u ook bij het detecteren van afwijkingen die mogelijk betrekking hebben op beveiliging.

Gebruik [Azure-toepassing inzichten](https://azure.microsoft.com/documentation/services/application-insights) om de beschik baarheid, prestaties en het gebruik van uw toepassing te bewaken, ongeacht of deze in de Cloud of on-premises worden gehost. U kunt met behulp van Application Insights snel fouten in uw toepassing identificeren en onderzoeken zonder te hoeven wachten tot een gebruiker deze rapporteert. Met de informatie die u verzamelt, kunt u weloverwogen keuzes maken over het onderhoud en de verbeteringen van uw toepassing.

Application Insights heeft uitgebreide hulpmiddelen voor interactie met de gegevens die worden verzameld. Application Insights slaat zijn gegevens op in een gemeenschappelijke opslagplaats. Het kan profiteren van gedeelde functionaliteit zoals waarschuwingen, Dash boards en diepe analyse met de Kusto-query taal.

## <a name="perform-security-penetration-testing"></a>Beveiligings indringings tests uitvoeren
Het valideren van beveiligings beveiliging is net zo belang rijk als het testen van andere functies. Zorg voor [indringings test](pen-testing.md) een standaard onderdeel van uw build-en implementatie proces. Plan regel matige beveiligings tests en beveiligings problemen met het scannen van geïmplementeerde toepassingen en controleer op open poorten, eind punten en aanvallen.

Fuzz testen is een methode voor het vinden van programma fouten (code fouten) door onjuiste invoer gegevens op te geven voor programma-interfaces (ingangs punten) die deze gegevens parseren en gebruiken. [Micro soft beveiligings risico detectie](https://www.microsoft.com/en-us/security-risk-detection/) is een cloud-gebaseerd hulp programma dat u kunt gebruiken om te zoeken naar fouten en andere beveiligings problemen in uw software voordat u deze implementeert in Azure. Het hulp programma is ontworpen om beveiligings problemen te ondervangen voordat u software implementeert, zodat u geen bug hoeft op te lossen, met crashes kunt omgaan of op een aanval kunt reageren nadat de software is uitgebracht.


## <a name="next-steps"></a>Volgende stappen
In dit artikel zijn we gericht op beveiligings voordelen van een Azure PaaS-implementatie en aanbevolen beveiligings procedures voor Cloud toepassingen. Vervolgens leert u aanbevolen procedures voor het beveiligen van uw PaaS-webtoepassingen en mobiele oplossingen met behulp van specifieke Azure-Services. We beginnen met Azure App Service, Azure SQL Database en Azure Synapse Analytics en Azure Storage. Wanneer artikelen over aanbevolen procedures voor andere Azure-Services beschikbaar worden, worden koppelingen in de volgende lijst weer gegeven:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database en Azure Synapse Analytics](paas-applications-using-sql.md)
- [Azure Storage](paas-applications-using-storage.md)
- Azure Cache voor Redis
- Azure Service Bus
- Firewalls voor webtoepassingen

Zie voor het ontwikkelen van [beveiligde toepassingen op Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) voor beveiligings vragen en-besturings elementen kunt u overwegen in elke fase van de levens cyclus van software ontwikkeling bij het ontwikkelen van toepassingen voor de Cloud.

Zie [Aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van uw cloud oplossingen met behulp van Azure.

De volgende resources zijn beschikbaar om meer algemene informatie te geven over Azure-beveiliging en gerelateerde micro soft-Services:
* [Blog van het Azure-beveiligings team](/archive/blogs/azuresecurity/) : voor actuele informatie over de nieuwste Azure-beveiliging
* [Micro soft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) : waar micro soft-beveiligings problemen, met inbegrip van problemen met Azure, kunnen worden gerapporteerd of via e-mail worden verzonden naar secure@microsoft.com