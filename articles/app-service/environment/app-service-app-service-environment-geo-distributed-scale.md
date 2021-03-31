---
title: Geografisch gedistribueerde schaal
description: Meer informatie over het Horizon taal schalen van apps met behulp van geo-distributie met Traffic Manager en App Service omgevingen.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85833601"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geografisch gedistribueerde schaal met App Service-omgevingen
## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toepassings scenario's waarvoor een zeer hoge schaal is vereist, kunnen de beschik bare capaciteit van de reken resource voor één implementatie van een app overschrijden.  Stem toepassingen, sport evenementen en televised entertainment-gebeurtenissen zijn alle voor beelden van scenario's waarvoor een zeer hoge schaal nodig is. Aan high Scale-vereisten kan worden voldaan door apps horizon taal te schalen. Voor het afhandelen van extreme belasting vereisten kunnen veel app-implementaties worden uitgevoerd binnen één regio en in verschillende regio's.

App Service omgevingen zijn een ideaal platform voor Horizon taal schalen. Na het selecteren van een App Service Environment configuratie die een bekende aanvraag snelheid kan ondersteunen, kunnen ontwikkel aars extra App Service omgevingen in de mode ' cookie-snij punt ' implementeren om een gewenste piek belasting capaciteit te bereiken.

Stel bijvoorbeeld dat een app die wordt uitgevoerd op een App Service Environment configuratie is getest op het afhandelen van 20.000-aanvragen per seconde (RPS).  Als de gewenste piek belasting capaciteit 100.000 RPS is, kunnen er vijf (5) App Service omgevingen worden gemaakt en geconfigureerd om ervoor te zorgen dat de toepassing de maximale geschatte belasting kan afhandelen.

Omdat klanten doorgaans toegang hebben tot apps met behulp van een aangepast (of Vanity) domein, hebben ontwikkel aars een manier nodig om app-aanvragen te distribueren over alle exemplaren van de App Service Environment.  Een goede manier om dit doel te bereiken is het oplossen van het aangepaste domein met behulp van een [Azure Traffic Manager-profiel][AzureTrafficManagerProfile].  Het Traffic Manager profiel kan worden geconfigureerd om te verwijzen naar alle afzonderlijke App Service omgevingen.  Traffic Manager wordt het distribueren van klanten in alle App Service omgevingen automatisch verwerkt op basis van de instellingen voor taak verdeling in het Traffic Manager profiel.  Deze aanpak werkt ongeacht of alle App Service omgevingen in één Azure-regio worden geïmplementeerd of wereld wijd worden geïmplementeerd in meerdere Azure-regio's.

Bovendien zijn klanten niet op de hoogte van het aantal App Service omgevingen dat een app uitvoert, omdat klanten toegang hebben tot apps via het domein Vanity.  Als gevolg hiervan kunnen ontwikkel aars snel en eenvoudig App Service omgevingen toevoegen en verwijderen op basis van de geobserveerde belasting van het verkeer.

In het conceptuele diagram hieronder ziet u een app die horizon taal is geschaald over drie App Service omgevingen binnen één regio.

![Conceptuele architectuur][ConceptualArchitecture] 

In de rest van dit onderwerp worden de stappen beschreven die worden uitgevoerd bij het instellen van een gedistribueerde topologie voor de voor beeld-app met meerdere App Service omgevingen.

## <a name="planning-the-topology"></a>De topologie plannen
Voordat u een gedistribueerde app-footprint maakt, is het handig om een paar stukjes informatie te hebben.

* **Aangepast domein voor de app:**  Wat is de aangepaste domein naam die klanten gebruiken om toegang te krijgen tot de app?  Voor de voor beeld-app is de aangepaste domein naam `www.scalableasedemo.com` .
* **Traffic Manager domein:** Kies een domein naam bij het maken van een [Azure Traffic Manager-profiel][AzureTrafficManagerProfile].  Deze naam wordt gecombineerd met het achtervoegsel *trafficmanager.net* om een domein vermelding te registreren die wordt beheerd door Traffic Manager.  Voor de voor beeld-app is de gekozen naam *schaalbaar-ASE-demo*.  Als gevolg hiervan is de volledige domein naam die wordt beheerd door Traffic Manager *Scalable-ASE-demo.trafficmanager.net*.
* **Strategie voor het schalen van de app-footprint:**  Wordt het Opper vlak van de toepassing verdeeld over meerdere App Service omgevingen in één regio?  Meerdere regio's?  Een combi natie van beide benaderingen?  Baseer de beslissing op verwachtingen van waar klant verkeer van afkomstig is en hoe goed de rest van de ondersteunende back-end-infra structuur van een app kan worden geschaald.  Een app kan bijvoorbeeld met een stateless toepassing van 100% worden geschaald met behulp van een combi natie van veel App Service omgevingen in elke Azure-regio, vermenigvuldigd met App Service omgevingen die zijn geïmplementeerd in verschillende Azure-regio's.  Met 15 + wereld wijde Azure-regio's die beschikbaar zijn voor keuze, kunnen klanten echt een grote hoeveelheid ruimte op de Hyper-Scale-toepassing bouwen.  Voor de voor beeld-app die wordt gebruikt voor dit artikel, zijn er drie App Service omgevingen gemaakt in één Azure-regio (Zuid-Centraal).
* **Naam Conventie voor de app service omgevingen:**  Elk App Service Environment vereist een unieke naam.  Naast een of twee App Service omgevingen is het handig om een naam Conventie te hebben om elke App Service Environment te identificeren.  Voor de voor beeld-app is een eenvoudige naam conventie gebruikt.  De namen van de drie App Service omgevingen zijn *fe1ase*, *fe2ase* en *fe3ase*.
* **Naam Conventie voor de apps:**  Omdat er meerdere exemplaren van de app worden geïmplementeerd, is een naam vereist voor elk exemplaar van de geïmplementeerde app.  Een weinig bekende maar handige functie van App Service omgevingen is dat dezelfde app-naam kan worden gebruikt in meerdere App Service omgevingen.  Omdat elke App Service Environment een uniek domein achtervoegsel heeft, kunnen ontwikkel aars kiezen om dezelfde app-naam in elke omgeving te gebruiken.  Een ontwikkelaar kan bijvoorbeeld de volgende apps hebben:  *MyApp.foo1.p.azurewebsites.net*, *MyApp.foo2.p.azurewebsites.net*, *MyApp.foo3.p.azurewebsites.net*, etc.  Voor de voor beeld-app heeft elk exemplaar van de app echter ook een unieke naam.  De namen van de app-exemplaren die worden gebruikt, zijn *webfrontend1*, *webfrontend2* en *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Het Traffic Manager profiel instellen
Zodra meerdere exemplaren van een app op meerdere App Service omgevingen zijn geïmplementeerd, kunnen de afzonderlijke app-exemplaren worden geregistreerd met Traffic Manager.  Voor de voor beeld-app is een Traffic Manager profiel nodig voor *Scalable-ASE-demo.trafficmanager.net* die klanten kunnen omleiden naar een van de volgende geïmplementeerde app-exemplaren:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Een exemplaar van de voor beeld-app die is geïmplementeerd op de eerste App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Een exemplaar van de voor beeld-app die is geïmplementeerd op de tweede App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Een exemplaar van de voor beeld-app die is geïmplementeerd op de derde App Service Environment.

De eenvoudigste manier om meerdere Azure App Service-eind punten te registreren, die allemaal in **dezelfde** Azure-regio worden uitgevoerd, is met de Power shell- [Azure Resource Manager Traffic Manager ondersteuning][ARMTrafficManager].  

De eerste stap is het maken van een Azure Traffic Manager-profiel.  De volgende code laat zien hoe het profiel is gemaakt voor de voor beeld-app:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

U ziet hoe de para meter *RelativeDnsName* is ingesteld op *Scalable-ASE-demo*.  Deze para meter zorgt ervoor dat de domein naam *Scalable-ASE-demo.trafficmanager.net* wordt gemaakt en gekoppeld aan een Traffic Manager profiel.

De para meter *TrafficRoutingMethod* definieert het taakverdelings beleid dat Traffic Manager gebruikt om te bepalen hoe de belasting van klanten wordt verdeeld over alle beschik bare eind punten.  In dit voor beeld is de *gewogen* methode gekozen.  Als gevolg van deze keuze worden klant aanvragen verspreid over alle geregistreerde toepassings eindpunten op basis van de relatieve gewichten die zijn gekoppeld aan elk eind punt. 

Als het profiel is gemaakt, wordt elk exemplaar van de app toegevoegd aan het profiel als een systeem eigen Azure-eind punt.  Met de volgende code wordt een verwijzing naar elke front-end-web-app opgehaald. Vervolgens wordt elke app als Traffic Manager eind punt toegevoegd via de para meter *TargetResourceId* .

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

U ziet dat er één aanroep van *add-AzureTrafficManagerEndpointConfig* is voor elke afzonderlijke app-instantie.  De *TargetResourceId* -para meter in elke Power shell-opdracht verwijst naar een van de drie geïmplementeerde app-exemplaren.  Het Traffic Manager profiel verspreidt de belasting over alle drie de eind punten die in het profiel zijn geregistreerd.

Alle drie eind punten gebruiken dezelfde waarde (10) voor de *Weight* -para meter.  Deze situatie leidt ertoe dat Traffic Manager aanvragen van klanten relatief gelijkmatig over de drie app-instanties spreiden. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Het aangepaste domein van de app op het Traffic Manager domein aanwijzen
De laatste stap is nodig om het aangepaste domein van de app te laten verwijzen naar het Traffic Manager domein.  Voor de voor beeld-app verwijzen we u `www.scalableasedemo.com` naar `scalable-ase-demo.trafficmanager.net` .  Voer deze stap uit met de domein registratie-instantie die het aangepaste domein beheert.  

Met de domein beheer hulpprogramma's van uw registratie service moet u een CNAME-record maken die verwijst naar het aangepaste domein op het Traffic Manager domein.  In de onderstaande afbeelding ziet u een voor beeld van hoe deze CNAME-configuratie er als volgt uitziet:

![CNAME voor aangepast domein][CNAMEforCustomDomain] 

Hoewel dit niet in dit onderwerp wordt behandeld, moet u er rekening mee houden dat voor elk afzonderlijk app-exemplaar ook het aangepaste domein moet worden geregistreerd.  Als er een aanvraag wordt gedaan voor een app-exemplaar en de toepassing het aangepaste domein niet bij de app heeft geregistreerd, mislukt de aanvraag.

In dit voor beeld is het aangepaste domein `www.scalableasedemo.com` , en aan elk toepassings exemplaar is het aangepaste domein gekoppeld.

![Aangepast domein][CustomDomain] 

Zie [aangepaste domeinen registreren][RegisterCustomDomain]voor een samen vatting van het registreren van een aangepast domein met Azure app service-apps.

## <a name="trying-out-the-distributed-topology"></a>De gedistribueerde topologie wordt uitgeprobeerd
Het eind resultaat van de Traffic Manager-en DNS-configuratie is dat aanvragen voor doorlopend `www.scalableasedemo.com` worden door de volgende volg orde:

1. Een browser of apparaat maakt een DNS-zoek opdracht voor `www.scalableasedemo.com`
2. De CNAME-vermelding in het domein registratie proces zorgt ervoor dat de DNS-zoek opdracht wordt omgeleid naar Azure Traffic Manager.
3. Er wordt een DNS-zoek opdracht uitgevoerd voor *Scalable-ASE-demo.trafficmanager.net* op een van de Azure Traffic Manager DNS-servers.
4. Op basis van het taakverdelings beleid dat eerder in de para meter *TrafficRoutingMethod* is opgegeven, selecteert Traffic Manager een van de geconfigureerde eind punten. Vervolgens wordt de FQDN-naam van dat eind punt naar de browser of het apparaat geretourneerd.
5. Omdat de FQDN-naam van het eind punt de URL is van een app-exemplaar dat wordt uitgevoerd op een App Service Environment, vraagt de browser of het apparaat een Microsoft Azure DNS-server om de FQDN naar een IP-adres op te lossen. 
6. De browser of het apparaat verzendt de HTTP/S-aanvraag naar het IP-adres.  
7. De aanvraag ontvangt een van de app-exemplaren die worden uitgevoerd op een van de App Service omgevingen.

In de onderstaande console afbeelding ziet u een DNS-zoek opdracht voor het aangepaste domein van de voor beeld-app. De oplossing wordt omgezet in een app-exemplaar dat wordt uitgevoerd op een van de drie voor beelden App Service omgevingen (in dit geval de tweede van de drie App Service omgevingen):

![DNS-zoek opdracht][DNSLookup] 

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en informatie
Documentatie over de ondersteuning van de Power shell- [Azure Resource Manager Traffic Manager][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
