---
title: Geografisch gedistribueerde schaal
description: Meer informatie over het horizontaal schalen van apps met behulp van geo-distributie met Traffic Manager en App Service Omgevingen.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834296"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geografisch gedistribueerde schaal met App Service-omgevingen
## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toepassingsscenario's waarvoor zeer grote schaal nodig is, kunnen de rekenresourcecapaciteit overschrijden die beschikbaar is voor één implementatie van een app.  Stemtoepassingen, sportevenementen en entertainmentgebeurtenissen op tv zijn allemaal voorbeelden van scenario's waarvoor zeer grote schaal vereist is. Aan grootschalige vereisten kan worden voldaan door apps horizontaal uit te schalen. Voor het afhandelen van extreme belastingsvereisten kunnen veel app-implementaties binnen één regio en tussen regio's worden gemaakt.

App Service omgevingen zijn een ideaal platform voor horizontaal uitschalen. Na het selecteren van een App Service Environment-configuratie die ondersteuning kan bieden voor een bekend aanvraagtarief, kunnen ontwikkelaars extra App Service-omgevingen op 'cookie'-manier implementeren om een gewenste piekbelastingscapaciteit te bereiken.

Stel bijvoorbeeld dat een app die wordt uitgevoerd op een App Service Environment is getest om 20.000 aanvragen per seconde (RPS) te verwerken.  Als de gewenste piekcapaciteit 100.000 RPS is, kunnen er vijf (5) App Service-omgevingen worden gemaakt en geconfigureerd om ervoor te zorgen dat de toepassing de maximale verwachte belasting kan verwerken.

Omdat klanten apps meestal openen met behulp van een aangepast (of vanity)-domein, hebben ontwikkelaars een manier nodig om app-aanvragen te distribueren over alle App Service Environment exemplaren.  Een uitstekende manier om dit doel te bereiken, is door het aangepaste domein om te brengen met behulp van [Azure Traffic Manager profiel][AzureTrafficManagerProfile].  Het Traffic Manager kan worden geconfigureerd om te wijzen op alle afzonderlijke omgevingen App Service omgevingen.  Traffic Manager verdeelt klanten automatisch over alle App Service-omgevingen, op basis van de taakverdelingsinstellingen in het Traffic Manager profiel.  Deze aanpak werkt ongeacht of alle App Service-omgevingen zijn geïmplementeerd in één Azure-regio of wereldwijd worden geïmplementeerd in meerdere Azure-regio's.

Omdat klanten toegang hebben tot apps via het vanity-domein, zijn klanten bovendien niet op de hoogte van het aantal App Service omgevingen waarin een app wordt uitgevoerd.  Hierdoor kunnen ontwikkelaars snel en eenvoudig omgevingen toevoegen en App Service verwijderen op basis van waargenomen verkeersbelasting.

In het onderstaande conceptuele diagram wordt een app horizontaal uitgeschaald over drie App Service omgevingen binnen één regio.

![Conceptuele architectuur][ConceptualArchitecture] 

In de rest van dit onderwerp worden de stappen besproken voor het instellen van een gedistribueerde topologie voor de voorbeeld-app met behulp van App Service Omgevingen.

## <a name="planning-the-topology"></a>De topologie plannen
Voordat u een gedistribueerde app-footprint gaat bouwen, is het beter om van tevoren enkele stukjes informatie te hebben.

* **Aangepast domein voor de app:**  Wat is de aangepaste domeinnaam die klanten gebruiken voor toegang tot de app?  Voor de voorbeeld-app is de aangepaste domeinnaam `www.scalableasedemo.com` .
* **Traffic Manager domein:** Kies een domeinnaam bij het maken van een [Azure Traffic Manager profiel][AzureTrafficManagerProfile].  Deze naam wordt gecombineerd met het *achtervoegsel trafficmanager.net* om een domeininvoer te registreren die wordt beheerd door Traffic Manager.  Voor de voorbeeld-app is de gekozen naam *scalable-ase-demo.*  Als gevolg hiervan wordt de volledige domeinnaam die wordt beheerd door Traffic Manager, *scalable-ase-demo.trafficmanager.net*.
* **Strategie voor het schalen van de app-footprint:**  Wordt de footprint van de toepassing verdeeld over meerdere App Service omgevingen in één regio?  Meerdere regio's?  Een combinatie van beide benaderingen?  Baseer de beslissing op verwachtingen van waar het klantverkeer vandaan komt en hoe goed de rest van de ondersteunende back-endinfrastructuur van een app kan worden geschaald.  Met een staatloze toepassing van 100% kan een app bijvoorbeeld enorm worden geschaald met behulp van een combinatie van veel App Service-omgevingen in elke Azure-regio, vermenigvuldigd met App Service-omgevingen die zijn geïmplementeerd in veel Azure-regio's.  Met meer dan 15 wereldwijde Azure-regio's waar u uit kunt kiezen, kunnen klanten wereldwijd een grootschalige toepassingsvoetafdruk bouwen.  Voor de voorbeeld-app die voor dit artikel wordt gebruikt, zijn App Service omgevingen gemaakt in één Azure-regio (VS - zuid-centraal).
* **Naamconventie voor App Service omgevingen:**  Voor App Service Environment is een unieke naam vereist.  Naast een of twee App Service-omgevingen is het handig om een naamconventie te hebben om elke omgeving te App Service Environment.  Voor de voorbeeld-app is een eenvoudige naamconventie gebruikt.  De namen van de drie App Service Environments zijn *fe1ase,* *fe2ase* en *fe3ase.*
* **Naamconventie voor de apps:**  Omdat er meerdere exemplaren van de app worden geïmplementeerd, is er een naam nodig voor elk exemplaar van de geïmplementeerde app.  Een weinig bekende maar handige functie van App Service Environments is dat dezelfde app-naam kan worden gebruikt in meerdere omgevingen App Service omgevingen.  Omdat elke App Service Environment een uniek domeinachtervoegsel heeft, kunnen ontwikkelaars ervoor kiezen om dezelfde app-naam in elke omgeving opnieuw te gebruiken.  Een ontwikkelaar kan bijvoorbeeld apps met de naam als volgt hebben:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*, enzovoort.  Voor de voorbeeld-app heeft elk app-exemplaar echter ook een unieke naam.  De gebruikte namen van app-exemplaren zijn *webfrontend1,* *webfrontend2* en *webfrontend3.*

## <a name="setting-up-the-traffic-manager-profile"></a>Het Traffic Manager instellen
Zodra meerdere exemplaren van een app zijn geïmplementeerd in meerdere omgevingen App Service, kunnen de afzonderlijke app-exemplaren worden geregistreerd bij Traffic Manager.  Voor de voorbeeld-app is Traffic Manager profiel nodig *voor* scalable-ase-demo.trafficmanager.net waarmee klanten kunnen worden doorgeleid naar een van de volgende geïmplementeerde app-exemplaren:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd op de eerste App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd op de tweede App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Een exemplaar van de voorbeeld-app die is geïmplementeerd op de derde App Service Environment.

De eenvoudigste manier om meerdere eindpunten Azure App Service registreren, die  allemaal worden uitgevoerd in dezelfde Azure-regio, is met de PowerShell-Azure Resource Manager Traffic Manager [ondersteunen.][ARMTrafficManager]  

De eerste stap is het maken van een Azure Traffic Manager profiel.  In de onderstaande code ziet u hoe het profiel is gemaakt voor de voorbeeld-app:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

U ziet hoe de parameter *RelativeDnsName* is ingesteld op *scalable-ase-demo.*  Deze parameter zorgt ervoor dat de *domeinnaam scalable-ase-demo.trafficmanager.net* worden gemaakt en gekoppeld aan een Traffic Manager profiel.

De *parameter TrafficRoutingMethod* definieert het taakverdelingsbeleid dat Traffic Manager gebruikt om te bepalen hoe de belasting van klanten over alle beschikbare eindpunten wordt verdeeld.  In dit voorbeeld is de *methode Gewogen* gekozen.  Vanwege deze keuze worden aanvragen van klanten verdeeld over alle geregistreerde toepassings-eindpunten op basis van het relatieve gewicht dat aan elk eindpunt is gekoppeld. 

Wanneer het profiel is gemaakt, wordt elk app-exemplaar toegevoegd aan het profiel als een native Azure-eindpunt.  Met de volgende code wordt een verwijzing naar elke front-end-web-app opgehaald. Vervolgens wordt elke app toegevoegd als een Traffic Manager eindpunt via de parameter *TargetResourceId.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

U ziet dat er één aanroep naar *Add-AzureTrafficManagerEndpointConfig* is voor elk afzonderlijk app-exemplaar.  De *parameter TargetResourceId* in elke PowerShell-opdracht verwijst naar een van de drie geïmplementeerde app-exemplaren.  Het Traffic Manager verspreidt de belasting over alle drie de eindpunten die zijn geregistreerd in het profiel.

Alle drie eindpunten gebruiken dezelfde waarde (10) voor de parameter *Weight.*  Deze situatie leidt tot een Traffic Manager van aanvragen van klanten over alle drie de app-exemplaren relatief gelijkmatig te spreiden. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>De app-Custom Domain naar het Traffic Manager-domein
De laatste stap die nodig is, is het aangepaste domein van de app naar het Traffic Manager wijzen.  Wijs voor de voorbeeld-app `www.scalableasedemo.com` naar `scalable-ase-demo.trafficmanager.net` .  Voltooi deze stap met de domeinregistrar die het aangepaste domein beheert.  

Met behulp van de hulpprogramma's voor domeinbeheer van uw registrar moeten er CNAME-records worden gemaakt die naar het aangepaste domein op het Traffic Manager wijst.  In de onderstaande afbeelding ziet u een voorbeeld van hoe deze CNAME-configuratie eruitziet:

![CNAME voor Custom Domain][CNAMEforCustomDomain] 

Hoewel dit niet in dit onderwerp wordt behandeld, moet u er wel voor zorgen dat voor elk afzonderlijk app-exemplaar ook het aangepaste domein moet zijn geregistreerd.  Als een aanvraag naar een app-exemplaar komt en de toepassing het aangepaste domein niet heeft geregistreerd bij de app, mislukt de aanvraag.

In dit voorbeeld is het aangepaste domein en aan elk toepassings exemplaar `www.scalableasedemo.com` is het aangepaste domein gekoppeld.

![Aangepast domein][CustomDomain] 

Zie Aangepaste domeinen registreren voor een samenvatting van het registreren van een aangepast domein Azure App Service [apps.][RegisterCustomDomain]

## <a name="trying-out-the-distributed-topology"></a>De gedistribueerde topologie proberen
Het eindresultaat van de Traffic Manager dns-configuratie is dat aanvragen voor `www.scalableasedemo.com` door de volgende reeks stromen:

1. Een browser of apparaat maakt een DNS-zoekactie voor `www.scalableasedemo.com`
2. De CNAME-vermelding bij de domeinregistrar zorgt ervoor dat de DNS-zoekactie wordt omgeleid naar Azure Traffic Manager.
3. Er wordt een DNS-zoekactie *gemaakt voor scalable-ase-demo.trafficmanager.net* een van de Azure Traffic Manager DNS-servers.
4. Op basis van het taakverdelingsbeleid dat eerder is opgegeven in de parameter *TrafficRoutingMethod,* selecteert Traffic Manager een van de geconfigureerde eindpunten. Vervolgens wordt de FQDN van dat eindpunt naar de browser of het apparaat retourneert.
5. Omdat de FQDN van het eindpunt de URL is van een app-exemplaar dat wordt uitgevoerd op een App Service Environment, vraagt de browser of het apparaat een Microsoft Azure DNS-server om de FQDN om te zetten in een IP-adres. 
6. De browser of het apparaat verzendt de HTTP/S-aanvraag naar het IP-adres.  
7. De aanvraag wordt ontvangen bij een van de app-exemplaren die worden uitgevoerd op een van de App Service omgevingen.

In de onderstaande consoleafbeelding ziet u een DNS-zoekactie voor het aangepaste domein van de voorbeeld-app. Het wordt opgelost naar een app-exemplaar dat wordt uitgevoerd op een van de drie voorbeeldomgevingen App Service (in dit geval de tweede van de drie App Service Environments):

![DNS-zoekactie][DNSLookup] 

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en informatie
Documentatie over de [PowerShell-Azure Resource Manager Traffic Manager ondersteuning.][ARMTrafficManager]  

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
