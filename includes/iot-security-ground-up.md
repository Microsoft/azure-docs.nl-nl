---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 234407e6973657ba3ad0d78189e7cb1d363c15e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95561675"
---
De Internet of Things (IoT) vormt unieke beveiligings-, privacy-en nalevings uitdagingen voor bedrijven wereld wijd. In tegens telling tot traditionele Cyber technologie waarbij deze problemen rond software en hoe deze worden geïmplementeerd, is IoT wat er gebeurt wanneer de Cyber en de fysieke werelden convergeren. Het beveiligen van IoT-oplossingen vereist dat beveiligde inrichting van apparaten, beveiligde connectiviteit tussen deze apparaten en de Cloud, en de beveiliging van gegevens in de Cloud wordt gewaarborgd tijdens de verwerking en opslag. Voor het werken met deze functionaliteit zijn echter apparaten die zijn beperkt tot resources, geografische distributie van implementaties en een groot aantal apparaten binnen een oplossing.

In dit artikel wordt uitgelegd hoe de accelerators van de IoT-oplossing een veilige en privé Internet of Things Cloud oplossing bieden. De oplossings versnellers bieden een volledige end-to-end oplossing, waarbij de beveiliging in elke fase van het wegdek is ingebouwd. Bij micro soft is het ontwikkelen van beveiligde software onderdeel van de software techniek-praktijk, geroot in de lange ervaring van micro soft voor het ontwikkelen van beveiligde software. Om ervoor te zorgen, is Security Development Lifecycle (SDL) de Foundational development methodologie, gekoppeld aan een host van beveiligings Services op infrastructuur niveau, zoals Operational Security Assurance (OSA) en de micro soft-eenheid voor digitale misdrijven, micro soft Security Response Center en micro soft Malware Protection Center.

De oplossings versnellers bieden unieke functies die het inrichten, verbinding maken met en opslaan van gegevens van IoT-apparaten eenvoudig en transparant maken en, vooral veilig. In dit artikel worden de beveiligings functies van de Azure IoT-oplossing en implementatie strategieën onderzocht om te zorgen voor beveiligings-, privacy-en nalevings problemen.

## <a name="introduction"></a>Inleiding

De Internet of Things (IoT) is de Golf van de toekomst en biedt bedrijven onmiddellijke en praktijk mogelijkheden om de kosten te verlagen, de omzet te verhogen en hun bedrijf te transformeren. Veel bedrijven zijn echter cloudhoster om IoT in hun organisaties te implementeren, omdat er problemen zijn met de veiligheid, privacy en naleving. Een belang rijk punt van bezorgdheid is gebaseerd op de uniekheid van de IoT-infra structuur, die de Cyber-en fysieke werelden samen voegt, waarbij de afzonderlijke Risico's die inherent zijn aan deze twee werelden, worden samengevoegd. De beveiliging van IoT is van toepassing op het garanderen van de integriteit van code die wordt uitgevoerd op apparaten, het verlenen van verificatie van apparaten en gebruikers, het definiëren van duidelijke eigendom van apparaten (evenals de gegevens die door deze apparaten worden gegenereerd), en het is flexibel voor Cyber-en fysieke aanvallen.

Vervolgens is het probleem met de privacy. Bedrijven willen transparantie hebben op het verzamelen van gegevens, zoals wordt verzameld en waarom, wie het kan zien, wie de toegang beheert, enzovoort. Ten slotte zijn er algemene veiligheids problemen van de apparatuur, samen met de mensen die ze gebruiken, en de problemen met het behoud van de naleving van de industrie normen.

Gezien de beveiligings-, privacy-, transparantie-en nalevings problemen, is het kiezen van de juiste provider voor IoT-oplossingen een uitdaging. Het combi neren van afzonderlijke onderdelen van IoT-software en-services die door verschillende leveranciers worden meegeleverd, introduceert de beveiliging, privacy, transparantie en naleving, die mogelijk moeilijk te detecteren zijn. De keuze van de juiste IoT-software en-service provider is gebaseerd op het zoeken naar providers met uitgebreide ervaring met het uitvoeren van services, die over verticale en geografies beschikken, maar ook op een veilige en transparante manier kan worden geschaald. Op deze manier kan de geselecteerde provider tien tallen ervaring hebben met het ontwikkelen van beveiligde software die wordt uitgevoerd op miljarden machines wereld wijd, en u de mogelijkheid wilt bieden om de bedreigings mogelijkheden van deze nieuwe wereld van de Internet of Things te waarderen.

## <a name="secure-infrastructure-from-the-ground-up"></a>Infra structuur vanaf het begin beveiligen

De [Microsoft Cloud](https://azure.microsoft.com) -infra structuur biedt ondersteuning voor meer dan 1.000.000.000 klanten in 127 landen/regio's. De Microsoft Cloud biedt een grotere mate van verbeterde beveiliging, privacy, naleving en bedreigingen die de meeste klanten op hun eigen manier kunnen belasten op de decennia lange ervaring van micro soft met het bouwen van bedrijfs software en het uitvoeren van een van de grootste onlineservices ter wereld.

De [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/) biedt een verplicht ontwikkelings proces voor het hele bedrijf waarmee beveiligings vereisten in de gehele levens cyclus van de software worden inge sloten. Om ervoor te zorgen dat operationele activiteiten hetzelfde beveiligings beleid volgen, maakt SDL gebruik van strenge beveiligings richtlijnen die zijn opgenomen in het proces van micro soft Operational Security Assurance (OSA). Micro soft werkt ook samen met audit bedrijven van derden om te verifiëren dat het voldoet aan de nalevings verplichtingen en dat micro soft zich bezighoudt met het maken van een ruime beveiliging, met inbegrip van de micro soft-eenheid voor digitale misdrijven, micro soft Security Response Center en het micro soft Malware Protection Center.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>Microsoft Azure-veilige IoT-infra structuur voor uw bedrijf

Microsoft Azure biedt een volledige Cloud oplossing, een die een voortdurend groeiende verzameling van geïntegreerde Cloud Services combineert: analyse, machine learning, opslag, beveiliging, netwerken en Internet, met een toonaangevende toezeg ging voor de bescherming en privacy van uw gegevens. In het geval van een [schending](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) van micro soft wordt gebruikgemaakt van een speciaal *rood team* van software beveiligings experts die aanvallen simuleren, de mogelijkheid van Azure testen om te detecteren, te beschermen tegen opkomende bedreigingen en te herstellen van inbreuken. Het [wereld wijde incidenten](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) team van micro soft werkt rond de klok om de gevolgen van aanvallen en schadelijke activiteiten te verhelpen. Het team volgt vastgelegde procedures voor incident beheer, communicatie en herstel, en maakt gebruik van Detecteer bare en voorspelbaarde interfaces met interne en externe partners.

De systemen van micro soft bieden voortdurende indringings detectie en preventie, preventie van aanvallen van services, regel matige indringings tests en forensische-hulpprogram ma's waarmee bedreigingen kunnen worden geïdentificeerd en verminderd. [Multi-factor Authentication](../articles/active-directory/authentication/concept-mfa-howitworks.md) biedt een extra beveiligingslaag waarmee eind gebruikers toegang hebben tot het netwerk. En voor de toepassing en de host-provider biedt micro soft toegangs beheer, bewaking, anti-malware, scan van beveiligings problemen, patches en configuratie beheer.

De oplossings Accelerators maken gebruik van de beveiliging en privacy die zijn ingebouwd in het Azure-platform, samen met de SDL-en OSA-processen voor een veilige ontwikkeling en werking van alle micro soft-software. Deze procedures bieden beveiliging van de infra structuur, netwerk beveiliging en identiteits-en beheer functies die van toepassing zijn op de beveiliging van elke oplossing.

De [Azure-IOT hub](../articles/iot-hub/about-iot-hub.md) in de [IOT-oplossings versnellers](../articles/iot-fundamentals/iot-introduction.md) biedt een volledig beheerde service die betrouw bare en veilige bidirectionele communicatie mogelijk maakt tussen IOT-apparaten en Azure-Services, zoals [Azure machine learning](../articles/machine-learning/classic/index.yml) en [Azure stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md) met behulp van beveiligings referenties per apparaat en toegangs beheer.

Voor de beste communicatie over de beveiligings-en privacyfuncties die zijn ingebouwd in de Azure IoT-oplossings accelerators, wordt in dit artikel de suite onderverdeeld in de drie primaire beveiligings gebieden.

![Azure IoT-oplossingsversnellers](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Inrichting en verificatie van het apparaat beveiligen

De oplossings Accelerators beveiligen apparaten in het veld door een unieke id-sleutel op te geven voor elk apparaat, dat door de IoT-infra structuur kan worden gebruikt om te communiceren met het apparaat wanneer dit in werking is. Het proces is snel en gemakkelijk in te stellen. De gegenereerde sleutel met een door de gebruiker geselecteerde apparaat-ID vormt de basis van een token dat wordt gebruikt in alle communicatie tussen het apparaat en de Azure IoT Hub.

Apparaat-Id's kunnen worden gekoppeld aan een apparaat tijdens de productie (dat is geknipperd in een hardware Trust module) of kan een bestaande vaste identiteit als een proxy gebruiken (bijvoorbeeld CPU-serie nummers). Omdat het wijzigen van de identiteits gegevens op het apparaat niet eenvoudig is, is het belang rijk om Id's van logische apparaten op te nemen voor het geval de hardware van het onderliggende apparaat verandert, maar het logische apparaat blijft hetzelfde. In sommige gevallen kan de koppeling van een apparaat-id optreden op het tijdstip van de implementatie van het apparaat (bijvoorbeeld een geverifieerde veld technicus heeft fysiek een nieuw apparaat geconfigureerd tijdens de communicatie met de back-end van de oplossing). Het [Azure IOT hub Identity-REGI ster](../articles/iot-hub/iot-hub-devguide.md) biedt beveiligde opslag van apparaat-id's en beveiligings sleutels voor een oplossing. Individuele identiteiten of groepen met apparaat-id's kunnen worden toegevoegd aan een lijst met toegestane apparaten of aan een blokkerings lijst, waardoor de toegang tot het apparaat volledig kan worden beheerd.

Azure IoT Hub Access Control-beleid in de Cloud activeren en uitschakelen van de apparaat-id, zodat een apparaat kan worden ontkoppeld van een IoT-implementatie wanneer dat nodig is. Deze koppeling en ontkoppeling van apparaten is gebaseerd op elke apparaat-id.

Aanvullende beveiligings functies voor apparaten zijn onder andere:

* Apparaten accepteren geen ongevraagde netwerk verbindingen. Ze maken alle verbindingen en routes op een uitgaande manier. Als een apparaat een opdracht van de back-end wil ontvangen, moet het apparaat een verbinding initiëren om te controleren of er in behandeling zijnde opdrachten kunnen worden verwerkt. Als er een verbinding tussen het apparaat en IoT Hub veilig tot stand is gebracht, kan berichten van de Cloud naar het apparaat en het apparaat op transparante wijze worden verzonden.

* Apparaten maken alleen verbinding met of maken routes naar bekende services waarmee ze zijn verbonden, zoals een Azure-IoT Hub.

* Verificatie en verificatie op systeem niveau maken gebruik van identiteiten per apparaat, zodat er bijna direct toegang wordt verleend tot referenties en machtigingen.

### <a name="secure-connectivity"></a>Beveiligde connectiviteit

De duurzaamheid van berichten is een belang rijk onderdeel van een IoT-oplossing. De nood zaak om blijvend te leveren en/of gegevens van apparaten te ontvangen, is onderstreept op basis van het feit dat IoT-apparaten zijn verbonden via internet of andere vergelijk bare netwerken die onbetrouwbaar kunnen zijn. Azure IoT Hub biedt de duurzaamheid van berichten tussen Clouds en apparaten via een systeem van bevestigingen in antwoord op berichten. Extra duurzaamheid voor berichten wordt bereikt door de berichten in de IoT Hub Maxi maal zeven dagen voor telemetrie en twee dagen voor opdrachten in de cache op te zetten.

Efficiëntie is belang rijk voor het behoud van de instand houding van resources en bewerkingen in een omgeving die is beperkt tot een resource. HTTPS (HTTP Secure), de industrie standaard beveiligde versie van het populaire http-protocol, wordt ondersteund door Azure IoT Hub, waardoor efficiënte communicatie mogelijk is. Advanced Message Queueing Protocol (AMQP) en Message Queuing telemetrie-Trans Port (MQTT), ondersteund door Azure IoT Hub, zijn niet alleen ontworpen voor de efficiëntie van resource gebruik, maar ook betrouw bare aflevering van berichten. 

Voor schaal baarheid moet u de mogelijkheid bieden om veilig te kunnen omgaan met een breed scala aan apparaten. Met Azure IoT hub kunt u een beveiligde verbinding maken met IP-en niet-IP-apparaten. Apparaten waarvoor IP is ingeschakeld, kunnen rechtstreeks verbinding maken met de IoT Hub via een beveiligde verbinding en met elkaar communiceren. Apparaten die niet geschikt zijn voor IP-verkeer, zijn beperkt en kunnen alleen verbinding maken via protocollen voor de korte afstand, zoals Zwave, ZigBee en Bluetooth. Een veld Gateway wordt gebruikt voor het samen voegen van deze apparaten en voert protocol omzetting uit om een veilige bidirectionele communicatie met de Cloud mogelijk te maken.

Aanvullende beveiligings functies voor verbindingen zijn onder andere:

* Het communicatie traject tussen apparaten en Azure IoT Hub, of tussen gateways en Azure IoT Hub, wordt beveiligd met behulp van de industrie norm Transport Layer Security (TLS) met Azure IoT Hub geverifieerd met behulp van X. 509-protocol.

* Om apparaten te beschermen tegen ongevraagde binnenkomende verbindingen, opent Azure IoT Hub geen verbinding met het apparaat. Het apparaat initieert alle verbindingen.

* Azure IoT Hub blijvend slaat berichten op voor apparaten en wacht op het apparaat om verbinding te maken. Deze opdrachten worden twee dagen opgeslagen, waardoor apparaten die sporadisch verbinding maken, vanwege stroom-of connectiviteits problemen, deze opdrachten kunnen ontvangen. Azure IoT Hub houdt per apparaat een wachtrij voor elk apparaat bij.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Beveiligde verwerking en opslag in de Cloud

Met de versleutelde communicatie om gegevens in de cloud te verwerken, helpen de oplossings versnellers de gegevens veilig te houden. Het biedt flexibiliteit voor het implementeren van extra versleuteling en beheer van beveiligings sleutels.

Met behulp van Azure Active Directory (AAD) voor gebruikers verificatie en-autorisatie kunnen Azure IoT-oplossings Accelerators een op beleid gebaseerd autorisatie model bieden voor gegevens in de Cloud, waardoor eenvoudig toegangs beheer kan worden gecontroleerd en gecontroleerd. Met dit model kunt u de toegang tot gegevens in de Cloud, en van apparaten die zijn verbonden met de Azure IoT-oplossings accelerators, direct intrekken.

Zodra de gegevens zich in de cloud bevinden, kunnen ze worden verwerkt en opgeslagen in een door de gebruiker gedefinieerde werk stroom. De toegang tot elk deel van de gegevens wordt bepaald door Azure Active Directory, afhankelijk van de gebruikte opslag service.

Alle sleutels die worden gebruikt door de IoT-infra structuur, worden opgeslagen in de cloud in beveiligde opslag, met de mogelijkheid om in te richten op de sleutel sleutels moet opnieuw worden ingericht. Gegevens kunnen worden opgeslagen in [Azure Cosmos DB](../articles/cosmos-db/introduction.md) of in [SQL database](../articles/azure-sql/database/sql-database-paas-overview.md), waardoor de definitie van het gewenste beveiligings niveau kan worden bepaald. Daarnaast biedt Azure een manier om alle toegang tot uw gegevens te bewaken en te controleren om u te waarschuwen over inbreuken of onbevoegde toegang.

## <a name="conclusion"></a>Conclusie

De Internet of Things begint met uw dingen, de dingen die het meeste zijn voor bedrijven. IoT kan verbluffende waarde aan een bedrijf leveren door de kosten te verlagen, de omzet te verhogen en de bedrijfs activiteiten te transformeren. Het slagen van deze trans formatie is grotendeels afhankelijk van de keuze van de juiste IoT-software en-service provider. Dit betekent dat u een provider kunt vinden die niet alleen catalyzes deze trans formatie door inzicht te krijgen in de behoeften en vereisten van uw bedrijf, maar biedt ook services en software die zijn gebouwd met beveiliging, privacy, transparantie en naleving als belang rijke ontwerp overwegingen. Micro soft heeft uitgebreide ervaring met het ontwikkelen en implementeren van beveiligde software en services en blijft een leider zijn in deze nieuwe leeftijd van Internet of Things.

De oplossings Accelerators maken gebruik van beveiligings maatregelen, waardoor het mogelijk is om bedrijfs middelen veilig te controleren, de operationele prestaties te verbeteren en geavanceerde gegevens analyse te gebruiken om bedrijven te transformeren. Dankzij de gelaagde benadering van beveiliging, meerdere beveiligings functies en ontwerp patronen, helpen de oplossings versnellers een infra structuur te implementeren die kan worden vertrouwd om elk bedrijf te transformeren.

## <a name="additional-information"></a>Aanvullende informatie

Elke oplossings versneller maakt exemplaren van Azure-Services, zoals:

* [**Azure IOT hub**](https://azure.microsoft.com/services/iot-hub/): uw gateway die de Cloud verbindt met apparaten. U kunt schalen naar miljoenen verbindingen per hub en enorme hoeveel heden gegevens verwerken met verificatie per apparaat om u te helpen uw oplossing te beveiligen.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): een schaal bare, volledig geïndexeerde database service voor semi-gestructureerde gegevens die meta gegevens beheert voor de apparaten die u voorziet, zoals kenmerken, configuratie en beveiligings eigenschappen. Azure Cosmos DB biedt verwerkings processen met hoge prestaties en hoge door Voer, neutraal indexering van gegevens en een geavanceerde SQL-query interface.

* [**Azure stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): realtime stroom verwerking in de Cloud waarmee u snel een goedkope analyse oplossing kunt ontwikkelen en implementeren om realtime inzichten te verkrijgen op basis van apparaten, Sens oren, infra structuur en toepassingen. De gegevens van deze volledig beheerde service kunnen naar elk volume worden geschaald, terwijl toch hoge door Voer, lage latentie en tolerantie wordt bereikt.

* [**Azure-app Services**](https://azure.microsoft.com/services/app-service/): een Cloud platform voor het bouwen van krachtige web-en mobiele apps die overal verbinding maken met gegevens. in de Cloud of on-premises. Bouw aantrekkelijke mobiele apps voor iOS, Android en Windows. Integreer met uw software als een service (SaaS) en zakelijke toepassingen met out-of-the-box-connectiviteit tot tien tallen Cloud Services en zakelijke toepassingen. Code in uw favoriete taal en IDE, .NET, Node.js, PHP, python of Java, om sneller dan ooit web-apps en Api's te bouwen.

* [**Logic apps**](https://azure.microsoft.com/services/app-service/logic/): de Logic apps functie van Azure app service helpt uw IOT-oplossing te integreren met uw bestaande line-of-Business-systemen en om werk stroom processen te automatiseren. Met Logic Apps kunnen ontwikkel aars werk stromen ontwerpen die beginnen met een trigger en vervolgens een reeks stappen uitvoeren: regels en acties die gebruikmaken van krachtige connectors om te integreren met uw bedrijfs processen. Logic Apps biedt kant-en-klare connectiviteit met een aanzienlijk ecosysteem van SaaS-, Cloud-en on-premises toepassingen.

* [**Azure Blob-opslag**](https://azure.microsoft.com/services/storage/): betrouw bare, voordelige Cloud opslag voor de gegevens die uw apparaten naar de Cloud verzenden.