---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557836"
---
Bij het ontwerpen van een systeem is het belang rijk om inzicht te krijgen in de mogelijke dreigingen van dat systeem en de juiste verdedigingen dienovereenkomstig, aangezien het systeem is ontworpen en gearchitectd. Het is belang rijk dat u het product ontwerpt van het begin met beveiliging in het gedrang, omdat u kunt zien hoe een aanvaller een systeem kan misbruiken om ervoor te zorgen dat de juiste oplossingen van het begin worden uitgevoerd.

## <a name="security-starts-with-a-threat-model"></a>Beveiliging begint met een bedreigings model

Micro soft heeft lange gebruikte bedreigings modellen voor de producten en heeft het bedreigings model voor het bedrijf openbaar beschikbaar gemaakt. De ervaring van het bedrijf heeft gedemonstreerd dat de modelleerder onverwachte voor delen heeft dan een onmiddellijke kennis van welke bedreigingen het meest betrekking hebben. Er wordt bijvoorbeeld ook een tijd gemaakt voor een open discussie met anderen buiten het ontwikkel team. Dit kan leiden tot nieuwe ideeën en verbeteringen in het product.

Het doel van bedreigings modellering is om te begrijpen hoe een aanvaller een systeem kan binnendringen en vervolgens te zorgen dat er geschikte oplossingen zijn. Met bedreigings modellen wordt het ontwerp team gedwongen om oplossingen te beschouwen als het systeem is ontworpen in plaats van nadat een systeem is geïmplementeerd. Dit is zeer belang rijk, omdat retrofitting-beveiligings beveiliging op allerlei apparaten in het veld niet haalbaar is, fout gevoelig is en klanten risico lopen.

Veel ontwikkel teams doen een uitstekende taak voor het vastleggen van de functionele vereisten voor het systeem voor het voor deel van klanten. Het identificeren van niet-duidelijke manieren waarop iemand het systeem kan misbruiken, is echter lastiger. Met bedreigings modellen kunnen ontwikkel teams begrijpen wat een aanvaller kan doen en waarom. Bedreigings modellering is een structureel proces dat een discussie maakt over de beslissingen over het ontwerpen van het ontwerp in het systeem, evenals wijzigingen in het ontwerp dat wordt toegepast op de manier waarop de beveiliging wordt beïnvloed. Hoewel een bedreigings model gewoon een document is, is deze documentatie ook een ideale manier om de continuïteit van kennis, het bewaren van geleerde lessen te waarborgen en het nieuwe team snel op de weg te helpen. Ten slotte is het mogelijk een resultaat van het model leren van bedreigingen te maken, zodat u rekening moet houden met andere aspecten van de beveiliging, zoals welke beveiligings verplichtingen u voor uw klanten wilt bieden. Deze toezeg gingen in combi natie met bedreigings modellen informeren en testen van uw Internet of Thingse IoT-oplossing.

### <a name="when-to-do-threat-modeling"></a>Wanneer moet u een bedreigings model maken?

[Threat modellering](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) biedt de grootste waarde wanneer u deze opneemt in de ontwerp fase. Wanneer u ontwerpt, hebt u de grootste flexibiliteit voor het maken van wijzigingen in het elimineren van bedreigingen. Het verwijderen van bedreigingen per ontwerp is het gewenste resultaat. Het is veel eenvoudiger dan het toevoegen van oplossingen, het testen ervan, en ervoor te zorgen dat ze actueel blijven en bovendien kan deze schrap ping niet altijd worden uitgevoerd. Het is lastig om bedreigingen te elimineren naarmate een product rijpder wordt, en uiteindelijk meer werk te vereisen en veel moeilijker te worden gecompromisd dan voor de ontwikkeling van bedreigingen.

### <a name="what-to-consider-for-threat-modeling"></a>Wat u kunt overwegen voor bedreigings modellen

Bekijk de volledige oplossing en richt u ook op de volgende gebieden:

* De beveiligings-en privacyfuncties
* De functies waarvan de fouten van belang zijn voor beveiliging
* De functies die de grens van een vertrouwens relatie aanraken

### <a name="who-performs-threat-modeling"></a>Wie Threat Modeler uitvoert

Bedreigings modellering is een proces zoals elk ander. Het is een goed idee om het bedreigings model document te behandelen zoals elk ander onderdeel van de oplossing en dit te valideren. Veel ontwikkel teams doen een uitstekende taak voor het vastleggen van de functionele vereisten voor het systeem voor het voor deel van klanten. Het identificeren van niet-duidelijke manieren waarop iemand het systeem kan misbruiken, is echter lastiger. Met bedreigings modellen kunnen ontwikkel teams begrijpen wat een aanvaller kan doen en waarom.

### <a name="how-to-perform-threat-modeling"></a>Bedreigings modellen uitvoeren

Het proces voor het maken van bedreigings modellen bestaat uit vier stappen. de stappen zijn:

* De toepassing model leren
* Bedreigingen opsommen
* Bedreigingen beperken
* De beperkingen valideren

#### <a name="the-process-steps"></a>De proces stappen

Drie vuist regels die u moet onthouden wanneer u een bedreigings model bouwt:

1. Een diagram maken van de referentie architectuur.

2. Begin breedte-eerst. Bekijk een overzicht en begrijp het systeem als geheel, voordat u dieper gaat. Deze aanpak zorgt ervoor dat u op de juiste plaatsen dieper op de hoogte bent.

3. Zorg dat het proces wordt uitgevoerd. Als u een probleem ondervindt in de model fase en u dit wilt verkennen, gaat u naar! U hoeft deze stappen niet uit te voeren slavishly.

#### <a name="threats"></a>Bedreigingen

De vier kern elementen van een bedreigings model zijn:

* Processen zoals webservices, Win32-services en * nix-daemons. Sommige complexe entiteiten (bijvoorbeeld veld gateways en Sens oren) kunnen als een proces worden abstract als een technisch inzoomen op deze gebieden niet mogelijk is.

* Gegevens archieven (overal waar gegevens worden opgeslagen, zoals een configuratie bestand of data base)

* Gegevens stroom (waarbij gegevens worden verplaatst tussen andere elementen in de toepassing)

* Externe entiteiten (alles wat communiceert met het systeem, maar niet onder controle van de toepassing, bijvoorbeeld gebruikers en satelliet feeds)

Alle elementen in het architecturale diagram zijn onderhevig aan verschillende bedreigingen. Dit artikel de STRIDE-instructie. Lees de [bedreigings modellen opnieuw, STRIDE](/archive/blogs/larryosterman/threat-modeling-again-stride) voor meer informatie over de STRIDE-elementen.

Voor verschillende elementen van het toepassings diagram gelden bepaalde STRIDE-bedreigingen:

* Processen zijn onderhevig aan STRIDE
* Voor gegevens stromen gelden TID
* Gegevens archieven zijn onderhevig aan TID en soms R, wanneer de gegevens archieven logboek bestanden zijn.
* Externe entiteiten zijn onderhevig aan SRD

## <a name="security-in-iot"></a>Beveiliging in IoT

Verbonden apparaten met een speciaal doel hebben een groot aantal potentiële communicatie gebieden en interactie patronen, die allemaal moeten worden overwogen om een Framework te bieden voor het beveiligen van digitale toegang tot die apparaten. De term ' Digital Access ' wordt hier gebruikt om onderscheid te maken tussen bewerkingen die worden uitgevoerd door middel van directe apparaten interactie, waarbij toegang tot beveiliging via fysieke toegangs beheer wordt gegeven. Zet het apparaat bijvoorbeeld in een ruimte met een vergren deling van de deur. Fysieke toegang kan niet worden geweigerd met software en hardware, maar u kunt maat regelen nemen om te voor komen dat fysieke toegang tot systeem storingen leidt.

Bekijk bij het verkennen van de interactie patronen ' apparaatbesturing ' en ' apparaatgegevens ' met hetzelfde niveau van aandacht. "Apparaatbesturing" kan worden geclassificeerd als alle gegevens die door een partij aan een apparaat worden verstrekt, met het doel van het wijzigen of invloed hebben op de werking ervan naar de staat of de status van de omgeving. ' Apparaatgegevens ' kunnen worden geclassificeerd als informatie die een apparaat meebrengt naar een andere partij over zijn staat en de waargenomen status van de omgeving.

Als u de aanbevolen beveiligings procedures wilt optimaliseren, is het raadzaam om een typische IoT-architectuur te verdelen in verschillende onderdelen/zones als onderdeel van de sporten van de bedreigings modellering. Deze zones worden volledig beschreven in deze sectie en bevatten onder andere:

* Apparaatconfiguratie
* Veld Gateway,
* Cloud gateways en
* Onderzoeksservices.

Zones zijn een brede manier om een oplossing te segmenteren. elke zone heeft vaak een eigen vereisten voor gegevens en verificatie en autorisatie. Zones kunnen ook worden gebruikt voor het isoleren van schade en het beperken van de impact van laag vertrouwens zones in hogere vertrouwens zones.

Elke zone wordt gescheiden door een grens van de vertrouwens relatie, die wordt vermeld als de gestippelde rode lijn in het volgende diagram. Dit vertegenwoordigt een overgang van gegevens/informatie van de ene bron naar een andere. Tijdens deze overgang kunnen de gegevens/informatie worden onderhevig aan vervalsing, knoeien, afwijzing, vrijgeven van informatie, denial of service en uitbrei ding van bevoegdheden (STRIDE).

![IoT-beveiligings zones](media/iot-security-architecture/iot-security-architecture-fig1.png) 

De onderdelen die in elke grens worden weer gegeven, worden ook onderhevig aan STRIDE, waardoor een volledig 360 bedreigings model weergave van de oplossing mogelijk is. In de volgende secties vindt u informatie over elk van de onderdelen en specifieke beveiligings problemen en oplossingen die moeten worden geïmplementeerd.

In de volgende secties worden de standaard onderdelen besproken die doorgaans in deze zones worden gevonden.

### <a name="the-device-zone"></a>De zone apparaat

De omgeving van het apparaat is de onmiddellijke fysieke ruimte rond het apparaat waar fysieke toegang en/of ' lokale netwerk ' peer-to-peer digitale toegang tot het apparaat haalbaar is. Er wordt aangenomen dat een ' lokale netwerk ' een netwerk is dat verschilt van, maar mogelijk is gebrugd op basis van het open bare Internet, en een draadloze radio technologie met een kort bereik die peer-to-peer communicatie van apparaten toestaat. Het bevat *geen* Netwerkvirtualisatie technologie die de illusie van een dergelijk lokaal netwerk maakt en bevat ook geen open bare-operator netwerken waarvoor twee apparaten moeten communiceren over open bare netwerk ruimte als ze een peer-to-peer-communicatie relatie zouden invoeren.

### <a name="the-field-gateway-zone"></a>De zone veld Gateway

De veld Gateway is een apparaat/toestel of enige algemene Server computer software die fungeert als communicatie-enabler en mogelijk als een apparaat voor Apparaatbeheer en apparaatgegevens verwerkings hub. De zone veld Gateway bevat de veld Gateway zelf en alle apparaten die eraan zijn gekoppeld. Zoals de naam al aangeeft, fungeren veld gateways buiten toegewezen gegevensverwerkings faciliteiten, meestal locatie gebonden, zijn ze mogelijk fysiek indringing en hebben ze beperkte operationele redundantie. Alles om te zeggen dat een veld Gateway meestal een aanraak-en sabotage is terwijl u weet wat de functie is.

Een veld Gateway wijkt af van een louter Traffic-router in dat het een actieve rol heeft bij het beheer van toegang en informatie stroom, wat betekent dat het een door een toepassing geadresseerde entiteit en netwerk verbinding of sessie-Terminal is. Een NAT-apparaat of-firewall komt daarentegen niet in aanmerking als veld gateways, omdat ze geen expliciete verbinding of sessie terminals zijn, maar in plaats daarvan een route-of blok-verbinding of-sessies. De veld Gateway heeft twee verschillende oppervlakte gebieden. Een van de apparaten die eraan zijn gekoppeld en die de binnenkant van de zone vertegenwoordigen, en de andere gezichten alle externe partijen en is de rand van de zone.

### <a name="the-cloud-gateway-zone"></a>De zone van de Cloud gateway

Een Cloud gateway is een systeem dat externe communicatie mogelijk maakt vanuit en naar apparaten of veld gateways van verschillende verschillende locaties via open bare netwerk ruimte, in het algemeen naar een systeem op basis van de Cloud en een gegevens analysesysteem, een Federatie van deze systemen. In sommige gevallen kan een Cloud gateway direct de toegang tot apparaten met speciale doel einden van terminals, zoals tablets of telefoons, vergemakkelijken. In de hier besproken context is ' Cloud ' bedoeld om te verwijzen naar een speciaal systeem voor gegevens verwerking dat niet is gebonden aan dezelfde site als de gekoppelde apparaten of veld gateways. In een Cloud zone kunnen operationele maat regelen geen gerichte fysieke toegang voor komen en niet noodzakelijkerwijs worden blootgesteld aan een ' open bare Cloud '-infra structuur.  

Een Cloud gateway kan mogelijk worden toegewezen aan een netwerk-virtualisatie-overlay om de Cloud gateway en alle bijbehorende apparaten of veld gateways van elk ander netwerk verkeer te isoleren. De Cloud gateway zelf is geen Apparaatbeheer systeem of een opslag ruimte voor apparaatgegevens. deze faciliteiten interface met de Cloud gateway. De zone Cloud gateway bevat de Cloud gateway zelf, samen met alle veld gateways en apparaten die rechtstreeks of indirect zijn gekoppeld. De rand van de zone is een unieke surface area waarbij alle externe partijen communiceren via.

### <a name="the-services-zone"></a>De services-zone

Een ' service ' wordt voor deze context gedefinieerd als software onderdeel of module die is gekoppeld aan apparaten via een veld-of Cloud gateway voor het verzamelen en analyseren van gegevens, en voor opdrachten en besturings elementen. Services zijn bemiddelaars. Ze handelen onder hun identiteit voor gateways en andere subsystemen, slaan en analyseren gegevens, autonoom uitgegeven opdrachten aan apparaten op basis van gegevens inzichten of planningen en bieden informatie en beheer mogelijkheden voor geautoriseerde eind gebruikers.

### <a name="information-devices-versus-special-purpose-devices"></a>Informatie-apparaten versus apparaten voor speciale doel einden

Pc's, telefoons en tablets zijn voornamelijk interactieve informatie apparaten. Telefoons en tablets zijn expliciet geoptimaliseerd om de levens duur van de accu te maximaliseren. Deze worden bij voor keur deels uitgeschakeld wanneer niet direct met een persoon communiceert of wanneer er geen services worden geboden zoals het afspelen van muziek of het verenigen van de eigenaar van een bepaalde locatie. Vanuit een perspectief van systemen zijn deze IT-apparaten voornamelijk gericht op het bereiken van personen. Het gaat hierbij om een suggestie voor het verzamelen van acties en ' mensen Sens oren '.

Apparaten voor speciale doel einden, van eenvoudige temperatuur Sens oren tot complexe fabrieks productie lijnen met duizenden onderdelen erin, verschillen. Deze apparaten zijn veel meer in het doel en zelfs als ze een bepaalde gebruikers interface bieden, zijn ze grotendeels gericht op interfacing met of worden geïntegreerd in assets in de fysieke wereld. Ze meten en rapporteren omgevings omstandigheden, scha kelen, kleppen, servos, geluids signalen, Switch verlichting en veel andere taken. Ze kunnen werk doen waarvoor een informatie apparaat te algemeen is, te kostbaar of te zwakke. Het concrete doel bepaalt direct het technische ontwerp en het beschik bare monetaire budget voor hun productie en de geplande levens duur. De combi natie van deze twee belang rijke factoren beperkt het beschik bare operationele energie budget, de fysieke footprint en zo beschik bare opslag, reken kracht en beveiligings mogelijkheden.

Als er iets mis gaat met geautomatiseerde of externe apparaten die kunnen worden beheerd, bijvoorbeeld fysieke defecten of logische fouten in het beheer van logica voor ongeoorloofde indringing en manipulatie van willful. De productie loten kunnen worden vernietigd, gebouwen kunnen worden looted of worden gebrand, en personen kunnen gewond of zelfs sterven. Dit is een geheel andere klasse beschadiging dan iemand die de limiet van een gestolen credit card maxing. De beveiligings balk voor apparaten die dingen verplaatsen en ook voor sensor gegevens die uiteindelijk resulteren in opdrachten die het verplaatsen van items veroorzaken, moet hoger zijn dan in een e-commerce-of Bank scenario.

### <a name="device-control-and-device-data-interactions"></a>Apparaatbesturing en apparaatgegevens interacties

Verbonden apparaten met een speciaal doel hebben een groot aantal potentiële communicatie gebieden en interactie patronen, die allemaal moeten worden overwogen om een Framework te bieden voor het beveiligen van digitale toegang tot die apparaten. De term ' Digital Access ' wordt hier gebruikt om onderscheid te maken tussen bewerkingen die worden uitgevoerd door middel van directe apparaten interactie, waarbij toegang tot beveiliging via fysieke toegangs beheer wordt gegeven. Zet het apparaat bijvoorbeeld in een ruimte met een vergren deling van de deur. Fysieke toegang kan niet worden geweigerd met software en hardware, maar u kunt maat regelen nemen om te voor komen dat fysieke toegang tot systeem storingen leidt.

Zoek bij het verkennen van de interactie patronen naar ' apparaatbesturing ' en ' apparaatgegevens ' met hetzelfde niveau van aandacht tijdens het maken van bedreigings modellen. "Apparaatbesturing" kan worden geclassificeerd als alle gegevens die door een partij aan een apparaat worden verstrekt, met het doel van het wijzigen of invloed hebben op de werking ervan naar de staat of de status van de omgeving. ' Apparaatgegevens ' kunnen worden geclassificeerd als informatie die een apparaat meebrengt naar een andere partij over zijn staat en de waargenomen status van de omgeving.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Bedreigings modellen voor de Azure IoT-referentie architectuur uitvoeren

Micro soft maakt gebruik van het Framework dat eerder is beschreven voor het maken van bedreigings modellen voor Azure IoT. In de volgende sectie wordt het concrete voor beeld van een Azure IoT-referentie architectuur gebruikt om te demonstreren hoe u kunt denken over bedreigings modellen voor IoT en hoe u de geïdentificeerde bedreigingen kunt aanpakken. In dit voor beeld worden vier hoofd gebieden van focus geïdentificeerd:

* Apparaten en gegevens bronnen,
* Gegevens transport,
* Apparaat-en gebeurtenis verwerking, en
* Stijl

![Bedreigings modellen voor Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Het volgende diagram biedt een vereenvoudigde weer gave van de IoT-architectuur van micro soft met behulp van een gegevensstroom diagram model dat wordt gebruikt door de Microsoft Threat Modeling Tool:

![Bedreigings modellen voor Azure IoT met behulp van MS Threat Modeling Tool](media/iot-security-architecture/iot-security-architecture-fig3.png)

Het is belang rijk te weten dat de architectuur de mogelijkheden van het apparaat en de gateway scheidt. Met deze aanpak kan de gebruiker gebruikmaken van gateway apparaten die veiliger zijn: ze kunnen communiceren met de Cloud gateway met behulp van beveiligde protocollen, die doorgaans een grotere verwerkings overhead vereist die een systeem eigen apparaat, zoals een thermo staat, kan bieden. Ga ervan uit dat de Cloud gateway wordt vertegenwoordigd door de Azure IoT Hub-service in de Azure-Services-zone.

### <a name="device-and-data-sourcesdata-transport"></a>Apparaat-en gegevens bronnen/gegevens transport

In deze sectie wordt een overzicht gegeven van de architectuur die eerder is beschreven in de lens van Threat modellering, en wordt uitgelegd hoe u een aantal van de belangen kunt aanpakken. Dit voor beeld is gericht op de belangrijkste elementen van een bedreigings model:

* Processen (zowel onder uw beheer als externe items)
* Communicatie (ook wel gegevens stromen genoemd)
* Opslag (ook wel gegevens archieven genoemd)

#### <a name="processes"></a>Processen

In elk van de categorieën die worden beschreven in de Azure IoT-architectuur, wordt in dit voor beeld geprobeerd een aantal verschillende dreigingen te beperken over de verschillende fasen gegevens/informatie: proces, communicatie en opslag. Hieronder vindt u een overzicht van de meest voorkomende taken voor de categorie ' proces ', gevolgd door een overzicht van hoe deze bedreigingen het beste kunnen worden beperkt:

**Spoofing (S)**: een aanvaller kan cryptografische-sleutel materiaal extra heren van een apparaat, hetzij op het niveau van de software of hardware, en vervolgens toegang krijgen tot het systeem met een ander fysiek of virtueel apparaat onder de identiteit van het apparaat waaruit het belang rijk materiaal afkomstig is. Een goede illustratie is externe besturings elementen waarmee u wille keurige TV en populaire Prankster-hulpprogram ma's kunt draaien.

**Denial of service (D)**: een apparaat kan worden gerenderd en kan niet worden gecommuniceerd door de radio frequenties of de snij draden te beïnvloeden. Zo kan een bewakings camera die de stroom of netwerk verbinding heeft uitgesp aard, geen gegevens meer rapporteren.

**Knoeien (T)**: een aanvaller kan de software die wordt uitgevoerd op het apparaat gedeeltelijk of volledig vervangen, waardoor de vervangen software de legitieme identiteit van het apparaat kan gebruiken als het sleutel materiaal of de cryptografische faciliteiten met belang rijke materialen beschikbaar zijn voor het illegale programma. Een aanvaller kan bijvoorbeeld gebruikmaken van geëxtraheerde sleutel materiaal om gegevens van het apparaat op het communicatie traject te onderscheppen en te onderdrukken en dit te vervangen door onjuiste gegevens die worden geverifieerd met het gestolen sleutel materiaal.

**Openbaar making van informatie (I)**: als het apparaat gemanipuleerde software uitvoert, zouden gemanipuleerde software mogelijk gegevens kunnen lekken bij niet-geautoriseerde partijen. Een aanvaller kan bijvoorbeeld gebruikmaken van geëxtraheerde sleutel materiaal om zichzelf te injecteren in het communicatie traject tussen het apparaat en een controller-of veld Gateway of een Cloud gateway om informatie uit te Siphon.

**Uitbrei ding van bevoegdheden (E)**: een apparaat dat een specifieke functie heeft, kan worden gedwongen om iets anders te doen. Zo kan een klep die is geprogrammeerd om te worden geopend, zo worden gerenderd dat deze helemaal kan worden geopend.

| **Onderdeel** | **Bedreiging** | **Oplossing** | **Risico** | **Implementatie** |
| --- | --- | --- | --- | --- |
| Apparaat |S |Identiteit toewijzen aan het apparaat en het apparaat verifiëren |Het apparaat of een deel van het apparaat vervangen door een ander apparaat. Hoe weet u dat u met het juiste apparaat praat? |Het apparaat verifiëren met behulp van Transport Layer Security (TLS) of IPSec. Infra structuur moet ondersteuning bieden voor het gebruik van een vooraf gedeelde sleutel (PSK) op de apparaten die geen volledige asymmetrische crypto grafie kunnen verwerken. Gebruik Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Pas tamperproof-mechanismen toe op het apparaat, bijvoorbeeld door het niet mogelijk te maken sleutels en andere cryptografische materialen van het apparaat te extra heren. |Het risico is als iemand het apparaat knoeit (fysieke storing). Hoe weet u zeker dat er niet met het apparaat is geknoeid. |De meest efficiënte oplossing is een TPM-mogelijkheid (Trusted Platform Module) waarmee sleutels kunnen worden opgeslagen in een speciale on-chip-circuit van waaruit de sleutels niet kunnen worden gelezen, maar kan alleen worden gebruikt voor cryptografische bewerkingen die de sleutel gebruiken, maar die de sleutel nooit vrijgeven. Geheugen versleuteling van het apparaat. Sleutel beheer voor het apparaat. De code te ondertekenen. |
|| E |Toegangs beheer van het apparaat hebben. Autorisatie schema. |Als het apparaat toestaat dat afzonderlijke acties worden uitgevoerd op basis van opdrachten uit een externe bron of zelfs gekraakte Sens oren, kan de aanval bewerkingen uitvoeren die niet op andere wijze toegankelijk zijn. |Verificatie schema voor het apparaat |
| Veld Gateway |S |De veld Gateway verifiëren voor de Cloud gateway (zoals CERT op basis van certificaat, PSK of claim op basis van) |Als iemand een veld Gateway kan vervalsen, kan deze als elk apparaat worden weer gegeven. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Alle aandachtspunten met betrekking tot dezelfde sleutel opslag en verklaring van apparaten in het algemeen: aanbevolen is TPM te gebruiken. 6LowPAN-extensie voor IPSec ter ondersteuning van draadloze sensor netwerken (WSN). |
|| TRID |De veld Gateway beveiligen tegen knoeien (TPM?) |Spoofing-aanvallen die de Cloud gateway ertoe leiden dat deze praten met de veld Gateway kan resulteren in het vrijgeven van informatie en het wijzigen van gegevens |Geheugen versleuteling, TPM, authenticatie. |
|| E |Mechanisme voor toegangs beheer voor veld Gateway | | |

Hier volgen enkele voor beelden van bedreigingen in deze categorie:

**Spoofing**: een aanvaller kan cryptografische-sleutel materiaal extra heren van een apparaat, hetzij op het niveau van de software of hardware, en vervolgens toegang krijgen tot het systeem met een ander fysiek of virtueel apparaat onder de identiteit van het apparaat waarvan het sleutel materiaal afkomstig is.

**Denial of service**: een apparaat kan worden gerenderd en kan niet worden gecommuniceerd door de radio frequenties of de snij draden te beïnvloeden. Zo kan een bewakings camera die de stroom of netwerk verbinding heeft uitgesp aard, geen gegevens meer rapporteren.

**Knoeien**: een aanvaller kan de software die wordt uitgevoerd op het apparaat gedeeltelijk of volledig vervangen, waardoor de vervangen software de legitieme identiteit van het apparaat kan gebruiken als het sleutel materiaal of de cryptografische faciliteiten met belang rijke materialen beschikbaar zijn voor het illegale programma.

**Knoeien**: een bewakings camera die een zicht bare afbeelding van een lege hallwaymonitoren weergeeft, kan worden gericht op een foto van een dergelijke hallwaymonitoren. Een rook-of brand sensor zou kunnen rapporteren dat iemand een lichtere IT heeft. In beide gevallen is het apparaat mogelijk technisch volledig betrouwbaar voor het systeem, maar wordt gemanipuleerde informatie gerapporteerd.

**Knoeien**: een aanvaller kan gebruikmaken van geëxtraheerde sleutel materiaal om gegevens van het apparaat te onderscheppen en te onderdrukken op het communicatie traject en dit te vervangen door onjuiste gegevens die worden geverifieerd met het gestolen sleutel materiaal.

**Knoeien**: een aanvaller kan de software die wordt uitgevoerd op het apparaat gedeeltelijk of volledig vervangen, waardoor de vervangen software de legitieme identiteit van het apparaat kan gebruiken als het sleutel materiaal of de cryptografische faciliteiten met belang rijke materialen beschikbaar zijn voor het illegale programma.

**Vrijgeven van informatie**: als er gemanipuleerde software op het apparaat wordt uitgevoerd, kunnen dergelijke gemanipuleerde software gegevens lekken bij niet-geautoriseerde partijen.

**Openbaar making van informatie**: een aanvaller kan gebruikmaken van geëxtraheerde sleutel materialen om zichzelf te injecteren in het communicatie traject tussen het apparaat en een controller of veld Gateway of de Cloud gateway om informatie uit te Siphon.

**Denial of service**: het apparaat kan worden uitgeschakeld of in een modus worden gezet, waar communicatie niet mogelijk is (wat opzettelijk op veel industriële computers is).

**Knoeien**: het apparaat kan opnieuw worden geconfigureerd om te worden uitgevoerd in een status die niet bekend is met het besturings systeem (buiten de bekende kalibratie parameters) en daarom gegevens levert die kunnen worden geïnterpreteerd

**Uitbrei ding van bevoegdheden**: een apparaat dat een specifieke functie heeft, kan worden gedwongen om iets anders te doen. Zo kan een klep die is geprogrammeerd om te worden geopend, zo worden gerenderd dat deze helemaal kan worden geopend.

**Denial of service**: het apparaat kan worden omgezet in een status waarin communicatie niet mogelijk is.

**Knoeien**: het apparaat kan opnieuw worden geconfigureerd om te worden uitgevoerd in een status die niet bekend is met het besturings systeem (buiten de bekende kalibratie parameters) en daarom gegevens levert die kunnen worden geïnterpreteerd.

**Spoofing/manipulatie/afwijzing**: als het apparaat niet is beveiligd (wat zelden het geval is met de externe besturings elementen van de consument), kan een aanvaller de status van een faxapparaat anoniem manipuleren. Een goede illustratie is externe besturings elementen waarmee u wille keurige TV en populaire Prankster-hulpprogram ma's kunt draaien.

#### <a name="communication"></a>Communicatie

Bedreigingen rond het communicatie traject tussen apparaten, apparaten en veld gateways en apparaat-en Cloud gateway. De volgende tabel bevat enkele richt lijnen rond open sockets op het apparaat/VPN:

| **Onderdeel** | **Bedreiging** | **Oplossing** | **Risico** | **Implementatie** |
| --- | --- | --- | --- | --- |
| Apparaat IoT Hub |TID |! TLS (PSK/RSA) om het verkeer te versleutelen |De communicatie tussen het apparaat en de gateway wordt geluisterd of verstoord |Beveiliging op het niveau van het protocol. Met aangepaste protocollen moet u weten hoe u ze kunt beveiligen. In de meeste gevallen wordt de communicatie van het apparaat naar de IoT Hub (het apparaat initieert de verbinding). |
| Apparaat naar apparaat |TID |! TLS (PSK/RSA) om het verkeer te versleutelen. |Gegevens lezen in transit tussen apparaten. Knoeien met de gegevens. Het apparaat overbelasten met nieuwe verbindingen |Beveiliging op het protocol niveau (MQTT/AMQP/HTTP/CoAP. Met aangepaste protocollen moet u weten hoe u ze kunt beveiligen. De risico beperking voor het DoS-risico is op peer-apparaten via een Cloud-of veld Gateway en heeft deze alleen als clients in het netwerk. De peering kan leiden tot een rechtstreekse verbinding tussen de peers na de Broker van de gateway |
| Apparaat voor externe entiteit |TID |Sterke koppeling van de externe entiteit aan het apparaat |De verbinding met het apparaat te verbreken. De communicatie met het apparaat wordt verstoord |De externe entiteit veilig koppelen aan het apparaat NFC/Bluetooth LE. Beheer van het operationeel paneel van het apparaat (fysiek) |
| Cloud gateway van de veld Gateway |TID |TLS (PSK/RSA) om het verkeer te versleutelen. |De communicatie tussen het apparaat en de gateway wordt geluisterd of verstoord |Beveiliging op het protocol niveau (MQTT/AMQP/HTTP/CoAP). Met aangepaste protocollen moet u weten hoe u ze kunt beveiligen. |
| Cloud gateway van apparaat |TID |TLS (PSK/RSA) om het verkeer te versleutelen. |De communicatie tussen het apparaat en de gateway wordt geluisterd of verstoord |Beveiliging op het protocol niveau (MQTT/AMQP/HTTP/CoAP). Met aangepaste protocollen moet u weten hoe u ze kunt beveiligen. |

Hier volgen enkele voor beelden van bedreigingen in deze categorie:

**Denial of service**: beperkte apparaten zijn doorgaans onder DOS-bedreiging wanneer ze actief Luis teren naar binnenkomende verbindingen of ongevraagde data grammen op een netwerk, omdat een aanvaller veel verbindingen parallel kan openen en deze niet kan onderhouden, of het apparaat kan worden overgelopen met ongevraagd verkeer. In beide gevallen kan het apparaat effectief worden gerenderd op het netwerk.

**Spoofing, vrijgeven van informatie**: beperkte apparaten en apparaten voor speciale doel einden hebben vaak één voor alle beveiligings faciliteiten, zoals wacht woord-of pincode beveiliging, of ze zijn volledig afhankelijk van het vertrouwen van het netwerk, wat betekent dat ze toegang krijgen tot informatie wanneer een apparaat zich op hetzelfde netwerk bevindt, en dat het netwerk vaak alleen wordt beveiligd door een gedeelde sleutel. Dit betekent dat wanneer het gedeelde geheim op het apparaat of netwerk wordt vermeld, het apparaat kan worden beheerd of dat de gegevens van het apparaat worden verzonden.  

**Spoofing**: een aanvaller kan de uitzending onderscheppen of gedeeltelijk onderdrukken en de herkomst (man in het midden) vervalsen.

**Knoeien**: een aanvaller kan de uitzending onderscheppen of de uitschakeling gedeeltelijk onderdrukken en valse informatie verzenden 

**Openbaar making van informatie:** een aanvaller kan Eavesdrop in een uitzending en informatie verkrijgen zonder een autorisatie- **denial of service:** een aanvaller kan het broadcast signaal opvangen en de distributie van gegevens weigeren

#### <a name="storage"></a>Storage

Elke apparaat-en veld Gateway heeft een vorm van opslag (tijdelijk voor het in de wachtrij plaatsen van gegevens, besturings systeem installatie kopie opslag).

| **Onderdeel** | **Bedreiging** | **Oplossing** | **Risico** | **Implementatie** |
| --- | --- | --- | --- | --- |
| Opslag van apparaten |TRID |Opslag versleuteling, de logboeken ondertekenen |Gegevens lezen uit de opslag (PII-gegevens), knoeien met telemetriegegevens. Knoeien met opdracht besturings gegevens in de wachtrij of in de cache. Knoeien met configuratie-of firmware-update pakketten die lokaal in de cache zijn opgeslagen of in de wachtrij staan, kunnen leiden tot het besturings systeem en/of systeem onderdelen die worden aangetast |Versleuteling, bericht verificatie code (MAC) of digitale hand tekening. Waar mogelijk, krachtig toegangs beheer via toegangs beheer lijsten (Acl's) of machtigingen voor de resource. |
| Installatie kopie van het besturings systeem van apparaat |TRID | |Knoeien met het besturings systeem/replacing de besturingssysteem onderdelen |Alleen-lezen OS-partitie, ondertekende OS-installatie kopie, versleuteling |
| Veld Gateway opslag (in de wachtrij plaatsen van de gegevens) |TRID |Opslag versleuteling, de logboeken ondertekenen |Gegevens lezen uit de opslag (PII-gegevens), knoeien met telemetriegegevens, knoeien met gegevens in de wachtrij of in de cache met opdracht besturings elementen. Knoeien met configuratie-of firmware-update pakketten (bestemd voor apparaten of veld Gateway) terwijl lokaal in de cache is opgeslagen of in de wachtrij is geplaatst, kan leiden tot besturings systeem-en/of systeem onderdelen die worden aangetast |BitLocker |
| Installatie kopie van het besturings systeem voor de veld Gateway |TRID | |Knoeien met het besturings systeem/replacing de besturingssysteem onderdelen |Alleen-lezen OS-partitie, ondertekende OS-installatie kopie, versleuteling |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Apparaat-en gebeurtenis verwerking/Cloud gateway zone

Een Cloud gateway is een systeem dat externe communicatie mogelijk maakt vanuit en naar apparaten of veld gateways van verschillende verschillende locaties via open bare netwerk ruimte, in het algemeen naar een systeem op basis van de Cloud en een gegevens analysesysteem, een Federatie van deze systemen. In sommige gevallen kan een Cloud gateway direct de toegang tot apparaten met speciale doel einden van terminals, zoals tablets of telefoons, vergemakkelijken. In de hier besproken context is ' Cloud ' bedoeld om te verwijzen naar een speciaal systeem voor gegevens verwerking dat niet is gebonden aan dezelfde site als de gekoppelde apparaten of veld gateways, en waarbij operationele maat regelen de beoogde fysieke toegang verhinderen, maar niet noodzakelijkerwijs een ' open bare Cloud '-infra structuur. Een Cloud gateway kan mogelijk worden toegewezen aan een netwerk-virtualisatie-overlay om de Cloud gateway en alle bijbehorende apparaten of veld gateways van elk ander netwerk verkeer te isoleren. De Cloud gateway zelf is geen Apparaatbeheer systeem of een opslag ruimte voor apparaatgegevens. deze faciliteiten interface met de Cloud gateway. De zone Cloud gateway bevat de Cloud gateway zelf, samen met alle veld gateways en apparaten die rechtstreeks of indirect zijn gekoppeld.

Cloud gateway is voornamelijk aangepaste ingebouwde software die wordt uitgevoerd als een service met blootgestelde eind punten waarmee veld Gateway en apparaten verbinding maken. Dit moet zo worden ontworpen met het oog op de beveiliging. Volg het [sdl](https://www.microsoft.com/sdl) -proces voor het ontwerpen en bouwen van deze service.

#### <a name="services-zone"></a>Services-zone

Een besturings systeem (of controller) is een software oplossing die is gekoppeld aan een apparaat of een veld Gateway, of met een Cloud gateway voor het beheren van een of meer apparaten en/of het verzamelen en/of ophalen en/of voor de analyse van apparaatgegevens voor presentatie of volgende controle doeleinden. Besturings systemen zijn de enige entiteiten in het bereik van deze discussie, waardoor de interactie met mensen direct kan worden vergemakkelijkt. De uitzonde ringen zijn tussenliggende fysieke beheerste Opper vlakken op apparaten, zoals een switch waarmee een persoon het apparaat kan uitschakelen of andere eigenschappen kan wijzigen, en waarvoor geen functioneel equivalent is dat digitaal kan worden geopend.

Tussenliggende fysieke controle oppervlakken zijn de Opper vlakten die de functie van het fysieke besturings element beheersen, zodat een gelijkwaardige functie op afstand kan worden geïnitieerd of invoer conflicten met externe invoer kunnen worden vermeden: dergelijke tussenliggende controle oppervlakken worden conceptueel gekoppeld aan een lokaal controle systeem dat gebruikmaakt van dezelfde onderliggende functionaliteit als elk ander systeem voor extern beheer waarop het apparaat parallel kan worden aangesloten. De belangrijkste bedreigingen voor cloud computing kunnen worden gelezen op de pagina [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) .

## <a name="additional-resources"></a>Aanvullende bronnen

Raadpleeg voor meer informatie de volgende artikelen:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [IoT-referentie architectuur Microsoft Azure](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)